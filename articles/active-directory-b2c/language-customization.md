---
title: Azure Active Directory B2C 中的語言自訂
description: 瞭解如何在 Azure Active Directory B2C 中自訂使用者流程的語言體驗。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 1012dad1b7e0a314687a38e420ff9ad4780051da
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111176"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的語言自訂

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) 中的語言自訂可讓您的使用者流程適應不同的語言，以符合您客戶的需求。 Microsoft 提供 [36 種語言](#supported-languages)的翻譯，但您也可以針對任何語言提供自己的翻譯。 即使您的體驗僅提供單一語言，您也可以自訂頁面上的任何文字。

## <a name="how-language-customization-works"></a>語言自訂的運作方式

您可以使用語言自訂來選取使用者流程所適用的語言。 此功能啟用後，您就可以從應用程式提供查詢字串參數 `ui_locales`。 當您呼叫 Azure AD B2C 時，您的頁面會轉譯為您所指定的地區設定。 此類型的組態可讓您完整控制使用者流程的語言，並忽略客戶瀏覽器的語言設定。

您可能不需要對客戶會看見哪種語言擁有這種程度的控制力。 如果您沒有提供 `ui_locales` 參數，則客戶的使用體驗會由其瀏覽器的設定來決定。 您仍可將各種語言新增為支援的語言，以控制要將使用者流程翻譯為何種語言。 如果客戶瀏覽器所設定要顯示的語言不是您想要支援的語言，則系統會改為顯示您選取作為支援的文化特性預設值語言。

* **ui-地區設定指定的語言**：當您啟用語言自訂之後，您的使用者流程會轉譯為此處指定的語言。
* **瀏覽器要求的語言**：如果未 `ui_locales` 指定任何參數，則 *如果支援該語言*，則會將您的使用者流程轉譯為瀏覽器所要求的語言。
* **原則預設語言**：如果瀏覽器未指定語言，或指定不支援的語言，則會將使用者流程轉譯為使用者流程的預設語言。

> [!NOTE]
> 如果您使用自訂使用者屬性，則必須提供自己的翻譯。 如需詳細資訊，請參閱[自訂您的字串](#customize-your-strings)。

::: zone pivot="b2c-custom-policy"

當地語系化需要三個步驟： 

1. 設定支援語言的明確清單
1. 提供特定語言的字串和集合
1. 編輯頁面的 [內容定義](contentdefinitions.md) 。 

::: zone-end 

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="support-requested-languages-for-ui_locales"></a>支援 ui_locales 所要求的語言

在語言自訂公開發行前所建立的原則必須先啟用此功能。 之後所建立的原則和使用者流程預設會啟用語言自訂。

當您對使用者流程啟用語言自訂時，您可藉由新增 `ui_locales` 參數來控制使用者流程的語言。

1. 在 Azure AD B2C 租用戶中，選取 [使用者流程]。
1. 按一下您想要啟用翻譯的使用者流程。
1. 選取 [語言]。
1. 選取 [啟用語言自訂]。

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>選取您的使用者流程中啟用的語言

啟用一組使用者流程的語言，以在瀏覽器要求但未使用 `ui_locales` 參數時，翻譯成該語言。

1. 透過前面的指示來確定您的使用者流程已啟用語言自訂。
1. 在使用者流程的 [語言] 頁面上，選取您想要支援的語言。
1. 在 [屬性] 窗格中，將 [已啟用] 變更為 [是]。
1. 選取 [屬性] 窗格頂端的 [儲存]。

>[!NOTE]
>如果您未提供 `ui_locales` 參數，則頁面會翻譯為客戶的瀏覽器語言 (但前提是已啟用該語言)。
>

## <a name="customize-your-strings"></a>自訂您的字串

語言自訂可讓您自訂使用者流程中的任何字串。

1. 透過前面的指示來確定您的使用者流程已啟用語言自訂。
1. 在使用者流程的 [語言] 頁面上，選取您想要自訂的語言。
1. 在 [Page-level-resources 檔案] 之下，選取您想要編輯的頁面。
1. 選取 [下載預設值] (如果您先前已編輯這個語言，請按一下 [下載覆寫])。

上述步驟會向您提供 JSON 檔案，以供您用來開始編輯字串。

### <a name="change-any-string-on-the-page"></a>變更頁面上的任何字串

1. 在 JSON 編輯器中開啟透過前面的指示所下載的 JSON 檔案。
1. 尋找您想要變更的元素。 您可以尋找 `StringId` 以取得您要尋找的字串，或尋找您要變更的 `Value` 屬性。
1. 以您想要顯示的內容來更新 `Value` 屬性。
1. 對於您想要變更的每個字串，請將 `Override` 變更為 `true`。
1. 儲存檔案，並上傳您的變更。 (您可以在您下載 JSON 檔案的相同位置找到上傳控制項)。

> [!IMPORTANT]
> 如果您需要覆寫字串，請務必將 `Override` 值設為 `true`。 如果該值未變更，則系統會忽略您的輸入。

### <a name="change-extension-attributes"></a>變更擴充屬性

如果您想要變更自訂使用者屬性的字串，或想要將字串新增到 JSON 中，其格式如下︰

```json
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

以自訂使用者屬性的名稱來取代 `<ExtensionAttribute>`。

以要顯示的新字串來取代 `<ExtensionAttributeValue>`。

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>使用 LocalizedCollections 提供值清單

如果您想要提供設定好的回應值清單，您需要建立 `LocalizedCollections` 屬性。 `LocalizedCollections` 是 `Name` 和 `Value` 的配對陣列。 項目的順序即為顯示它們的順序。 若要新增 `LocalizedCollections`，請使用下列格式：

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` 是使用者屬性，而其回應則是這個 `LocalizedCollections` 屬性。
* `Name` 是向使用者顯示的值。
* `Value` 是選取此選項時在宣告中所傳回的內容。

### <a name="upload-your-changes"></a>上傳您的變更

1. 對 JSON 檔案完成變更後，請瀏覽回到 B2C 租用戶。
1. 選取 [使用者流程]，然後按一下您想要啟用翻譯的使用者流程。
1. 選取 [語言]。
1. 選取您想要轉譯成什麼語言。
1. 選取您想要提供翻譯的頁面。
1. 選取資料夾圖示，然後選取要上傳的 JSON 檔案。

這些變更會自動儲存到您的使用者流程。

## <a name="customize-the-page-ui-by-using-language-customization"></a>使用語言自訂來自訂頁面 UI

有兩種方式可將 [HTML 內容](customize-ui-with-html.md)當地語系化。 一種方式是開啟[語言自訂](language-customization.md)。 啟用這項功能可讓 Azure AD B2C 將 OpenID Connect 參數轉送 `ui-locales` 至您的端點。 內容伺服器可以使用這個參數來提供語言特定的自訂 HTML 頁面。

或者，您也可以根據所使用的地區設定，從不同的地方提取內容。 在已啟用 CORS 的端點中，您可以針對特定語言設定主機內容的資料夾結構。 如果您使用萬用字元值 `{Culture:RFC5646}`，則會呼叫正確的語言。 例如，假設這是您的自訂頁面 URI：

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

您可以用 `fr` 載入頁面。 當頁面提取 HTML 和 CSS 內容時，它會從下列位置提取：

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>新增自訂語言

您也可以新增 Microsoft 目前不提供翻譯的語言。 您必須提供使用者流程中所有字串的翻譯。 僅限 ISO 639-1 標準中的語言和地區設定代碼。

1. 在 Azure AD B2C 租用戶中，選取 [使用者流程]。
2. 按一下您想要新增自訂語言的使用者流程，然後按一下 [語言]。
3. 從頁面頂端選取 [新增自訂語言]。
4. 在開啟的內容窗格中，輸入有效的地區設定代碼，以識別您提供哪種語言的翻譯。
5. 針對每一頁，您可以下載適用於英文的一組覆寫並處理翻譯。
6. 處理好 JSON 檔案後，您可以為每一頁上傳它們。
7. 選取 [啟用]，而您的使用者流程現在可以為您的使用者顯示此語言。
8. 儲存語言。

>[!IMPORTANT]
>您必須啟用自訂語言，或為它上傳覆寫才能儲存。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="set-up-the-list-of-supported-languages"></a>設定支援語言的清單

開啟原則的擴充檔案。 例如，<em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>。

1. 搜尋 [BuildingBlocks](buildingblocks.md) 元素。 如果此元素不存在，請加以新增。
1. 新增具有支援語言的 `Localization` 元素：英文 (預設值) 和西班牙文。  


```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="provide-language-specific-labels"></a>提供特定語言的標籤

`Localization` 元素的 [LocalizedResources](localization.md#localizedresources) 包含當地語系化字串的清單。 當地語系化資源元素具有識別碼，可用來唯一識別當地語系化資源。 稍後會在[內容定義](contentdefinitions.md)元素中使用此識別碼。

為內容定義與您想要支援的語言設定當地語系化資源元素。 若要自訂英文和西班牙文的統一註冊或登入頁面，請在 `</SupportedLanguages>` 元素的結尾後面加入下列 `LocalizedResources` 元素。

> [!NOTE]
> 在下列範例中，我們在每一行的開頭新增了井字型 `#` 符號，讓您可以輕鬆地在畫面上尋找當地語系化的標籤。

```xml
<!--Local account sign-up or sign-in page English-->
<Localization Enabled="true">
  ...
  <LocalizedResources Id="api.signuporsignin.en">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Email Address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Please enter your email</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Username</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Sign up now</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Please enter your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#Don't have an account?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#Forgot your password?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#OR</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#The user has forgotten their password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Sign in</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Sign in with your social account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Please enter your password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#The password you entered is not in the expected format.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Sign in with your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Sign in with your existing account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Please enter a valid email address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#We are having trouble signing you in. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Your password is incorrect.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#We can't seem to find your account.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Looks like you used an old password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#Invalid username or password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up or sign-in page Spanish-->
  <LocalizedResources Id="api.signuporsignin.es">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Este campo es obligatorio</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Registrarse ahora</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Escriba su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#¿No tiene una cuenta?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#¿Olvidó su contraseña?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#O</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#El usuario ha olvidado su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Iniciar sesión</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Iniciar sesión con su cuenta de redes sociales</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Escriba su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#La contraseña que ha escrito no está en el formato esperado.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Iniciar sesión con su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Iniciar sesión con su cuenta existente</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Escriba una dirección de correo electrónico válida</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#Tenemos problemas para iniciar su sesión. Vuelva a intentarlo más tarde.  </LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Su contraseña es incorrecta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#Parece que no podemos encontrar su cuenta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Parece que ha usado una contraseña antigua.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#El nombre de usuario o la contraseña no son válidos.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Se bloqueó su cuenta. Póngase en contacto con la persona responsable de soporte técnico para desbloquearla y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Su cuenta se bloqueó temporalmente para impedir un uso no autorizado. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page English-->
  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Email Address</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Email address that can be used to contact you.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Please enter a valid email address.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Enter new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirm New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirm new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Display Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Your display name.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Surname</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Your surname (also known as family name or last name).  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Given Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Your given name (also known as first name).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Create</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#A required field is missing. Please fill out all required fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#What is this?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Please provide the following details.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Please wait</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#This information is required.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Change e-mail</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Send new code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Send verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Verify code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#That code is expired. Please request a new code.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#You've made too many incorrect attempts. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#That code is incorrect. Please try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#Verification is necessary. Please click Send button.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#E-mail address verified. You can now continue.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Claim not verified: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#A user with the specified ID already exists. Please choose a different one.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Incorrect pattern for: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} has invalid input.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Missing required element: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error in validation by: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page Spanish-->
  <LocalizedResources Id="api.localaccountsignup.es">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Dirección de correo electrónico</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Introduzca una dirección de correo electrónico válida.  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#Nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Escriba la contraseña nueva</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#De 8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Nombre para mostrar</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Su nombre para mostrar.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Apellido</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Su apellido.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Nombre</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Su nombre (también conocido como nombre de pila).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Crear</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#Hay uno o varios campos rellenados de forma incorrecta. Compruebe las entradas y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#Los campos de entrada de contraseña no coinciden. Escriba la misma contraseña en ambos campos y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#Falta un campo obligatorio. Rellene todos los campos necesarios y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#¿Qué es esto?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Proporcione los siguientes detalles.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Espere</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#Esta información es obligatoria.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Cambiar correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Enviar nuevo código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Enviar código de comprobación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Comprobar código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#El código ha expirado. Solicite otro nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#Ha realizado demasiados intentos incorrectos. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#Ese código es incorrecto. Inténtelo de nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#Tenemos problemas para comprobar la dirección de correo electrónico. Escriba una dirección de correo electrónico válida y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#Ha habido demasiadas solicitudes para comprobar esta dirección de correo electrónico. Espere un poco y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Se ha enviado el código de verificación a su Bandeja de entrada. Cópielo en el siguiente cuadro de entrada.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Código de verificación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#La comprobación es obligatoria. Haga clic en el botón Enviar.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#Dirección de correo electrónico comprobada. Puede continuar.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Reclamación no comprobada: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Patrón incorrecto para: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} tiene una entrada no válida.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Falta un elemento obligatorio: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error en la validación de: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

## <a name="edit-the-content-definition-with-the-localization"></a>使用當地語系化來編輯內容定義

貼上您複製的完整 ContentDefinitions 元素內容，作為 BuildingBlocks 元素的子項目。

在下列範例中，英文 (en) 和西班牙文 (es) 自訂字串已新增至註冊或登入頁面，也新增到了本機帳戶的註冊頁面。 每個 **LocalizedResourcesReference** 的 **LocalizedResourcesReferenceId** 與其地區設定相同，但可使用任何字串做為識別碼。 至於每個語言和頁面組合，請指向先前建立的對應 **LocalizedResources**。

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.signuporsignin">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>

  <ContentDefinition Id="api.localaccountsignup">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>
</ContentDefinitions>
```

##  <a name="upload-and-test-your-updated-custom-policy"></a>上傳並測試更新的自訂原則

### <a name="upload-the-custom-policy"></a>上傳自訂原則

1. 儲存擴充檔案。
1. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 搜尋並選取 [Azure AD B2C]。
1. 在 [原則] 之下，選取 [Identity Experience Framework]。
1. 選取 [上傳自訂原則]。
1. 上傳您先前變更的擴充檔案。

### <a name="test-the-custom-policy-by-using-run-now"></a>使用 [立即執行] 測試自訂原則

1. 選取您上傳的原則，然後選取 [立即執行]。
1. 您應該可以看到當地語系化的註冊或登入頁面。
1. 按一下 [註冊] 連結，您應該就能看到當地語系化的註冊頁面。
1. 將您的瀏覽器預設語言切換為西班牙文。 或者，您也可以將查詢字串參數 `ui_locales` 新增至授權要求。 例如： 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize&client_id=0239a9cc-309c-4d41-12f1-31299feb2e82&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&ui_locales=es
```

::: zone-end


## <a name="additional-information"></a>其他資訊

::: zone pivot="b2c-user-flow"

### <a name="page-ui-customization-labels-as-overrides"></a>以頁面 UI 自訂標籤作為覆寫

當您啟用語言自訂時，您先前使用頁面 UI 自訂對標籤所進行的編輯會保存在英文 (en) 的 JSON 檔案中。 您可以上傳語言自訂中的語言資源，來繼續變更您的標籤和其他字串。

::: zone-end

### <a name="up-to-date-translations"></a>最新的翻譯

Microsoft 致力於提供最新的翻譯來供您使用。 Microsoft 會持續改善翻譯，並讓它們符合您的需求。 Microsoft 會找出全域術語中的錯誤和變更，並製作可在使用者流程中順暢運作的更新。

### <a name="support-for-right-to-left-languages"></a>支援從右至左的語言

Microsoft 目前不支援從右至左的語言。 您可以使用自訂地區設定，並使用 CSS 變更顯示字串的方式來完成此作業。 如果您需要此功能，請在 [Azure 意見反應](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag)上投票支持此功能。

### <a name="social-identity-provider-translations"></a>社交識別提供者的翻譯

Microsoft 為社交登入提供 `ui_locales` OIDC 參數。 但有些社交識別提供者不接受此參數，包括 Facebook 和 Google。

### <a name="browser-behavior"></a>瀏覽器行為

Chrome 和 Firefox 都會要求使用其設定的語言。 如果該語言受到支援，則會顯示在預設語言之前。 Microsoft Edge 目前未要求語言，而是會直接使用預設語言。

## <a name="supported-languages"></a>支援的語言

Azure AD B2C 包含下列語言的支援。 使用者流程語言是由 Azure AD B2C 提供。 [AZURE AD mfa](../active-directory/authentication/concept-mfa-howitworks.md)會提供多因素驗證 (mfa) 通知語言。

| Language              | 語言代碼 | 使用者流程         | MFA 通知  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| 阿拉伯文                | ar            | ![X 表示否。](./media/user-flow-language-customization/no.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 保加利亞文             | bg            | ![X 表示否。](./media/user-flow-language-customization/no.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 孟加拉文                | bn            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![X 表示否。](./media/user-flow-language-customization/no.png) |
| 卡達隆尼亞文               | ca            | ![X 表示否。](./media/user-flow-language-customization/no.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 捷克文                 | cs            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 丹麥文                | da            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 德文                | de            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 希臘文                 | el            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 英文               | en            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 西班牙文               | es            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 愛沙尼亞文              | et            | ![X 表示否。](./media/user-flow-language-customization/no.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 巴斯克文                | eu            | ![X 表示否。](./media/user-flow-language-customization/no.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 芬蘭文               | fi            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 法文                | fr            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 加利西亞文              | gl            | ![X 表示否。](./media/user-flow-language-customization/no.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 古吉拉特文              | gu            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![X 表示否。](./media/user-flow-language-customization/no.png) |
| Hebrew                | he            | ![X 表示否。](./media/user-flow-language-customization/no.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| Hindi                 | hi            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 克羅埃西亞文              | hr            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 匈牙利文             | hu            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 印尼文            | id            | ![X 表示否。](./media/user-flow-language-customization/no.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 義大利文               | it            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 日文              | ja            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 哈薩克文                | kk            | ![X 表示否。](./media/user-flow-language-customization/no.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 坎那達文               | kn            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![X 表示否。](./media/user-flow-language-customization/no.png) |
| 韓文                | ko            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 立陶宛文            | lt            | ![X 表示否。](./media/user-flow-language-customization/no.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 拉脫維亞文               | lv            | ![X 表示否。](./media/user-flow-language-customization/no.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 馬來亞拉姆文             | ml            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![X 表示否。](./media/user-flow-language-customization/no.png) |
| 馬拉地文               | mr            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![X 表示否。](./media/user-flow-language-customization/no.png) |
| 馬來文                 | ms            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 挪威文 (巴克摩)      | nb            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![X 表示否。](./media/user-flow-language-customization/no.png) |
| 荷蘭文                 | nl            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 挪威文             | 否            | ![X 表示否。](./media/user-flow-language-customization/no.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 旁遮普文               | pa            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![X 表示否。](./media/user-flow-language-customization/no.png) |
| 波蘭文                | pl            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 葡萄牙文 - 巴西   | pt-br         | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 葡萄牙文 - 葡萄牙 | pt-pt         | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 羅馬尼亞文              | ro            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 俄文               | ru            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 斯洛伐克文                | sk            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 斯洛維尼亞文             | sl            | ![X 表示否。](./media/user-flow-language-customization/no.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 塞爾維亞文 (斯拉夫)    | sr-cryl-cs    | ![X 表示否。](./media/user-flow-language-customization/no.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 塞爾維亞文 (拉丁)       | sr-latn-cs    | ![X 表示否。](./media/user-flow-language-customization/no.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 瑞典文               | sv            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 坦米爾文                 | ta            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![X 表示否。](./media/user-flow-language-customization/no.png) |
| 泰盧固文                | te            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![X 表示否。](./media/user-flow-language-customization/no.png) |
| 泰文                  | th            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 土耳其文               | tr            | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 烏克蘭文             | uk            | ![X 表示否。](./media/user-flow-language-customization/no.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 越南文            | vi            | ![X 表示否。](./media/user-flow-language-customization/no.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 中文 - 簡體  | zh-hans       | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |
| 中文 - 繁體 | zh-hant       | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) | ![綠色核取記號。](./media/user-flow-language-customization/yes.png) |


## <a name="next-steps"></a>後續步驟

::: zone pivot="b2c-user-flow"

在 Azure Active Directory B2C 中，您可以從 [自訂應用程式的使用者介面](customize-ui-with-html.md)，以取得如何自訂應用程式使用者介面的詳細資訊。

::: zone-end 

::: zone pivot="b2c-custom-policy"

- 深入了解 IEF 參考中的[當地語系化](localization.md)元素。
- 請參閱 Azure AD B2C 中提供的[當地語系化字串識別碼](localization-string-ids.md)清單。

::: zone-end 
