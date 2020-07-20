---
author: alexeystrakh
ms.author: alstrakh
ms.date: 06/11/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: c8271cc4de558ec5c97f41d6a070f71a9fc49cd2
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060412"
---
### <a name="create-the-react-native-solution"></a>建立 React Native 解決方案

1. 在 `Terminal` 中，更新您的環境工具，這些是您使用下列命令來處理 React Native 時所需的工具：

    ```bash
    # install node
    brew install node
    # or update
    brew update node
    # install watchman
    brew install watchman
    # or update
    brew upgrade watchman
    # install cocoapods
    sudo gem install cocoapods
    ```

1. 在 `Terminal` 中，如果您已安裝 `React Native` CLI，請執行下列命令將其解除安裝。 使用 `npx` 自動存取可用的最新 React Native CLI 版本：

    ```bash
    npm uninstall -g react-native-cli
    ```

    > [!NOTE]
    > React Native 具有內建的命令列介面。 我們建議您在執行階段使用 node.js 隨附的 `npx` 來存取目前的 CLI 版本，而不是全域安裝和管理特定的 CLI 版本。 搭配 `npx react-native <command>`，將會在命令執行時下載並執行目前穩定的 CLI 版本。

1. 瀏覽至您要建立新應用程式的專案資料夾。 藉由指定 `--template` 參數來使用 Typescript 型範本：

    ```bash
    # init new project with npx
    npx react-native init PushDemo --template react-native-template-typescript
    ```

1. 執行 Metro 伺服器，其會建立 JavaScript 套組並監視任何程式碼更新，以即時重新整理這些套組：

    ```bash
    cd PushDemo
    npx react-native start
    ```

1. 執行 iOS 應用程式來驗證設定。 執行下列命令之前，請確定您已啟動 iOS 模擬器或已連線 iOS 裝置：

    ```bash
    npx react-native run-ios
    ```

1. 執行 Android 應用程式來驗證設定。 需要一些額外的步驟，才能將 Android 模擬器或裝置設定為能夠存取 React Native Metro 伺服器。 下列命令會產生適用於 Android 的初始 JavaScript 套組，並將其放入資產資料夾中。

    ```bash
    # create assets folder for the bundle
    mkdir android/app/scr/main/assets
    # build the bundle
    npx react-native bundle --platform android --dev true --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res
    # enable ability for sim to access the localhost
    adb reverse tcp:8081 tcp:8081
    ```

    此指令碼將會以應用程式的初始版本預先部署。 一旦部署後，就會指定伺服器 IP 位址，將您的模擬器或裝置設定為存取 Metro 伺服器。 請執行下列命令來建置並執行 Android 應用程式：

    ```bash
    npx react-native run-android
    ```

    一旦進入應用程式，就請點擊 `CMD+M` (模擬器) 或搖動裝置以填入開發人員設定、瀏覽至 `Settings` > `Change Bundle Location`，然後指定具有預設埠的 Metro 伺服器 IP 位址：`<metro-server-ip-address>:8081`。

1. 在 `App.tsx` 檔案中，將任何變更套用至頁面配置、加以儲存，並讓變更自動反映在 iOS 和 Android 應用程式中。

    > [!NOTE]
    > [官方文件](https://reactnative.dev/docs/environment-setup)中提供詳細的開發環境設定指南

### <a name="install-required-packages"></a>安裝必要的套件

您需要下列三個套件，此範例才能執行：

1. [React Native 推播通知 iOS](https://www.npmjs.com/package/@react-native-community/push-notification-ios) - [專案 GitHub](https://github.com/react-native-community/push-notification-ios)

    此套件是在 PushNotificationIOS 從 React Native 核心分割出來時建立的。 此套件原本就會執行 iOS 的推播通知，並提供 React Native 介面來加以存取。 請執行下列命令來安裝套件：

    ```bash
    yarn add @react-native-community/push-notification-ios
    ```

1. [跨平台的 React Native 推播通知](https://www.npmjs.com/package/react-native-push-notification)

    此套件會以跨平台方式，在 iOS 和 Android 上實作本機和遠端通知。 請執行下列命令來安裝套件：

    ```bash
    yarn add react-native-push-notification
    ```

1. [裝置資訊套件](https://www.npmjs.com/package/react-native-device-info) 此套件會在執行階段提供裝置的相關資訊。 使用該套件來定義裝置識別碼，這可用來註冊推播通知。 請執行下列命令來安裝套件：

    ```bash
    yarn add react-native-device-info
    ```

### <a name="implement-the-cross-platform-components"></a>實作跨平台元件

1. 建立和實作 `DemoNotificationHandler`：

    ```typescript
    import PushNotification from 'react-native-push-notification';

    class DemoNotificationHandler {
      private _onRegister: any;
      private _onNotification: any;

      onNotification(notification: any) {
        console.log('NotificationHandler:', notification);

        if (typeof this._onNotification === 'function') {
          this._onNotification(notification);
        }
      }

      onRegister(token: any) {
        console.log('NotificationHandler:', token);

        if (typeof this._onRegister === 'function') {
          this._onRegister(token);
        }
      }

      attachTokenReceived(handler: any) {
        this._onRegister = handler;
      }

      attachNotificationReceived(handler: any) {
        this._onNotification = handler;
      }
    }

    const handler = new DemoNotificationHandler();

    PushNotification.configure({
      onRegister: handler.onRegister.bind(handler),
      onNotification: handler.onNotification.bind(handler),
      permissions: {
        alert: true,
        badge: true,
        sound: true,
      },
      popInitialNotification: true,
      requestPermissions: true,
    });

    export default handler;
    ```

1. 建立和實作 `DemoNotificationService`：

    ```typescript
    import PushNotification from 'react-native-push-notification';
    import DemoNotificationHandler from './DemoNotificationHandler';

    export default class DemoNotificationService {
      constructor(onTokenReceived: any, onNotificationReceived: any) {
        DemoNotificationHandler.attachTokenReceived(onTokenReceived);
        DemoNotificationHandler.attachNotificationReceived(onNotificationReceived);
        PushNotification.getApplicationIconBadgeNumber(function(number: number) {
          if(number > 0) {
            PushNotification.setApplicationIconBadgeNumber(0);
          }
        });
      }

      checkPermissions(cbk: any) {
        return PushNotification.checkPermissions(cbk);
      }

      requestPermissions() {
        return PushNotification.requestPermissions();
      }

      cancelNotifications() {
        PushNotification.cancelLocalNotifications();
      }

      cancelAll() {
        PushNotification.cancelAllLocalNotifications();
      }

      abandonPermissions() {
        PushNotification.abandonPermissions();
      }
    }
    ```

1. 建立和實作 `DemoNotificationRegistrationService`：

    ```typescript
    export default class DemoNotificationService {
        constructor(
            readonly apiUrl: string,
            readonly apiKey: string) {
        }

    async registerAsync(request: any): Promise<Response> {
            const method = 'PUT';
            const registerApiUrl = `${this.apiUrl}/notifications/installations`;
            const result = await fetch(registerApiUrl, {
                method: method,
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json',
                    'apiKey': this.apiKey
                },
                body: JSON.stringify(request)
            });

            this.validateResponse(registerApiUrl, method, request, result);
            return result;
        }

        async deregisterAsync(deviceId: string): Promise<Response> {
            const method = 'DELETE';
            const deregisterApiUrl = `${this.apiUrl}/notifications/installations/${deviceId}`;
            const result = await fetch(deregisterApiUrl, {
                method: method,
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json',
                    'apiKey': this.apiKey
                }
            });

            this.validateResponse(deregisterApiUrl, method, null, result);
            return result;
        }

        private validateResponse(requestUrl: string, method: string, requestPayload: any, response: Response) {
            console.log(`Request: ${method} ${requestUrl} => ${JSON.stringify(requestPayload)}\nResponse: ${response.status}`);
            if (!response || response.status != 200) {
                throw `HTTP error ${response.status}: ${response.statusText}`;
            }
        }
    }
    ```

1. 設定應用程式。 開啟 `package.json` 並新增下列指令碼定義：

    ```json
    "configure": "cp .app.config.tsx src/config/AppConfig.tsx"
    ```

    然後執行此指令碼，將預設組態複製到 `config` 資料夾。

    ```bash
    yarn configure
    ```

    最後一個步驟是使用 API 存取資訊，更新上一個步驟所複製的組態檔。 指定 `apiKey` 和 `apiUrl` 參數：

    ```typescript
    module.exports = {
        appName: "PushDemo",
        env: "production",
        apiUrl: "https://<azure-push-notifications-api-url>/api/",
        apiKey: "<api-auth-key>",
    };
    ```

### <a name="implement-the-cross-platform-ui"></a>實作跨平台 UI

1. 定義頁面配置

    ```typescript
    <View style={styles.container}>
      {this.state.isBusy &&
        <ActivityIndicator></ActivityIndicator>
      }
      <View style={styles.button}>
        <Button title="Register" onPress={this.onRegisterButtonPress.bind(this)} disabled={this.state.isBusy} />
      </View>
      <View style={styles.button}>
        <Button title="Deregister" onPress={this.onDeregisterButtonPress.bind(this)} disabled={this.state.isBusy} />
      </View>
    </View>
    ```

1. 套用樣式

    ```css
    const styles = StyleSheet.create({
      container: {
        flex: 1,
        alignItems: "center",
        justifyContent: 'flex-end',
        margin: 50,
      },
      button: {
        margin: 5,
        width: "100%",
      }
    });
    ```

1. 初始化頁面元件

    ```typescript
      state: IState;
      notificationService: DemoNotificationService;
      notificationRegistrationService: DemoNotificationRegistrationService;
      deviceId: string;

      constructor(props: any) {
        super(props);
        this.deviceId = DeviceInfo.getUniqueId();
        this.state = {
          status: "Push notifications registration status is unknown",
          registeredOS: "",
          registeredToken: "",
          isRegistered: false,
          isBusy: false,
        };

        this.notificationService = new DemoNotificationService(
          this.onTokenReceived.bind(this),
          this.onNotificationReceived.bind(this),
        );

        this.notificationRegistrationService = new DemoNotificationRegistrationService(
          Config.apiUrl,
          Config.apiKey,
        );
      }
    ```

1. 定義按鈕點選處理常式

    ```typescript
      async onRegisterButtonPress() {
        if (!this.state.registeredToken || !this.state.registeredOS) {
          Alert.alert("The push notifications token wasn't received.");
          return;
        }

        let status: string = "Registering...";
        let isRegistered = this.state.isRegistered;
        try {
          this.setState({ isBusy: true, status });
          const pnPlatform = this.state.registeredOS == "ios" ? "apns" : "fcm";
          const pnToken = this.state.registeredToken;
          const request = {
            installationId: this.deviceId,
            platform: pnPlatform,
            pushChannel: pnToken,
            tags: []
          };
          const response = await this.notificationRegistrationService.registerAsync(request);
          status = `Registered for ${this.state.registeredOS} push notifications`;
          isRegistered = true;
        } catch (e) {
          status = `Registration failed: ${e}`;
        }
        finally {
          this.setState({ isBusy: false, status, isRegistered });
        }
      }

      async onDeregisterButtonPress() {
        if (!this.notificationService)
          return;

        let status: string = "Deregistering...";
        let isRegistered = this.state.isRegistered;
        try {
          this.setState({ isBusy: true, status });
          await this.notificationRegistrationService.deregisterAsync(this.deviceId);
          status = "Deregistered from push notifications";
          isRegistered = false;
        } catch (e) {
          status = `Deregistration failed: ${e}`;
        }
        finally {
          this.setState({ isBusy: false, status, isRegistered });
        }
      }
    ```

1. 處理已接收的權杖註冊和推播通知

    ```typescript
      onTokenReceived(token: any) {
        console.log(`Received a notification token on ${token.os}`);
        this.setState({ registeredToken: token.token, registeredOS: token.os, status: `The push notifications token has been received.` });

        if (this.state.isRegistered && this.state.registeredToken && this.state.registeredOS) {
          this.onRegisterButtonPress();
        }
      }

      onNotificationReceived(notification: any) {
        console.log(`Received a push notification on ${this.state.registeredOS}`);
        this.setState({ status: `Received a push notification...` });

        if (notification.data.message) {
          Alert.alert(AppConfig.appName, `${notification.data.action} action received`);
        }
      }
    };
    ```