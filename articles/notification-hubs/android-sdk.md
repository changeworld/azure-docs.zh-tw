---
title: 使用 Azure 通知中樞和 Firebase SDK 1.0.0-preview1 版將推播通知傳送至 Android
description: 在本教學課程中，您將了解如何使用 Azure 通知中樞和 Google Firebase 雲端通訊，將推播通知傳送至 Android 裝置。
author: sethmanheim
ms.author: sethm
ms.date: 5/28/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 05/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ae1d2dfaf7d83d3b2323812f637bddd91b9a2ea2
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018224"
---
# <a name="tutorial-send-push-notifications-to-android-devices-using-firebase-sdk-version-100-preview1"></a>教學課程：使用 Firebase SDK 1.0.0-preview1 版將推播通知傳送至 Android 裝置

本教學課程說明如何使用 Azure 通知中樞和更新版的 Firebase 雲端通訊 (FCM) SDK (1.0.0-preview1 版)，將推播通知傳送到 Android 應用程式。 在本教學課程中，您會建立空白的 Android 應用程式，其可使用 Firebase 雲端通訊 (FCM) 接收推播通知。

您可以從 [GitHub](https://github.com/Azure/azure-notificationhubs-android/tree/v1-preview/notification-hubs-test-app-refresh) 下載本教學課程的完整程式碼。

本教學課程涵蓋下列步驟：

- 建立 Android Studio 專案。
- 建立支援 Firebase 雲端通訊的 Firebase 專案。
- 建立通知中樞。
- 將應用程式連線至中樞。
- 測試應用程式。

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，您必須具備有效的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](https://azure.microsoft.com/free/)。

您也需要下列項目：

- 建議使用最新版的  [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797)。
- 最低支援為 API 層級 16。

## <a name="create-an-android-studio-project"></a>建立 Android Studio 專案

第一個步驟是在 Android Studio 中建立專案：

1. 啟動 Android Studio。

2. 依序選取 [檔案] **** 、[新增] **** 以及 [新增專案]。

3. 在 [選擇您的專案] ****  頁面上，選取 [空的活動] **** ，然後選取 [下一步] **** 。

4. 在 [設定您的專案] ****  頁面上，執行下列動作：
   1. 輸入應用程式的名稱。
   2. 指定要用來儲存專案檔的位置。
   3. 選取 [完成] **** 。

   :::image type="content" source="media/android-sdk/configure-project.png" alt-text="設定專案":::

## <a name="create-a-firebase-project-that-supports-fcm"></a>建立支援 FCM 的 Firebase 專案

1. 登入 [Firebase 主控台](https://firebase.google.com/console/)。 建立新的 Firebase 專案 (如果您還沒有 Firebase 專案的話)。

2. 建立專案之後，請選取 **** [將 Firebase 新增至 Android 應用程式]。

   :::image type="content" source="media/android-sdk/get-started.png" alt-text="新增 Firebase":::

3. 在 [將 Firebase 新增至 Android 應用程式] ****  頁面上，執行下列動作：

   1. 對於 [Android 套件名稱] **** ，複製應用程式 **build.gradle** 檔案的 applicationId 值。 在此範例中為  `com.fabrikam.fcmtutorial1app`。

      :::image type="content" source="media/android-sdk/specify-package-name-fcm-settings.png" alt-text="指定套件名稱":::

   2. 選取 **註冊應用程式**。

4. 選取 [下載 google-services.json] **** ，將檔案儲存到專案的 **應用程式** 資料夾，然後選取 **** [下一步]。

   :::image type="content" source="media/android-sdk/download-google-service-button.png" alt-text="下載 Google 服務":::

5. 在 Firebase 主控台中，選取您專案的齒輪圖示。 然後選取 [專案設定] **** 。

   :::image type="content" source="media/android-sdk/notification-hubs-firebase-console-project-settings.png" alt-text="專案設定":::

6. 如果您尚未將 **google-services.json** 檔案下載到 Android Studio 專案的 **應用程式** 資料夾，可以在此頁面下載。

7. 切換至 [雲端通訊] ****  索引標籤。

8. 複製並儲存 [伺服器金鑰] ****  以供稍後使用。 您可以使用此值來設定中樞。

## <a name="configure-a-notification-hub"></a>設定通知中樞

1. 登入  [Azure 入口網站](https://portal.azure.com/)。

2. 選取左側功能表上的 [所有服務] ****  ，然後選取 [行動裝置] ****  區段中的 [通知中樞] ****  。 選取服務名稱旁邊的星號圖示，將服務新增到左側功能表上的 [我的最愛] ****  區段。 將 [通知中樞] ****  新增至 [我的最愛] **** 之後，在左側功能表上加以選取。

3. 在 [通知中樞] ****  頁面上，選取工具列上的 ****  [新增]。

   :::image type="content" source="media/android-sdk/add-hub.png" alt-text="新增中樞":::

4. 在 [通知中樞] ****  頁面上，執行下列動作：

   1. 在 [通知中樞] **** 中輸入名稱。

   2. 在 [建立新的命名空間] **** 中輸入名稱。 命名空間包含一或多個中樞。

   3. 從 [位置] ****  下拉式選單中選取一個值。 此值會指定您要在其中建立中樞的位置。

   4. 選取現有 [資源群組] **** 或建立新的資源群組。

   5. 選取 [建立] **** 。

      :::image type="content" source="media/android-sdk/create-hub.png" alt-text="建立中樞":::

5. 選取 [通知] ****  (鈴鐺圖示)，然後選取 **** [前往資源]。 您也可以重新整理 [通知中樞] ****  頁面中的清單，然後選取您的中樞。

   :::image type="content" source="media/android-sdk/notification-hubs.png" alt-text="選取中樞":::

6. 從清單中選取 ****  [存取原則]。 請注意，有兩個連接字串可供使用。 您稍後需要用到這些連接字串來處理推播通知。

   :::image type="content" source="media/android-sdk/access-policies.png" alt-text="存取原則":::

   > [!IMPORTANT]
   > 請勿在應用程式中使用 **DefaultFullSharedAccessSignature** 原則。 此原則僅適用於應用程式後端。

## <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>設定中樞的 Firebase 雲端通訊設定

1. 在左窗格的 [設定] ****  下方，選取 **** [Google (GCM/FCM)]。

2. 針對您稍早儲存的 FCM 專案，輸入其 ****  [伺服器金鑰]。

3. 在工具列上，選取 **** [儲存]。

   :::image type="content" source="media/android-sdk/fcm-server-key.png" alt-text="伺服器金鑰":::

4. Azure 入口網站會顯示訊息來指出中樞的更新已成功。 [儲存] ****  按鈕已停用。

您的通知中樞現在已設定為與 Firebase 雲端通訊搭配使用。 您也擁有了所需的連接字串，而可將通知傳送給裝置並註冊應用程式來接收通知。

## <a name="connect-your-app-to-the-notification-hub"></a>將您的應用程式連接到通知中樞

### <a name="add-google-play-services-to-the-project"></a>新增 Google Play 服務至專案

1. 在 Android Studio 中，選取功能表上的 ****  [工具]，然後選取 **** [SDK 管理員]。

2. 選取您專案中使用的 Android SDK 目標版本。 然後選取 **** [顯示套件詳細資料]。

   :::image type="content" source="media/android-sdk/notification-hubs-android-studio-sdk-manager.png" alt-text="SDK 管理員":::

3. 如果尚未安裝 **** [Google API]，請加以選取。

   :::image type="content" source="media/android-sdk/google-apis-selected.png" alt-text="API":::

4. 切換至 [SDK Tools] ****  索引標籤。 如果您尚未安裝 Google Play Services，請選取 ****  [Google Play Services]，如下圖所示。 然後選取 [套用] ****  以進行安裝。 請注意在稍後步驟中使用的 SDK 路徑。

   :::image type="content" source="media/android-sdk/google-play-services-selected.png" alt-text="Play 服務":::

5. 如果您看到 [確認變更] ****  對話方塊，請選取 **** [確定]。 元件安裝程式會安裝要求的元件。 在元件安裝完成後，選取 ****  [完成]。

6. 選取 [確定] ****  以關閉 [新專案的設定] ****  對話方塊。

### <a name="add-azure-notification-hubs-libraries"></a>新增 Azure 通知中樞程式庫

1. 在應用程式的 **build.gradle** 檔案中，於 [相依性] 區段中新增下列幾行：

   ```gradle
   implementation 'com.microsoft.azure:notification-hubs-android-sdk:1.0.0-preview1@aar'
   implementation 'androidx.appcompat:appcompat:1.0.0'

   implementation 'com.google.firebase:firebase-messaging:20.1.5'

   implementation 'com.android.volley:volley:1.1.1'
   ```

2. 在 [相依性] 區段後面新增下列存放庫：

   ```gradle
   repositories {
      maven {
         url "https://dl.bintray.com/microsoftazuremobile/SDK"
      }
   }
   ```

### <a name="add-google-firebase-support"></a>新增 Google Firebase 支援

1. 在檔案結尾處新增下列外掛程式 (如果尚未存在的話)。

   ```gradle
   apply plugin: 'com.google.gms.google-services'
   ```

2. 在工具列上選取 ****  [立即同步]。

### <a name="add-code"></a>新增程式碼

1. 建立 **NotificationHubListener** 物件，以處理從 Azure 通知中樞攔截訊息的工作。

   ```csharp
   public class CustomNotificationListener implements NotificationHubListener {

      @override

      public void onNotificationReceived(Context context, NotificationMessage message) {

      /* The following notification properties are available. */

      String title = message.getTitle();
      String message = message.getMessage();
      Map<String, String> data = message.getData();

      if (message != null) {
         Log.d(TAG, "Message Notification Title: " + title);
         Log.d(TAG, "Message Notification Body: " + message);
         }
      }
   }
   ```

2. 在 `MainActivity` 類別的 `OnCreate` 方法中，新增下列程式碼以便在活動建立時開始通知中樞初始化程序：

   ```java
   @Override
   protected void onCreate(Bundle savedInstanceState) {

      super.onCreate(savedInstanceState);
      setContentView(R.layout.activity\_main);
      NotificationHub.setListener(new CustomNotificationListener());
      NotificationHub.initialize(this.getApplication(), “Connection-String”, "Hub Name");

   }
   ```

3. 在 Android Studio 的功能表列上，選取 **** [建置]、[重新建置專案] ****  ，來確保程式碼中未沒有任何錯誤。 如果您收到有關 **ic_launcher** 圖示的錯誤，請移除 AndroidManifest.xml 檔案中的下列陳述式：

   ```xml
   android:icon="@mipmap/ic_launcher"
   ```

4. 確定您有可供執行應用程式的虛擬裝置。 如果沒有，請如下所示加以新增：

   1. :::image type="content" source="media/android-sdk/open-device-manager.png" alt-text="裝置管理員":::
   2. :::image type="content" source="media/android-sdk/your-virtual-devices.png" alt-text="虛擬裝置":::
   3. 在您所選的裝置上執行應用程式，並確認該應用程式已向中樞註冊成功。

      :::image type="content" source="media/android-sdk/device-registration.png" alt-text="裝置註冊":::

      > [!NOTE]
      > 註冊可能會在初始啟動期間失敗，直到呼叫執行個體識別碼服務的 `onTokenRefresh()` 方法為止。 重新整理作業應該會起始向通知中樞註冊的作業並且會成功。

## <a name="send-a-test-notification"></a>傳送測試通知

您可以從 [Azure 入口網站](https://portal.azure.com/)將推播通知傳送至您的通知中樞，如下所示：

1. 在 Azure 入口網站中，於中樞的 [通知中樞] 頁面上，選取 [疑難排解] ****  區段中的 ****  [測試傳送]。

2. 在 [平台] **** 中，選取 **** [Android]。

3. 選取 **** [傳送]。 您尚未在 Android 裝置上執行行動應用程式，所以不會在裝置上看到通知。 在您執行行動應用程式後，請再次選取 [傳送] ****  按鈕，以查看通知訊息。

4. 在入口網站頁面底部的清單中查看作業的結果。

   :::image type="content" source="media/android-sdk/notification-hubs-test-send.png" alt-text="傳送測試通知":::

5. 您會在裝置上看到通知訊息。

推播通知通常會以後端服務傳送，例如 Mobile Apps 或使用相容程式庫的 ASP.NET。 如果您的後端無法使用程式庫，您也可以直接使用 REST API 來傳送通知訊息。

## <a name="run-the-mobile-app-on-emulator"></a>在模擬器上執行行動應用程式

在模擬器內測試推播通知之前，請先確定您的模擬器映像支援您為應用程式選擇的 Google API 層級。 如果您的映像不支援原生 Google API，可能會遇到 **SERVICE_NOT_AVAILABLE** 例外狀況。

此外，請確定已將 Google 帳戶新增至執行中模擬器的 [設定] ****  >[帳戶] **** 下方。 否則，嘗試向 FCM 註冊可能會導致 **AUTHENTICATION_FAILED** 例外狀況。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已使用 Firebase 雲端通訊將通知廣播至所有向服務註冊的 Android 裝置。 若想了解如何將通知推送至特定裝置，請繼續進行下列教學課程：

> [!div class="nextstepaction"]
>[教學課程：將通知傳送給特定使用者](push-notifications-android-specific-users-firebase-cloud-messaging.md)

以下是一些傳送通知的其他教學課程清單：

- Azure Mobile Apps：如需透過範例來了解如何從已與通知中樞整合的 Mobile Apps 後端傳送通知，請參閱 [將推播通知新增至您的 iOS 應用程式](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push)。

- ASP.NET： [使用通知中樞將推播通知傳送給使用者](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)。

- Azure 通知中樞 Java SDK：請參閱 [如何從 Java 使用通知中樞](notification-hubs-java-push-notification-tutorial.md) 以便從 Java 傳送通知。 對於 Android 的開發已經在 Eclipse 中測試。

- PHP： [如何從 PHP 使用通知中樞](notification-hubs-php-push-notification-tutorial.md)。