---
title: 適用于 Azure 通訊服務的用戶端程式庫和 REST Api
titleSuffix: An Azure Communication Services concept document
description: 深入瞭解 Azure 通訊服務用戶端程式庫和 REST Api。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/18/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: f26f3e6e80bf854ff4f57503aa43de2558f07a1e
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888550"
---
# <a name="client-libraries-and-rest-apis"></a>用戶端程式庫和 REST API

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Azure 通訊服務功能在概念上會組織成六個區域。 某些區域具有完全開放原始碼的用戶端程式庫。 呼叫的用戶端程式庫會使用專屬的網路介面，且目前為關閉的來源，而聊天庫則包含關閉來源的相依性。 用戶端程式庫的範例和其他技術詳細資料會發佈在 [Azure 通訊服務 GitHub](https://github.com/Azure/communication)存放庫中。

## <a name="client-libraries"></a>用戶端程式庫

| 組件               | 通訊協定             |開啟與關閉的來源| 命名空間                          | 功能                                                      |
| ---------------------- | --------------------- | ---|-------------------------- | --------------------------------------------------------------------------- |
| Azure Resource Manager | REST | 開啟            | Azure ResourceManager 通訊 | 布建和管理通訊服務資源             |
| 通用                 | REST | 開啟               | Azure 通訊。 Common          | 提供其他用戶端程式庫的基底類型 |
| 系統管理         | REST | 開啟               | Azure 通訊。管理  | 管理使用者、存取權杖及電話號碼、配置符合標準的 STUN 並開啟伺服器 |
| 聊天                   | 具有專屬信號的 REST | 開啟檔案關閉的來源信號套件    | Azure 通訊。聊天            | 在您的應用程式中新增即時以文字為基礎的聊天  |
| sms                    | REST | 開啟              | Azure。 SMS             | 傳送和接收 SMS 訊息 |
| 呼叫                | 專屬傳輸 | 封閉式 |Azure 通訊。呼叫         | 利用語音、影片、螢幕共用和其他即時資料通訊功能          |

請注意，Azure Resource Manager、系統管理和 SMS 用戶端程式庫都是以服務整合為焦點，在許多情況下，如果您將這些功能整合到使用者應用程式中，就會發生安全性問題。 常見和聊天用戶端程式庫適用于服務和用戶端應用程式。 呼叫的用戶端程式庫是專為用戶端應用程式所設計。 著重于服務案例的用戶端程式庫正在開發中。

### <a name="languages-and-publishing-locations"></a>語言和發佈位置

以下詳述個別用戶端程式庫套件的發佈位置。 

| 區域           | JavaScript | .NET | Python | Java SE | iOS | Android | 其他                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | -         | [NuGet](https://www.nuget.org/packages/Azure.ResourceManager.Communication)    |   [PyPi](https://pypi.org/project/azure-mgmt-communication/)    |  -  | -              | -  | [Go via GitHub](https://github.com/Azure/azure-sdk-for-go/releases/tag/v46.3.0) |
| 通用         | [npm](https://www.npmjs.com/package/@azure/communication-common) \(英文\)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Common/)    | N/A      | [Maven](https://search.maven.org/search?q=a:azure-communication-common)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases/tag/1.0.0-beta.1)            | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-common)             | -                              |
| 系統管理 | [npm](https://www.npmjs.com/package/@azure/communication-administration) \(英文\)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Administration)    | [PyPi](https://pypi.org/project/azure-communication-administration/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-administration)   | -              | -              | -                            |
| 聊天           | [npm](https://www.npmjs.com/package/@azure/communication-chat) \(英文\)        | [NuGet](https://www.nuget.org/packages/Azure.Communication.Chat)     | [PyPi](https://pypi.org/project/azure-communication-chat/)     | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)  | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | -                              |
| sms            | [npm](https://www.npmjs.com/package/@azure/communication-sms) \(英文\)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Sms)    | [PyPi](https://pypi.org/project/azure-communication-sms/)       | [Maven](https://search.maven.org/artifact/com.azure/azure-communication-sms)   | -              | -              | -                              |
| 呼叫        | [npm](https://www.npmjs.com/package/@azure/communication-calling) \(英文\)         | -      | -      | -     | [GitHub](https://github.com/Azure/Communication/releases/tag/v1.0.0-beta.2)     | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-calling/)            | -                              |
| 參考文件     | [docs](https://azure.github.io/azure-sdk-for-js/communication.html)         | [docs](https://azure.github.io/azure-sdk-for-net/communication.html)      | -      | [docs](http://azure.github.io/azure-sdk-for-java/communication.html)     |  (Obj-C) ✔️     | ✔️            | -                              |
## <a name="rest-apis"></a>REST API

通訊服務 Api 會與其他 Azure REST Api 一起記載于 [docs.microsoft.com](/rest/api/azure/)中。 本檔將告訴您如何結構您的 HTTP 訊息，並提供使用 Postman 的指引。 本檔也會在 [GitHub](https://github.com/Azure/azure-rest-api-specs)上以 Swagger 格式提供。

## <a name="additional-support-details"></a>其他支援詳細資料

### <a name="ios-and-android-support-details"></a>iOS 和 Android 支援詳細資料

- 通訊服務 iOS 用戶端程式庫以 iOS 13 版 + 和 Xcode 11 + 為目標。
- Android JAVA 用戶端程式庫以 Android API 層級 21 + 和 Android Studio 4.0 + 為目標

### <a name="net-support-details"></a>.NET 支援詳細資料

除了呼叫之外，通訊服務封裝的目標 .NET Standard 2.0 支援下列平臺。

透過 .NET Framework 4.6.1 的支援
- Windows 10、8.1、8和7
- Windows Server 2012 R2、2012和 2008 R2 SP1

透過 .NET Core 2.0 的支援：
- Windows 10 (1607 +) 、7 SP1 +、8。1
- Windows Server 2008 R2 SP1+
- 最大 OS X 10.12 +
- Linux 多個版本/發行版本
- UWP 10.0.16299 (RS3) 2017 年9月
- Unity 2018。1
- Mono 5.4
- Xamarin iOS 10.14
- Xamarin Mac 3。8

## <a name="api-stability-expectations"></a>API 穩定性的期望 

> [!IMPORTANT]
> 本節提供 REST Api 和用戶端程式庫標記為 **穩定** 的指引。 標記為發行前版本、預覽或搶鮮版的 Api 可能會變更或淘汰， **恕不另行通知**。 Azure 通訊服務目前處於 **公開預覽** 狀態，而 api 則標示為如此。

未來，我們可能會淘汰通訊服務用戶端程式庫的版本，而我們可能會對 REST Api 和發行的用戶端程式庫引進重大變更。 Azure 通訊服務 *通常* 會遵循兩個可支援性原則來淘汰服務版本：

- 由於通訊服務介面變更，您將會在需要變更程式碼前三年通知您。 所有記載的 REST Api 和用戶端程式庫 Api 在介面解除委任之前，通常至少會享有三年的警告。
- 在您必須將用戶端程式庫元件更新至最新的次要版本之前，系統將會通知您至少一年。 這些必要的更新不需要變更任何程式碼，因為它們是在相同的主要版本中。 這特別適用于具有即時元件的呼叫和聊天程式庫，這些程式庫經常需要安全性和效能更新。 強烈建議您保持通訊服務用戶端程式庫的更新。

### <a name="api-and-client-library-decommissioning-examples"></a>API 和用戶端程式庫解除委任範例

**您已將 SMS REST API 的 v24 版本整合到您的應用程式中。Azure 通訊版本 v25。**

在這些 Api 停止運作之前，您將會收到3年的警告，並強制更新為 v25。 此更新可能需要變更程式碼。

**您已將呼叫用戶端程式庫的2.02 版本整合到您的應用程式。Azure 通訊版本 v 2.05。**

您可能需要在2.05 版本的12個月內，更新至2.05 版的呼叫用戶端程式庫。 這應該可以簡單地取代成品，而不需要變更程式碼，因為 v 2.05 是在 v2 主要版本中，而且沒有任何重大變更。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列用戶端程式庫總覽：

- [呼叫用戶端程式庫總覽](../concepts/voice-video-calling/calling-sdk-features.md)
- [聊天用戶端程式庫總覽](../concepts/chat/sdk-features.md)
- [SMS 用戶端程式庫總覽](../concepts/telephony-sms/sdk-features.md)

若要開始使用 Azure 通訊服務：

- [建立 Azure 通訊資源](../quickstarts/create-communication-resource.md)
- 產生 [使用者存取權杖](../quickstarts/access-tokens.md)