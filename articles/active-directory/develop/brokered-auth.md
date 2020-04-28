---
title: Android 中的代理驗證 |Azure
titlesuffix: Microsoft identity platform
description: 瞭解在 Microsoft 身分識別平臺中，適用于 Android 的代理驗證 & 授權
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman, hahamil, brianmel
ms.openlocfilehash: a734589178438fd65d9a2d156fd91fc82807f578
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76697892"
---
# <a name="brokered-authentication-in-android"></a>Android 中的代理驗證

您必須使用 Microsoft 的其中一個驗證代理程式參與全裝置單一登入（SSO），並符合組織的條件式存取原則。 與 broker 整合可提供下列優點：

- 裝置單一登入
- 條件式存取：
  - Intune 應用程式保護
  - 裝置註冊（Workplace Join）
  - 行動裝置管理
- 全裝置帳戶管理
  -  透過 Android AccountManager & 帳戶設定
  - 「工作帳戶」-自訂帳戶類型

在 Android 上，Microsoft Authentication Broker 是[Microsoft Authenticator 應用程式](https://play.google.com/store/apps/details?id=com.azure.authenticator)隨附的元件， [Intune 公司入口網站](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)

> [!TIP]
> 只有一個主控訊息代理程式的應用程式，一次只會使用一個主控制項。 作為訊息代理程式的作用中應用程式是由裝置上的安裝順序所決定。 第一個要安裝的，或裝置上的最後一個會變成作用中的訊息代理程式。

下圖說明您的應用程式、Microsoft 驗證程式庫（MSAL），以及 Microsoft 的驗證代理程式之間的關聯性。

![Broker 部署圖表](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>安裝裝載 broker 的應用程式

裝置擁有者可以隨時從其 app store （通常是 Google Play 商店）安裝代理人主控應用程式。 不過，某些 Api （資源）會受到需要裝置的條件式存取原則所保護：

- 已註冊（已加入工作場所）和/或
- 已在裝置管理中註冊，或
- 已在 Intune 應用程式防護中註冊

如果裝置尚未安裝 broker 應用程式，MSAL 會指示使用者在應用程式嘗試以互動方式取得權杖時，立即安裝一個。 然後，應用程式將需要引導使用者進行步驟，讓裝置符合必要的原則。

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>安裝和卸載 broker 的效果

### <a name="when-a-broker-is-installed"></a>當代理程式已安裝時

當代理程式安裝在裝置上時，所有後續的互動式權杖要求（呼叫`acquireToken()`）都會由訊息代理程式處理，而不是在本機 MSAL。 先前可供 MSAL 使用的任何 SSO 狀態，都無法供訊息代理程式使用。 因此，使用者必須重新驗證，或從裝置已知的現有帳戶清單中選取帳戶。

安裝訊息代理程式不需要使用者重新登入。 只有在使用者需要解決時，才`MsalUiRequiredException`會將下一個要求移至訊息代理程式。 `MsalUiRequiredException`會因為許多原因而擲回，而且需要以互動方式解決。 以下是一些常見的原因：

- 使用者變更了與其帳戶相關聯的密碼。
- 使用者的帳戶不再符合條件式存取原則。
- 使用者已撤銷其對應用程式的同意，使其與其帳戶相關聯。

### <a name="when-a-broker-is-uninstalled"></a>當代理程式卸載時

如果只有一個已安裝的 broker 主控應用程式，而且已移除，則使用者必須重新登入。 卸載 active broker 會從裝置移除帳戶和相關聯的權杖。

如果 Intune 公司入口網站已安裝，而且是以作用中的訊息代理程式操作，而且也已安裝 Microsoft Authenticator，則如果卸載 Intune 公司入口網站（主動代理人），使用者就必須重新登入。 一旦他們再次登入，Microsoft Authenticator 應用程式就會變成作用中的代理人。

## <a name="integrating-with-a-broker"></a>與 broker 整合

### <a name="generating-a-redirect-uri-for-a-broker"></a>產生訊息代理程式的重新導向 URI

您必須註冊與訊息代理程式相容的重新導向 URI。 Broker 的重新導向 URI 必須包含您應用程式的套件名稱，以及應用程式簽章的 base64 編碼標記法。

重新導向 URI 的格式為：`msauth://<yourpackagename>/<base64urlencodedsignature>`

使用您應用程式的簽署金鑰，產生以 Base64 url 編碼的簽章。 以下是一些使用您的偵錯工具簽署金鑰的範例命令：

#### <a name="macos"></a>macOS

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

#### <a name="windows"></a>Windows

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

如需簽署應用程式的相關資訊，請參閱[簽署應用程式](https://developer.android.com/studio/publish/app-signing)。

> [!IMPORTANT]
> 針對您應用程式的實際執行版本使用您的生產簽署金鑰。

### <a name="configure-msal-to-use-a-broker"></a>將 MSAL 設定為使用訊息代理程式

若要在您的應用程式中使用 broker，您必須證明已設定您的 broker 重新導向。 例如，包含已啟用 broker 的重新導向 URI--並指出您已註冊，方法是在 MSAL 設定檔中包含下列內容：

```javascript
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

> [!TIP]
> 新的 Azure 入口網站應用程式註冊 UI 可協助您產生 broker 重新導向 URI。 如果您使用舊版體驗註冊應用程式，或使用 Microsoft 應用程式註冊入口網站，您可能需要產生重新導向 URI，並手動更新入口網站中的重新導向 Uri 清單。

### <a name="broker-related-exceptions"></a>Broker 相關的例外狀況

MSAL 會以兩種方式與訊息代理程式通訊：

- 代理程式系結服務
- Android AccountManager

MSAL 會先使用代理程式系結服務，因為呼叫此服務不需要任何 Android 許可權。 如果系結至系結服務失敗，MSAL 將會使用 Android AccountManager API。 只有當您的應用程式已被授與許可權時`"READ_CONTACTS"` ，MSAL 才會這麼做。

如果您收到`MsalClientException`具有錯誤碼的`"BROKER_BIND_FAILURE"`，則會有兩個選項：

- 要求使用者停用 Microsoft Authenticator 應用程式和 Intune 公司入口網站的電源優化。
- 要求使用者授與`"READ_CONTACTS"`許可權
