---
title: 语音设备 SDK 文档
titleSuffix: Azure Cognitive Services
description: 发行说明提供了有关语音设备 SDK 的更新、增强功能、bug 修复和更改的日志。 本文随每个版本的语音设备 SDK 进行更新。
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: wellsi
ms.openlocfilehash: 29dcb3c0e74482fd6670d1a0983e751043379d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80371621"
---
# <a name="release-notes-speech-devices-sdk"></a>发行说明：语音设备 SDK

以下部分列出了最新版本中的更改。

## <a name="speech-devices-sdk-190"></a>语音设备 SDK 1.9.0：

- 提供了[Urbetter DDK](https://aka.ms/sdsdk-download-urbetter) （Linux ARM64） 的初始二进制文件。
- Roobo v1 现在使用 Maven 进行语音 SDK
- 将[语音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference)组件更新为版本 1.9.0。 有关详细信息，请参阅其[发行说明](https://aka.ms/csspeech/whatsnew)。

## <a name="speech-devices-sdk-170"></a>语音设备 SDK 1.7.0：

- 现在支持 Linux ARM。
- 提供了[Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2)的初始二进制文件（Linux ARM64）。
- Windows 用户可以使用 `AudioConfig.fromDefaultMicrophoneInput()` 或 `AudioConfig.fromMicrophoneInput(deviceName)` 指定要使用的麦克风。
- 库大小已经过优化。
- 支持使用相同的语音/意向识别器对象进行多轮次识别。
- 修复停止识别时可能发生的偶尔挂起。
- 示例应用现在包含一个示例 participants.properties 文件，用于演示文件的格式。
- 已将[语音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 组件更新到 1.7.0 版。 有关详细信息，请参阅其[发行说明](https://aka.ms/csspeech/whatsnew)。

## <a name="speech-devices-sdk-160"></a>语音设备 SDK 1.6.0：

- 在 Windows 和 Linux 上支持 [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)，提供常见的[示例应用程序](https://aka.ms/sdsdk-download)
- 已将[语音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 组件更新到 1.6.0 版。 有关详细信息，请参阅其[发行说明](https://aka.ms/csspeech/whatsnew)。

## <a name="speech-devices-sdk-151"></a>语音设备 SDK 1.5.1：

- 在示例应用中包括[对话听录](conversation-transcription-service.md)。
- 已将[语音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 组件更新到 1.5.1 版。 有关详细信息，请参阅其[发行说明](https://aka.ms/csspeech/whatsnew)。

## <a name="speech-devices-sdk-150-2019-may-release"></a>语音设备 SDK 1.5.0： 2019-5 月发布

- 语音设备 SDK 现为 GA，不再是受限的预览版。
- 已将[语音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 组件更新到 1.5.0 版。 有关详细信息，请参阅其[发行说明](https://aka.ms/csspeech/whatsnew)。
- 新的关键字技术带来了显著的质量改进，请参阅“中断性变更”。
- 新的音频处理管道改进了远端域识别。

**重大更改**

- 由于新出现的关键字技术，所有关键字必须在改进的关键字门户中重新创建。 若要从设备中完全删除旧的关键字，请卸载旧应用。
  - adb 卸载 com.microsoft.认知服务.语音.sample.sdsdkstarterapp

## <a name="speech-devices-sdk-140-2019-apr-release"></a>语音设备 SDK 1.4.0： 2019-4 月版本

- 已将[语音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 组件更新到 1.4.0 版。 有关详细信息，请参阅其[发行说明](https://aka.ms/csspeech/whatsnew)。

## <a name="speech-devices-sdk-131-2019-mar-release"></a>语音设备 SDK 1.3.1：2019-3 月版本

- 已将[语音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 组件更新到 1.3.1 版。 有关详细信息，请参阅其[发行说明](https://aka.ms/csspeech/whatsnew)。
- 更新了关键字处理，请参阅“中断性变更”。
- 示例应用程序添加了选择语言的功能，适用于语音识别和翻译。

**重大更改**

- [安装关键字](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)已简化，它现在是应用程序的一部分，不需要单独安装在设备上。
- 关键字识别已更改，支持两个事件。
  - `RecognizingKeyword,`指示语音结果包含（未经验证）关键字文本。
  - `RecognizedKeyword`，指示关键字识别已完成对给定关键字的识别。

## <a name="speech-devices-sdk-110-2018-nov-release"></a>语音设备 SDK 1.1.0： 2018-11 月版本

- 已将[语音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 组件更新到 1.1.0 版。 有关详细信息，请参阅其[发行说明](https://aka.ms/csspeech/whatsnew)。
- 远场语音识别准确性已通过我们增强的音频处理算法得到提高。
- 示例应用程序添加了中文语音识别支持。

## <a name="speech-devices-sdk-101-2018-oct-release"></a>语音设备 SDK 1.0.1： 2018-10 月版本

- 将[语音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 组件更新到了 1.0.1 版。 有关详细信息，请参阅其[发行说明](https://aka.ms/csspeech/whatsnew)。
- 我们改进的音频处理算法将提高语音识别的准确性
- 修复了一个连续识别音频会话 bug。

**重大更改**

- 该版本中推出了大量重大更改。 有关 API 的详细信息，请查看[此页](https://aka.ms/csspeech/breakingchanges_1_0_0)。
- KWS 模型文件与语音设备 SDK 1.0.1 不兼容。 将新关键字文件写入设备后，将删除现有关键字文件。

## <a name="speech-devices-sdk-050-2018-aug-release"></a>语音设备 SDK 0.5.0： 2018-8 月版本

- 通过修复音频处理代码中的 Bug，改进了语音识别的准确性。
- 将[语音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 组件更新到了 0.5.0 版。 有关详细信息，请参阅其[发行说明](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release)。

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>语音设备 SDK 0.2.12733： 2018-5 月发布

认知服务语音设备 SDK 的第一个公共预览版本。
