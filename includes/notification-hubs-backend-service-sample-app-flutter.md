---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 2f8cd5e554980225850780cc474106c3dc69288a
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156277"
---
### <a name="create-the-flutter-solution"></a>建立 Flutter 解決方案

1. 開啟新的 **Visual Studio Code** 執行個體。

1. 開啟 [命令選擇區] (**Shift** + **Command** + **P**)。

1. 選取 [Flutter：新增專案] 命令，然後按 **Enter** 鍵。

1. 輸入 *push_demo* 作為 [專案名稱]，然後選取 [專案位置]。

1. 當系統提示您進行時，選擇 [取得套件]。

1. **Control** + 按一下 **kotlin** 資料夾 (在 **app** > **src** > **main** 底下)，然後選擇 [在 Finder 中顯示]。 然後，將子資料夾 (在 **kotlin** 資料夾底下) 分別重新命名為 ```com```、```<your_organization>``` 和 ```pushdemo```。

    > [!NOTE]
    > 使用 **Visual Studio Code** 範本時，這些資料夾會預設為 **com**、**example**、 **<project_name>** 。 假設使用 **mobcat** 作為 [組織]，則資料夾結構應該會顯示為：
    >
    > - kotlin
    >     - com
    >         - mobcat
    >             - pushdemo

1. 回到 **Visual Studio Code**，將 **android** > **app** > **build.gradle** 中的 **applicationId** 值更新為 `com.<your_organization>.pushdemo`。

    > [!NOTE]
    > 您應該在 *<your_organization>* 預留位置中使用自己的組織名稱。 例如，使用 **mobcat** 作為組織時，將會產生值為 *com.mobcat.pushdemo* 的 [套件名稱]。

1. 分別更新 **src** > **debug**、**src** > **main** 和 **src** > **profile** 底下的 **AndroidManifest.xml** 檔案中所含的**套件**屬性。 確定這些值符合您在上一個步驟中使用的 **applicationId**。

    ```xml
    <manifest
        xmlns:android="http://schemas.android.com/apk/res/android"
        package="com.<your_organization>.pushdemo>">
        ...
    </manifest>
    ```

1. 將 **src** > **main** 底下的 **AndroidManifest.xml** 檔案中所含的 ```android:label``` 屬性更新為 *PushDemo*。 然後，將 ```android:allowBackup``` 屬性直接新增到 ```android:label``` 底下，並將其值設定為 **false**。

    ```xml
    <application
        android:name="io.flutter.app.FlutterApplication"
        android:label="PushDemo"
        android:allowBackup="false"
        android:icon="@mipmap/ic_launcher">
        ...
    </application>
    ```

1. 開啟應用程式層級的 **build.gradle** 檔案 (**android** > **app** > **build.gradle**)，然後更新 *compileSdkVersion* (從 **android** 區段) 以使用 API **29**。 然後，將 *minSdkVersion* 和 *targetSdkVersion* 值 (從 **defaultConfig** 區段) 分別更新為 **26** 和 **29**。

    > [!NOTE]
    > 本教學課程僅支援執行 **API 層級 26 和更新版本**的裝置，但您可以將其延伸而能夠支援執行較舊版本的裝置。

1. **Control** + **按一下** **ios** 資料夾，然後選擇 [在 Xcode 中開啟]。

1. 在 **Xcode** 中按一下 [執行器] (頂端的 **xcodeproj**，而不是資料夾)。 然後，選取 [執行器] 目標，然後選取 [一般] 索引標籤。在選取了 [所有] 組建組態的情況下，將 [套件組合識別碼] 更新為 `com.<your_organization>.PushDemo`。

    > [!NOTE]
    > 您應該在 *<your_organization>* 預留位置中使用自己的組織名稱。 例如，使用 **mobcat** 作為組織時，將會產生值為 *com.mobcat.PushDemo* 的 [套件組合識別碼]。

1. 按一下 [Info.plist]，然後將 [套件組合名稱] 值更新為 [PushDemo]

1. 關閉 **Xcode** 並返回 **Visual Studio Code**。

1. 回到 **Visual Studio Code**、開啟 **pubspec.yaml**，新增 [http](https://pub.dev/packages/http) 和 [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage) **Dart 套件**作為相依性。 然後儲存檔案，並在出現提示時按一下 [取得套件]。

    ```yaml
    dependencies:
      flutter:
        sdk: flutter

      http: ^0.12.1
      flutter_secure_storage: ^3.3.3
    ```

1. 在 [終端機] 中，將目錄變更為 **ios** 資料夾 (針對您的 Flutter 專案)。 然後，執行 [**pod install**](https://guides.cocoapods.org/using/getting-started.html#installation) 命令，以安裝新的 Pod ([flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage) 套件所需項目)。

1. **Control** + **按一下** [lib] 資料夾，然後從功能表中選擇 [新增檔案] 並使用 *main_page.dart* 作為檔案名稱。 然後，新增下列程式碼。

    ```kotlin
    import 'package:flutter/material.dart';

    class MainPage extends StatefulWidget {
      @override
      _MainPageState createState() => _MainPageState();
    }

    class _MainPageState extends State<MainPage> {
      @override
      Widget build(BuildContext context) {
        return Scaffold(
          body: SafeArea(
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.stretch,
              children: <Widget>[],
            )
          )
        );
      }
    }
    ```

1. 在 **main.dart** 中，將樣板化程式碼取代為下列程式碼。

    ```dart
    import 'package:flutter/material.dart';
    import 'package:push_demo/main_page.dart';

    final navigatorKey = GlobalKey<NavigatorState>();

    void main() => runApp(MaterialApp(home: MainPage(), navigatorKey: navigatorKey));
    ```

1. 在 [終端機] 中，於每個目標平台上建置和執行應用程式，以測試樣板化應用程式在您裝置上的執行情形。 確定支援的裝置皆已連線。

    ```bash
    flutter run
    ```

### <a name="implement-the-cross-platform-components"></a>實作跨平台元件

1. **Control** + **按一下** [lib] 資料夾，然後從功能表中選擇 [新增檔案] 並使用 *models* 作為 [資料夾名稱]。

1. **Control** + **按一下** [models] 資料夾，然後從功能表中選擇 [新增檔案] 並使用 *device_installation.dart* 作為檔案名稱。 然後，新增下列程式碼。

    ```dart
    class DeviceInstallation {
        final String deviceId;
        final String platform;
        final String token;
        final List<String> tags;

        DeviceInstallation(this.deviceId, this.platform, this.token, this.tags);

        DeviceInstallation.fromJson(Map<String, dynamic> json)
          : deviceId = json['installationId'],
            platform = json['platform'],
            token = json['pushChannel'],
            tags = json['tags'];

        Map<String, dynamic> toJson() =>
        {
          'installationId': deviceId,
          'platform': platform,
          'pushChannel': token,
          'tags': tags,
        };
    }
    ```

1. 將稱為 *push_demo_action.dart* 的新檔案新增至 **models** 資料夾，以定義此範例要支援的動作列舉。

    ```dart
    enum PushDemoAction {
      actionA,
      actionB,
    }
    ```

1. 將新資料夾新增至名為 **services** 的專案，然後使用下列實作，將新檔案新增至名為 *device_installation_service.dart* 的資料夾。

    ```dart
    import 'package:flutter/services.dart';

    class DeviceInstallationService {
      static const deviceInstallation = const MethodChannel('com.<your_organization>.pushdemo/deviceinstallation');
      static const String getDeviceIdChannelMethod = "getDeviceId";
      static const String getDeviceTokenChannelMethod = "getDeviceToken";
      static const String getDevicePlatformChannelMethod = "getDevicePlatform";

      Future<String> getDeviceId() {
        return deviceInstallation.invokeMethod(getDeviceIdChannelMethod);
      }

      Future<String> getDeviceToken() {
        return deviceInstallation.invokeMethod(getDeviceTokenChannelMethod);
      }

      Future<String> getDevicePlatform() {
        return deviceInstallation.invokeMethod(getDevicePlatformChannelMethod);
      }
    }
    ```

    > [!NOTE]
    > 您應該在 *<your_organization>* 預留位置中使用自己的組織名稱。 例如，使用 **mobcat** 作為組織時，會導致 [MethodChannel](https://api.flutter.dev/flutter/services/MethodChannel-class.html) 名稱為 *com.mobcat.pushdemo/deviceinstallation*。
    >
    > 這個類別會封裝使用基礎原生平台，以取得必要的裝置安裝詳細資料。 [MethodChannel](https://api.flutter.dev/flutter/services/MethodChannel-class.html) 可加速與基礎原生平台的雙向非同步通訊。 此通道的平台特定對應項目將會在稍後的步驟中建立。

1. 使用下列實作，將另一個檔案新增至名為 *notification_action_service.dart* 的資料夾。

    ```dart
    import 'package:flutter/services.dart';
    import 'dart:async';
    import 'package:push_demo/models/push_demo_action.dart';

    class NotificationActionService {
      static const notificationAction =
          const MethodChannel('com.<your_organization>.pushdemo/notificationaction');
      static const String triggerActionChannelMethod = "triggerAction";
      static const String getLaunchActionChannelMethod = "getLaunchAction";

      final actionMappings = {
        'action_a' : PushDemoAction.actionA,
        'action_b' : PushDemoAction.actionB
      };

      final actionTriggeredController = StreamController.broadcast();

      NotificationActionService() {
        notificationAction
            .setMethodCallHandler(handleNotificationActionCall);
      }

      Stream get actionTriggered => actionTriggeredController.stream;

      Future<void> triggerAction({action: String}) async {

        if (!actionMappings.containsKey(action)) {
          return;
        }

        actionTriggeredController.add(actionMappings[action]);
      }

      Future<void> checkLaunchAction() async {
        final launchAction = await notificationAction.invokeMethod(getLaunchActionChannelMethod) as String;

        if (launchAction != null) {
          triggerAction(action: launchAction);
        }
      }

      Future<void> handleNotificationActionCall(MethodCall call) async {
        switch (call.method) {
          case triggerActionChannelMethod:
            return triggerAction(action: call.arguments as String);
          default:
            throw MissingPluginException();
            break;
        }
      }
    }
    ```

    > [!NOTE]
    > 這會作為集中處理通知動作的簡單機制，以便您可以使用強型別的列舉以跨平台方式處理通知動作。 當通知承載中指定了一個動作時，服務可讓基礎原生平台觸發該動作。 其也可以讓通用程式碼回溯檢查當 Flutter 準備好要處理動作時，是否已在啟動應用程式期間指定該動作。 例如，從通知中心點選通知以啟動應用程式時。

1. 使用下列實作，將新檔案新增至名為 *notification_registration_service.dart* 的 **services** 資料夾。

    ```dart
    import 'dart:convert';
    import 'package:flutter/services.dart';
    import 'package:http/http.dart' as http;
    import 'package:push_demo/services/device_installation_service.dart';
    import 'package:push_demo/models/device_installation.dart';
    import 'package:flutter_secure_storage/flutter_secure_storage.dart';

    class NotificationRegistrationService {
      static const notificationRegistration =
          const MethodChannel('com.<your_organization>.pushdemo/notificationregistration');

      static const String refreshRegistrationChannelMethod = "refreshRegistration";
      static const String installationsEndpoint = "api/notifications/installations";
      static const String cachedDeviceTokenKey = "cached_device_token";
      static const String cachedTagsKey = "cached_tags";

      final deviceInstallationService = DeviceInstallationService();
      final secureStorage = FlutterSecureStorage();

      String baseApiUrl;
      String apikey;

      NotificationRegistrationService(this.baseApiUrl, this.apikey) {
        notificationRegistration
            .setMethodCallHandler(handleNotificationRegistrationCall);
      }

      String get installationsUrl => "$baseApiUrl$installationsEndpoint";

      Future<void> deregisterDevice() async {
        final cachedToken = await secureStorage.read(key: cachedDeviceTokenKey);
        final serializedTags = await secureStorage.read(key: cachedTagsKey);

        if (cachedToken == null || serializedTags == null) {
          return;
        }

        var deviceId = await deviceInstallationService.getDeviceId();

        if (deviceId.isEmpty) {
          throw "Unable to resolve an ID for the device.";
        }

        var response = await http
            .delete("$installationsUrl/$deviceId", headers: {"apikey": apikey});

        if (response.statusCode != 200) {
          throw "Deregister request failed: ${response.reasonPhrase}";
        }

        await secureStorage.delete(key: cachedDeviceTokenKey);
        await secureStorage.delete(key: cachedTagsKey);
      }

      Future<void> registerDevice(List<String> tags) async {
        try {
          final deviceId = await deviceInstallationService.getDeviceId();
          final platform = await deviceInstallationService.getDevicePlatform();
          final token = await deviceInstallationService.getDeviceToken();

          final deviceInstallation =
              DeviceInstallation(deviceId, platform, token, tags);

          final response = await http.put(installationsUrl,
              body: jsonEncode(deviceInstallation),
              headers: {"apikey": apikey, "Content-Type": "application/json"});

          if (response.statusCode != 200) {
            throw "Register request failed: ${response.reasonPhrase}";
          }

          final serializedTags = jsonEncode(tags);

          await secureStorage.write(key: cachedDeviceTokenKey, value: token);
          await secureStorage.write(key: cachedTagsKey, value: serializedTags);
        } on PlatformException catch (e) {
          throw e.message;
        } catch (e) {
          throw "Unable to register device: $e";
        }
      }

      Future<void> refreshRegistration() async {
        final currentToken = await deviceInstallationService.getDeviceToken();
        final cachedToken = await secureStorage.read(key: cachedDeviceTokenKey);
        final serializedTags = await secureStorage.read(key: cachedTagsKey);

        if (currentToken == null ||
            cachedToken == null ||
            serializedTags == null ||
            currentToken == cachedToken) {
          return;
        }

        final tags = jsonDecode(serializedTags);

        return registerDevice(tags);
      }

      Future<void> handleNotificationRegistrationCall(MethodCall call) async {
        switch (call.method) {
          case refreshRegistrationChannelMethod:
            return refreshRegistration();
          default:
            throw MissingPluginException();
            break;
        }
      }
    }
    ```

    > [!NOTE]
    > 此類別會封裝 **DeviceInstallationService** 的使用，以及封裝為了執行必要的註冊、取消註冊和重新整理註冊動作而對後端服務提出的要求。 只有在您選擇完成[使用 API 金鑰來驗證用戶端](#authenticate-clients-using-an-api-key-optional)一節時，才需要 **apiKey** 引數。

1. 使用下列實作，將新檔案新增至名為 *config.dart* 的 **lib** 資料夾。

    ```dart
    class Config {
      static String apiKey = "API_KEY";
      static String backendServiceEndpoint = "BACKEND_SERVICE_ENDPOINT";
    }
    ```

    > [!NOTE]
    > 這會作為定義應用程式秘密的簡單方式。 以您自己的值取代預留位置值。 您應該在建立後端服務時，記下這些值。 **API 應用程式 URL** 應為 `https://<api_app_name>.azurewebsites.net/`。 只有在您選擇完成[使用 API 金鑰來驗證用戶端](#authenticate-clients-using-an-api-key-optional)一節時，才需要 **apiKey** 成員。
    >
    > 請務必將此資料新增至您的 gitignore 檔案，以避免將這些秘密認可至原始檔控制。

### <a name="implement-the-cross-platform-ui"></a>實作跨平台 UI

1. 在 **main_page.dart** 中，以下列各項取代 **build** 函式。

    ```dart
    @override
    Widget build(BuildContext context) {
    return Scaffold(
        body: Padding(
          padding: const EdgeInsets.symmetric(horizontal: 20.0, vertical: 40.0),
          child: Column(
            mainAxisAlignment: MainAxisAlignment.end,
            crossAxisAlignment: CrossAxisAlignment.stretch,
            children: <Widget>[
              FlatButton(
                child: Text("Register"),
                onPressed: registerButtonClicked,
              ),
              FlatButton(
                child: Text("Deregister"),
                onPressed: deregisterButtonClicked,
              ),
            ],
          ),
        ),
      );
    }
    ```

1. 將必要的匯入新增至 **main_page.dart** 檔案的頂端。

    ```dart
    import 'package:push_demo/services/notification_registration_service.dart';
    import 'config.dart';
    ```

1. 將欄位新增至 **_MainPageState** 類別，以儲存 **NotificationRegistrationService** 的參考。

    ```dart
    final notificationRegistrationService = NotificationRegistrationService(Config.backendServiceEndpoint, Config.apiKey);
    ```

1. 在 **_MainPageState** 類別中，為 [註冊] 和 [取消註冊] 按鈕的 **onPressed** 事件實作事件處理常式。 呼叫對應的 [註冊]/[取消註冊] 方法，然後顯示警示以指出結果。

    ```dart
    void registerButtonClicked() async {
        try {
          await notificationRegistrationService.registerDevice(List<String>());
          await showAlert(message: "Device registered");
        }
        catch (e) {
          await showAlert(message: e);
        }
      }

      void deregisterButtonClicked() async {
        try {
          await notificationRegistrationService.deregisterDevice();
          await showAlert(message: "Device deregistered");
        }
        catch (e) {
          await showAlert(message: e);
        }
      }

      Future<void> showAlert({ message: String }) async {
        return showDialog<void>(
          context: context,
          barrierDismissible: false,
          builder: (BuildContext context) {
            return AlertDialog(
              title: Text('PushDemo'),
              content: SingleChildScrollView(
                child: ListBody(
                  children: <Widget>[
                    Text(message),
                  ],
                ),
              ),
              actions: <Widget>[
                FlatButton(
                  child: Text('OK'),
                  onPressed: () {
                    Navigator.of(context).pop();
                  },
                ),
              ],
            );
          },
        );
      }
    ```

1. 現在，在 **main.dart** 中確定檔案頂端有下列匯入。

    ```dart
    import 'package:flutter/material.dart';
    import 'package:push_demo/models/push_demo_action.dart';
    import 'package:push_demo/services/notification_action_service.dart';
    import 'package:push_demo/main_page.dart';
    ```

1. 宣告變數以儲存 **NotificationActionService** 執行個體的參考，並將其初始化。

    ```dart
    final notificationActionService = NotificationActionService();
    ```

1. 新增函式以在觸發動作時處理警示的顯示。

    ```dart
    void notificationActionTriggered(PushDemoAction action) {
      showActionAlert(message: "${action.toString().split(".")[1]} action received");
    }

    Future<void> showActionAlert({ message: String }) async {
      return showDialog<void>(
        context: navigatorKey.currentState.overlay.context,
        barrierDismissible: false,
        builder: (BuildContext context) {
          return AlertDialog(
            title: Text('PushDemo'),
            content: SingleChildScrollView(
              child: ListBody(
                children: <Widget>[
                  Text(message),
                ],
              ),
            ),
            actions: <Widget>[
              FlatButton(
                child: Text('OK'),
                onPressed: () {
                  Navigator.of(context).pop();
                },
              ),
            ],
          );
        },
      );
    }
    ```

1. 更新 **main** 函式以觀察 **NotificationActionService** **actionTriggered** 串流，並檢查在應用程式啟動期間所擷取的任何動作。

    ```dart
    void main() async {
      runApp(MaterialApp(home: MainPage(), navigatorKey: navigatorKey,));
      notificationActionService.actionTriggered.listen((event) { notificationActionTriggered(event as PushDemoAction); });
      await notificationActionService.checkLaunchAction();
    }
    ```

    > [!NOTE]
    > 這只是為了示範推播通知動作的接收和傳播。 一般而言，這些作業會以無訊息方式處理，例如瀏覽至特定的檢視或重新整理某些資料，而不是在此情況下顯示警示。
