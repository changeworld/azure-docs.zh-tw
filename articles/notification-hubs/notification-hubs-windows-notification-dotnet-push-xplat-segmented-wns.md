---
title: 將通知傳送至特定裝置 (通用 Windows 平台) | Microsoft Docs
description: 使用 Azure 通知中樞搭配註冊中的標籤，將即時新聞傳送至通用 Windows 平台應用程式。
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 994d2eed-f62e-433c-bf65-4afebf1c0561
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: be20cd583be7c9c87d9f3a3cebc6609764384c9a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87084223"
---
# <a name="tutorial-send-notifications-to-specific-devices-running-universal-windows-platform-applications"></a>教學課程：將通知傳送至執行通用平台應用程式的特定 Windows 裝置

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>概觀

本教學課程說明如何使用 Azure 通知中樞來廣播即時新聞通知。 本教學課程涵蓋 Windows Store 或 Windows Phone 8.1 (非 Silverlight) 應用程式。 如果您的目標是 Windows Phone 8.1 Silverlight，請參閱[使用 Azure 通知中樞將通知推送至特定 Windows Phone 裝置](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)。

在本教學課程中，您將了解如何使用 Azure 通知中樞，將通知推送至執行通用 Windows 平台 (UWP) 應用程式的特定 Windows 裝置。 完成本教學課程之後，您可以註冊感興趣的即時新聞類別。 您只會收到那些類別的推播通知。

若要啟用廣播案例，請在您於通知中樞內建立註冊時包含一或多個「標籤」  。 當標籤收到通知時，所有已註冊此標籤的裝置都會收到通知。 如需標籤的詳細資訊，請參閱[路由與標記運算式](notification-hubs-tags-segment-push-message.md)。

> [!NOTE]
> Windows 市集和 Windows Phone 8.1 版與更早版本的專案在 Visual Studio 2019 不受支援。 如需詳細資訊，請參閱 [Visual Studio 2019 平台目標及相容性](/visualstudio/releases/2019/compatibility)。

在此教學課程中，您會執行下列工作：

> [!div class="checklist"]
> * 在行動應用程式中新增類別選項
> * 註冊通知
> * 傳送加註標記的通知
> * 執行應用程式並產生通知

## <a name="prerequisites"></a>Prerequisites

完成[教學課程：使用 Azure 通知中樞將通知傳送至通用 Windows 平台應用程式][get-started]，再開始此教學課程。  

## <a name="add-category-selection-to-the-app"></a>在應用程式中新增類別選項

第一個步驟是在您的現有主頁面上新增 UI 元素，以便使用者選取要註冊的類別。 選取的類別會儲存在裝置上。 應用程式啟動時會在您的通知中樞內建立裝置註冊，並以所選取的類別作為標籤。

1. 開啟 MainPage.xaml  專案檔案，然後在 `Grid` 元素中複製下列程式碼：

    ```xml
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition/>
            <ColumnDefinition/>
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
        <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
    </Grid>
    ```

1. 在 [方案總管]  中，以滑鼠右鍵按一下專案，然後選取 [新增]   > [類別]  。 在 [新增項目]  中，將類別命名為「通知」  ，然後選取 [新增]  。 如有必要，請將 `public` 修飾詞新增至類別定義。

1. 將下列 `using` 陳述式新增到新檔案：

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.Storage;
    using System.Threading.Tasks;
    ```

1. 將下列程式碼複製到新的 `Notifications` 類別中：

    ```csharp
    private NotificationHub hub;

    public Notifications(string hubName, string listenConnectionString)
    {
        hub = new NotificationHub(hubName, listenConnectionString);
    }

    public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
    {
        ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
        return await SubscribeToCategories(categories);
    }

    public IEnumerable<string> RetrieveCategories()
    {
        var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
        return categories != null ? categories.Split(','): new string[0];
    }

    public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        if (categories == null)
        {
            categories = RetrieveCategories();
        }

        // Using a template registration to support notifications across platforms.
        // Any template notifications that contain messageParam and a corresponding tag expression
        // will be delivered for this registration.

        const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

        return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
                categories);
    }
    ```

    本類別會使用本機儲存體來儲存此裝置必須接收的新聞類別。 我們會呼叫 `RegisterTemplateAsync` 而非 `RegisterNativeAsync` 方法，以使用範本註冊來註冊類別。

    如果您想要註冊多個範本，請提供範本名稱，例如 simpleWNSTemplateExample  。 您可以為範本命名，讓您可以更新或刪除範本。 您可以註冊多個範本，一個用於快顯通知，一個用於圖格。

    >[!NOTE]
    > 透過通知中樞，裝置可使用相同標籤註冊多個範本。 在此情況下，當傳入的訊息符合該標籤時，就會有多個通知傳遞至裝置 (每個通知各用於一個範本)。 透過此流程，您就能讓相同訊息顯示在多個視覺通知中，例如以徽章形式和 Windows 市集應用程式中的快顯通知形式。

    如需詳細資訊，請參閱 [範本](notification-hubs-templates-cross-platform-push-messages.md)。

1. 在 App.xaml.cs  專案檔案中，新增下列屬性至 `App` 類別：

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

    您可以使用此屬性建立並存取 `Notifications` 執行個體。

    在程式碼中，使用您的通知中樞名稱及先前取得的 **DefaultListenSharedAccessSignature** 連接字串，來取代 `<hub name>` 和 `<connection string with listen access>` 預留位置。

   > [!NOTE]
   > 因為隨用戶端應用程式散佈的憑證通常不安全，您應只將*接聽*存取權的金鑰隨用戶端應用程式散佈。 您的應用程式可透過接聽存取權來註冊通知，但無法修改現有的註冊或無法傳送通知。 在安全的後端服務中，會使用完整存取金鑰來傳送通知和變更現有的註冊。

1. 在 MainPage.xaml.cs  檔案中，新增下面這行：

    ```csharp
    using Windows.UI.Popups;
    ```

1. 在 MainPage.xaml.cs  檔案中，新增下列方法：

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var categories = new HashSet<string>();
        if (WorldToggle.IsOn) categories.Add("World");
        if (PoliticsToggle.IsOn) categories.Add("Politics");
        if (BusinessToggle.IsOn) categories.Add("Business");
        if (TechnologyToggle.IsOn) categories.Add("Technology");
        if (ScienceToggle.IsOn) categories.Add("Science");
        if (SportsToggle.IsOn) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

        var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    ```

    此方法會建立一份類別清單，並使用 `Notifications` 類別在本機儲存體中儲存清單。 也會在通知中心註冊對應標籤。 類別變更時，系統會使用新類別重新建立註冊。

您的應用程式現在可以在裝置上的本機儲存體中儲存一組類別。 每當使用者變更類別選取項目時，應用程式就會向通知中樞註冊。

## <a name="register-for-notifications"></a>註冊通知

在此案例中，您會在啟動時，使用已儲存在本機儲存體中的類別向通知中心註冊。

> [!NOTE]
> 由於 Windows 通知服務 (WNS) 所指派的通道 URI 可以隨時變更，您應經常註冊通知以避免通知失敗。 此範例會在應用程式每次啟動時註冊通知。 若是經常執行的應用程式 (例如，一天多次)，如果距離上次註冊的時間不到一天，則您可能可以略過註冊以保留頻寬。

1. 若要使用 `notifications` 類別根據類別來訂閱，開啟 App.xaml.cs  檔案並更新 `InitNotificationsAsync` 方法。

    ```csharp
    // *** Remove or comment out these lines ***
    //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    //var hub = new NotificationHub("your hub name", "your listen connection string");
    //var result = await hub.RegisterNativeAsync(channel.Uri);

    var result = await notifications.SubscribeToCategories();
    ```

    此程序可確保應用程式會在啟動時，從本機儲存體擷取類別。 接著，再要求這些類別的註冊。 您已在[使用 Azure 通知中樞將通知傳送至通用 Windows 平台應用程式][get-started]教學課程中建立了 `InitNotificationsAsync` 方法。
2. 在 MainPage.xaml.cs  專案檔案中，於 `OnNavigatedTo` 方法內新增下列程式碼：

    ```csharp
    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldToggle.IsOn = true;
        if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
        if (categories.Contains("Business")) BusinessToggle.IsOn = true;
        if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
        if (categories.Contains("Science")) ScienceToggle.IsOn = true;
        if (categories.Contains("Sports")) SportsToggle.IsOn = true;
    }
    ```

    此程式碼會根據原先儲存的類別狀態更新主頁面。

應用程式現已完成。 其可以在裝置的本機儲存體中儲存一組類別。 當使用者變更選取的類別時，便會使用已儲存的類別來向通知中樞註冊。 在下一節中，您會定義可將類別通知傳送至此應用程式的後端。

## <a name="run-the-uwp-app"></a>執行 UWP 應用程式

1. 在 Visual Studio 中，選取 F5 以編譯並啟動應用程式。 應用程式 UI 提供一組切換，可讓您選擇要訂閱的類別。

   ![即時新聞應用程式](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breaking-news.png)

1. 啟用一或多個類別切換，然後選取 [訂閱]  。

   應用程式會將選取的類別轉換成標籤，並在通知中心內為選取的標籤要求新裝置註冊。 應用程式會在對話方塊中顯示已註冊的類別。

    ![類別切換和訂閱按鈕](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast.png)

## <a name="create-a-console-app-to-send-tagged-notifications"></a>建立主控台應用程式來傳送加註標記的通知

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-console-app-to-send-tagged-notifications"></a>執行主控台應用程式來傳送加註標記的通知

執行在上一節中建立的應用程式。 選取的類別通知會以快顯通知方式出現。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何按類別廣播即時新聞。 後端應用程式會將加註標記的通知，推送至已註冊要接收該標記之通知的裝置。 若要了解如何將通知推送給特定使用者 (無論他們使用何種裝置)，請繼續進行下列教學課程：

> [!div class="nextstepaction"]
> [使用 Azure 通知中樞將當地語系化的通知推送至 Windows 應用程式](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- URLs.-->
[get-started]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
