---
title: Azure IoT 中心模块标识和模块孪生 （Python）
description: 了解如何使用用于 Python 的 IoT SDK 创建模块标识和更新模块孪生。
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: menchi
ms.openlocfilehash: f846af548913e0cb3e872560e4b8438da306a255
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756938"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-python"></a>IoT 中心模块标识和模块孪生 (Python) 入门

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [模块标识和模块孪生](iot-hub-devguide-module-twins.md)类似于 Azure IoT 中心设备标识和设备孪生，但提供更精细的粒度。 当 Azure IoT 中心设备标识和设备孪生使后端应用程序能够配置设备并提供设备条件的可见性时，模块标识和模块孪生为设备的单个组件提供了这些功能。 在具有多个组件（如基于操作系统的设备或固件设备）的功能设备上，它们允许为每个组件提供隔离的配置和条件。
>

在本教程结束时，您有三个 Python 应用：

* **CreateModule**，它创建设备标识、模块标识和相关安全密钥来连接设备和模块客户端。

* **更新模块Twin希望属性**，它向 IoT 中心发送更新的模块孪生所需属性。

* **接收模块Twin希望属性补丁**，它接收您设备上的模块孪生所需属性修补程序。

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>先决条件

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>获取 IoT 中心连接字符串

在本文中，将创建一个后端服务，该服务在标识注册表中添加一个设备，然后向该设备添加一个模块。 此服务需要**注册表写入**权限（还包括**注册表读取**）。 您还可以创建一个服务，将所需属性添加到新创建的模块的模块孪生。 此服务需要**服务连接**权限。 尽管存在单独授予这些权限的默认共享访问策略，但在本节中，您可以创建包含这两种权限的自定义共享访问策略。

[!INCLUDE [iot-hub-include-find-service-regrw-connection-string](../../includes/iot-hub-include-find-service-regrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>在 IoT 中心中创建设备标识和模块标识

在本节中，您将创建一个 Python 服务应用，在 IoT 中心中的标识注册表中创建设备标识和模块标识。 设备或模块无法连接到 IoT 中心，除非它在标识注册表中具有条目。 有关详细信息，请参阅了解[IoT 中心中的标识注册表](iot-hub-devguide-identity-registry.md)。 运行此控制台应用时，它会为设备和模块生成唯一的 ID 和密钥。 设备和模块在向 IoT 中心发送设备到云的消息时，使用这些值来标识自身。 ID 区分大小写。

1. 在命令提示符处，运行以下命令以安装 **azure-iot-hub** 包：

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. 在命令提示符下，运行以下命令以安装**msrest**包。 您需要此包来捕获**HTTP 操作错误**异常。

    ```cmd/sh
    pip install msrest
    ```

1. 使用文本编辑器，在工作目录中创建名为**CreateModule.py**的文件。

1. 将以下代码添加到 Python 文件。 将*IoTHubConnectString*替换为在[获取 IoT 中心连接字符串](#get-the-iot-hub-connection-string)中复制的连接字符串。

    ```python
    import sys
    from msrest.exceptions import HttpOperationError
    from azure.iot.hub import IoTHubRegistryManager

    CONNECTION_STRING = "YourIotHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        try:
            # CreateDevice - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            device_state = "enabled"
            new_device = iothub_registry_manager.create_device_with_sas(
                DEVICE_ID, primary_key, secondary_key, device_state
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the device already exists.
                new_device = iothub_registry_manager.get_device(DEVICE_ID)
            else:
                raise

        print("device <" + DEVICE_ID +
              "> has primary key = " + new_device.authentication.symmetric_key.primary_key)

        try:
            # CreateModule - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            managed_by = ""
            new_module = iothub_registry_manager.create_module_with_sas(
                DEVICE_ID, MODULE_ID, managed_by, primary_key, secondary_key
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the module already exists.
                new_module = iothub_registry_manager.get_module(DEVICE_ID, MODULE_ID)
            else:
                raise

        print("device/module <" + DEVICE_ID + "/" + MODULE_ID +
              "> has primary key = " + new_module.authentication.symmetric_key.primary_key)

    except Exception as ex:
        print("Unexpected error {0}".format(ex))
    except KeyboardInterrupt:
        print("IoTHubRegistryManager sample stopped")
    ```

1. 在命令提示符下，运行以下命令：

    ```cmd/sh
    python CreateModule.py
    ```

此应用在设备“myFirstDevice”下创建 ID 为“myFirstDevice”的设备标识，以及 ID 为“myFirstModule”的模块标识************。 （如果标识注册表中已存在设备或模块 ID，则代码只需检索现有设备或模块信息。应用显示每个标识的 ID 和主键。

> [!NOTE]
> IoT 中心标识注册表只存储设备和模块标识，以启用对 IoT 中心的安全访问。 标识注册表存储用作安全凭据的设备 ID 和密钥。 标识注册表还为每个设备存储启用/禁用标志，该标志可以用于禁用对该设备的访问。 如果应用程序需要存储其他特定于设备的元数据，则应使用特定于应用程序的存储。 没有针对模块标识的“已启用/已禁用”标记。 有关详细信息，请参阅了解[IoT 中心中的标识注册表](iot-hub-devguide-identity-registry.md)。
>

## <a name="update-the-module-twin-using-python-service-sdk"></a>使用 Python 服务 SDK 更新模块孪生

在本节中，您将创建一个 Python 服务应用，用于更新模块孪生所需属性。

1. 在命令提示符下，运行以下命令以安装**azure-iot 集线器**包。 如果在上一节中安装了**azure-iot 集线器**包，则可以跳过此步骤。

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. 使用文本编辑器，在工作目录中创建名为**UpdateModuleTwinDesiredProperties.py**的文件。

1. 将以下代码添加到 Python 文件。 将*IoTHubConnectString*替换为在[获取 IoT 中心连接字符串](#get-the-iot-hub-connection-string)中复制的连接字符串。

    ```python
    import sys
    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties

    CONNECTION_STRING = "YourIoTHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        module_twin = iothub_registry_manager.get_module_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Module twin properties before update    :" )
        print ( "{0}".format(module_twin.properties) )

        # Update twin
        twin_patch = Twin()
        twin_patch.properties = TwinProperties(desired={"telemetryInterval": 122})
        updated_module_twin = iothub_registry_manager.update_module_twin(
            DEVICE_ID, MODULE_ID, twin_patch, module_twin.etag
        )
        print ( "" )
        print ( "Module twin properties after update     :" )
        print ( "{0}".format(updated_module_twin.properties) )

    except Exception as ex:
        print ( "Unexpected error {0}".format(ex) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

## <a name="get-updates-on-the-device-side"></a>在设备端获取更新

在本节中，您将创建一个 Python 应用，以获得设备上的模块孪生所需属性更新。

1. 获取模块连接字符串。 在[Azure 门户](https://portal.azure.com/)中，导航到 IoT 中心，并在左侧窗格中选择**IoT 设备**。 从设备列表中选择**myFirstDevice**并打开它。 在**模块标识**下，选择**我的第一个模块**。 复制模块连接字符串。 您需要在下面的步骤。

   ![Azure 门户模块详细信息](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

1. 在命令提示符处，运行以下命令以安装 **azure-iot-device** 包：

    ```cmd/sh
    pip install azure-iot-device
    ```

1. 使用文本编辑器，在工作目录中创建名为**ReceiveModuleTwinDesiredPropertiesPatch.py**的文件。

1. 将以下代码添加到 Python 文件。 将*ModuleConnectString*替换为步骤 1 中复制的模块连接字符串。

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient

    CONNECTION_STRING = "YourModuleConnectionString"


    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("")
            print("Twin desired properties patch received:")
            print(patch)

    def iothub_client_sample_run():
        try:
            module_client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(module_client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            while True:
                time.sleep(1000000)

        except KeyboardInterrupt:
            print("IoTHubModuleClient sample stopped")


    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

## <a name="run-the-apps"></a>运行应用

在本节中，您将运行 **"接收模块Twin希望属性"** 设备应用，然后运行**UpdateModuleTwin 希望属性**服务应用以更新模块所需的属性。

1. 打开命令提示并运行设备应用：

    ```cmd/sh
    python ReceiveModuleTwinDesiredPropertiesPatch.py
    ```

   ![设备应用初始输出](./media/iot-hub-python-python-module-twin-getstarted/device-1.png)

1. 打开单独的命令提示符并运行服务应用：

    ```cmd/sh
    python UpdateModuleTwinDesiredProperties.py
    ```

    请注意，服务应用输出中更新的模块孪生中将显示 **"遥测间隔**"所需属性：

   ![服务应用输出](./media/iot-hub-python-python-module-twin-getstarted/service.png)

    相同的属性显示在设备应用输出中接收的所需属性修补程序中：

   ![设备应用输出显示所需的属性修补程序](./media/iot-hub-python-python-module-twin-getstarted/device-2.png)

## <a name="next-steps"></a>后续步骤

若要继续了解 IoT 中心入门知识并浏览其他 IoT 方案，请参阅：

* [设备管理入门](iot-hub-node-node-device-management-get-started.md)

* [IoT 边缘入门](../iot-edge/tutorial-simulate-device-linux.md)