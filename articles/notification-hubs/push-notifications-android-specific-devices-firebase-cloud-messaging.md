---
title: 使用 Azure 通知中樞和 Google Firebase 雲端通訊將推播通知傳送至特定裝置 | Microsoft Docs
description: 了解如何使用 Azure 通知中樞和 Google Firebase 雲端通訊 (FCM) 將通知推送至特定 Android 裝置。
services: notification-hubs
documentationcenter: android
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 3c23cb80-9d35-4dde-b26d-a7bfd4cb8f81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/30/2019
ms.openlocfilehash: 7143ecbb04fc19c990c4a5d71f3d6b0e8e05f229
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86529721"
---
# <a name="tutorial-send-notifications-to-specific-devices-using-notification-hubs-and-google-firebase-cloud-messaging"></a>教學課程：使用通知中樞和 Google Firebase 雲端通訊將通知傳送至特定裝置

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>概觀

本教學課程說明如何使用 Azure 通知中樞，將即時新聞通知廣播至 Android 應用程式。 完成時，您便能夠註冊您所感興趣的即時新聞類別，並僅接收這些類別的推播通知。 此情況為適用於許多應用程式的常見模式，其中必須將通知傳送給先前宣告對該通知感興趣的使用者群組，例如 RSS 閱讀程式、供樂迷使用的應用程式等等。

在通知中樞內建立註冊時，您可以透過包含一或多個 *tags* 來啟用廣播案例。 當標籤收到通知時，所有已註冊此標籤的裝置都會收到通知。 由於標籤只是簡單的字串而已，您無需預先佈建標籤。 如需標記的詳細資訊，請參閱[通知中樞路由與標記運算式](notification-hubs-tags-segment-push-message.md)。

在本教學課程中，您會執行下列動作：

> [!div class="checklist"]
> * 在行動應用程式中新增類別選項。
> * 註冊附有標記的通知。
> * 傳送加註標記的通知。
> * 測試應用程式

## <a name="prerequisites"></a>必要條件

本教學課程以您在以下教學課程中建立的 Android 應用程式為基礎：[教學課程：使用 Azure 通知中樞和 Firebase 雲端通訊將通知推送至 Android 裝置](notification-hubs-android-push-notification-google-fcm-get-started.md)。 開始進行本教學課程之前，請完成[教學課程：使用 Azure 通知中樞和 Firebase 雲端通訊將通知推送至 Android 裝置](notification-hubs-android-push-notification-google-fcm-get-started.md)。

## <a name="add-category-selection-to-the-app"></a>在應用程式中新增類別選項

第一個步驟是在您現有的主要活動上新增 UI 元素，以便使用者選取要註冊的類別。 使用者所選取的類別會儲存在裝置上。 啟動應用程式時，您的通知中心內會建立以所選取類別作為標籤的裝置註冊。

1. 開啟 `res/layout/activity_main.xml file`，然後以下列程式碼取代內容：

    ```xml
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context="com.example.breakingnews.MainActivity"
        android:orientation="vertical">

            <CheckBox
                android:id="@+id/worldBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_world" />
            <CheckBox
                android:id="@+id/politicsBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_politics" />
            <CheckBox
                android:id="@+id/businessBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_business" />
            <CheckBox
                android:id="@+id/technologyBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_technology" />
            <CheckBox
                android:id="@+id/scienceBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_science" />
            <CheckBox
                android:id="@+id/sportsBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_sports" />
            <Button
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:onClick="subscribe"
                android:text="@string/button_subscribe" />
            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Hello World!"
                android:id="@+id/text_hello"
            />
    </LinearLayout>
    ```
2. 開啟 `res/values/strings.xml` 檔案並新增下列幾行：

    ```xml
    <string name="button_subscribe">Subscribe</string>
    <string name="label_world">World</string>
    <string name="label_politics">Politics</string>
    <string name="label_business">Business</string>
    <string name="label_technology">Technology</string>
    <string name="label_science">Science</string>
    <string name="label_sports">Sports</string>
    ```

    您的 `main_activity.xml` 圖形版面配置應如下圖所示：

    ![顯示主要活動 X M L 圖形版面配置外觀的模擬器螢幕擷取畫面。][A1]
3. 在與 `MainActivity` 類別相同的套件中建立 `Notifications` 類別。

    ```java
    import java.util.HashSet;
    import java.util.Set;
    import java.util.concurrent.TimeUnit;
    
    import android.content.Context;
    import android.content.SharedPreferences;
    import android.os.AsyncTask;
    import android.util.Log;
    import android.widget.Toast;
    
    import com.google.android.gms.tasks.OnSuccessListener;
    import com.google.firebase.iid.FirebaseInstanceId;
    import com.google.firebase.iid.InstanceIdResult;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    
    public class Notifications {
        private static final String PREFS_NAME = "BreakingNewsCategories";
        private FirebaseInstanceId fcm;
        private NotificationHub hub;
        private Context context;
        private String senderId;
        public static String FCM_token = "";
        private static final String TAG = "Notifications";
    
        public Notifications(Context context, String hubName, String listenConnectionString) {
            this.context = context;
            this.senderId = senderId;
    
            fcm = FirebaseInstanceId.getInstance();
            hub = new NotificationHub(hubName, listenConnectionString, context);
        }
    
        public void storeCategoriesAndSubscribe(Set<String> categories)
        {
            SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
            settings.edit().putStringSet("categories", categories).commit();
            subscribeToCategories(categories);
        }
    
        public Set<String> retrieveCategories() {
            SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
            return settings.getStringSet("categories", new HashSet<String>());
        }
    
        public void subscribeToCategories(final Set<String> categories) {
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        FirebaseInstanceId.getInstance().getInstanceId().addOnSuccessListener(new OnSuccessListener<InstanceIdResult>() {
                            @Override
                            public void onSuccess(InstanceIdResult instanceIdResult) {
                                FCM_token = instanceIdResult.getToken();
                                Log.d(TAG, "FCM Registration Token: " + FCM_token);
                            }
                        });
    
                        TimeUnit.SECONDS.sleep(1);
    
                        String templateBodyFCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";
    
                        hub.registerTemplate(FCM_token,"simpleFCMTemplate", templateBodyFCM,
                                categories.toArray(new String[categories.size()]));
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to register - " + e.getMessage());
                        return e;
                    }
                    return null;
                }
    
                protected void onPostExecute(Object result) {
                    String message = "Subscribed for categories: "
                            + categories.toString();
                    Toast.makeText(context, message,
                            Toast.LENGTH_LONG).show();
                }
            }.execute(null, null, null);
        }
    
    }
    ```

    本類別會使用本機儲存體來儲存此裝置必須接收的新聞類別。 它也包含註冊這些類別的方法。
4. 在您的 `MainActivity` 類別中，新增 `Notifications` 的欄位：

    ```java
    private Notifications notifications;
    ```
5. 接著，更新 `onCreate` 方法，如下列程式碼所示。 您可以在 [通知] 類別的 **subscribeToCategories** 方法中向通知中樞註冊。 

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;

        FirebaseService.createChannelAndHandleNotifications(getApplicationContext());
        notifications = new Notifications(this, NotificationSettings.HubName, NotificationSettings.HubListenConnectionString);
        notifications.subscribeToCategories(notifications.retrieveCategories());
    }
    ```

    確認中樞名稱和連接字串已正確設定於 NotificationSettings 類別中。

    > [!NOTE]
    > 因為隨用戶端應用程式散佈的憑證通常不安全，您應只將接聽存取權的金鑰隨用戶端應用程式散佈。 您的應用程式可透過接聽存取權來註冊通知，但無法修改現有的註冊或無法傳送通知。 在安全的後端服務中，會使用完整存取金鑰來傳送通知和變更現有的註冊。

6. 然後，新增下列匯入：

    ```java
    import android.widget.CheckBox;
    import java.util.HashSet;
    import java.util.Set;
    import android.view.View;
    ```
7. 新增下列 `subscribe` 方法，以處理 [訂閱] 按鈕的 click 事件：

    ```java
    public void subscribe(View sender) {
        final Set<String> categories = new HashSet<String>();

        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        if (world.isChecked())
            categories.add("world");
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        if (politics.isChecked())
            categories.add("politics");
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        if (business.isChecked())
            categories.add("business");
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        if (technology.isChecked())
            categories.add("technology");
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        if (science.isChecked())
            categories.add("science");
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        if (sports.isChecked())
            categories.add("sports");

        notifications.storeCategoriesAndSubscribe(categories);
    }
    ```

    此方法會建立一份類別清單，並使用 `Notifications` 類別在本機儲存體中儲存清單，並在通知中心註冊對應標籤。 變更類別時，系統會使用新類別重新建立註冊。

您的應用程式現在可以在裝置上的本機儲存體中儲存一組類別，並在使用者每次變更類別選項時在通知中心註冊。

## <a name="register-for-notifications"></a>註冊通知

這些步驟會在啟動時，使用已儲存在本機儲存體中的類別在通知中心註冊。

1. 確認 `MainActivity` 類別中的 `onCreate` 方法結尾處有下列程式碼：

    ```java
    notifications.subscribeToCategories(notifications.retrieveCategories());
    ```

    此程式碼可確保應用程式每次啟動時都會從本機儲存體擷取類別，並要求這些類別的註冊。
2. 然後如下所示，更新 `MainActivity` 類別的 `onStart()` 方法：

    ```java
    @Override
    protected void onStart() {

        super.onStart();
        isVisible = true;

        Set<String> categories = notifications.retrieveCategories();

        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        world.setChecked(categories.contains("world"));
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        politics.setChecked(categories.contains("politics"));
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        business.setChecked(categories.contains("business"));
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        technology.setChecked(categories.contains("technology"));
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        science.setChecked(categories.contains("science"));
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        sports.setChecked(categories.contains("sports"));
    }
    ```

    此程式碼會根據原先儲存的類別狀態更新主要活動。

現在已完成此應用程式，且可在裝置本機儲存體中儲存一組類別，以供每次使用者變更類別選項在通知中心註冊時使用。 接著，定義可將類別通知傳送至此應用程式的後端。

## <a name="send-tagged-notifications"></a>傳送加註標記的通知

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="test-the-app"></a>測試應用程式

1. 使用 Android Studio，在您的 Android 裝置或模擬器上執行應用程式。 應用程式 UI 提供一組切換，可讓您選擇要訂閱的類別。
2. 啟用一或多個類別切換，然後按一下 [訂閱] 。 應用程式會將選取的類別轉換成標籤，並在通知中心內為選取的標籤要求新裝置註冊。 隨即會傳回已註冊的類別，且會顯示在快顯通知中。

    ![訂閱類別](./media/notification-hubs-aspnet-backend-android-breaking-news/subscribe-for-categories.png)
3. 執行 .NET 主控台應用程式，這會傳送每個類別的通知。 選取的類別通知會以快顯通知方式出現。

    ![技術新聞通知](./media/notification-hubs-aspnet-backend-android-breaking-news/technolgy-news-notification.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已將廣播通知傳送至已註冊相關類別的特定 Android 裝置。 若想了解如何將通知推送給特定使用者，請繼續進行下列教學課程：

> [!div class="nextstepaction"]
>[將通知推送給特定使用者](push-notifications-android-specific-users-firebase-cloud-messaging.md)

<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Azure portal]: https://portal.azure.com
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
