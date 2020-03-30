---
title: 診斷 Azure 通知中心中已刪除的通知
description: 了解如何透過 Azure 通知中樞卸除的通知診斷常見問題。
services: notification-hubs
documentationcenter: Mobile
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 02/25/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/04/2019
ms.openlocfilehash: 1f3c16e6fe1855cf7882d83e620c70d15ce3cb92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657505"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>診斷 Azure 通知中心中已刪除的通知

有關 Azure 通知中心的常見問題是，當來自應用程式的通知未顯示在用戶端設備上時，如何進行故障排除。 客戶想知道通知的刪除位置和原因，以及如何解決此問題。 本文介紹卸除通知的可能原因，或裝置未收到通知的可能原因。 它還解釋了如何確定根本原因。

請務必先了解通知中樞如何推播通知至裝置。

![通知中樞架構][0]

一般傳送通知流程中，訊息會從*應用程式後端*傳送至通知中樞。 通知中心處理所有註冊。 它考慮配置的標記和標記運算式來確定目標。 目標是需要接收推送通知的註冊。 這些註冊可以跨越我們支援的任何平臺：安卓、百度（中國的安卓設備）、消防作業系統（亞馬遜）iOS、Windows和Windows手機。

透過建立目標，通知中樞會將推播通知到裝置平台的*推播通知服務*。 示例包括適用于 iOS 和 macOS 的 Apple 推送通知服務 （APN）以及適用于 Android 設備的火基雲消息 （FCM）。 通知中樞跨多個批次註冊推播通知。 它根據在 Azure 門戶中設置的憑據，在 **"配置通知中心**"下使用相應的推送通知服務進行身份驗證。 隨後，推播通知服務會將通知轉送到各個*用戶端裝置*。

通知傳遞的最後一站是在平臺的推送通知服務和設備之間。 在推送通知過程的四個階段（用戶端、應用程式後端、通知中心和平臺的推送通知服務）中，通知傳遞都可能失敗。 如需更多通知中樞架構的相關資訊，請參閱[通知中樞概觀]。

在初始測試/暫存階段，可能會出現無法傳遞通知。 在此階段卸除的通知可能表示設定有問題。 如果在生產中發生無法傳遞通知的情況，可能會刪除部分或全部通知。 在這種情況下，會指示更深層次的應用程式或消息模式問題。

下一節將介紹可能刪除通知的情況，從常見到罕見。

## <a name="notification-hubs-misconfiguration"></a>通知中樞設定錯誤

要向相應的推送通知服務發送通知，通知中心必須在應用程式的上下文中進行身份驗證。 您必須使用目標平臺的通知服務（微軟、蘋果、谷歌等）創建開發者帳戶。 然後，您必須在作業系統中註冊應用程式，在那裡獲取用於使用目標 PNS 的權杖或金鑰。

您必須將平台認證新增至 Azure 入口網站。 如果未到達設備，則第一步是確保在通知中心中配置正確的憑據。 憑據必須與在特定于平臺的開發人員帳戶下創建的應用程式匹配。

若要完成此程序的逐步指示，請參閱[開始使用 Azure 通知中樞]。

以下是一些常見的錯誤設定檢查：

### <a name="notification-hub-name-location"></a>通知中心名稱位置

確認通知中樞名稱與以下各項位置相同 (無錯別字)：

* 從用戶端註冊的位置
* 從後端發送通知的位置
* 配置推送通知服務憑據的位置

確保在用戶端和應用程式後端使用正確的共用訪問簽名配置字串。 通常，您必須在用戶端上使用**預設偵聽共用訪問簽名**，在應用程式後端使用**預設完全共用訪問簽名**。 這將授予向通知中心發送通知的許可權。

### <a name="apn-configuration"></a>APN 配置

您必須維護兩個不同的集線器：一個用於生產，另一個用於測試。 您必須將在沙箱環境中使用的證書上載到比在生產中使用證書/集線器的單獨集線器。 請勿嘗試將不同類型的憑證上傳至相同的中樞。 這將導致通知失敗。

如果無意中將不同類型的證書上載到同一中心，則應刪除集線器，然後重新開始使用新中心。 如果由於某種原因無法刪除中心，則至少必須從中心中刪除所有現有註冊。

### <a name="fcm-configuration"></a>FCM 配置

1. 確保從 Firebase 獲得的*伺服器金鑰*與在 Azure 門戶中註冊的伺服器金鑰匹配。

   ![Firebase 伺服器金鑰][3]

2. 確認您已在用戶端上設定**專案識別碼**。 您可以從 Firebase 儀表板取得**專案識別碼**的值。

   ![Firebase 專案識別碼][1]

## <a name="application-issues"></a>應用程式問題

### <a name="tags-and-tag-expressions"></a>標記和標記運算式

如果使用標記或標記運算式對訪問群體進行細分，則發送通知時可能找不到目標。 此錯誤基於發送調用中的指定標記或標記運算式。

查看註冊以確保發送通知時標記匹配。 然後，僅驗證具有這些註冊的用戶端的通知接收。

例如，假設您使用通知中心的所有註冊都使用"政治"標籤。 如果隨後發送帶有"體育"標籤的通知，則通知不會發送到任何設備。 複雜案例可能涉及使用"標記 A"*或*"標記 B"註冊標記運算式，但目標為"標記 A && 標記 B"。 本文後面的自我診斷提示部分演示如何查看註冊及其標籤。

### <a name="template-issues"></a>範本問題

如果您使用範本，請確定遵循[範本]中所述的指導方針。

### <a name="invalid-registrations"></a>無效註冊

如果正確配置了通知中心，並且標記或標記運算式使用正確，則找到有效目標。 通知應該傳送給這些目標。 然後，通知中樞會以並行的方式一連串地傳送數個處理批次。 每個批次會將訊息傳送至一組註冊。

> [!NOTE]
> 由於通知中心並行處理批次處理，因此不能保證通知的行程順序。

通知中心針對"一次最多"的消息傳遞模型進行了優化。 我們會嘗試刪除重複的項目，避免系統將通知多次傳遞給同一部裝置。 檢查註冊以確保在將消息發送到推送通知服務之前，每個設備識別碼僅發送一條消息。

每個批次處理都發送到推送通知服務，而推送通知服務又接受並驗證註冊。 在此過程中，推送通知服務可能會檢測到批次處理中一個或多個註冊的錯誤。 然後，推送通知服務將錯誤返回通知中心，進程將停止。 推播通知服務會完全卸除該批次。 使用 TCP 流協定的 APN 尤其如此。

在這種情況下，故障註冊將從資料庫中刪除。 然後，我們會針對該批次中的其餘裝置，重試通知傳遞。

要獲取有關針對註冊的失敗傳遞嘗試的更多錯誤資訊，可以使用通知中心 REST API[每消息遙測：獲取通知訊息遙測](https://docs.microsoft.com/rest/api/notificationhubs/get-notification-message-telemetry)和[PNS 回饋](https://msdn.microsoft.com/library/azure/mt705560.aspx)。 如需範例程式碼，請參閱[傳送 REST 範例](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/)。

## <a name="push-notification-service-issues"></a>推播通知服務問題

推送通知服務收到通知後，它將通知傳遞到設備。 此時，通知中心無法控制通知傳遞到設備。

由於平臺通知服務是可靠的，因此通知往往在幾秒鐘內到達設備。 如果推送通知服務正在限制，則通知中心將應用指數回退策略。 如果推送通知服務在 30 分鐘內仍然無法訪問，則有一個策略可以永久過期和丟棄消息。

如果推送通知服務嘗試傳遞通知，但設備處於離線狀態，則推送通知服務將存儲通知。 它只存儲一段有限的時間。 並且在可使用裝置時，再行傳遞。

每個應用只存儲一個最近的通知。 如果在設備離線時發送了多個通知，則每個新通知都會丟棄最後一個通知。 只保留最新的通知在 APN 中稱為*合併*和在 FCM 中*折疊*。 （FCM 使用折疊金鑰。當設備長時間處於離線狀態時，將丟棄為設備存儲的通知。 有關詳細資訊，請參閱[APN 概述][和關於 FCM 消息]。

使用通知中心，您可以使用泛型 Send通知 API 通過 HTTP 標頭傳遞合併金鑰。 例如，對於 .NET SDK，您會使用 `SendNotificationAsync`。 Send通知 API 還獲取按樣傳遞到相應推送通知服務的 HTTP 標頭。

## <a name="self-diagnosis-tips"></a>自我診斷秘訣

以下是診斷通知中心中丟棄通知的根本原因的路徑。

### <a name="verify-credentials"></a>驗證認證

#### <a name="push-notification-service-developer-portal"></a>推送通知服務開發人員門戶

確認個別推播通知服務開發人員入口網站 (APNs、FCM、Windows 通知服務等等) 中的認證。 有關詳細資訊，請參閱[教程：通過使用 Azure 通知中心向通用 Windows 平臺應用發送通知](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification)。

#### <a name="azure-portal"></a>Azure 入口網站

要查看憑據並將其與從推送通知服務開發人員門戶獲取的憑據匹配，請訪問 Azure 門戶中的 **"訪問策略**"選項卡。

![Azure 入口網站存取原則][4]

### <a name="verify-registrations"></a>驗證註冊

#### <a name="visual-studio"></a>Visual Studio

在視覺化工作室中，您可以通過伺服器資源管理器連接到 Azure，以查看和管理多個 Azure 服務，包括通知中心。 此快捷方式主要適用于您的開發/測試環境。

![Visual Studio 伺服器總管][9]

![Server Explorer](media/notification-hubs-push-notification-fixer/vsserverexplorer2.png)

您可以查看和管理中心中的所有註冊。 註冊可以按平臺、本機或範本註冊、標記、推送通知服務識別碼、註冊 ID 和到期日期進行分類。 您也可以在此頁面上編輯註冊。 它對於編輯標記特別有用。

按右鍵**伺服器資源管理器**中的通知中心，然後選擇 **"診斷**"。 

![視覺化工作室伺服器資源管理器：診斷功能表](./media/notification-hubs-push-notification-fixer/diagnose-menu.png)

您將看到以下頁面：

![視覺化工作室：診斷頁面](./media/notification-hubs-push-notification-fixer/diagnose-page.png)

切換到**設備註冊**頁面：

![視覺化工作室：設備註冊](./media/notification-hubs-push-notification-fixer/VSRegistrations.png)

您可以使用 **"測試發送"** 頁發送測試通知訊息：

![視覺化工作室：測試發送](./media/notification-hubs-push-notification-fixer/test-send-vs.png)

> [!NOTE]
> 使用 Visual Studio 僅在開發/測試期間編輯註冊，並且註冊數量有限。 如果需要大量編輯註冊，請考慮使用["如何：批量匯出和修改註冊](https://msdn.microsoft.com/library/dn790624.aspx)"中描述的匯出和導入註冊功能。

#### <a name="service-bus-explorer"></a>服務匯流排總管

許多客戶使用[服務匯流排資源管理器](https://github.com/paolosalvatori/ServiceBusExplorer)來查看和管理其通知中心。 服務匯流排總管是開放原始碼專案。 

### <a name="verify-message-notifications"></a>驗證訊息通知

#### <a name="azure-portal"></a>Azure 入口網站

若要將測試通知傳送到您的用戶端，而不需要運作後端服務，請在 [支援 + 疑難排解]**** 下，選取 [測試傳送]****。

![測試 Azure 中的傳送功能][7]

#### <a name="visual-studio"></a>Visual Studio

您也可以從 Visual Studio 傳送測試通知。

![測試 Visual Studio 中的傳送功能][10]

如需更多使用通知中樞與 Visual Studio 伺服器總管的相關資訊，請參閱下列文章：

* [如何查看通知中心的設備註冊](https://docs.microsoft.com/previous-versions/windows/apps/dn792122(v=win.10))
* [深入探討：Visual Studio 2013 Update 2 RC 和 Azure SDK 2.3]
* [宣佈發行 Visual Studio 2013 Update 3 和 Azure SDK 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>偵錯失敗的通知並檢閱通知結果

#### <a name="enabletestsend-property"></a>EnableTestSend 屬性

當您通過通知中心發送通知時，通知最初處於排隊。 通知中樞會確定正確的目標，然後將通知傳送至推播通知服務。 如果您使用的是 REST API 或任何用戶端 SDK，則發送調用的返回僅意味著消息使用通知中心排隊。 它不提供對通知中心最終將通知發送到推送通知服務時發生的情況的見解。

如果通知未到達用戶端設備，則當通知中心嘗試將其傳遞到推送通知服務時，可能會出現錯誤。 例如，承載大小可能會超過推播通知服務允許的上限，或通知中樞中設定的認證可能無效。

若要深入了解推播通知服務的錯誤，您可以使用 [EnableTestSend] 屬性。 當您從入口網站或 Visual Studio 用戶端傳送測試訊息時，會自動啟用該屬性。 可以使用此屬性查看詳細的調試資訊，也可以通過 API 查看。 目前，您可以在 .NET SDK 中使用該屬性。 它最終將添加到所有用戶端 SDK 中。

若要搭配使用 `EnableTestSend` 屬性與 REST 呼叫，請在傳送呼叫結尾附加名為 test** 的查詢字串參數。 例如：

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>.NET SDK 示例

以下是使用 .NET SDK 傳送原生快顯視窗 (快顯) 通知的範例：

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

在執行的結尾處，`result.State` 只會指出`Enqueued`。 結果無法深入瞭解推送通知發生的情況。

接著，您可以使用 `EnableTestSend` 布林值屬性。 初始化 `NotificationHubClient` 時，請使用 `EnableTestSend` 屬性，以取得關於傳送通知時發生的推播通知服務錯誤詳細狀態。 發送呼叫需要額外的時間返回，因為它首先需要通知中心將通知傳遞到推送通知服務。

```csharp
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }
```

#### <a name="sample-output"></a>範例輸出

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

此消息指示通知中心中配置的憑據無效，或者中心中的註冊出現問題。 刪除此註冊，讓用戶端在發送消息之前重新創建註冊。

> [!NOTE]
> 使用 `EnableTestSend` 屬性會進行大幅度的節流處理。 僅在開發/測試環境和有限的註冊集中使用此選項。 調試通知僅發送到 10 台設備。 處理調試發送也有限制，每分鐘 10 次。

### <a name="review-telemetry"></a>檢閱遙測

#### <a name="azure-portal"></a>Azure 入口網站

在入口網站中，您可以取得通知中樞上所有活動的簡要概觀。

1. 在 [概觀]**** 索引標籤上，您可以依平台查看註冊、通知和錯誤的彙總檢視。

   ![通知中樞概觀儀表板][5]

2. 在 [監視器]**** 索引標籤上，您可以新增許多其他平台特定度量以獲得更深入的了解。 您可以具體查看通知中心嘗試將通知發送到推送通知服務時返回的錯誤。

   ![Azure 入口網站活動記錄][6]

3. 首先，檢閱**內送郵件**、**註冊作業**，以及**成功通知**。 然後，移至每個平台的索引標籤，以檢閱特定於推播通知服務的錯誤。

4. 如果您通知中樞的驗證設定不正確，會出現 **PNS 驗證錯誤**訊息。 檢查推送通知服務憑據是一個很好的指示。

#### <a name="programmatic-access"></a>以程式設計方式存取

有關程式設計訪問的詳細資訊，請參閱[程式設計訪問](https://docs.microsoft.com/previous-versions/azure/azure-services/dn458823(v=azure.100))。

> [!NOTE]
> 幾個與遙測有關的功能，例如匯出和匯入註冊，以及透過 API 進行遙測存取，只能在標準服務層級上使用。 如果嘗試使用免費或基本服務層中的這些功能，則如果使用 SDK，則會收到一條異常消息。 如果您直接從 REST API 使用這些功能，則您會收到 HTTP 403（禁止）錯誤。
>
> 要使用與遙測相關的功能，首先在 Azure 門戶中確保使用標準服務層。  

<!-- IMAGES -->
[0]: ./media/notification-hubs-push-notification-fixer/Architecture.png
[1]: ./media/notification-hubs-push-notification-fixer/FCMConfigure.png
[3]: ./media/notification-hubs-push-notification-fixer/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-push-notification-fixer/PortalDashboard.png
[6]: ./media/notification-hubs-push-notification-fixer/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-push-notification-fixer/VSRegistrations.png
[9]: ./media/notification-hubs-push-notification-fixer/vsserverexplorer.png
[10]: ./media/notification-hubs-push-notification-fixer/VSTestNotification.png

<!-- LINKS -->
[通知中心概述]: notification-hubs-push-notification-overview.md
[開始使用 Azure 通知中樞]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[範本]: https://msdn.microsoft.com/library/dn530748.aspx
[APNs 概觀]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[關於 FCM 訊息]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: https://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: https://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[深入探討：Visual Studio 2013 Update 2 RC 和 Azure SDK 2.3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[宣佈發行 Visual Studio 2013 Update 3 和 Azure SDK 2.4]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Programmatic telemetry access]: https://msdn.microsoft.com/library/azure/dn458823.aspx
