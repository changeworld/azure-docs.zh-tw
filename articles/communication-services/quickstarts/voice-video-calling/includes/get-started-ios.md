---
title: 快速入門 - 使用 Azure 通訊服務將通話新增至 iOS 應用程式
description: 在本快速入門中，您將了解如何使用適用於 iOS 的 Azure 通訊服務通話用戶端程式庫。
author: matthewrobertson
ms.author: marobert
ms.date: 07/24/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 5f604847faf01d1b267e6cbb73481d57ef397bd9
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95564232"
---
在本快速入門中，您將了解如何使用適用於 iOS 的 Azure 通訊服務通話用戶端程式庫開始進行通話。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您需要下列必要條件：

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 執行 [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532) 的 Mac，以及安裝在您 Keychain 中的有效開發人員憑證。
- 已部署通訊服務資源。 [建立通訊服務資源](../../create-communication-resource.md)。
- 針對您的 Azure 通訊服務的[使用者存取權杖](../../access-tokens.md)。

## <a name="setting-up"></a>設定

### <a name="creating-the-xcode-project"></a>建立 XCode 專案

在 Xcode 中建立新的 iOS 專案，並選取 [單一檢視應用程式] 範本。 本教學課程使用 [SwiftUI 架構](https://developer.apple.com/xcode/swiftui/)，因此您應將 [語言] 設定為 [Swift]，並將 [使用者介面] 設定為 [SwiftUI]。 進行本快速入門期間，您不會建立測試。 您可以取消核取 [包含測試]。

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="顯示 Xcode 內 [新增專案] 視窗的螢幕擷取畫面。":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>使用 CocoaPods 安裝套件和相依性

1. 為您的應用程式建立 Podfile，如下所示：

   ```
   platform :ios, '13.0'
   use_frameworks!

   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.5'
     pod 'AzureCommunication', '~> 1.0.0-beta.5'
     pod 'AzureCore', '~> 1.0.0-beta.5'
   end
   ```

2. 執行 `pod install`。
3. 使用 Xcode 開啟 `.xcworkspace`。

### <a name="request-access-to-the-microphone"></a>要求存取麥克風

您必須以 `NSMicrophoneUsageDescription` 更新應用程式的資訊屬性清單，才能存取裝置的麥克風。 您可以將相關聯的值設定為 `string`，此值會包含在系統用來向使用者要求存取權的對話中。

以滑鼠右鍵按一下專案樹狀結構的 `Info.plist` 項目，然後選取 [開啟形式]  >  [原始程式碼]。 將以下幾行新增至最上層 `<dict>` 區段中，然後儲存檔案。

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>設定應用程式架構

開啟專案的 **ContentView.swift** 檔案，並且將 `import` 宣告新增至檔案頂端，以匯入 `AzureCommunicationCalling library`。 此外，匯入 `AVFoundation`，我們需要這個項目以便在程式碼中進行音訊權限要求。

```swift
import AzureCommunicationCalling
import AVFoundation
```

使用一些簡單的 UI 控制項來取代 `ContentView` 結構的實作，讓使用者可以起始和結束通話。 在本快速入門中，我們會將商務邏輯附加至這些控制項。

```swift
struct ContentView: View {
    @State var callee: String = ""
    @State var callClient: CallClient?
    @State var callAgent: CallAgent?
    @State var call: Call?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Who would you like to call?", text: $callee)
                    Button(action: startCall) {
                        Text("Start Call")
                    }.disabled(callAgent == nil)
                    Button(action: endCall) {
                        Text("End Call")
                    }.disabled(call == nil)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
        }
    }

    func startCall() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                // Add start call logic
            }
        }
    }

    func endCall() {
        // Add end call logic
    }
}
```

## <a name="object-model"></a>物件模型

下列類別和介面會處理 Azure 通訊服務通話用戶端程式庫的一些主要功能：

| Name                                  | 描述                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ACSCallClient | CallClient 是通話用戶端程式庫的主要進入點。|
| ACSCallAgent | CallAgent 是用來啟動和管理通話。 |
| CommunicationUserCredential | CommunicationUserCredential 會當做權杖認證使用，以具現化 CallAgent。| 
| CommunicationIdentifier | CommunicationIdentifier 用來代表使用者的身分識別，可以是下列其中一項：CommunicationUser/PhoneNumber/CallingApplication。 |

## <a name="authenticate-the-client"></a>驗證用戶端

使用使用者存取權杖來初始化 `CallAgent` 執行個體，讓我們能夠進行和接收通話。 將下列程式碼新增至 **ContentView.swift** 中的 `onAppear` 回撥：

```swift
var userCredential: CommunicationUserCredential?
do {
    userCredential = try CommunicationUserCredential(token: "<USER ACCESS TOKEN>")
} catch {
    print("ERROR: It was not possible to create user credential.")
    return
}

self.callClient = CallClient()

// Creates the call agent
self.callClient?.createCallAgent(userCredential) { (agent, error) in
    if error != nil {
        print("ERROR: It was not possible to create a call agent.")
    }

    if let agent = agent {
        self.callAgent = agent
        print("Call agent successfully created.")
    }
}
```

您必須將 `<USER ACCESS TOKEN>` 取代為資源的有效使用者存取權杖。 如果您還沒有可用的權杖，請參閱[使用者存取權杖](../../access-tokens.md)文件。

## <a name="start-a-call"></a>開始通話

`startCall` 方法會設定為點選 [開始通話] 按鈕時要執行的動作。 更新此實作，以使用 `ASACallAgent` 開始通話：

```swift
func startCall()
{
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            // start call logic
            let callees:[CommunicationIdentifier] = [CommunicationUser(identifier: self.callee)]
            self.call = self.callAgent?.call(callees, options: StartCallOptions())
        }
    }
}
```

您也可以使用 `StartCallOptions` 中的屬性來設定通話的初始選項 (亦即，允許在麥克風靜音的情況下開始通話)。

## <a name="end-a-call"></a>結束通話

當點選 [結束通話] 按鈕時，請實作 `endCall` 方法來結束目前的通話。

```swift
func endCall()
{    
    self.call!.hangup(HangupOptions()) { (error) in
        if (error != nil) {
            print("ERROR: It was not possible to hangup the call.")
        }
    }
}
```

## <a name="run-the-code"></a>執行程式碼

您可以藉由選取 [產品] > [執行] 或使用 (&#8984;-R) 鍵盤快速鍵，在 iOS 模擬器上建置並執行應用程式。

:::image type="content" source="../media/ios/quick-start-make-call.png" alt-text="快速入門應用程式的最終外觀與風格":::

您可以在文字欄位中提供使用者識別碼並且點選 [開始通話] 按鈕，以進行外撥 VOIP 通話。 呼叫 `8:echo123` 會將您連線到 Echo Bot，這非常適合用於開始使用和驗證音訊裝置。 

> [!NOTE]
> 第一次進行通話時，系統會提示您需要麥克風的存取權。 在實際執行環境應用程式中，您應該使用 `AVAudioSession` API [檢查權限狀態](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources)，並在未授與權限時，正常地更新應用程式的行為。

## <a name="sample-code"></a>範例程式碼

您可以從 [GitHub](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/Add%20Voice%20Calling) 下載範例應用程式
