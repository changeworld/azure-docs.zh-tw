---
title: 通知中樞與 App Service Mobile Apps 整合
description: 了解如何搭配使用 Azure 通知中樞和 Azure App Service Mobile Apps。
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 08/06/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: e7042be7e407e8e0827e142ba6878dfff812e1f6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "88004057"
---
# <a name="integration-with-app-service-mobile-apps"></a>與 App Service Mobile Apps 整合

為了在 Azure 服務間促進完美且統一的體驗，[App Service Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) 具備使用 Azure 通知中樞來提供通知的內建支援。 [App Service Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) 具有高擴充性且可供全球使用，是專為企業開發人員與系統整合人員設計的行動應用程式開發平台，能提供一組豐富的功能給行動應用程式開發人員。

Mobile Apps 開發人員可以使用下列流程來利用通知中樞：

1. 擷取裝置 PNS 控制代碼。
2. 使用 Mobile Apps 用戶端 SDK 註冊 API 向通知中樞註冊裝置。

    > [!NOTE]
    > 請注意，Mobile Apps 會基於安全性考量，去除註冊上的所有標籤。 直接從後端使用通知中樞，將標籤關聯至裝置。

3. 從 App 後端使用通知中樞傳送通知。

此整合提供的一些優點包括：

- **Mobile Apps 用戶端 SDK**：這些多平台 SDK 會提供用來註冊的 API，並且會與行動應用程式所連結的通知中樞進行通訊。 您不需要通知中樞認證，也不需要使用其他服務。
  - *推送給使用者*︰SDK 會使用 Mobile Apps 驗證的使用者識別碼自動標記指定的裝置，以啟用「推送至使用者」案例。
  - *推送至裝置*︰SDK 會自動使用 Mobile Apps 安裝識別碼作為 GUID 來向通知中樞註冊，因此不需要維護多個服務 GUID。
- **安裝模型**：Mobile Apps 會使用通知中樞的最新推送模型，來呈現 JSON 安裝中所有與裝置相關聯的推送屬性，其會與推播通知密切合作且易於使用。
- **彈性**：即使已就地整合，開發人員一律還是可以選擇直接使用通知中樞。
- **[Azure 入口網站](https://portal.azure.com)中的整合體驗**：Mobile Apps 中會以視覺化方式呈現以功能形式出現的推送，而開發人員可以透過 Mobile Apps 輕鬆使用相關聯的通知中樞。
