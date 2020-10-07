---
title: 使用 Azure 通知中樞和 iOS SDK 2.0.4 版將推播通知傳送至 iOS
description: 在本教學課程中，您將了解如何使用 Azure 通知中樞和 Apple Push Notification Service 將推播通知傳送至 iOS 裝置 (2.0.4 版)。
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: ffa562a734e0e6f898aaff89622362080bf1a053
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318189"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs-version-204"></a>教學課程：使用 Azure 通知中樞 (2.0.4 版) 將推播通知傳送至 iOS 應用程式

本教學課程示範如何使用 Azure 通知中樞將推播通知傳送至 iOS 應用程式 (使用 Azure 通知中樞 SDK 2.0.4 版)。

本教學課程涵蓋下列步驟：

- 建立 iOS 應用程式範例。
- 將您的 iOS 應用程式連線至 Azure 通知中樞。
- 傳送測試推播通知。
- 確認您的應用程式可接收通知。

您可以從 [GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/v3-preview2/Samples) 下載本教學課程的完整程式碼。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您需要下列必要條件：

- 執行 [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532) 的 Mac，以及安裝在您 Keychain 中的有效開發人員憑證。
- 執行 iOS 10 或更新版本的 iPhone 或 iPad。
- 在 [Apple 入口網站](https://developer.apple.com/)中註冊且與您憑證相關聯的實體裝置。

在繼續之前，請務必先完成上一個教學課程，以了解如何開始使用[適用於 iOS 應用程式的 Azure 通知中樞](ios-sdk-get-started.md)，以在您的通知中樞內設定推送認證。 即使您先前沒有 iOS 開發的經驗，您應該仍然可以遵循這些步驟。

> [!NOTE]
> 基於推播通知的組態需求，您必須在實體 iOS 裝置 (iPhone 或 iPad)，而不是在 iOS 模擬器上部署和測試推播通知。

## <a name="connect-your-ios-app-to-notification-hubs"></a>將您的 iOS 應用程式連接到通知中樞

1. 在 Xcode 中建立新的 iOS 專案，並選取 [單一檢視應用程式] ****  範本。

   :::image type="content" source="media/ios-sdk/image1.png" alt-text="選取範本":::

2. 設定新專案的選項時，請務必使用您在 Apple 開發人員入口網站上設定套件組合識別碼時使用的相同 **產品名稱** 和 **組織識別碼** 。

3. 在 [專案導覽] 底下，選取 [目標] **** 下的專案名稱，然後選取 [簽署與功能] ****   索引標籤。 請務必要為您的 Apple 開發人員帳戶選取適合的 **團隊** 。 XCode 應該會根據您的套件組合識別碼，自動提取您先前建立的佈建設定檔。

   如果畫面未顯示您在 Xcode 中建立的新佈建設定檔，請嘗試重新整理簽署身分識別的設定檔。 按一下功能表列上的  **Xcode** ，再依序按一下 [喜好設定] **** 、[帳戶] ****   索引標籤、[檢視詳細資料] ****   按鈕、您的簽署身分識別，然後按一下右下角的 [重新整理] 按鈕。

   :::image type="content" source="media/ios-sdk/image2.png" alt-text="選取範本":::

4. 在 [簽署與功能] ****   索引標籤中，選取 [+ 功能] **** 。 按兩下 [推播通知] ****   加以啟用。

   :::image type="content" source="media/ios-sdk/image3.png" alt-text="選取範本"
      ```

      - 接下來，更新組建相依性：

      ```shell
      $ carthage update
      ```

      如需有關使用 Carthage 的詳細資訊，請參閱  [Carthage GitHub 存放庫](https://github.com/Carthage/Carthage)。

   - 透過將二進位檔複製到您的專案來進行整合：您可以藉由將二進位檔複製到專案中來進行整合，如下所示：

        - 下載以 zip 檔案形式提供的  [Azure 通知中樞 SDK](https://github.com/Azure/azure-notificationhubs-android/releases)  架構，然後將其解壓縮。

        - 在 Xcode 中，以滑鼠右鍵按一下您的專案，然後按一下 [新增檔案至] ****   選項，將  **WindowsAzureMessaging.framework**  資料夾新增至 Xcode 專案。 選取 [選項] ****  ，並確定已選取 [必要時複製項目] ****  ，然後按一下 [新增] **** 。

          :::image type="content" source="media/ios-sdk/image4.png" alt-text="選取範本":::

6. 將新的標頭檔新增至名為  **Constants.h** 的專案。 若要這麼做，請以滑鼠右鍵按一下專案名稱並選取 [新增檔案...] **** 。然後選取 [標頭檔] **** 。 此檔案會保存通知中樞的常數。 然後選取 [下一步] **** 。 將檔案命名為  **Constants.h**。

7. 將下列程式碼新增至 Constants.h 檔案：

   ```objc
   #ifndef Constants_h
   #define Constants_h
   extern NSString* const NHInfoConnectionString;
   extern NSString* const NHInfoHubName;
   extern NSString* const NHUserDefaultTags;
   #endif /* Constants_h */
   ```

8. 新增 Constants.h 的實作檔。 若要這麼做，請以滑鼠右鍵按一下專案名稱並選取 [新增檔案...] **** 。選取 [Objective-C 檔案] **** ，然後選取 [下一步] **** 。 將檔案命名為  **Constants.m**。

   :::image type="content" source="media/ios-sdk/image5.png" alt-text="選取範本"

   NSString* const NHInfoConnectionString = @"NotificationHubConnectionString";
   NSString* const NHInfoHubName = @"NotificationHubName";NSString* const NHUserDefaultTags = @"notification_tags";
   ```

10. 開啟專案  **AppDelegate.h**  檔案，並以下列程式碼取代其內容：

    ```objc
    #import <UIKit/UIKit.h>
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h> 

    @interface AppDelegate : UIResponder <UIApplicationDelegate,   UNUserNotificationCenterDelegate>

    @property (strong, nonatomic) UIWindow *window;

    - (void)handleRegister;
    - (void)handleUnregister;

    @end
    ```

11. 在專案  **AppDelegate.m**  檔案中，新增下列  `import`  陳述式：

    ```objc
    #import "Constants.h"
    #import "NotificationDetailViewController.h"
    ```

12. 此外在  **AppDelegate.m**  檔案中，根據您的 iOS 版本在  `didFinishLaunchingWithOptions`  方法內新增以下這行程式碼。 此程式碼會向 APN 註冊裝置控制代碼：

    ```objc
    [[UNUserNotificationCenter currentNotificationCenter] setDelegate:self];
    ```

13. 在相同的  **AppDelegate.m**  檔案中，使用下列程式碼取代  `didFinishLaunchingWithOptions`  之後的所有程式碼：

    ```objc
    // Tells the app that a remote notification arrived that indicates there is data to be fetched.

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))completionHandler {
    NSLog(@"Received remote (silent) notification");
    [self logNotificationDetails:userInfo];

    //
    // Let the system know the silent notification has been processed.
    //
    completionHandler(UIBackgroundFetchResultNoData);
    }

    // Tells the delegate that the app successfully registered with Apple Push Notification service (APNs).

    - (void) application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
    NSMutableSet *tags = [[NSMutableSet alloc] init];

    // Load and parse stored tags
    NSString *unparsedTags = [[NSUserDefaults standardUserDefaults] valueForKey:NHUserDefaultTags];
    if (unparsedTags.length > 0) {
        NSArray *tagsArray = [unparsedTags componentsSeparatedByString: @","];
        [tags addObjectsFromArray:tagsArray];
    }

    // Register the device with the Notification Hub.
    // If the device has not already been registered, this will create the registration.
    // If the device has already been registered, this will update the existing registration.
    //
    SBNotificationHub* hub = [self getNotificationHub];
    [hub registerNativeWithDeviceToken:deviceToken tags:tags completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        } else {
            [self showAlert:@"Registered" withTitle:@"Registration Status"];
        }
    }];
    }

    // UNUserNotificationCenterDelegate methods
    //
    // Asks the delegate how to handle a notification that arrived while the app was running in the  foreground.

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler {
    NSLog(@"Received notification while the application is in the foreground");

    // The system calls this delegate method when the app is in the foreground. This allows the app to handle the notification
    // itself (and potentially modify the default system behavior).

    // Handle the notification by displaying custom UI.
    //
    [self showNotification:notification.request.content.userInfo];

    // Use 'options' to specify which default behaviors to enable.
    // - UNAuthorizationOptionBadge: Apply the notification's badge value to the app’s icon.
    // - UNAuthorizationOptionSound: Play the sound associated with the notification.
    // - UNAuthorizationOptionAlert: Display the alert using the content provided by the notification.
    //
    // In this case, do not pass UNAuthorizationOptionAlert because the notification was handled by the app.
    //
    completionHandler(UNAuthorizationOptionBadge | UNAuthorizationOptionSound);
    }

    // Asks the delegate to process the user's response to a delivered notification.
    //

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)(void))completionHandler {
    NSLog(@"Received notification while the application is in the background");

    // The system calls this delegate method when the user taps or responds to the system notification.

    // Handle the notification response by displaying custom UI
    //
    [self showNotification:response.notification.request.content.userInfo];

    // Let the system know the response has been processed.
    //
    completionHandler();
    }

    // App logic and helpers

    - (SBNotificationHub *)getNotificationHub {
    NSString *hubName = [[NSBundle mainBundle] objectForInfoDictionaryKey:NHInfoHubName];
    NSString *connectionString = [[NSBundle mainBundle] objectForInfoDictionaryKey:NHInfoConnectionString];

    return [[SBNotificationHub alloc] initWithConnectionString:connectionString notificationHubPath:hubName];
    }

    - (void)handleRegister {
    UNUserNotificationCenter *center = [UNUserNotificationCenter currentNotificationCenter];

    UNAuthorizationOptions options =  UNAuthorizationOptionAlert | UNAuthorizationOptionSound | UNAuthorizationOptionBadge;
    [center requestAuthorizationWithOptions:(options) completionHandler:^(BOOL granted, NSError * _Nullable error) {
        if (error != nil) {
            NSLog(@"Error requesting for authorization: %@", error);
        }
    }];
    [[UIApplication sharedApplication] registerForRemoteNotifications];
    }

    - (void)handleUnregister {
    //
    // Unregister the device with the Notification Hub.
    //
    SBNotificationHub *hub = [self getNotificationHub];
    [hub unregisterNativeWithCompletion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error unregistering for push: %@", error);
        } else {
            [self showAlert:@"Unregistered" withTitle:@"Registration Status"];
        }
    }];
    }

    - (void)logNotificationDetails:(NSDictionary *)userInfo {
    if (userInfo != nil) {
        UIApplicationState state = [UIApplication sharedApplication].applicationState;
        BOOL background = state != UIApplicationStateActive;
        NSLog(@"Received %@notification: \n%@", background ? @"(background) " : @"", userInfo);
    }
    }

    - (void)showAlert:(NSString *)message withTitle:(NSString *)title {
    if (title == nil) {
        title = @"Alert";
    }
    UIAlertController *alert = [UIAlertController alertControllerWithTitle:title message:message preferredStyle:UIAlertControllerStyleAlert];
    [alert addAction:[UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:nil]];
    [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:alert animated:YES completion:nil];
    }

    - (void)showNotification:(NSDictionary *)userInfo {
    [self logNotificationDetails:userInfo];

    NotificationDetailViewController *notificationDetail = [[NotificationDetailViewController alloc] initWithUserInfo:userInfo];
    [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:notificationDetail animated:YES completion:nil];
    }

    @end
    ```

    此程式碼會使用您在  **Constants.h** 中指定的連接資訊連接到通知中心。 然後，其可提供裝置權杖給通知中樞，讓中樞能夠傳送通知。

### <a name="create-notificationdetailviewcontroller-header-file"></a>建立 NotificationDetailViewController 標頭檔

1. 類似先前的指示，新增另一個名為  **NotificationDetailViewController.h** 的標頭檔。 將新標頭檔的內容取代為下列程式碼：

   ```objc
   #import <UIKit/UIKit.h>

   NS_ASSUME_NONNULL_BEGIN

   @interface NotificationDetailViewController : UIViewController

   @property (strong, nonatomic) IBOutlet UILabel *titleLabel;
   @property (strong, nonatomic) IBOutlet UILabel *bodyLabel;
   @property (strong, nonatomic) IBOutlet UIButton *dismissButton;

   @property (strong, nonatomic) NSDictionary *userInfo;

   - (id)initWithUserInfo:(NSDictionary *)userInfo;

   @end

   NS_ASSUME_NONNULL_END
   ```

2. 新增實作檔  **NotificationDetailViewController.m**。 將檔案的內容取代為下列程式碼，其可實作 UIViewController 方法：

   ```objc
   #import "NotificationDetailViewController.h"

   @interface NotificationDetailViewController ()

   @end

   @implementation NotificationDetailViewController

   - (id)initWithUserInfo:(NSDictionary *)userInfo {
    self = [super initWithNibName:@"NotificationDetail" bundle:nil];
    if (self) {
        _userInfo = userInfo;
    }
    return self;
   }

   - (void)viewDidLayoutSubviews {
    [self.titleLabel sizeToFit];
    [self.bodyLabel sizeToFit];
   }

   - (void)viewDidLoad {
    [super viewDidLoad];

    NSString *title = nil;
    NSString *body = nil;

    NSDictionary *aps = [_userInfo valueForKey:@"aps"];
    NSObject *alertObject = [aps valueForKey:@"alert"];
    if (alertObject != nil) {
        if ([alertObject isKindOfClass:[NSDictionary class]]) {
            NSDictionary *alertDict = (NSDictionary *)alertObject;
            title = [alertDict valueForKey:@"title"];
            body = [alertObject valueForKey:@"body"];
        } else if ([alertObject isKindOfClass:[NSString class]]) {
            body = (NSString *)alertObject;
        } else {
            NSLog(@"Unable to parse notification content. Unexpected format: %@", alertObject);
        }
    }

    if (title == nil) {
        title = @"<unset>";
    }

    if (body == nil) {
        body = @"<unset>";
    }

    self.titleLabel.text = title;
    self.bodyLabel.text = body;
   }

   - (IBAction)handleDismiss:(id)sender {
    [self dismissViewControllerAnimated:YES completion:nil];
   }

   @end
   ```

### <a name="viewcontroller"></a>ViewController

1. 在專案  **ViewController.h**  檔案中，新增下列  `import`  陳述式：

   ```objc
   #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
   #import <UserNotifications/UserNotifications.h>
   ```

2. 此外，在  **ViewController.h** 中的  `@interface`  宣告之後新增下列屬性宣告：

   ```objc
   @property (strong, nonatomic) IBOutlet UITextField *tagsTextField;
   @property (strong, nonatomic) IBOutlet UIButton *registerButton;
   @property (strong, nonatomic) IBOutlet UIButton *unregisterButton;
   ```

3. 在專案的  **ViewController.m**  實作檔中，將檔案的內容取代為下列程式碼：

   ```objc
   #import "ViewController.h"
   #import "Constants.h"
   #import "AppDelegate.h"

   @interface ViewController ()

   @end

   @implementation ViewController

   // UIViewController methods

   - (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event {
    // Simple method to dismiss keyboard when user taps outside of the UITextField.
    [self.view endEditing:YES];
   }

   - (void)viewDidLoad {
    [super viewDidLoad];

    // Load raw tags text from storage and initialize the text field
    self.tagsTextField.text = [[NSUserDefaults standardUserDefaults] valueForKey:NHUserDefaultTags];
   }

   - (IBAction)handleRegister:(id)sender {
    // Save raw tags text in storage
    [[NSUserDefaults standardUserDefaults] setValue:self.tagsTextField.text forKey:NHUserDefaultTags];

   // Delegate processing the register action to the app delegate.
   [[[UIApplication sharedApplication] delegate] performSelector:@selector(handleRegister)];
   }

   - (IBAction)handleUnregister:(id)sender {
    [[[UIApplication sharedApplication] delegate] performSelector:@selector(handleUnregister)];
   }

   @end
   ```

4. 為了確認應用程式能夠順利運作，在裝置上建置並執行應用程式。

## <a name="send-test-push-notifications"></a>傳送測試推播通知

您可以在  [Azure 入口網站](https://portal.azure.com/)中，使用 [測試傳送] ****   選項測試應用程式能否接收通知。 它會將測試推播通知傳送至您的裝置。

:::image type="content" source="media/ios-sdk/image6.png" alt-text="選取範本":::

推播通知通常會以後端服務傳送，例如 Mobile Apps 或使用相容程式庫的 ASP.NET。 如果您的後端無法使用程式庫，您也可以直接使用 REST API 來傳送通知訊息。

以下是可供您在傳送通知時檢閱的其他教學課程清單：

- Azure Mobile Apps：如需透過範例來了解如何從已與通知中樞整合的 Mobile Apps 後端傳送通知，請參閱 [將推播通知新增至您的 iOS 應用程式](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push)。
- ASP.NET： [使用通知中樞將推播通知傳送給使用者](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)。
- Azure 通知中樞 Java SDK：請參閱 [如何從 Java 使用通知中樞](notification-hubs-java-push-notification-tutorial.md) 以便從 Java 傳送通知。 對於 Android 的開發已經在 Eclipse 中測試。
- PHP： [如何從 PHP 使用通知中樞](notification-hubs-php-push-notification-tutorial.md)。

## <a name="verify-that-your-app-receives-push-notifications"></a>確認您的應用程式可接收推播通知

若要在 iOS 上測試推播通知，您必須將應用程式部署至實體 iOS 裝置。 您無法利用 iOS 模擬器傳送 Apple 推播通知。

1. 執行應用程式並確認註冊成功，然後按下 [確定] **** 。

   :::image type="content" source="media/ios-sdk/image7.png" alt-text="選取範本":::

2. 接下來，請依照上一節的說明，從  [Azure 入口網站](https://portal.azure.com/)傳送測試推播通知。

3. 推播通知會從指定的通知中樞傳送至所有已註冊要接收通知的裝置。

   :::image type="content" source="media/ios-sdk/image8.png" alt-text="選取範本":::

## <a name="next-steps"></a>後續步驟

在此簡單範例中，您會將推播通知廣播到您已註冊的所有 iOS 裝置。 若要了解如何將推播通知推送至特定 iOS 裝置，請繼續進行下列教學課程：

[教學課程：將推播通知傳送至特定裝置](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

如需詳細資訊，請參閱下列文章：

- [Azure 通知中樞概觀](notification-hubs-push-notification-overview.md)
- [通知中樞 REST API](/rest/api/notificationhubs/)
- [適用於後端作業的通知中樞 SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [GitHub 上的通知中樞 SDK](https://github.com/Azure/azure-notificationhubs)
- [向應用程式後端註冊](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [註冊管理](notification-hubs-push-notification-registration-management.md)
- [使用標記](notification-hubs-tags-segment-push-message.md)
- [使用自訂範本](notification-hubs-templates-cross-platform-push-messages.md)
- [使用共用存取簽章的服務匯流排存取控制](../service-bus-messaging/service-bus-sas.md)
- [以程式設計方式產生 SAS 權杖](/rest/api/eventhub/generate-sas-token)
- [Apple 安全性：一般加密](https://developer.apple.com/security/)
- [Unix Epoch 時間](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)