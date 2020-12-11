---
title: 當地語系化字串識別碼 - Azure Active Directory B2C | Microsoft Docs
description: 在 Azure Active Directory B2C 的自訂原則中，使用 signuporsignin.xml 的識別碼指定內容定義的識別碼。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 11/09/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 81a1263d0eacbffa77e2e35e4594e23235394183
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108666"
---
# <a name="localization-string-ids"></a>當地語系化字串識別碼

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Localization** 元素可讓您在適用於使用者旅程圖的原則中支援多個地區設定或語言。 此文章提供您可以在原則中使用的當地語系化識別碼清單。 若要熟悉 UI 當地語系化，請參閱[當地語系化](localization.md)。

## <a name="sign-up-or-sign-in-page-elements"></a>註冊或登入頁面元素

下列識別碼會用於識別碼為的內容定義 `api.signuporsignin` ，以及自我判斷的 [技術設定檔](self-asserted-technical-profile.md)。

| 識別碼 | 預設值 | 頁面配置版本 |
| -- | ------------- | ------ |
| **forgotpassword_link** | 忘記密碼了嗎? | `All` |
| **createaccount_intro** | 還沒有帳戶嗎？ | `All` |
| **button_signin** | 登入 | `All` |
| **social_intro** | 使用您的社交帳戶登入 | `All` |
| **remember_me** |讓我保持登入。 | `All` |
| **unknown_error** | 我們無法將您登入。 請稍後再試一次。 | `All` |
| **divider_title** | 或者 | `All` |
| **local_intro_email** | 使用您的現有帳戶登入 | `< 2.0.0` |
| **logonIdentifier_email** | 電子郵件地址 | `< 2.0.0` |
| **requiredField_email** | 請輸入您的電子郵件 | `< 2.0.0` |
| **invalid_email** | 請輸入有效的電子郵件地址 | `< 2.0.0` |
| **email_pattern** | ^ [a-zA-Z0-9.！# $% & ' ' \* +/=？ ^ \_ \` { \| } ~-] + @ [Z0-9-] + (？： \\ . [a-zA-Z0-9-] +) \* $ |`< 2.0.0` |
| **local_intro_username** | 使用您的使用者名稱登入 | `< 2.0.0` |
| **logonIdentifier_username** | 使用者名稱 | `< 2.0.0` |
| **requiredField_username** | 請輸入您的使用者名稱 | `< 2.0.0` |
| **password** | 密碼 | `< 2.0.0` |
| **requiredField_password** | 請輸入您的密碼 | `< 2.0.0` |
| **createaccount_link** | 立即註冊 | `< 2.0.0` |
| **cancel_message** | 使用者忘記密碼 | `< 2.0.0` |
| **invalid_password** | 您輸入的密碼未採用預期的格式。 | `< 2.0.0` |
| **createaccount_one_link** | 立即註冊 | `>= 2.0.0` |
| **createaccount_two_links** | 使用或註冊 {0}{1} | `>= 2.0.0` |
| **createaccount_three_links** | 使用 {0} 、或註冊 {1}{2} | `>= 2.0.0` |
| **local_intro_generic** | 使用您的 {0} | `>= 2.1.0` |
| **requiredField_generic** | 請輸入您的 {0} | `>= 2.1.0` |
| **invalid_generic** | 請輸入有效的 {0} | `>= 2.1.1` |
| **標題** | 登入 | `>= 2.1.1` |


> [!NOTE]
> * 這類預留位置 {0} 將會自動填入的 `DisplayName` 值 `ClaimType` 。 
> * 若要瞭解如何當地語系化 `ClaimType` ，請參閱 [註冊或登入範例](#signupsigninexample)。

下列範例示範在註冊或登入頁面中使用某些使用者介面元素的方式：

:::image type="content" source="./media/localization-string-ids/localization-susi-2.png" alt-text="顯示註冊或登入頁面 U X 元素的螢幕擷取畫面。":::

### <a name="sign-up-or-sign-in-identity-providers"></a>註冊或登入身分識別提供者

在使用者旅程圖 **ClaimsExchange** 元素中設定識別提供者的識別碼。 若要將識別提供者的標題當地語系化，請將 **ElementType** 設定為 `ClaimsProvider`，同時將 **StringId** 設定為 `ClaimsExchange` 的識別碼。

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="MicrosoftExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccount" />
  </ClaimsExchanges>
</OrchestrationStep>
```

下列範例會將 Facebook 識別提供者當地語系化為阿拉伯文：

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

### <a name="sign-up-or-sign-in-error-messages"></a>註冊或登入錯誤訊息

| 識別碼 | 預設值 |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | 您的密碼不正確。 |
| **UserMessageIfPasswordExpired**| 您的密碼過期。|
| **UserMessageIfClaimsPrincipalDoesNotExist** | 找不到您的帳戶。 |
| **UserMessageIfOldPasswordUsed** | 您使用的似乎是舊密碼。 |
| **DefaultMessage** | 無效的使用者名稱或密碼。 |
| **UserMessageIfUserAccountDisabled** | 您的帳戶已遭鎖定。 請連絡您的支援人員將其解除鎖定，然後再試一次。 |
| **UserMessageIfUserAccountLocked** | 您的帳戶已暫時鎖定以防未經授權的使用。 請稍後再試。 |
| **AADRequestsThrottled** | 目前的要求過多。 請稍後再重試。 |

<a name="signupsigninexample"></a>
### <a name="sign-up-or-sign-in-example"></a>註冊或登入範例

```xml
<LocalizedResources Id="api.signuporsignin.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email Address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="heading">Sign in</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="social_intro">Sign in with your social account</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="local_intro_generic">Sign in with your {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_password">Please enter your password</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_generic">Please enter your {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_generic">Please enter a valid {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_one_link">Sign up now</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_two_links">Sign up with {0} or {1}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_three_links">Sign up with {0}, {1}, or {2}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">Forgot your password?</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_signin">Sign in</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="divider_title">OR</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="unknown_error">We are having trouble signing you in. Please try again later.</LocalizedString>
    <!-- Uncomment the remember_me only if the keep me signed in is activated. 
    <LocalizedString ElementType="UxElement" StringId="remember_me">Keep me signed in</LocalizedString> -->
    <LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">Your password is incorrect.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfPasswordExpired">Your password has expired.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">Looks like you used an old password.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">Invalid username or password.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>註冊和自我判斷頁面使用者介面元素

以下是內容定義的識別碼，其識別碼為 `api.localaccountsignup` 或開頭為的任何內容定義 `api.selfasserted` ，例如 `api.selfasserted.profileupdate` 和 `api.localaccountpasswordreset` ，以及 [自我判斷技術設定檔](self-asserted-technical-profile.md)。

| 識別碼 | 預設值 |
| -- | ------------- |
| **ver_sent** | 已將驗證碼傳送至： |
| **ver_but_default** | 預設 |
| **cancel_message** | 使用者已取消輸入自動判斷的資訊 |
| **preloader_alt** | 請稍候 |
| **ver_but_send** | 傳送驗證碼 |
| **alert_yes** | 是 |
| **error_fieldIncorrect** | 有一或多個欄位填寫不正確。 請檢查您的輸入，然後再試一次。 |
| **year** | Year |
| **verifying_blurb** | 正在處理您的資訊，請稍候。 |
| **button_cancel** | 取消 |
| **ver_fail_no_retry** | 您已嘗試太多次，但都不正確。 請稍後再試一次。 |
| **month** | Month |
| **ver_success_msg** | 已驗證電子郵件地址。 您現在可以繼續。 |
| **months** | 一月、二月、三月、四月、五月、六月、七月、八月、九月、十月、十一月、十二月 |
| **ver_fail_server** | 我們無法驗證您的電子郵件地址。 請輸入有效的電子郵件地址，然後再試一次。 |
| **error_requiredFieldMissing** | 遺漏必要欄位。 請填寫所有必要欄位，然後再試一次。 |
| **initial_intro** | 請提供下列詳細資料。 |
| **ver_but_resend** | 傳送新的驗證碼 |
| **button_continue** | 建立 |
| **error_passwordEntryMismatch** | 密碼輸入欄位不相符。 請在兩個欄位中輸入相同密碼，然後再試一次。 |
| **ver_incorrect_format** | 格式不正確。 |
| **ver_but_edit** | 變更電子郵件 |
| **ver_but_verify** | 檢查驗證碼 |
| **alert_no** | 否 |
| **ver_info_msg** | 驗證碼已經傳送到您的收件匣。 請將其複製到下方輸入方塊。 |
| **day** | 天 |
| **ver_fail_throttled** | 驗證此電子郵件地址時有過多要求。 請稍候，然後再試一次。 |
| **helplink_text** | 這是什麼？ |
| **ver_fail_retry** | 該驗證碼不正確。 請再試一次。 |
| **alert_title** | 取消輸入您的詳細資料 |
| **required_field** | 此為必要資訊。 |
| **alert_message** | 確定要取消輸入您的詳細資料嗎? |
| **ver_intro_msg** | 必須進行驗證。 請按一下 [傳送] 按鈕。 |
| **ver_input** | 驗證碼 |

### <a name="sign-up-and-self-asserted-pages-error-messages"></a>註冊和自我判斷頁面的錯誤訊息

| 識別碼 | 預設值 |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | 已存在具有所指定識別碼的使用者。 請選擇不同的識別碼。 |
| **UserMessageIfClaimNotVerified** | 宣告未驗證: {0} |
| **UserMessageIfIncorrectPattern** | 以下項目的模式不正確: {0} |
| **UserMessageIfMissingRequiredElement** | 遺失必要元素: {0} |
| **UserMessageIfValidationError** | 驗證時的錯誤，依: {0} |
| **UserMessageIfInvalidInput** | {0} 的輸入無效。 |
| **ServiceThrottled** | 目前的要求過多。 請稍後再重試。 |

下列範例示範在註冊頁面中使用某些使用者介面元素的方式：

![標記為其 UI 元素名稱的註冊頁面](./media/localization-string-ids/localization-sign-up.png)

下列範例示範在使用者按一下傳送驗證碼按鈕之後，於註冊頁面中使用某些使用者介面元素的方式：

![註冊頁面電子郵件驗證 UX 元素](./media/localization-string-ids/localization-email-verification.png)

## <a name="sign-up-and-self-asserted-pages-example"></a>註冊和自我判斷頁面範例

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email Address</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Email address that can be used to contact you.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">New Password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">Enter new password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">Confirm New Password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">Confirm new password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">Display Name</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">Your display name.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">Surname</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">Your surname (also known as family name or last name).</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">Given Name</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">Your given name (also known as first name).</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">A required field is missing. Please fill out all required fields and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="helplink_text">What is this?</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="initial_intro">Please provide the following details.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="preloader_alt">Please wait</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="required_field">This information is required.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_edit">Change e-mail</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_resend">Send new code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_send">Send verification code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_verify">Verify code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">That code is expired. Please request a new code.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">You've made too many incorrect attempts. Please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">That code is incorrect. Please try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_server">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_info_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_input">Verification code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_success_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">Claim not verified: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">A user with the specified ID already exists. Please choose a different one.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">Incorrect pattern for: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">{0} has invalid input.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">Missing required element: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">Error in validation by: {0}</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="phone-factor-authentication-page-user-interface-elements"></a>電話要素驗證頁面使用者介面元素

以下是識別碼為之內容定義的識別碼 `api.phonefactor` ，以及 [電話係數技術設定檔](phone-factor-technical-profile.md)。

| 識別碼 | 預設值 |
| -- | ------------- |
| **button_verify** | 撥電話給我 |
| **country_code_label** | 國碼 (地區碼) |
| **cancel_message** | 使用者已取消多重要素驗證 |
| **text_button_send_second_code** | 傳送新的驗證碼 |
| **code_pattern** | \\d{6} |
| **intro_mixed** | 我們為您記錄了下列號碼。 我們將會透過簡訊傳送代碼以對您進行驗證。 |
| **intro_mixed_p** | 我們為您記錄了下列號碼。 請選擇一組電話號碼來讓我們傳送驗證碼簡訊或撥打，從而驗證您的身分。 |
| **button_verify_code** | 驗證碼 |
| **requiredField_code** | 請輸入您收到的驗證碼 |
| **invalid_code** | 請輸入您收到的6位數代碼 |
| **button_cancel** | 取消 |
| **local_number_input_placeholder_text** | 電話號碼 |
| **button_retry** | 重試 |
| **alternative_text** | 我的手機不在身邊 |
| **intro_phone_p** | 我們為您記錄了下列號碼。 請選擇一組電話號碼來讓我們撥打，從而驗證您的身分。 |
| **intro_phone** | 我們為您記錄了下列號碼。 我們將撥打電話來驗證您的身分。 |
| **enter_code_text_intro** | 請在下方輸入您的驗證碼，或  |
| **intro_entry_phone** | 請在下方輸入一組電話號碼來讓我們撥打，從而驗證您的身分。 |
| **intro_entry_sms** | 請在下方輸入一組電話號碼來讓我們傳送驗證碼簡訊，從而驗證您的身分。 |
| **button_send_code** | 傳送驗證碼 |
| **invalid_number** | 請輸入有效的電話號碼 |
| **intro_sms** | 我們為您記錄了下列號碼。 我們將會透過簡訊傳送代碼以對您進行驗證。 |
| **intro_entry_mixed** | 請在下方輸入一組電話號碼來讓我們傳送驗證碼簡訊或撥打，從而驗證您的身分。 |
| **number_pattern** | ^\\+(?:[0-9][\\x20-]?){6,14}[0-9]$ |
| **intro_sms_p** |我們為您記錄了下列號碼。 請選擇可以簡訊傳送驗證碼的號碼，以驗證您的身分。 |
| **requiredField_countryCode** | 請選取您的國碼 (地區碼) |
| **requiredField_number** | 請輸入您的電話號碼 |
| **country_code_input_placeholder_text** |國家或區域 |
| **number_label** | 電話號碼 |
| **error_tryagain** | 提供的電話號碼目前忙線中或不通。 請檢查號碼，然後再試一次。 |
| **error_incorrect_code** | 您輸入的驗證碼與我們的記錄不符。 請再試一次，或要求新的驗證碼。 |
| **countryList** | 請參閱 [國家/地區清單](#phone-factor-authentication-page-example)。 |
| **error_448** | 提供的手機號碼不通。 |
| **error_449** | 使用者已超過重試次數限制。 |
| **verification_code_input_placeholder_text** | 驗證碼 |

下列範例示範在 MFA 註冊頁面中使用某些使用者介面元素的方式：

![電話要素驗證註冊 UX 元素](./media/localization-string-ids/localization-mfa1.png)

下列範例示範在 MFA 驗證頁面中使用某些使用者介面元素的方式：

![電話要素驗證驗證 UX 元素](./media/localization-string-ids/localization-mfa2.png)

## <a name="phone-factor-authentication-page-example"></a>電話要素驗證頁面範例

```xml
<LocalizedResources Id="api.phonefactor.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="button_verify">Call Me</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="country_code_label">Country Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="cancel_message">The user has canceled multi-factor authentication</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="text_button_send_second_code">Send a new code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="code_pattern">\d{6}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_mixed">We have the following number on record for you. We can send a code via SMS or phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_mixed_p">We have the following numbers on record for you. Choose a number that we can phone or send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_code">Please enter the verification code you received</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_code">Please enter the 6-digit code you received</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="local_number_input_placeholder_text">Phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_retry">Retry</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="alternative_text">I don't have my phone</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_phone_p">We have the following numbers on record for you. Choose a number that we can phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_phone">We have the following number on record for you. We will phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="enter_code_text_intro">Enter your verification code below, or</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_phone">Enter a number below that we can phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_sms">Enter a number below that we can send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_number">Please enter a valid phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_sms">We have the following number on record for you. We will send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_mixed">Enter a number below that we can send a code via SMS or phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="number_pattern">^\+(?:[0-9][\x20-]?){6,14}[0-9]$</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_sms_p">We have the following numbers on record for you. Choose a number that we can send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_countryCode">Please select your country code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_number">Please enter your phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="country_code_input_placeholder_text">Country or region</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="number_label">Phone Number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_tryagain">The phone number you provided is busy or unavailable. Please check the number and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_incorrect_code">The verification code you have entered does not match our records. Please try again, or request a new code.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="countryList">{"DEFAULT":"Country/Region","AF":"Afghanistan","AX":"Åland Islands","AL":"Albania","DZ":"Algeria","AS":"American Samoa","AD":"Andorra","AO":"Angola","AI":"Anguilla","AQ":"Antarctica","AG":"Antigua and Barbuda","AR":"Argentina","AM":"Armenia","AW":"Aruba","AU":"Australia","AT":"Austria","AZ":"Azerbaijan","BS":"Bahamas","BH":"Bahrain","BD":"Bangladesh","BB":"Barbados","BY":"Belarus","BE":"Belgium","BZ":"Belize","BJ":"Benin","BM":"Bermuda","BT":"Bhutan","BO":"Bolivia","BQ":"Bonaire","BA":"Bosnia and Herzegovina","BW":"Botswana","BV":"Bouvet Island","BR":"Brazil","IO":"British Indian Ocean Territory","VG":"British Virgin Islands","BN":"Brunei","BG":"Bulgaria","BF":"Burkina Faso","BI":"Burundi","CV":"Cabo Verde","KH":"Cambodia","CM":"Cameroon","CA":"Canada","KY":"Cayman Islands","CF":"Central African Republic","TD":"Chad","CL":"Chile","CN":"China","CX":"Christmas Island","CC":"Cocos (Keeling) Islands","CO":"Colombia","KM":"Comoros","CG":"Congo","CD":"Congo (DRC)","CK":"Cook Islands","CR":"Costa Rica","CI":"Côte d'Ivoire","HR":"Croatia","CU":"Cuba","CW":"Curaçao","CY":"Cyprus","CZ":"Czech Republic","DK":"Denmark","DJ":"Djibouti","DM":"Dominica","DO":"Dominican Republic","EC":"Ecuador","EG":"Egypt","SV":"El Salvador","GQ":"Equatorial Guinea","ER":"Eritrea","EE":"Estonia","ET":"Ethiopia","FK":"Falkland Islands","FO":"Faroe Islands","FJ":"Fiji","FI":"Finland","FR":"France","GF":"French Guiana","PF":"French Polynesia","TF":"French Southern Territories","GA":"Gabon","GM":"Gambia","GE":"Georgia","DE":"Germany","GH":"Ghana","GI":"Gibraltar","GR":"Greece","GL":"Greenland","GD":"Grenada","GP":"Guadeloupe","GU":"Guam","GT":"Guatemala","GG":"Guernsey","GN":"Guinea","GW":"Guinea-Bissau","GY":"Guyana","HT":"Haiti","HM":"Heard Island and McDonald Islands","HN":"Honduras","HK":"Hong Kong SAR","HU":"Hungary","IS":"Iceland","IN":"India","ID":"Indonesia","IR":"Iran","IQ":"Iraq","IE":"Ireland","IM":"Isle of Man","IL":"Israel","IT":"Italy","JM":"Jamaica","JP":"Japan","JE":"Jersey","JO":"Jordan","KZ":"Kazakhstan","KE":"Kenya","KI":"Kiribati","KR":"Korea","KW":"Kuwait","KG":"Kyrgyzstan","LA":"Laos","LV":"Latvia","LB":"Lebanon","LS":"Lesotho","LR":"Liberia","LY":"Libya","LI":"Liechtenstein","LT":"Lithuania","LU":"Luxembourg","MO":"Macao SAR","MK":"North Macedonia","MG":"Madagascar","MW":"Malawi","MY":"Malaysia","MV":"Maldives","ML":"Mali","MT":"Malta","MH":"Marshall Islands","MQ":"Martinique","MR":"Mauritania","MU":"Mauritius","YT":"Mayotte","MX":"Mexico","FM":"Micronesia","MD":"Moldova","MC":"Monaco","MN":"Mongolia","ME":"Montenegro","MS":"Montserrat","MA":"Morocco","MZ":"Mozambique","MM":"Myanmar","NA":"Namibia","NR":"Nauru","NP":"Nepal","NL":"Netherlands","NC":"New Caledonia","NZ":"New Zealand","NI":"Nicaragua","NE":"Niger","NG":"Nigeria","NU":"Niue","NF":"Norfolk Island","KP":"North Korea","MP":"Northern Mariana Islands","NO":"Norway","OM":"Oman","PK":"Pakistan","PW":"Palau","PS":"Palestinian Authority","PA":"Panama","PG":"Papua New Guinea","PY":"Paraguay","PE":"Peru","PH":"Philippines","PN":"Pitcairn Islands","PL":"Poland","PT":"Portugal","PR":"Puerto Rico","QA":"Qatar","RE":"Réunion","RO":"Romania","RU":"Russia","RW":"Rwanda","BL":"Saint Barthélemy","KN":"Saint Kitts and Nevis","LC":"Saint Lucia","MF":"Saint Martin","PM":"Saint Pierre and Miquelon","VC":"Saint Vincent and the Grenadines","WS":"Samoa","SM":"San Marino","ST":"São Tomé and Príncipe","SA":"Saudi Arabia","SN":"Senegal","RS":"Serbia","SC":"Seychelles","SL":"Sierra Leone","SG":"Singapore","SX":"Sint Maarten","SK":"Slovakia","SI":"Slovenia","SB":"Solomon Islands","SO":"Somalia","ZA":"South Africa","GS":"South Georgia and South Sandwich Islands","SS":"South Sudan","ES":"Spain","LK":"Sri Lanka","SH":"St Helena, Ascension, Tristan da Cunha","SD":"Sudan","SR":"Suriname","SJ":"Svalbard","SZ":"Swaziland","SE":"Sweden","CH":"Switzerland","SY":"Syria","TW":"Taiwan","TJ":"Tajikistan","TZ":"Tanzania","TH":"Thailand","TL":"Timor-Leste","TG":"Togo","TK":"Tokelau","TO":"Tonga","TT":"Trinidad and Tobago","TN":"Tunisia","TR":"Turkey","TM":"Turkmenistan","TC":"Turks and Caicos Islands","TV":"Tuvalu","UM":"U.S. Outlying Islands","VI":"U.S. Virgin Islands","UG":"Uganda","UA":"Ukraine","AE":"United Arab Emirates","GB":"United Kingdom","US":"United States","UY":"Uruguay","UZ":"Uzbekistan","VU":"Vanuatu","VA":"Vatican City","VE":"Venezuela","VN":"Vietnam","WF":"Wallis and Futuna","YE":"Yemen","ZM":"Zambia","ZW":"Zimbabwe"}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_448">The phone number you provided is unreachable.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_449">User has exceeded the number of retry attempts.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_code_input_placeholder_text">Verification code</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="strongAuthenticationPhoneNumber" StringId="DisplayName">Phone Number</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>

```

## <a name="verification-display-control-user-interface-elements"></a>驗證顯示控制項使用者介面元素

以下是具有[頁面配置版本](page-layout.md)2.1.0 或更高版本之[驗證顯示控制項](display-control-verification.md)的識別碼。

| 識別碼 | 預設值 |
| -- | ------------- |
|intro_msg| 必須進行驗證。 請按一下 [傳送] 按鈕。|
|success_send_code_msg | 驗證碼已經傳送到您的收件匣。 請將其複製到下方輸入方塊。|
|failure_send_code_msg | 我們無法驗證您的電子郵件地址。 請輸入有效的電子郵件地址，然後再試一次。|
|success_verify_code_msg | 已驗證電子郵件地址。 您現在可以繼續。|
|failure_verify_code_msg | 我們無法驗證您的電子郵件地址。 請再試一次。|
|but_send_code | 傳送驗證碼|
|but_verify_code | 檢查驗證碼|
|but_send_new_code | 傳送新的驗證碼|
|but_change_claims | 變更電子郵件|

### <a name="verification-display-control-example"></a>驗證顯示控制項範例

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
   <!-- Display control UI elements-->
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_send_code_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_send_code_msg">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_verify_code_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_verify_code_msg">We are having trouble verifying your email address. Please try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_code">Send verification code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_verify_code">Verify code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_new_code">Send new code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_change_claims">Change e-mail</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="verification-display-control-user-interface-elements-deprecated"></a> (已淘汰的驗證顯示控制項使用者介面元素) 

以下是具有[頁面配置版本](page-layout.md)2.0.0 之[驗證顯示控制項](display-control-verification.md)的識別碼。

| 識別碼 | 預設值 |
| -- | ------------- |
|verification_control_but_change_claims |變更 |
|verification_control_fail_send_code |無法傳送程式碼，請稍後再試一次。 |
|verification_control_fail_verify_code |無法驗證程式代碼，請稍後再試一次。 |
|verification_control_but_send_code |傳送驗證碼 |
|verification_control_but_send_new_code |傳送新的程式碼 |
|verification_control_but_verify_code |驗證碼 |
|verification_control_code_sent| 已傳送驗證碼。 請將其複製到下方輸入方塊。 |

### <a name="verification-display-control-example-deprecated"></a> (已淘汰的驗證顯示控制項範例) 

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_change_claims">Change</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_send_code">Failed to send the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_verify_code">Failed to verify the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_new_code">Send New Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_code_sent">Verification code has been sent. Please copy it to the input box below.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="restful-service-error-messages"></a>Restful 服務錯誤訊息

以下是 [Restful 服務技術設定檔](restful-technical-profile.md) 錯誤訊息的識別碼：

| 識別碼 | 預設值 |
| -- | ------------- |
|DefaultUserMessageIfRequestFailed | 無法建立與 restful 服務端點的連接。 Restful 服務 URL： {0} |
|UserMessageIfCircuitOpen | {0} Restful 服務 URL： {1} |
|UserMessageIfDnsResolutionFailed | 無法解析 restful 服務端點的主機名稱。 Restful 服務 URL： {0} |
|UserMessageIfRequestTimeout | 無法在超時限制秒內建立與 restful 服務端點的連接 {0} 。 Restful 服務 URL： {1} |


### <a name="restful-service-example"></a>Restful 服務範例

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="DefaultUserMessageIfRequestFailed">Failed to establish connection to restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCircuitOpen">Unable to connect to the restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfDnsResolutionFailed">Failed to resolve the hostname of the restful service endpoint.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfRequestTimeout">Failed to establish connection to restful service end point within timeout limit.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-ad-mfa-error-messages"></a>Azure AD MFA 錯誤訊息

以下是 [AZURE AD MFA 技術設定檔](multi-factor-auth-technical-profile.md) 錯誤訊息的識別碼：

| 識別碼 | 預設值 |
| -- | ------------- |
|UserMessageIfCouldntSendSms | 無法將 SMS 傳送至電話，請嘗試另一個電話號碼。 |
|UserMessageIfInvalidFormat | 您的電話號碼格式不正確，請更正它，然後再試一次。|
|UserMessageIfMaxAllowedCodeRetryReached | 輸入錯誤的程式碼太多次，請稍後再試一次。|
|UserMessageIfServerError | 無法使用 MFA 服務，請稍後再試一次。|
|UserMessageIfThrottled | 已對您的要求進行節流處理，請稍後再試一次。|
|UserMessageIfWrongCodeEntered|輸入了錯誤的代碼，請再試一次。|

### <a name="azure-ad-mfa-example"></a>Azure AD MFA 範例

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCouldntSendSms">Cannot Send SMS to the phone, please try another phone number.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidFormat">Your phone number is not in a valid format, please correct it and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxAllowedCodeRetryReached">Wrong code entered too many times, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfServerError">Cannot use MFA service, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">Your request has been throttled, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfWrongCodeEntered">Wrong code entered, please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-ad-sspr"></a>Azure AD SSPR

以下是 [AZURE AD SSPR 技術設定檔](aad-sspr-technical-profile.md) 錯誤訊息的識別碼：

| 識別碼 | 預設值 |
| -- | ------------- |
|UserMessageIfChallengeExpired | 程式碼已過期。|
|UserMessageIfInternalError | 電子郵件服務發生內部錯誤，請稍後再試一次。|
|UserMessageIfThrottled | 您傳送了太多要求，請稍後再試一次。|
|UserMessageIfVerificationFailedNoRetry | 您已超過驗證嘗試次數上限。|
|UserMessageIfVerificationFailedRetryAllowed | 驗證失敗，請再試一次。|


### <a name="azure-ad-sspr-example"></a>Azure AD SSPR 範例

```XML
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInternalError">We are having trouble verifying your email address. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfChallengeExpired">That code is expired. Please request a new code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedNoRetry">You've made too many incorrect attempts. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="one-time-password-error-messages"></a>單次密碼錯誤訊息

以下是 [一次性密碼技術設定檔](one-time-password-technical-profile.md) 錯誤訊息的識別碼

| 識別碼 | 預設值 |
| -- | ------------- |
|UserMessageIfMaxRetryAttempted |一次提供的密碼驗證超過嘗試次數上限 |
|UserMessageIfSessionDoesNotExist |單次密碼驗證會話已過期 |
|UserMessageIfSessionConflict |單次密碼驗證會話發生衝突 |
|UserMessageIfInvalidCode |提供驗證的一次密碼不正確 |
|UserMessageIfVerificationFailedRetryAllowed |該驗證碼不正確。 請再試一次。 | 

### <a name="one-time-password-example"></a>一次性密碼範例

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="claims-transformations-error-messages"></a>宣告轉換錯誤訊息

以下是宣告轉換錯誤訊息的識別碼：

| 識別碼 | 宣告轉換 | 預設值 |
| -- | ------------- |------------- |
|UserMessageIfClaimsTransformationBooleanValueIsNotEqual |[AssertBooleanClaimIsEqualToValue](boolean-transformations.md#assertbooleanclaimisequaltovalue) | 宣告類型 "inputClaim" 的布林值宣告值比較失敗。| 
|DateTimeGreaterThan |[AssertDateTimeIsGreaterThan](date-transformations.md#assertdatetimeisgreaterthan) | 宣告值比較失敗：提供的左運算元大於右運算元。|
|UserMessageIfClaimsTransformationStringsAreNotEqual |[AssertStringClaimsAreEqual](string-transformations.md#assertstringclaimsareequal) | 使用 StringComparison "OrdinalIgnoreCase" 進行宣告值比較失敗。|

### <a name="claims-transformations-example"></a>宣告轉換範例

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Your email address hasn't been verified.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DateTimeGreaterThan">Expiration date must be greater that the current date.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationStringsAreNotEqual">The email entry fields do not match. Please enter the same email address in both fields and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="next-steps"></a>後續步驟

請參閱下列文章以取得當地語系化範例：

- [在 Azure Active Directory B2C 中使用自訂原則進行語言自訂](language-customization.md)
- [使用 Azure Active Directory B2C 中的使用者流程進行語言自訂](language-customization.md)
