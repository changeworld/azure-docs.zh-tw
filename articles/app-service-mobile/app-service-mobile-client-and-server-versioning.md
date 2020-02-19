---
title: 用戶端和伺服器 SDK 版本控制
description: 適用于行動服務和 Azure Mobile Apps 的用戶端 Sdk 和伺服器 SDK 版本的相容性清單。
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: f24ae0a48b835785a2e000210f3609b82d42d0f6
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461550"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Mobile Apps 和行動服務中的用戶端和伺服器版本控制

Azure 行動服務的最新版本是 Azure App Service 的 **Mobile Apps** 功能。

Mobile Apps 用戶端和伺服器 SDK 最初是以行動服務中的 SDK 為基礎，但是它們彼此「不」相容。
也就是說，您必須使用 Mobile Apps 用戶端 SDK 搭配 Mobile Apps 伺服器 SDK，「行動服務」也是類似作法。 您可以透過用戶端和伺服器 SDK 所使用的特殊標頭值 `ZUMO-API-VERSION` 來強制執行。

注意：每當這份文件提及 *行動服務* 後端時，它不一定需要裝載於行動服務。 現在可以將行動服務移轉到 App Service 上執行而不用變更任何程式碼，但是服務還是會使用「行動服務」SDK 版本。

## <a name="header-specification"></a>標頭規格
鍵值 `ZUMO-API-VERSION` 可以在 HTTP 標頭或查詢字串中指定。 值是版本字串，格式為 **x.y.z**。

例如：

GET https://service.azurewebsites.net/tables/TodoItem

HEADERS: ZUMO-API-VERSION: 2.0.0

POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>選擇不進行版本檢查
您可以將應用程式設定 **MS_SkipVersionCheck** 的值設定為 **true**，選擇不要進行版本檢查。 在 web.config 中或在 Azure 入口網站的 [應用程式設定] 區段中都可指定這項設定。

> [!NOTE]
> 行動服務和 Mobile Apps 之間有許多行為改變了，尤其是在離線同步處理、驗證和推播通知的區域。 您應該在完成測試之後才選擇不要進行版本檢查，確保這些行為的變更不會影響您的 app 功能。

## <a name="2.0.0"></a>Azure Mobile Apps 用戶端和伺服器
### <a name="MobileAppsClients"></a> Mobile Apps 用戶端 SDK
版本檢查是從 **Azure Mobile Apps**下列版本的用戶端 SDK 開始引進：

| 用戶端平台 | 版本 | 版本標頭值 |
| --- | --- | --- |
| 受控用戶端 (Windows、Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

### <a name="mobile-apps-server-sdks"></a>Mobile「Apps」 伺服器 SDK
下列伺服器 SDK 版本包含版本檢查：

| 伺服器平台 | SDK | 接受的版本標頭 |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azure-mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Mobile Apps 後端的行為
| ZUMO-API-VERSION | MS_SkipVersionCheck 的值 | 回應 |
| --- | --- | --- |
| x.y.z 或 Null |True |200 - 確定 |
| Null |False/未指定 |400 - 不正確的要求 |
| 1.x.y |False/未指定 |400 - 不正確的要求 |
| 2.0.0-2.x.y |False/未指定 |200 - 確定 |
| 3.0.0-3.x.y |False/未指定 |400 - 不正確的要求 |

[Mobile Services clients]: #MobileServicesClients
[Mobile Apps clients]: #MobileAppsClients
[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
