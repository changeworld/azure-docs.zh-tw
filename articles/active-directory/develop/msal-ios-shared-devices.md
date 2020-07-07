---
title: 適用於 iOS 裝置的共用裝置模式
titleSuffix: Microsoft identity platform | Azure
description: 瞭解如何啟用共用裝置模式，以允許第一線背景工作角色共用 iOS 裝置
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80550249"
---
# <a name="shared-device-mode-for-ios-devices"></a>適用於 iOS 裝置的共用裝置模式

> [!NOTE]
> 這項功能處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

第一線的工作者（例如零售協會、航班小組成員，以及現場服務工作者）通常會使用共用的行動裝置來執行其工作。 如果您的使用者刻意共用其密碼或 Pin，以存取共用裝置上的客戶和商務資料，這些共用裝置可能會帶來安全性風險。

共用裝置模式可讓您設定 iOS 13 或更新版本的裝置，使其更容易且更安全地由員工共用。 員工可以快速登入並存取客戶資訊。 當他們完成其轉移或工作時，可以登出裝置，並立即準備好供下一位員工使用。

共用裝置模式也會提供 Microsoft 以身分識別支援的裝置管理。

這項功能會使用[Microsoft Authenticator 應用程式](../user-help/user-help-auth-app-overview.md)來管理裝置上的使用者，以及散發[適用于 Apple 裝置的 Microsoft 企業 SSO 外掛程式](apple-sso-plugin.md)。

## <a name="create-a-shared-device-mode-app"></a>建立共用裝置模式應用程式

若要建立共用裝置模式應用程式，開發人員和雲端裝置管理員會共同作業：

1. **應用程式開發人員**會撰寫單一帳戶應用程式（共用裝置模式中不支援多帳戶應用程式），並撰寫程式碼來處理像是共用裝置登出之類的專案。

1. **裝置系統管理員**會使用行動裝置管理（MDM）提供者（如 Microsoft Intune）來準備要共用的裝置，以管理其組織中的裝置。 MDM 會將 Microsoft Authenticator 應用程式推送至裝置，並透過裝置的設定檔更新，為每個裝置開啟「共用模式」。 此共用模式設定會變更裝置上支援之應用程式的行為。 這項來自 MDM 提供者的設定會設定裝置的共用裝置模式，並為共用裝置模式所需的[Apple 裝置啟用 Microsoft 企業 SSO 外掛程式](apple-sso-plugin.md)。

1. [**僅限公開預覽期間所需**]具有[雲端裝置管理員](../users-groups-roles/directory-assign-admin-roles.md#cloud-device-administrator)角色的使用者必須啟動[Microsoft Authenticator 應用程式](../user-help/user-help-auth-app-overview.md)，並將其裝置加入組織。

    若要在 Azure 入口網站中設定組織角色的成員資格： **Azure Active Directory**  >  **角色和**  >  **系統管理員雲端裝置管理員**

下列各節可協助您更新應用程式，以支援共用裝置模式。

## <a name="use-intune-to-enable-shared-device-mode--sso-extension"></a>使用 Intune 來啟用共用裝置模式 & SSO 擴充功能

> [!NOTE]
> 只有在公開預覽期間，才需要執行下列步驟。

您的裝置必須設定為支援共用裝置模式。 它必須已安裝 iOS 13 +，並已註冊 MDM。 MDM 設定也必須啟用[適用于 Apple 裝置的 Microsoft 企業 SSO 外掛程式](apple-sso-plugin.md)。 若要深入瞭解 SSO 延伸模組，請參閱[Apple 影片](https://developer.apple.com/videos/play/tech-talks/301/)。

1. 在 Intune 設定入口網站中，告知裝置使用下列設定來啟用[Apple 裝置的 Microsoft 企業 SSO 外掛程式](apple-sso-plugin.md)：

    - **類型**：重新導向
    - **延伸模組識別碼**： azureauthenticator. ssoextension
    - **小組識別碼**： SGGM6D27TK
    - **Url**：https://login.microsoftonline.com
    - 要設定的其他資料：
      - 索引鍵： sharedDeviceMode
      - 類型：布林值
      - 值： True

    如需使用 Intune 設定的詳細資訊，請參閱[intune 設定檔](https://docs.microsoft.com/intune/configuration/ios-device-features-settings)。

1. 接下來，設定您的 MDM 以透過 MDM 設定檔將 Microsoft Authenticator 應用程式推送至您的裝置。

    設定下列設定選項以開啟 [共用裝置模式]：

    - 設定 1：
      - 索引鍵： sharedDeviceMode
      - 類型：布林值
      - 值： True

## <a name="modify-your-ios-application-to-support-shared-device-mode"></a>修改您的 iOS 應用程式以支援共用裝置模式

您的使用者取決於您，以確保其資料不會洩漏給另一位使用者。 下列各節提供有用的指示，指出您的應用程式已發生變更，應加以處理。

您必須負責在每次使用您的應用程式時檢查裝置上的使用者狀態，然後再清除先前的使用者資料。 這包括在多工中從背景重載的情況。

在使用者變更時，您應該確定已清除前一個使用者的資料，並移除應用程式中顯示的任何快取資料。 我們強烈建議您和您的公司在更新您的應用程式以支援共用裝置模式後，進行安全性審查流程。

### <a name="detect-shared-device-mode"></a>偵測共用裝置模式

偵測共用裝置模式對您的應用程式很重要。 在共用裝置上使用應用程式時，許多應用程式都需要變更使用者體驗（UX）。 例如，您的應用程式可能會有「註冊」功能，這不適合第一線的背景工作角色，因為他們可能已經有帳戶。 如果您的應用程式是共用的裝置模式，您可能也會想要為其處理資料增加額外的安全性。

使用 `getDeviceInformationWithParameters:completionBlock:` 中的 API `MSALPublicClientApplication` 來判斷應用程式是否在共用裝置模式的裝置上執行。

下列程式碼片段顯示使用 API 的範例 `getDeviceInformationWithParameters:completionBlock:` 。

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

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>取得已登入的使用者，並判斷裝置上的使用者是否已變更

支援共用裝置模式的另一個重要部分，是判斷裝置上的使用者狀態，並在使用者已變更或裝置上沒有任何使用者時清除應用程式資料。 您必須負責確保資料不會洩漏給另一位使用者。

您可以使用 `getCurrentAccountWithParameters:completionBlock:` API 來查詢裝置上目前已登入的帳戶。

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

當裝置設定為共用裝置時，您的應用程式可以呼叫 `acquireTokenWithParameters:completionBlock:` API 來登入帳戶。 當第一個應用程式登入帳戶之後，裝置上的所有合格應用程式都可全域使用此帳戶。

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"api://myapi/scope"] webviewParameters:[self msalTestWebViewParameters]];

parameters.loginHint = self.loginHintTextField.text;

[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

### <a name="globally-sign-out-a-user"></a>全域登出使用者

下列程式碼會移除登入的帳戶，並從應用程式以外的裝置中清除快取的權杖，但也會從處於共用裝置模式的裝置中清除。 不過，它並不會從您的應用程式中清除*資料*。 您必須清除應用程式中的資料，並清除應用程式可能會向使用者顯示的任何快取資料。

#### <a name="clear-browser-state"></a>清除瀏覽器狀態

> [!NOTE]
> 只有在公開預覽期間，才需要執行下列步驟。

在此公開預覽版本中，[適用于 Apple 裝置的 Microsoft 企業 SSO 外掛程式](apple-sso-plugin.md)只會清除應用程式的狀態。 它不會清除 Safari 瀏覽器上的狀態。 建議您手動清除瀏覽器會話，以確保不會留下任何使用者狀態的追蹤。 您可以使用如下 `signoutFromBrowser` 所示的選擇性屬性來清除任何 cookie。 這會導致瀏覽器在裝置上短暫啟動。

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

若要查看作用中的共用裝置模式，下列 GitHub 上的程式碼範例包含在 iOS 裝置上以共用裝置模式執行第一線背景工作應用程式的範例：

[MSAL iOS Swift Microsoft Graph API 範例](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)
