---
title: 什麼是 Azure 通知中樞？
description: 了解如何使用 Azure 通知中樞新增推播通知功能。
author: sethmanheim
manager: femila
editor: tjsomasundaram
services: notification-hubs
documentationcenter: ''
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 03/23/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 04/30/2019
ms.openlocfilehash: b8ac964c62ac4b30e9280e4ae921297ba860d7c2
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "86529925"
---
# <a name="what-is-azure-notification-hubs"></a>什麼是 Azure 通知中樞？

Azure 通知中樞提供易於使用且相應放大的推播引擎，可讓您從任何後端 (雲端或內部部署) 傳送通知到任何平台 (iOS、Android、Windows 等)。 通知中樞很適合企業和消費者案例。 以下是一些範例案例：

- 傳送即時新聞通知給數百萬人，且延遲時間很低。
- 將位置型折價券傳送給感興趣的使用者區段。
- 將事件相關通知傳送給媒體/運動/財金/遊戲應用程式的使用者或群組。
- 將促銷內容推播到應用程式，來與客戶互動及行銷。
- 對使用者發送企業事件通知，例如新訊息和工作項目。
- 傳送 Multi-Factor Authentication 的程式碼。

## <a name="what-are-push-notifications"></a>什麼是推播通知？

推播通知是一種由應用程式對使用者發出的通訊形式，通常會以行動裝置的快顯視窗或對話方塊對行動應用程式的使用者發出其所需資訊的通知。 使用者一般會選擇檢視或關閉訊息；選擇前者會開啟傳送通知的行動應用程式。 有些通知是無訊息的 - 在幕後傳遞，以供應用程式處理並決定後續動作。

推播通知是很重要的功能，對於消費者應用程式來說，可提高應用程式的參與和使用率，對於企業應用程式來說，則可傳送最新的企業資訊。 此功能是最佳的應用程式對使用者通訊，因為它不會過度耗用行動裝置的電量、可讓通知傳送者保有彈性，並且可在對應的應用程式未執行時使用。

> [!NOTE]
> Azure 通知中樞並未正式支援 Voice Over 網際網路通訊協定 (VOIP) 推播通知；不過，[本文將說明如何透過 Azure 通知中樞來使用 APNS VOIP 通知](voip-apns.md)。

如需幾個熱門平台推播通知的詳細資訊，請參閱以下主題：

- [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
- [iOS](https://developer.apple.com/notifications/)
- [Windows](/previous-versions/windows/apps/hh779725(v=win.10))

## <a name="how-do-push-notifications-work"></a>推播通知如何運作？

推播通知可透過名為「平台通知系統 (PNS)」  的平台特定基礎結構來傳遞。 其可提供基本的推播功能，以所提供的控制代碼傳遞訊息至裝置，且沒有通用介面。 若要將通知傳送給 Android、iOS 和 Windows 版應用程式的所有客戶，開發人員必須個別使用 Apple Push Notification Service (APNS)、Firebase Cloud Messaging (FCM) 和 Windows 通知服務 (WNS)。

概括而言，以下是推播功能的運作方式︰

1. 應用程式想要接收通知，因此對正在執行應用程式的目標平台聯繫其 PNS，並要求唯一的暫時性推播控制代碼。 控制代碼的類型視系統而定 (例如，WNS 使用 URI，APNS 則使用權杖)。
2. 用戶端應用程式會將此控制代碼儲存在應用程式後端或提供者。
3. 若要傳送推播通知，應用程式後端必須使用控制代碼連絡 PNS，以將特定的用戶端應用程式定為目標。
4. PNS 會將通知轉送至控制代碼所指定的裝置。

![推播通知工作流程](./media/notification-hubs-overview/registration-diagram.png)

## <a name="the-challenges-of-push-notifications"></a>推播通知的挑戰

PNS 的功能強大， 但很多部分都必須由應用程式開發人員處理，即使是實作普通推播通知的案例，例如對不同客層的使用者廣播推播通知。

傳送推播通知需要的基礎結構很複雜，且與應用程式主要商務邏輯無關。 基礎結構方面的某些挑戰包括︰

- **平台相依性**
  - 後端必須有複雜且難以維護的平台特有邏輯，才能將通知傳送給各種平台上的裝置，因為 PNS 並未統一。
- **調整**
  - 根據 PNS 準則，在每次啟動應用程式時，都必須重新整理裝置權杖。 後端會處理大量的流量以及資料庫的存取權，都只是為了讓權杖保持最新狀態。 當裝置數目增加至數億和數十億時，建立及維護此基礎結構的成本將會很可觀。
  - 大部分的 PNS 並不支援廣播至多個裝置。 對百萬個裝置的一個簡單廣播就會產生百萬個 PNS 呼叫。 想要擴增這種數量的流量卻又要將延遲降到最低並非易事。
- **路由**
  - 雖然 PNS 提供了將訊息傳送至裝置的方法，但大部分應用程式通知都是以使用者或相關群組為目標。 這表示後端必須維護登錄，以將裝置關聯到相關群組、使用者、屬性等。這項額外工作將導致應用程式的上市時程延宕和維護成本提高。

## <a name="why-use-azure-notification-hubs"></a>為何要使用 Azure 通知中樞？

自行從應用程式後端傳送推播通知時，會產生相關的複雜事項，而通知中樞可免去這一切麻煩。 其多平台、可相應放大的推播通知基礎結構可減少推播相關編碼，並簡化您的後端。 使用通知中樞時，裝置只需負責向中樞註冊其 PNS 控制代碼，而由後端負責將訊息傳送給使用者或相關群組，如下圖所示︰

![通知中樞圖表](./media/notification-hubs-overview/notification-hub-diagram.png)

通知中樞是立即可用的推播引擎，其具有下列優勢︰

- **跨平台**
  - 支援所有主要的推播平台。
  - 具有通用介面，可以平台特定或平台無關的格式推播至所有平台，但不必進行平台特定工作。
  - 集中在一個位置管理裝置控制代碼。
- **跨後端**
  - 雲端或內部部署。
  - .NET、Node.js、Java、Python 等
- **豐富的傳遞模式集**
  - 廣播至一或多個平台：只要單一 API 呼叫，您就可以立即跨平台廣播到數百萬個裝置。
  - 推送至裝置︰您可以將通知目標鎖定為個別裝置。
  - 推送給使用者︰標籤和範本可協助您觸達某位使用者的所有跨平台裝置。
  - 以動態標記推送至區段︰標籤功能可協助您將裝置分區段，並根據需求推送至這些區段，不論是要傳送給一個區段或一個運算式的區段 (例如，作用中 AND 居住地西雅圖 NOT 新的使用者)。 不必侷限於發佈/訂閱模式，您可以隨時隨地更新裝置的標籤。
  - 當地語系化的推播︰範本功能可協助您實現當地語系化，又不影響後端程式碼。
  - 無訊息推播︰您可以將無訊息通知傳送至裝置，並觸發它們完成特定提取或動作，以啟用推播到提取模式。
  - 排定推播︰您可以排定在任何時間傳送通知。
  - 直接推播：您可以略過向通知中樞服務註冊裝置，而直接批次推送至裝置控制代碼清單。
  - 個人化推播︰裝置推播變數可協助您以自訂的索引鍵/值組傳送裝置特定的個人化推播通知。
- **豐富的遙測**
  - 您可以在 Azure 入口網站中，以及透過程式設計方式取得一般的推播、裝置、錯誤和作業遙測。
  - 每一訊息遙測會追蹤每個推播，從您的初始要求呼叫，至通知中樞服務成功地傳送推播。
  - 平台通知系統意見反應會傳送 PNS 的所有意見反應，以協助偵錯。
- **可擴縮性**
  - 將快速訊息傳送至數百萬個裝置，而不需要經過重新架構或裝置分區化程序。
- **安全性**
  - 共用存取密碼 (SAS) 或同盟驗證。

## <a name="next-steps"></a>後續步驟

依照[教學課程：將通知推送至行動應用程式](notification-hubs-android-push-notification-google-fcm-get-started.md)操作，開始建立及使用通知中樞。

[0]: ./media/notification-hubs-overview/registration-diagram.png
[1]: ./media/notification-hubs-overview/notification-hub-diagram.png

[How customers are using Notification Hubs]: https://azure.microsoft.com/services/notification-hubs
[Notification Hubs tutorials and guides]: https://azure.microsoft.com/documentation/services/notification-hubs
[iOS]: ./notification-hubs-push-notification-fixer.md
[Android]: ./notification-hubs-android-push-notification-google-gcm-get-started.md
[Windows Universal]: ./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Windows Phone]: ./notification-hubs-windows-mobile-push-notifications-mpns.md
[Kindle]: ./notification-hubs-android-push-notification-google-fcm-get-started.md
[Xamarin.iOS]: ./xamarin-notification-hubs-ios-push-notification-apns-get-started.md
[Xamarin.Android]: ./xamarin-notification-hubs-push-notifications-android-gcm.md
[Microsoft.WindowsAzure.Messaging.NotificationHub]: /previous-versions/azure/reference/dn339221(v=azure.100)
[Microsoft.ServiceBus.Notifications]: /previous-versions/azure/
[App Service Mobile Apps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[templates]: notification-hubs-templates-cross-platform-push-messages.md
[Azure portal]: https://portal.azure.com
[tags]: (https://msdn.microsoft.com/library/azure/dn530749.aspx)
