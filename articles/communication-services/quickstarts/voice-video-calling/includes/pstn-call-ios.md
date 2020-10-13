---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/11/2020
ms.author: nikuklic
ms.openlocfilehash: 63fd0af819fde7d78df289a1b8f5cefa2e415101
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779817"
---
[!INCLUDE [Emergency Calling Notice](../../../includes/emergency-calling-notice-include.md)]
## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 已部署通訊服務資源。 [建立通訊服務資源](../../create-communication-resource.md)。
- 在通訊服務資源中取得的電話號碼。 [如何取得電話號碼](../../telephony-sms/get-phone-number.md)。
- 用來啟用通話用戶端的 `User Access Token`。 如需[如何取得 `User Access Token`](../../access-tokens.md) 的詳細資訊
- 完成[開始將通話新增至應用程式](../getting-started-with-calling.md)的快速入門

### <a name="prerequisite-check"></a>先決條件檢查

- 若要檢視與您通訊服務資源相關聯的電話號碼，請登入 [Azure 入口網站](https://portal.azure.com/)、尋找您的通訊服務資源，然後從左側瀏覽窗格開啟 [電話號碼] 索引標籤。
- 您可以使用適用於 iOS 的 Azure 通訊服務通話用戶端程式庫，建置並執行您的應用程式：

## <a name="setting-up"></a>設定

## <a name="start-a-call-to-phone"></a>啟動通話

指定您在通訊服務資源中取得的電話號碼，將用來啟動通話：
> [!WARNING]
> 請注意，電話號碼應以 E. 164 國際標準格式提供。 (例如：+12223334444)

修改當點選 [啟動通話] 按鈕時將執行的 `startCall` 事件處理常式：

```swift
func startCall() {
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            let startCallOptions = ACSStartCallOptions()
            startCallOptions!.alternateCallerID = PhoneNumber(phoneNumber: "+12223334444")
            self.call = self.callAgent!.call([PhoneNumber(phoneNumber: self.callee)], options: startCallOptions)
            self.callDelegate = CallDelegate(self)
            self.call!.delegate = self.callDelegate
        }
    }
}
```

## <a name="run-the-code"></a>執行程式碼

您可以藉由選取 [產品] > [執行] 或使用 (&#8984;-R) 鍵盤快速鍵，在 iOS 模擬器上建置並執行應用程式。

![快速入門應用程式的最終外觀與風格](../media/ios/quick-start-make-call.png)

您可以在新增的文字欄位中提供電話號碼並按一下 [啟動通話] 按鈕，以進行通話。
> [!WARNING]
> 請注意，電話號碼應以 E. 164 國際標準格式提供。 (例如：+12223334444)

> [!NOTE]
> 第一次進行通話時，系統會提示您需要麥克風的存取權。 在實際執行環境應用程式中，您應該使用 `AVAudioSession` API [檢查權限狀態](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources)，並在未授與權限時，正常地更新應用程式的行為。
