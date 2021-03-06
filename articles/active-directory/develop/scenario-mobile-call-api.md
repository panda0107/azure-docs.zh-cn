---
title: 从移动应用调用 Web API | Azure
titleSuffix: Microsoft identity platform
description: 了解如何构建用于调用 Web API 的移动应用。 （调用 Web API。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 28f57c5657ce2f8537a654a7f67ed4481fab2c91
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882686"
---
# <a name="call-a-web-api-from-a-mobile-app"></a>从移动应用调用 Web API

应用在用户中签名并接收令牌后，Microsoft 身份验证库 （MSAL） 会公开有关用户、用户环境和已颁发的令牌的信息。 应用可以使用这些值来调用 Web API 或向用户显示欢迎消息。

在本文中，我们将首先查看 MSAL 结果。 然后，我们将了解如何使用来自`AuthenticationResult`或`result`调用受保护 Web API 的访问令牌。

## <a name="msal-result"></a>MSAL 结果
MSAL 提供以下值： 

- `AccessToken`在 HTTP 承载请求中调用受保护的 Web API。
- `IdToken`包含有关登录用户的有用信息。 此信息包括用户名、主租户和存储的唯一标识符。
- `ExpiresOn`是令牌的过期时间。 MSAL 处理应用的自动刷新。
- `TenantId`是用户登录的租户的标识符。 对于 Azure 活动目录 （Azure AD） B2B 中的来宾用户，此值标识用户登录的租户。 该值不标识用户的家庭租户。  
- `Scopes`指示使用令牌授予的范围。 授予的作用域可能是请求的作用域的子集。

MSAL 还为`Account`值提供了抽象。 值`Account`表示当前用户的登录帐户：

- `HomeAccountIdentifier`标识用户的家庭租户。
- `UserName`是用户的首选用户名。 对于 Azure AD B2C 用户，此值可能为空。
- `AccountIdentifier`标识登录用户。 在大多数情况下，此值与值相同，`HomeAccountIdentifier`除非用户是另一个租户中的来宾。

## <a name="call-an-api"></a>调用 API

获得访问令牌后，可以调用 Web API。 你的应用将使用令牌生成 HTTP 请求，然后运行该请求。

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing.
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called Graph. Process data and send to UI.
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error.
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put access token in HTTP request.
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="msal-for-ios-and-macos"></a>适用于 iOS 和 MacOS 的 MSAL

用于获取令牌的方法返回一个 `MSALResult` 对象。 `MSALResult`公开`accessToken`属性。 您可以使用`accessToken`调用 Web API。 在调用访问受保护的 Web API 之前，将此属性添加到 HTTP 授权标头。

```objc
NSMutableURLRequest *urlRequest = [NSMutableURLRequest new];
urlRequest.URL = [NSURL URLWithString:"https://contoso.api.com"];
urlRequest.HTTPMethod = @"GET";
urlRequest.allHTTPHeaderFields = @{ @"Authorization" : [NSString stringWithFormat:@"Bearer %@", accessToken] };
        
NSURLSessionDataTask *task =
[[NSURLSession sharedSession] dataTaskWithRequest:urlRequest
     completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {}];
[task resume];
```

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]
     
let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

### <a name="xamarin"></a>Xamarin

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

## <a name="make-several-api-requests"></a>发出多个 API 请求

如果需要多次调用同一 API，或者需要调用多个 API，则在构建应用时请考虑以下主题：

- **增量同意**：Microsoft 标识平台允许应用在需要权限时获得用户同意，而不是在开始时获得所有权限。 每次应用准备好调用 API 时，它应仅请求所需的作用域。

- **条件访问**：当您发出多个 API 请求时，在某些情况下，您可能需要满足其他条件访问要求。 如果第一个请求没有条件访问策略，并且你的应用尝试静默访问需要条件访问的新 API，则需求可能会以这种方式增加。 要处理此问题，请确保捕获来自静默请求的错误，并准备好发出交互式请求。  有关详细信息，请参阅[条件访问指南](../azuread-dev/conditional-access-dev-guide.md)。

## <a name="call-several-apis-by-using-incremental-consent-and-conditional-access"></a>使用增量同意和条件访问调用多个 API

如果需要为同一用户调用多个 API，在为用户获取令牌后，可以通过随后调用`AcquireTokenSilent`以获取令牌来避免重复向用户请求凭据：

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

以下情况需要交互：

- 用户已许可第一个 API，但现在需要许可更多范围。 在这种情况下，您使用增量同意。
- 第一个 API 不需要多重身份验证，但下一个 API 需要多重身份验证。

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [移到生产环境](scenario-mobile-production.md)
