---
title: iOS 裝置的共享裝置模式
titleSuffix: Microsoft identity platform | Azure
description: 瞭解如何啟用共用設備模式,以允許一線工作人員共用 iOS 設備
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 7cecbc48eb362c2c0f1741352e6f7f5f6ad40c9e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550249"
---
# <a name="shared-device-mode-for-ios-devices"></a>iOS 裝置的共享裝置模式

> [!NOTE]
> 這項功能處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

一線員工(如零售員工、機組人員和現場服務人員)通常使用共用行動裝置來執行任務。 如果使用者有意或無意共用其密碼或 PIN 以造訪共用裝置上的客戶和業務數據,則這些共享設備可能會帶來安全風險。

共用設備模式允許您將 iOS 13 或更高版本的設備配置為員工更輕鬆、更安全地共用設備。 員工可以快速登錄和訪問客戶資訊。 當他們完成輪班或任務后,他們可以註銷設備,並立即準備好供下一位員工使用。

共用設備模式還提供 Microsoft 標識支援的設備管理。

此功能使用[微軟身份驗證器應用程式](../user-help/user-help-auth-app-overview.md)來管理裝置上的使用者,並分發[適用於蘋果設備的微軟企業 SSO 外掛程式](apple-sso-plugin.md)。

## <a name="create-a-shared-device-mode-app"></a>建立共享裝置模式應用程式

要建立共享設備模式應用,開發人員和雲端裝置管理員將協同工作:

1. **應用程式開發人員**編寫單帳戶應用(在共用設備模式下不支援多帳戶應用),並編寫代碼來處理共用設備註銷等內容。

1. **設備管理員**使用Microsoft Intune等行動裝置管理 (MDM)供應商來管理其組織中的設備,準備要共用的設備。 MDM 將 Microsoft 身份驗證器應用推送到設備,並透過對裝置的設定檔更新打開每個裝置的「共用模式」。 此共用模式設置是更改設備上受支援應用的行為的原因。 MDM 提供者的此設定為裝置設定共享裝置模式,並啟用共用裝置模式所需的[Apple 裝置的 Microsoft 企業 SSO 外掛程式](apple-sso-plugin.md)。

1. [**僅在公共預覽期間需要**]然後,具有[雲設備管理員](../users-groups-roles/directory-assign-admin-roles.md#cloud-device-administrator)角色的用戶必須啟動[Microsoft 身份驗證器應用](../user-help/user-help-auth-app-overview.md),並將其設備加入組織。

    在 Azure 門戶中設定組織角色的成員身份 **:Azure 活動目錄** > **角色和管理員** > **雲設備管理員**

以下部分可説明您更新應用程式以支援共享設備模式。

## <a name="use-intune-to-enable-shared-device-mode--sso-extension"></a>使用 Intune 啟用共用裝置模式& SSO 擴充

> [!NOTE]
> 以下步驟僅在公共預覽期間是必需的。

您的裝置需要配置為支援共享設備模式。 它必須安裝 iOS 13+ 並註冊 MDM。 MDM 設定需要開啟[適用於 Apple 裝置的 Microsoft SSO 外掛程式](apple-sso-plugin.md)。 要瞭解有關 SSO 擴充的更多,請參閱[Apple 影片](https://developer.apple.com/videos/play/tech-talks/301/)。

1. 在 Intune 設定門戶中,告訴裝置啟用具有以下配置[的 Apple 裝置的 Microsoft 企業 SSO 外掛程式](apple-sso-plugin.md):

    - **型態**: 重定向
    - **延伸代碼**: com.microsoft.azure 身份驗證器.sso 擴展
    - **小組代碼**: SGGM6D27TK
    - **網址**:https://login.microsoftonline.com
    - 要設定的其他資料:
      - 金鑰:共用裝置模式
      - 類型：布林值
      - 值: 正確

    有關使用 Intune 進行設定的詳細資訊,請參考[Intune 設定文件](https://docs.microsoft.com/intune/configuration/ios-device-features-settings)。

1. 接下來,配置 MDM 以透過 MDM 設定檔將 Microsoft 身份驗證器應用推送到您的裝置。

    設定以下設定選項以開啟共享裝置模式:

    - 設定 1：
      - 金鑰:共用裝置模式
      - 類型：布林值
      - 值: 正確

## <a name="modify-your-ios-application-to-support-shared-device-mode"></a>變更 iOS 應用程式以支援共享裝置模式

您的用戶依賴您來確保其數據不會洩露給其他使用者。 以下各節提供有用的信號,以向應用程式指示發生了更改,應進行處理。

您負責在每次使用應用時檢查設備上的用戶狀態,然後清除前一個用戶的數據。 這包括如果在多任務處理中從後台重新載入它,則包括它。

在使用者更改時,應確保清除前一個用戶的數據,並刪除應用程式中顯示的任何緩存數據。 我們強烈建議您和您的公司在更新應用以支援共享設備模式後進行安全審查過程。

### <a name="detect-shared-device-mode"></a>偵測共享裝置模式

檢測共用設備模式對於應用程式非常重要。 許多應用程式在共享設備上使用應用程式時,需要更改其使用者體驗 (UX)。 例如,您的應用程式可能具有"註冊"功能,這不適用於一線工作人員,因為它們可能已經具有帳戶。 如果數據處於共用設備模式,您可能還希望為應用程式處理數據添加額外的安全性。

使用`getDeviceInformationWithParameters:completionBlock:`中的 API`MSALPublicClientApplication`確定應用是否以共享設備模式在設備上運行。

以下代碼段顯示了使用`getDeviceInformationWithParameters:completionBlock:`API 的範例。

#### <a name="swift"></a>Swift

```swift
application.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in

    guard let deviceInfo = deviceInformation else {
        return
    }

    let isSharedDevice = deviceInfo.deviceMode == .shared
    // Change your app UX if needed
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
[application getDeviceInformationWithParameters:nil
                                completionBlock:^(MSALDeviceInformation * _Nullable deviceInformation, NSError * _Nullable error)
{
    if (!deviceInformation)
    {
        return;
    }

    BOOL isSharedDevice = deviceInformation.deviceMode == MSALDeviceModeShared;
    // Change your app UX if needed
}];
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>取得登入使用者並確定使用者是否在裝置上已變更

支援共享設備模式的另一個重要部分是確定使用者在設備上的狀態,並在使用者已更改或設備上根本沒有使用者時清除應用程式數據。 您有責任確保數據不會洩露給其他使用者。

您可以使用`getCurrentAccountWithParameters:completionBlock:`API 查詢裝置上的登入帳戶。

#### <a name="swift"></a>Swift

```swift
let msalParameters = MSALParameters()
msalParameters.completionBlockQueue = DispatchQueue.main

application.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in

    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALParameters *parameters = [MSALParameters new];
parameters.completionBlockQueue = dispatch_get_main_queue();

[application getCurrentAccountWithParameters:parameters
                             completionBlock:^(MSALAccount * _Nullable account, MSALAccount * _Nullable previousAccount, NSError * _Nullable error)
{
    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
}];
```

### <a name="globally-sign-in-a-user"></a>全域登入使用者

當設備配置為共享設備時,應用程式可以調用`acquireTokenWithParameters:completionBlock:`API 登錄帳戶。 該帳戶將在帳戶中出現第一個應用簽名后,面向設備上的所有符合條件的應用在全球可用。

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"api://myapi/scope"] webviewParameters:[self msalTestWebViewParameters]];

parameters.loginHint = self.loginHintTextField.text;

[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

### <a name="globally-sign-out-a-user"></a>全域登出使用者

以下代碼刪除登錄帳戶,並清除緩存的權杖,不僅從應用,而且從處於共用設備模式的設備。 但是,它不會清除應用程式*中的數據*。 您必須清除應用程式中的數據,以及清除應用程式可能向用戶顯示的任何緩存資料。

#### <a name="clear-browser-state"></a>清除瀏覽器狀態

> [!NOTE]
> 以下步驟僅在公共預覽期間是必需的。

在此公共預覽版本中,適用於[Apple 設備的 Microsoft 企業 SSO 外掛程式](apple-sso-plugin.md)僅清除應用程式的狀態。 它不清除 Safari 瀏覽器上的狀態。 我們建議您手動清除瀏覽器會話,以確保不會留下使用者狀態的痕跡。 您可以使用下面顯示的可選`signoutFromBrowser`屬性清除任何 Cookie。 這將導致瀏覽器在設備上短暫啟動。

#### <a name="swift"></a>Swift

```swift
let account = .... /* account retrieved above */

let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParamaters!)
signoutParameters.signoutFromBrowser = true // Only needed for Public Preview.

application.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in

    if let error = error {
        // Signout failed
        return
    }

    // Sign out completed successfully
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALAccount *account = ... /* account retrieved above */;

MSALSignoutParameters *signoutParameters = [[MSALSignoutParameters alloc] initWithWebviewParameters:webViewParameters];
signoutParameters.signoutFromBrowser = YES; // Only needed for Public Preview.

[application signoutWithAccount:account signoutParameters:signoutParameters completionBlock:^(BOOL success, NSError * _Nullable error)
{
    if (!success)
    {
        // Signout failed
        return;
    }

    // Sign out completed successfully
}];
```

## <a name="next-steps"></a>後續步驟

要檢視共享裝置模式在起作用,GitHub 上的以下代碼範例包括一個在共享裝置模式下在 iOS 裝置上執行一線輔助應用的範例:

[MSAL iOS 斯威夫特微軟圖形 API 範例](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)
