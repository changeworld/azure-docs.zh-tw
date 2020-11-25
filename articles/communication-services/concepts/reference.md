---
title: Azure 通訊服務的參考文件概觀
titleSuffix: An Azure Communication Services concept document
description: 了解通訊服務的參考文件。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/29/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: a6d554b6addb85e444da9e2d72b66812252730cd
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888618"
---
# <a name="reference-documentation-overview"></a>參考文件概觀

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

下表詳細說明可用的通訊服務套件，以及對應的參考文件：

<!--note that this table also exists here and should be synced: https://github.com/Azure/Communication/blob/master/README.md -->

| 區域           | JavaScript | .NET | Python | Java SE | iOS | Android | 其他                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | -         | [NuGet](https://www.nuget.org/packages/Azure.ResourceManager.Communication)    |   [PyPi](https://pypi.org/project/azure-mgmt-communication/)    |  -  | -              | -  | [Go via GitHub](https://github.com/Azure/azure-sdk-for-go/releases/tag/v46.3.0) |
| 通用         | [npm](https://www.npmjs.com/package/@azure/communication-common) \(英文\)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Common/)    | N/A      | [Maven](https://search.maven.org/search?q=a:azure-communication-common)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases/tag/1.0.0-beta.1)            | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-common)             | -                              |
| 系統管理 | [npm](https://www.npmjs.com/package/@azure/communication-administration) \(英文\)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Administration)    | [PyPi](https://pypi.org/project/azure-communication-administration/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-administration)   | -              | -              | -                            |
| 聊天           | [npm](https://www.npmjs.com/package/@azure/communication-chat) \(英文\)        | [NuGet](https://www.nuget.org/packages/Azure.Communication.Chat)     | [PyPi](https://pypi.org/project/azure-communication-chat/)     | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)  | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | -                              |
| sms            | [npm](https://www.npmjs.com/package/@azure/communication-sms) \(英文\)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Sms)    | [PyPi](https://pypi.org/project/azure-communication-sms/)       | [Maven](https://search.maven.org/artifact/com.azure/azure-communication-sms)   | -              | -              | -                              |
| 呼叫        | [npm](https://www.npmjs.com/package/@azure/communication-calling) \(英文\)         | -      | -      | -     | [GitHub](https://github.com/Azure/Communication/releases/tag/v1.0.0-beta.2) ([文件](/objectivec/communication-services/calling/))     | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-calling/)            | -                              |
| 參考文件     | [docs](https://azure.github.io/azure-sdk-for-js/communication.html)         | [docs](https://azure.github.io/azure-sdk-for-net/communication.html)      | -      | [docs](http://azure.github.io/azure-sdk-for-java/communication.html)     | -     | -            | -                              |