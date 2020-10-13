---
title: Android 中的代理驗證 |蔚藍
titleSuffix: Microsoft identity platform
description: Microsoft 身分識別平臺中適用于 Android 的代理驗證 & 授權總覽
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2020
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman, hahamil, brianmel
ms.openlocfilehash: 5042bfad2cfe06c7c368c6b476aa1b02d67bcc9c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91760749"
---
# <a name="brokered-authentication-in-android"></a>Android 中的代理驗證

您必須使用其中一個 Microsoft 驗證訊息代理程式來參與全裝置的單一登入 (SSO) 並符合組織的條件式存取原則。 與 broker 整合可提供下列優點：

- 裝置單一登入
- 條件式存取：
  - Intune 應用程式保護
  - 裝置註冊 (Workplace Join) 
  - 行動裝置管理
- 全裝置帳戶管理
  -  透過 Android AccountManager & 帳戶設定
  - 「工作帳戶」-自訂帳戶類型

在 Android 上，Microsoft 驗證訊息代理程式是包含在 [Microsoft Authenticator 應用程式](https://play.google.com/store/apps/details?id=com.azure.authenticator) 和 [Intune 公司入口網站](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)中的元件。

下圖說明您的應用程式、Microsoft 驗證程式庫 (MSAL) 和 Microsoft 驗證代理程式之間的關聯性。

![此圖顯示應用程式與 MSAL、訊息代理程式應用程式和 Android 帳戶管理員之間的關聯。](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>安裝裝載 broker 的應用程式

訊息代理程式裝載應用程式可以由裝置擁有者從其 app store 安裝 (通常會 Google Play 商店) 。 不過，某些 (資源) 的 Api 會受到要求裝置必須是下列條件的條件式存取原則所保護：

- 已註冊 (workplace join) 和/或
- 註冊裝置管理或
- 在 Intune 應用程式防護中註冊

如果裝置尚未安裝 broker 應用程式，MSAL 會指示使用者在應用程式嘗試以互動方式取得權杖時，立即安裝一個。 然後，應用程式必須引導使用者完成這些步驟，才能使裝置符合所需的原則。

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>安裝和卸載 broker 的效果

### <a name="when-a-broker-is-installed"></a>當訊息代理程式安裝時

當訊息代理程式安裝在裝置上時， (呼叫) 的所有後續互動式權杖要求 `acquireToken()` 都會由訊息代理程式處理，而不是在本機 MSAL。 任何先前可供 MSAL 使用的 SSO 狀態都無法供訊息代理程式使用。 如此一來，使用者將需要再次驗證，或從裝置已知的現有帳戶清單中選取帳戶。

安裝訊息代理程式不需要使用者再次登入。 只有當使用者需要解析時 `MsalUiRequiredException` ，下一個要求才會移至訊息代理程式。 `MsalUiRequiredException` 可能會因為許多原因而擲回，且需要以互動方式解決。 例如：

- 使用者變更了與其帳戶相關聯的密碼。
- 使用者的帳戶不再符合條件式存取原則。
- 使用者已撤銷其同意，讓應用程式與他們的帳戶相關聯。

#### <a name="multiple-brokers"></a>多個訊息代理程式

如果有多個訊息代理程式安裝在裝置上，則第一個安裝的訊息代理程式一律為使用中的訊息代理程式。 只有單一訊息代理程式可以在裝置上啟用。

### <a name="when-a-broker-is-uninstalled"></a>當代理程式卸載時

如果只有一個已安裝的代理程式裝載應用程式，且已移除，則使用者將需要再次登入。 卸載主動 broker 會從裝置移除帳戶和相關聯的權杖。

如果 Intune 公司入口網站已安裝並以作用中的訊息代理程式運作，而且也已安裝 Microsoft Authenticator，則如果已卸載 Intune 公司入口網站 (active broker) ，則使用者將需要再次登入。 一旦使用者再次登入，Microsoft Authenticator 應用程式就會變成作用中的訊息代理程式。

## <a name="integrating-with-a-broker"></a>與 broker 整合

### <a name="generating-a-redirect-uri-for-a-broker"></a>產生 broker 的重新導向 URI

您必須註冊與 broker 相容的重新導向 URI。 訊息代理程式的重新導向 URI 應該包含應用程式的套件名稱，以及應用程式簽章的 Base64 編碼標記法。

重新導向 URI 的格式為： `msauth://<yourpackagename>/<base64urlencodedsignature>`

您可以使用 [keytool](https://manpages.debian.org/buster/openjdk-11-jre-headless/keytool.1.en.html) ，利用應用程式的簽署金鑰來產生 Base64 編碼的簽章雜湊，然後使用 Azure 入口網站產生使用該雜湊的重新導向 URI。

Linux 和 macOS：

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

Windows：

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

當您使用 *keytool*產生簽章雜湊之後，請使用 Azure 入口網站來產生重新導向 URI：

1. 登入 [Azure 入口網站](https://portal.azure.com) ，並在 **應用程式註冊**中選取您的 Android 應用程式。
1. 選取 [**驗證**]  >  **新增平臺**  >  **Android**。
1. 在 [ **設定您的 Android 應用程式** ] 窗格中，輸入您稍早產生的簽章 **雜湊** 和 **套件名稱**。
1. 選取 [ **設定** ] 按鈕。

Azure 入口網站會為您產生重新導向 URI，並將其顯示在 [ **Android** 設定] 窗格的 [重新 **導向 uri** ] 欄位中。

如需有關簽署應用程式的詳細資訊，請參閱 Android Studio 使用者指南中的 [簽署應用程式](https://developer.android.com/studio/publish/app-signing) 。

> [!IMPORTANT]
> 針對應用程式的實際執行版本使用您的生產簽署金鑰。

### <a name="configure-msal-to-use-a-broker"></a>將 MSAL 設定為使用 broker

若要在您的應用程式中使用訊息代理程式，您必須證明您已設定 broker 重新導向。 例如，包含已啟用 broker 的重新導向 URI，並指出您已註冊它，方法是在您的 MSAL 設定檔中包含下列設定：

```json
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

### <a name="broker-related-exceptions"></a>Broker 相關的例外狀況

MSAL 與 broker 通訊的方式有兩種：

- Broker 系結服務
- Android AccountManager

MSAL 會先使用 broker 系結服務，因為呼叫此服務不需要任何 Android 許可權。 如果系結至系結的服務失敗，MSAL 將會使用 Android AccountManager API。 只有當您的應用程式已被授與許可權時，MSAL 才會這樣做 `"READ_CONTACTS"` 。

如果您收到 `MsalClientException` 錯誤碼，則有 `"BROKER_BIND_FAILURE"` 兩個選項：

- 要求使用者停用 Microsoft Authenticator 應用程式和 Intune 公司入口網站的電源優化。
- 要求使用者授與 `"READ_CONTACTS"` 許可權

## <a name="verifying-broker-integration"></a>驗證 broker 整合

它可能不會立即明確地清楚訊息代理程式整合是否正常運作，但您可以使用下列步驟來檢查：

1. 在您的 Android 裝置上，使用訊息代理程式完成要求。
1. 在 Android 裝置的設定中，尋找與您用來驗證的帳戶相對應的新建立帳戶。 帳戶應該是 *工作帳戶*類型。

如果您想要重複此測試，您可以從設定中移除帳戶。

## <a name="next-steps"></a>後續步驟

[適用于 android 裝置的共用裝置模式](msal-android-shared-devices.md) ，可讓您設定 android 裝置，讓多個員工輕鬆共用該裝置。
