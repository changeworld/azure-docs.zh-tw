---
title: 使用 Azure 通知中樞將推播通知傳送至 iOS 應用程式 | Microsoft Docs
description: 在本教學課程中，您將了解如何使用 Azure 通知中樞，將推播通知傳送至 iOS 應用程式。
services: notification-hubs
documentationcenter: ios
keywords: 推播通知,推播通知,ios 推播通知
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: tutorial
ms.custom: mvc
ms.date: 11/21/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 032ca8d4ecbcf1fc7f3c22cbe5a0ee934fc5e17c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74407366"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>教學課程：使用 Azure 通知中樞將推播通知傳送至 iOS 應用程式

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

在本教學課程中，您會使用 Azure 通知中樞將推播通知傳送至 iOS 應用程式。 您會使用 [Apple Push Notification Service (APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)，建立可接收推播通知的空白 iOS 應用程式。

在本教學課程中，您會執行下列步驟：

> [!div class="checklist"]
> * 產生憑證簽署要求檔案
> * 為您的應用程式要求推播通知
> * 建立應用程式的佈建設定檔
> * 針對 iOS 推播通知設定您的通知中樞
> * 將您的 iOS 應用程式連線至通知中樞
> * 傳送測試推播通知
> * 確認您的應用程式可接收通知

您可以在 [GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/master/Samples) 上找到本教學課程的完整程式碼。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要下列必要條件：

* 使用中的 Azure 帳戶。 如果您沒有帳戶，可以[建立一個免費 Azure 帳戶](https://azure.microsoft.com/free)。
* [Windows Azure 傳訊架構]
* 最新版的 [Xcode]
* 能使用 iOS 版本10 (或更新版本) 的裝置
* [Apple Developer Program](https://developer.apple.com/programs/) 成員資格。
  
  > [!NOTE]
  > 基於推播通知的組態需求，您必須在實體 iOS 裝置 (iPhone 或 iPad)，而不是在 iOS 模擬器上部署和測試推播通知。
  
完成本教學課程是參加 iOS app 所有其他通知中樞教學課程的先決條件。

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-notification-hubs"></a>將您的 iOS 應用程式連接到通知中樞

1. 在 Xcode 中建立新的 iOS 專案，並選取 [單一檢視應用程式]  範本。

    ![Xcode - 單一檢視應用程式][8]

2. 設定新專案的選項時，請務必使用您在 Apple 開發人員入口網站上設定套件組合識別碼時使用的相同**產品名稱**和**組織識別碼**。

3. 在 [專案導覽] 底下，選取 [目標]  下的專案名稱，然後選取 [簽署與功能]  索引標籤。請務必要為您的 Apple 開發人員帳戶選取適合的**團隊**。 XCode 應該會根據您的套件組合識別碼，自動提取您先前建立的佈建設定檔。

    如果畫面未顯示您在 Xcode 中建立的新佈建設定檔，請嘗試重新整理簽署身分識別的設定檔。 按一下功能表列上的 Xcode  ，再依序按一下 [喜好設定]  、[帳戶]  索引標籤、[檢視詳細資料]  按鈕、您的簽署身分識別，然後按一下右下角的 [重新整理] 按鈕。

    ![Xcode - 佈建設定檔][9]

4. 在 [簽署與功能]  索引標籤中，選取 [+ 功能]  。  按兩下 [推播通知]  加以啟用。

    ![Xcode - 推播功能][12]

5. 新增 Azure 通知中樞 SDK 模組。

   您也可以使用 [Cocoapods](https://cocoapods.org)，或手動將二進位檔新增至您的專案，來將 Azure 通知中樞 SDK 整合至應用程式中。

   - 透過 Cocoapods 整合

     將下列相依性新增至 `podfile`，以在您的應用程式中包含 Azure 通知中樞 SDK。

     ```ruby
     pod 'AzureNotificationHubs-iOS'
     ```

     執行 `pod install` 來安裝新定義的 Pod，並開啟您的 `.xcworkspace`。

     > [!NOTE]
     > 如果您在執行 `pod install` 時看到 **找不到 AzureNotificationHubs-iOS 規格**之類的錯誤，請執行 `pod repo update`，以從 Cocoapods 存放庫取得最新的 Pod，然後執行 `pod install`。

   - 透過 Carthage 整合

     將下列相依性新增至 `Cartfile`，以在您的應用程式中包含 Azure 通知中樞 SDK。

     ```ruby
     github "Azure/azure-notificationhubs-ios"
     ```

     接下來，更新及建置相依性：

     ```shell
     $ carthage update
     ```

     如需有關使用 Carthage 的詳細資訊，請參閱 [Carthage GitHub 存放庫](https://github.com/Carthage/Carthage)。

   - 透過將二進位檔複製到您的專案來進行整合

     1. 下載以 zip 檔案形式提供的 [Azure 通知中樞 SDK](https://github.com/Azure/azure-notificationhubs-ios/releases) 架構，然後將其解壓縮。

     2. 在 Xcode 中，以滑鼠右鍵按一下您的專案，然後按一下 [新增檔案至]  選項，將 **WindowsAzureMessaging.framework** 資料夾新增至 Xcode 專案。 選取 [選項]  ，並務必要選取 [必要時複製項目]  ，然後按一下 [新增]  。

        ![解壓縮 Azure SDK][10]

6. 將新的標頭檔新增至名為 **Constants.h** 的專案。 若要這麼做，請以滑鼠右鍵按一下專案名稱並選取 [新增檔案...]  。然後選取 [標頭檔]  。 此檔案會保存通知中樞的常數。 然後選取 [下一步]  。 將檔案命名為 **Constants.h**。

7. 將下列程式碼新增至 Constants.h 檔案：

    ```objc
    #ifndef Constants_h
    #define Constants_h

    extern NSString* const NHInfoConnectionString;
    extern NSString* const NHInfoHubName;
    extern NSString* const NHUserDefaultTags;

    #endif /* Constants_h */
    ```

8. 新增 Constants.h 的實作檔。 若要這麼做，請以滑鼠右鍵按一下專案名稱並選取 [新增檔案...]  。選取 [Objective-C檔案]  ，然後選取 [下一步]  。 將檔案命名 **Constants.m**。

    ![新增 .m 檔案](media/notification-hubs-ios-get-started/new-file-objc.png)

9. 開啟 **Constants.m** 檔案，並以下列程式碼取代其內容。 以您先前從入口網站取得的中樞名稱和 **DefaultListenSharedAccessSignature**，分別取代字串常值預留位置 `NotificationHubConnectionString` 和 `NotificationHubConnectionString`：

    ```objc
    #import <Foundation/Foundation.h>
    #import "Constants.h"

    NSString* const NHInfoConnectionString = @"NotificationHubConnectionString";
    NSString* const NHInfoHubName = @"NotificationHubName";
    NSString* const NHUserDefaultTags = @"notification_tags";
    ```

10. 開啟專案的 **AppDelegate.h** 檔案，並以下列程式碼取代其內容：

    ```objc
    #import <UIKit/UIKit.h>
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h> 

    @interface AppDelegate : UIResponder <UIApplicationDelegate,UNUserNotificationCenterDelegate>

    @property (strong, nonatomic) UIWindow *window;

    - (void)handleRegister;
    - (void)handleUnregister;

    @end

    ```

11. 在專案的 **AppDelegate.m** 檔案中，新增下列 `import` 陳述式：

    ```objc
    #import "Constants.h"
    #import "NotificationDetailViewController.h"
    ```

12. 此外在 **AppDelegate.m** 檔案中，根據您的 iOS 版本在 `didFinishLaunchingWithOptions` 方法內新增以下這行程式碼。 此程式碼會向 APN 註冊裝置控制代碼：

    ```objc
    [[UNUserNotificationCenter currentNotificationCenter] setDelegate:self];
    ```

13. 在相同的 **AppDelegate.m** 檔案中，使用下列程式碼取代 `didFinishLaunchingWithOptions` 之後的所有程式碼：

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

    此程式碼會使用您在 **Constants.h** 中指定的連接資訊連接到通知中心。 然後，它可提供裝置權杖給通知中樞，讓通知中樞能夠傳送通知。

### <a name="notificationdetailviewcontroller"></a>NotificationDetailViewController

1. 類似先前的指示，新增另一個名為 **NotificationDetailViewController.h**的標頭檔。 將新標頭檔的內容取代為下列程式碼：

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

2. 新增實作檔 **NotificationDetailViewController.m**。 將檔案的內容取代為下列程式碼，其可實作 `UIViewController` 方法：

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

1. 在專案的 **ViewController.h** 檔案中，新增下列 `import` 陳述式：

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h>
    ```

2. 此外，在 **ViewController.h**中的 `@interface` 宣告之後，新增下列屬性宣告：

    ```objc
    @property (strong, nonatomic) IBOutlet UITextField *tagsTextField;
    @property (strong, nonatomic) IBOutlet UIButton *registerButton;
    @property (strong, nonatomic) IBOutlet UIButton *unregisterButton;
    ```

3. 在專案的 **ViewController.m** 實作檔中，將檔案的內容取代為下列程式碼：

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

您可以在 [Azure 入口網站] 中，使用 [測試傳送]  選項測試應用程式能否接收通知。 它會將測試推播通知傳送至您的裝置。

![Azure 入口網站 - 測試傳送][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## <a name="verify-that-your-app-receives-push-notifications"></a>確認您的應用程式可接收推播通知

若要在 iOS 上測試推播通知，您必須將應用程式部署至實體 iOS 裝置。 您無法利用 iOS 模擬器傳送 Apple 推播通知。

1. 執行應用程式並確認註冊成功，然後按下 [確定]  。

    ![iOS 應用程式推播通知註冊測試][33]

2. 接下來，您應依照上一節的說明，從 [Azure 入口網站]傳送測試推播通知。

3. 推播通知會從特定通知中樞傳送至所有已註冊要接收通知的所有裝置。

    ![iOS 應用程式推播通知接收測試][35]

## <a name="next-steps"></a>後續步驟

在此簡單範例中，您會將推播通知廣播到您已註冊的所有 iOS 裝置。 若要了解如何將通知推送至特定 iOS 裝置，請繼續進行下列教學課程：

> [!div class="nextstepaction"]
>[將通知推送至特定裝置](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png
[12]: ./media/notification-hubs-ios-get-started/notification-hubs-enable-push.png
[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png

<!-- URLs. -->
[Windows Azure 傳訊架構]: https://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: https://go.microsoft.com/fwlink/p/?LinkId=272456
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure Notification Hubs Notify Users for iOS with .NET backend]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
[Local and Push Notification Programming Guide]: https://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Azure 入口網站]: https://portal.azure.com
