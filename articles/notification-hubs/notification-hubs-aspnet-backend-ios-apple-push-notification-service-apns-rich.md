---
title: Azure 通知中心豐富內容推播
description: 了解如何從 Azure 將各種推播通知傳送至 iOS 應用程式。 程式碼範例是以 Objective-C 及 C# 撰寫。
documentationcenter: ios
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/17/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 33626b7aee615d07ef88dd9fbca46e6512e2cafc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90090358"
---
# <a name="azure-notification-hubs-rich-push"></a>Azure 通知中心豐富內容推播

## <a name="overview"></a>概觀

為了與使用者進行即時豐富內容交流，應用程式可能會想要推播純文字以外的內容。 這些通知會提升使用者互動，並呈現 Url、音效、影像/贈券等內容。 本教學課程是以 [通知使用者](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) 教學課程為基礎，並示範如何傳送包含承載的推播通知 (例如，) 的映射。

本教學課程與 iOS 7 和8相容。

  ![三個螢幕擷取畫面：具有 [傳送推播] 按鈕的應用程式畫面、裝置上的 [開始] 畫面，以及具有 [上一頁] 按鈕的 Windows 標誌。][IOS1]

概括而言：

1. 應用程式後端：
   * 會在此情況下儲存豐富的承載 (在此案例中，映射) 在後端資料庫/本機儲存體中。
   * 將此豐富通知的識別碼傳送至裝置。
2. 裝置上的應用程式：
   * 向後端要求具有所收到識別碼的豐富承載。
   * 當資料抓取完成時，會在裝置上傳送使用者通知，並在使用者點擊以深入瞭解時立即顯示承載。

## <a name="webapi-project"></a>WebAPI 專案

1. 在 Visual Studio 中，開啟您在 [通知使用者] **** 教學課程中所建立的 [AppBackend](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) 專案。
2. 取得您想要用來通知使用者的影像，並將它放在專案目錄的 **img** 資料夾中。
3. 按一下 [方案總管] 中的 [顯示所有檔案]****，然後以滑鼠右鍵按一下資料夾以 [加入至專案]****。
4. 選取映射之後，請將其 [**屬性**] 視窗中的 [**組建] 動作**變更為 [**內嵌資源**]。

    ![方案總管的螢幕擷取畫面。 選取影像檔案，並在其 [屬性] 窗格中，將內嵌資源列為 [建立] 動作。][IOS2]
5. 在中 `Notifications.cs` ，加入下列 `using` 語句：

    ```csharp
    using System.Reflection;
    ```

6. 使用下列程式碼取代 `Notifications` 類別。 請務必將預留位置取代為您的通知中樞認證和影像檔案名稱：

    ```csharp
    public class Notification {
        public int Id { get; set; }
        // Initial notification message to display to users
        public string Message { get; set; }
        // Type of rich payload (developer-defined)
        public string RichType { get; set; }
        public string Payload { get; set; }
        public bool Read { get; set; }
    }

    public class Notifications {
        public static Notifications Instance = new Notifications();

        private List<Notification> notifications = new List<Notification>();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            // Placeholders: replace with the connection string (with full access) for your notification hub and the hub name from the Azure Classics Portal
            Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",  "{hub name}");
        }

        public Notification CreateNotification(string message, string richType, string payload) {
            var notification = new Notification() {
                Id = notifications.Count,
                Message = message,
                RichType = richType,
                Payload = payload,
                Read = false
            };

            notifications.Add(notification);

            return notification;
        }

        public Stream ReadImage(int id) {
            var assembly = Assembly.GetExecutingAssembly();
            // Placeholder: image file name (for example, logo.png).
            return assembly.GetManifestResourceStream("AppBackend.img.{logo.png}");
        }
    }
    ```

7. 在中 `NotificationsController.cs` ， `NotificationsController` 使用下列程式碼重新定義。 這會將初始的無訊息豐富通知識別碼傳送至裝置，並允許用戶端抓取映射：

    ```csharp
    // Return http response with image binary
    public HttpResponseMessage Get(int id) {
        var stream = Notifications.Instance.ReadImage(id);

        var result = new HttpResponseMessage(HttpStatusCode.OK);
        result.Content = new StreamContent(stream);
        // Switch in your image extension for "png"
        result.Content.Headers.ContentType = new System.Net.Http.Headers.MediaTypeHeaderValue("image/{png}");

        return result;
    }

    // Create rich notification and send initial silent notification (containing id) to client
    public async Task<HttpResponseMessage> Post() {
        // Replace the placeholder with image file name
        var richNotificationInTheBackend = Notifications.Instance.CreateNotification("Check this image out!", "img",  "{logo.png}");

        var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

        // Silent notification with content available
        var aboutUser = "{\"aps\": {\"content-available\": 1, \"sound\":\"\"}, \"richId\": \"" + richNotificationInTheBackend.Id.ToString() + "\",  \"richMessage\": \"" + richNotificationInTheBackend.Message + "\", \"richType\": \"" + richNotificationInTheBackend.RichType + "\"}";

        // Send notification to apns
        await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(aboutUser, usernameTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

8. 現在，將此應用程式重新部署至 Azure 網站，使其可從所有裝置存取。 以滑鼠右鍵按一下 **AppBackend** 專案，然後選取 [發佈]****。
9. 選取 **Azure 網站** 作為發佈目標。 使用您的 Azure 帳戶登入，並選取現有或新的網站，並在 [**連接**] 索引標籤中記下 [**目的地 URL** ] 屬性。我們稍後會在本教學課程中將此 URL 稱為您的*後端端點*。 選取 [發佈]  。

## <a name="modify-the-ios-project"></a>修改 iOS 專案

現在您已將應用程式後端修改為只傳送通知的 *識別碼* ，請變更您的 iOS 應用程式以處理該識別碼，並從後端取出豐富的訊息：

1. 開啟 iOS 專案，然後移至 [目標] **** 區段中的主要應用程式目標，以啟用遠端通知。
2. 選取 [ **功能**]、[啟用 **背景模式**]，然後核取 [ **遠端通知** ] 核取方塊。

    ![顯示 [功能] 畫面的 iOS 專案螢幕擷取畫面。 背景模式會開啟，並已選取 [遠端通知] 核取方塊。][IOS3]
3. 開啟 `Main.storyboard` ，並確定您在本教學課程中有一個 View controller (稱為「首頁視圖控制器」) 從 [通知使用者](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) 教學課程中進行。
4. 將 **流覽控制器** 新增至您的分鏡腳本，並將主視圖控制器拖曳至流覽的 **根視圖** 。 請確定已為流覽控制器選取 [屬性] 偵測器中的 [ **為初始視圖控制器** ]。
5. 將 **View Controller** 新增至腳本，並加入 **影像視圖**。 這是使用者選擇要深入了解，在按一下通知之後會看到的頁面。 您的腳本應如下所示：

    ![分鏡腳本的螢幕擷取畫面。 顯示三個應用程式畫面：導覽視圖、首頁視圖，以及影像視圖。][IOS4]
6. 按一下分鏡腳本中的 **Home View 控制器** ，並確定它已 **HomeViewController** 為其 **自訂類別** 和分鏡腳本 **識別碼** （位於身分識別偵測器底下）。
7. 針對「影像視圖控制器」進行相同的動作，例如 **>imageviewcontroller**。
8. 然後，建立名為 **>imageviewcontroller** 的新視圖控制器類別，以處理您剛才建立的 UI。
9. 在 **>imageviewcontroller**中，將下列程式碼加入至控制器的介面宣告。 請務必按住 Ctrl 再從腳本影像檢視拖曳到這些屬性，以連結兩者：

    ```objc
    @property (weak, nonatomic) IBOutlet UIImageView *myImage;
    @property (strong) UIImage* imagePayload;
    ```

10. 在 `imageViewController.m` 中，於 `viewDidload` 結尾處新增下列項目：

    ```objc
    // Display the UI Image in UI Image View
    [self.myImage setImage:self.imagePayload];
    ```

11. 在 `AppDelegate.m` 中，匯入您建立的影像控制器：

    ```objc
    #import "imageViewController.h"
    ```

12. 使用下列宣告新增介面區段：

    ```objc
    @interface AppDelegate ()

    @property UIImage* imagePayload;
    @property NSDictionary* userInfo;
    @property BOOL iOS8;

    // Obtain content from backend with notification id
    - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;

    // Redirect to Image View Controller after notification interaction
    - (void)redirectToImageViewWithImage: (UIImage *)img;

    @end
    ```

13. 在 `AppDelegate` 中，請確定您的應用程式已註冊 `application: didFinishLaunchingWithOptions` 中的無訊息通知：

    ```objc
    // Software version
    self.iOS8 = [[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)];

    // Register for remote notifications for iOS8 and previous versions
    if (self.iOS8) {
        NSLog(@"This device is running with iOS8.");

        // Action
        UIMutableUserNotificationAction *richPushAction = [[UIMutableUserNotificationAction alloc] init];
        richPushAction.identifier = @"richPushMore";
        richPushAction.activationMode = UIUserNotificationActivationModeForeground;
        richPushAction.authenticationRequired = NO;
        richPushAction.title = @"More";

        // Notification category
        UIMutableUserNotificationCategory* richPushCategory = [[UIMutableUserNotificationCategory alloc] init];
        richPushCategory.identifier = @"richPush";
        [richPushCategory setActions:@[richPushAction] forContext:UIUserNotificationActionContextDefault];

        // Notification categories
        NSSet* richPushCategories = [NSSet setWithObjects:richPushCategory, nil];

        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                UIUserNotificationTypeAlert |
                                                UIUserNotificationTypeBadge
                                                                                    categories:richPushCategories];

        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];

    }
    else {
        // Previous iOS versions
        NSLog(@"This device is running with iOS7 or earlier versions.");

        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    }

    return YES;
    ```

14. 取代下列的執行， `application:didRegisterForRemoteNotificationsWithDeviceToken` 以將分鏡腳本 UI 變更納入考慮：

    ```objc
    // Access navigation controller which is at the root of window
    UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
    // Get home view controller from stack on navigation controller
    homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
    hvc.deviceToken = deviceToken;
    ```

15. 接著，將下列方法加入至 `AppDelegate.m`，以擷取您的端點影像，並在擷取完成時傳送本機通知。 請務必以後端端點取代預留位置 `{backend endpoint}` ：

    ```objc
    NSString *const GetNotificationEndpoint = @"{backend endpoint}/api/notifications";

    // Helper: retrieve notification content from backend with rich notification id
    - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion {
        UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
        homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
        NSString* authenticationHeader = hvc.registerClient.authenticationHeader;
        // Check if authenticated
        if (!authenticationHeader) return;

        NSURLSession* session = [NSURLSession
                                sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                delegate:nil
                                delegateQueue:nil];

        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, richId]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"GET"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {

            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200) {
                // From NSData to UIImage
                self.imagePayload = [UIImage imageWithData:data];

                completion(nil);
            }
            else {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(error);
                else {
                    completion([NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }

    // Handle silent push notifications when id is sent from backend
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler {
        self.userInfo = userInfo;
        int richId = [[self.userInfo objectForKey:@"richId"] intValue];
        NSString* richType = [self.userInfo objectForKey:@"richType"];

        // Retrieve image data
        if ([richType isEqualToString:@"img"]) {  
            [self retrieveRichImageWithId:richId completion:^(NSError* error) {
                if (!error){
                    // Send local notification
                    UILocalNotification* localNotification = [[UILocalNotification alloc] init];

                    // "5" is arbitrary here to give you enough time to quit out of the app and receive push notifications
                    localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:5];
                    localNotification.userInfo = self.userInfo;
                    localNotification.alertBody = [self.userInfo objectForKey:@"richMessage"];
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];

                    // iOS8 categories
                    if (self.iOS8) {
                        localNotification.category = @"richPush";
                    }

                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                    handler(UIBackgroundFetchResultNewData);
                }
                else{
                    handler(UIBackgroundFetchResultFailed);
                }
            }];
        }
        // Add "else if" here to handle more types of rich content such as url, sound files, etc.
    }
    ```

16. 使用下列方法，在中開啟影像視圖控制器來處理先前的本機通知 `AppDelegate.m` ：

    ```objc
    // Helper: redirect users to image view controller
    - (void)redirectToImageViewWithImage: (UIImage *)img {
        UINavigationController *navigationController = (UINavigationController*) self.window.rootViewController;
        UIStoryboard *mainStoryboard = [UIStoryboard storyboardWithName:@"Main" bundle: nil];
        imageViewController *imgViewController = [mainStoryboard instantiateViewControllerWithIdentifier: @"imageViewController"];
        // Pass data/image to image view controller
        imgViewController.imagePayload = img;

        // Redirect
        [navigationController pushViewController:imgViewController animated:YES];
    }

    // Handle local notification sent above in didReceiveRemoteNotification
    - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification {
        if (application.applicationState == UIApplicationStateActive) {
            // Show in-app alert with an extra "more" button
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:notification.alertBody delegate:self cancelButtonTitle:@"OK" otherButtonTitles:@"More", nil];
            [alert show];
        }
        // App becomes active from user's tap on notification
        else {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
    }

    // Handle buttons in in-app alerts and redirect with data/image
    - (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex {
        // Handle "more" button
        if (buttonIndex == 1)
        {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
        // Add "else if" here to handle more buttons
    }

    // Handle notification setting actions in iOS8
    - (void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forLocalNotification:(UILocalNotification *)notification completionHandler:(void (^)())completionHandler {
        // Handle richPush related buttons
        if ([identifier isEqualToString:@"richPushMore"]) {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
        completionHandler();
    }
    ```

## <a name="run-the-application"></a>執行應用程式

1. 在 XCode 中，在實體 iOS 裝置上執行應用程式 (推播通知無法在模擬器中運作)。
2. 在 iOS 應用程式 UI 中，輸入與驗證相同值的使用者名稱和密碼，然後按一下 [登入] ****。
3. 按一下 [傳送推播] **** ，您應該會看到一則應用程式內部警示。 如果您按一下 [詳細說明] ****，將會顯示您選擇要包含在應用程式後端的影像。
4. 您也可以按一下 [傳送推播] **** ，並立即按下裝置的主畫面按鈕。 在幾分鐘的時間內，您就會收到推播通知。 如果您點選該通知或按一下 [詳細說明]，則會顯示您的應用程式以及豐富的影像內容。

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
