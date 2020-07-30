---
title: 當地語系化字串識別碼 - Azure Active Directory B2C | Microsoft Docs
description: 在 Azure Active Directory B2C 的自訂原則中，以 signuporsignin.xml 的識別碼指定內容定義的識別碼。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 611d676f5f588ff32f981692456160e269642a43
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87428124"
---
# <a name="localization-string-ids"></a>當地語系化字串識別碼

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Localization** 元素可讓您在適用於使用者旅程圖的原則中支援多個地區設定或語言。 此文章提供您可以在原則中使用的當地語系化識別碼清單。 若要熟悉 UI 當地語系化，請參閱[當地語系化](localization.md)。

## <a name="sign-up-or-sign-in-page-elements"></a>註冊或登入頁面元素

下列識別碼會用於識別碼為的內容定義 `api.signuporsignin` 。

| 識別碼 | 預設值 |
| -- | ------------- |
| **local_intro_email** | 使用您的現有帳戶登入 |
| **logonIdentifier_email** | 電子郵件地址 |
| **requiredField_email** | 請輸入您的電子郵件 |
| **invalid_email** | 請輸入有效的電子郵件地址 |
| **email_pattern** | ^ [a-zA-Z0-9.！# $% & ' '*+/=？ ^ _ \` { \| } ~-] + @ [a-zA-Z0-9-] + （？： \\ . [a-zA-Z0-9-] +）*$ |
| **local_intro_username** | 使用您的使用者名稱登入 |
| **logonIdentifier_username** | 使用者名稱 |
| **requiredField_username** | 請輸入您的使用者名稱 |
| **password** | 密碼 |
| **requiredField_password** | 請輸入您的密碼 |
| **invalid_password** | 您輸入的密碼未採用預期的格式。 |
| **forgotpassword_link** | 忘記密碼了嗎? |
| **createaccount_intro** | 還沒有帳戶嗎？ |
| **createaccount_link** | 立即註冊 |
| **divider_title** | OR |
| **cancel_message** | 使用者忘記密碼 |
| **button_signin** | 登入 |
| **social_intro** | 使用您的社交帳戶登入 |
  **remember_me** |讓我保持登入|
| **unknown_error** | 我們無法將您登入。 請稍後再試一次。 |

下列範例示範在註冊或登入頁面中使用某些使用者介面元素的方式：

![註冊或登入頁面 UX 元素](./media/localization-string-ids/localization-susi.png)

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
| **UserMessageIfClaimsPrincipalDoesNotExist** | 找不到您的帳戶。 |
| **UserMessageIfOldPasswordUsed** | 您使用的似乎是舊密碼。 |
| **DefaultMessage** | 無效的使用者名稱或密碼。 |
| **UserMessageIfUserAccountDisabled** | 您的帳戶已遭鎖定。 請連絡您的支援人員將其解除鎖定，然後再試一次。 |
| **UserMessageIfUserAccountLocked** | 您的帳戶已暫時鎖定以防未經授權的使用。 請稍後再試一次。 |
| **AADRequestsThrottled** | 目前的要求過多。 請稍後再重試。 |

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>註冊和自我判斷頁面使用者介面元素

以下是適用於識別碼為 `api.localaccountsignup` 之內容定義的識別碼，或任何開頭為 `api.selfasserted` 之內容定義的識別碼，例如 `api.selfasserted.profileupdate` 和 `api.localaccountpasswordreset`。

| 識別碼 | 預設值 |
| -- | ------------- |
| **ver_sent** | 已將驗證碼傳送至： |
| **ver_but_default** | 預設 |
| **cancel_message** | 使用者已取消輸入自動判斷的資訊 |
| **preloader_alt** | 請稍候 |
| **ver_but_send** | 傳送驗證碼 |
| **alert_yes** | 是 |
| **error_fieldIncorrect** | 有一或多個欄位填寫不正確。 請檢查您的輸入，然後再試一次。 |
| **year** | 年 |
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

### <a name="sign-up-and-self-asserted-pages-error-messages"></a>註冊和自我判斷分頁錯誤訊息

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

![已標記其 UI 元素名稱的註冊頁面](./media/localization-string-ids/localization-sign-up.png)

下列範例示範在使用者按一下傳送驗證碼按鈕之後，於註冊頁面中使用某些使用者介面元素的方式：

![註冊頁面電子郵件驗證 UX 元素](./media/localization-string-ids/localization-email-verification.png)


## <a name="phone-factor-authentication-page-user-interface-elements"></a>電話要素驗證頁面使用者介面元素

以下為適用於識別碼為 `api.phonefactor` 之內容定義的識別碼。

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
| **countryList** | 請參閱[國家/地區清單](#countries-list)。 |
| **error_448** | 提供的手機號碼不通。 |
| **error_449** | 使用者已超過重試次數限制。 |
| **verification_code_input_placeholder_text** | 驗證碼 |

下列範例示範在 MFA 註冊頁面中使用某些使用者介面元素的方式：

![註冊頁面電子郵件驗證 UX 元素](./media/localization-string-ids/localization-mfa1.png)

下列範例示範在 MFA 驗證頁面中使用某些使用者介面元素的方式：

![註冊頁面電子郵件驗證 UX 元素](./media/localization-string-ids/localization-mfa2.png)

## <a name="verification-display-control-user-interface-elements"></a>驗證顯示控制項使用者介面元素

以下是[驗證顯示控制項](display-control-verification.md)的識別碼

| 識別碼 | 預設值 |
| -- | ------------- |
|verification_control_but_change_claims |變更 |
|verification_control_fail_send_code |無法傳送程式碼，請稍後再試一次。 |
|verification_control_fail_verify_code |無法驗證程式代碼，請稍後再試一次。 |
|verification_control_but_send_code |傳送驗證碼 |
|verification_control_but_send_new_code |傳送新程式碼 |
|verification_control_but_verify_code |驗證碼 |
|verification_control_code_sent| 已傳送驗證碼。 請將其複製到下方輸入方塊。 |

### <a name="example"></a>範例

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

以下是[Restful 服務技術設定檔](restful-technical-profile.md)錯誤訊息的識別碼：

| 識別碼 | 預設值 |
| -- | ------------- |
|DefaultUserMessageIfRequestFailed | 無法建立與 restful 服務端點的連接。 Restful 服務 URL：{0} |
|UserMessageIfCircuitOpen | {0}Restful 服務 URL：{1} |
|UserMessageIfDnsResolutionFailed | 無法解析 restful 服務端點的主機名稱。 Restful 服務 URL：{0} |
|UserMessageIfRequestTimeout | 無法在超時限制秒內建立與 restful 服務端點的連接 {0} 。 Restful 服務 URL：{1} |


### <a name="example"></a>範例

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

## <a name="azure-mfa-error-messages"></a>Azure MFA 錯誤訊息

以下是[AZURE MFA 技術設定檔](multi-factor-auth-technical-profile.md)錯誤訊息的識別碼：

| 識別碼 | 預設值 |
| -- | ------------- |
|UserMessageIfCouldntSendSms | 無法傳送 SMS 到手機，請嘗試另一個電話號碼。 |
|UserMessageIfInvalidFormat | 您的電話號碼格式無效，請更正後再試一次。|
|UserMessageIfMaxAllowedCodeRetryReached | 輸入錯誤的程式碼太多次，請稍後再試一次。|
|UserMessageIfServerError | 無法使用 MFA 服務，請稍後再試一次。|
|UserMessageIfThrottled | 您的要求已進行節流處理，請稍後再試一次。|
|UserMessageIfWrongCodeEntered|輸入的代碼錯誤，請再試一次。|

### <a name="example"></a>範例

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

以下是[AZURE AD SSPR 技術設定檔](aad-sspr-technical-profile.md)錯誤訊息的識別碼：

| 識別碼 | 預設值 |
| -- | ------------- |
|UserMessageIfChallengeExpired | 程式碼已過期。|
|UserMessageIfInternalError | 電子郵件服務發生內部錯誤，請稍後再試一次。|
|UserMessageIfThrottled | 您傳送了太多要求，請稍後再試一次。|
|UserMessageIfVerificationFailedNoRetry | 您已超過驗證嘗試次數的上限。|
|UserMessageIfVerificationFailedRetryAllowed | 驗證失敗，請再試一次。|


### <a name="example"></a>範例

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

## <a name="one-time-password-error-messages"></a>一次密碼錯誤訊息

以下是[一次性密碼技術設定檔](one-time-password-technical-profile.md)錯誤訊息的識別碼

| 識別碼 | 預設值 |
| -- | ------------- |
|UserMessageIfMaxRetryAttempted |一次密碼提供的驗證已超過嘗試次數上限 |
|UserMessageIfSessionDoesNotExist |一次密碼驗證會話已過期 |
|UserMessageIfSessionConflict |單次密碼驗證會話發生衝突 |
|UserMessageIfInvalidCode |提供給驗證的一次密碼不正確 |
|UserMessageIfVerificationFailedRetryAllowed |該驗證碼不正確。 請再試一次。 | 

### <a name="example"></a>範例

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceed the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceed the number of retries allowed.</LocalizedString>
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
|UserMessageIfClaimsTransformationBooleanValueIsNotEqual |[AssertBooleanClaimIsEqualToValue](boolean-transformations.md#assertbooleanclaimisequaltovalue) | 宣告類型 "inputClaim" 的布林宣告值比較失敗。| 
|DateTimeGreaterThan |[AssertDateTimeIsGreaterThan](date-transformations.md#assertdatetimeisgreaterthan) | 宣告值比較失敗：提供的左運算元大於右運算元。|
|UserMessageIfClaimsTransformationStringsAreNotEqual |[AssertStringClaimsAreEqual](string-transformations.md#assertstringclaimsareequal) | 使用 StringComparison "OrdinalIgnoreCase" 的宣告值比較失敗。|

### <a name="example"></a>範例

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Your email address hasn't been verified.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DateTimeGreaterThan">Expiration date must be greater that the current date.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationStringsAreNotEqual">The email entry fields do not match. Please enter the same email address in both fields and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="countries-list"></a>國家/地區清單

以下是多重要素驗證所使用的 countryList 值。

```JSON
{"DEFAULT":"Country/Region","AF":"Afghanistan","AX":"Åland Islands","AL":"Albania","DZ":"Algeria","AS":"American Samoa","AD":"Andorra","AO":"Angola","AI":"Anguilla","AQ":"Antarctica","AG":"Antigua and Barbuda","AR":"Argentina","AM":"Armenia","AW":"Aruba","AU":"Australia","AT":"Austria","AZ":"Azerbaijan","BS":"Bahamas","BH":"Bahrain","BD":"Bangladesh","BB":"Barbados","BY":"Belarus","BE":"Belgium","BZ":"Belize","BJ":"Benin","BM":"Bermuda","BT":"Bhutan","BO":"Bolivia","BQ":"Bonaire","BA":"Bosnia and Herzegovina","BW":"Botswana","BV":"Bouvet Island","BR":"Brazil","IO":"British Indian Ocean Territory","VG":"British Virgin Islands","BN":"Brunei","BG":"Bulgaria","BF":"Burkina Faso","BI":"Burundi","CV":"Cabo Verde","KH":"Cambodia","CM":"Cameroon","CA":"Canada","KY":"Cayman Islands","CF":"Central African Republic","TD":"Chad","CL":"Chile","CN":"China","CX":"Christmas Island","CC":"Cocos (Keeling) Islands","CO":"Colombia","KM":"Comoros","CG":"Congo","CD":"Congo (DRC)","CK":"Cook Islands","CR":"Costa Rica","CI":"Côte d'Ivoire","HR":"Croatia","CU":"Cuba","CW":"Curaçao","CY":"Cyprus","CZ":"Czech Republic","DK":"Denmark","DJ":"Djibouti","DM":"Dominica","DO":"Dominican Republic","EC":"Ecuador","EG":"Egypt","SV":"El Salvador","GQ":"Equatorial Guinea","ER":"Eritrea","EE":"Estonia","ET":"Ethiopia","FK":"Falkland Islands","FO":"Faroe Islands","FJ":"Fiji","FI":"Finland","FR":"France","GF":"French Guiana","PF":"French Polynesia","TF":"French Southern Territories","GA":"Gabon","GM":"Gambia","GE":"Georgia","DE":"Germany","GH":"Ghana","GI":"Gibraltar","GR":"Greece","GL":"Greenland","GD":"Grenada","GP":"Guadeloupe","GU":"Guam","GT":"Guatemala","GG":"Guernsey","GN":"Guinea","GW":"Guinea-Bissau","GY":"Guyana","HT":"Haiti","HM":"Heard Island and McDonald Islands","HN":"Honduras","HK":"Hong Kong SAR","HU":"Hungary","IS":"Iceland","IN":"India","ID":"Indonesia","IR":"Iran","IQ":"Iraq","IE":"Ireland","IM":"Isle of Man","IL":"Israel","IT":"Italy","JM":"Jamaica","JP":"Japan","JE":"Jersey","JO":"Jordan","KZ":"Kazakhstan","KE":"Kenya","KI":"Kiribati","KR":"Korea","KW":"Kuwait","KG":"Kyrgyzstan","LA":"Laos","LV":"Latvia","LB":"Lebanon","LS":"Lesotho","LR":"Liberia","LY":"Libya","LI":"Liechtenstein","LT":"Lithuania","LU":"Luxembourg","MO":"Macao SAR","MK":"North Macedonia","MG":"Madagascar","MW":"Malawi","MY":"Malaysia","MV":"Maldives","ML":"Mali","MT":"Malta","MH":"Marshall Islands","MQ":"Martinique","MR":"Mauritania","MU":"Mauritius","YT":"Mayotte","MX":"Mexico","FM":"Micronesia","MD":"Moldova","MC":"Monaco","MN":"Mongolia","ME":"Montenegro","MS":"Montserrat","MA":"Morocco","MZ":"Mozambique","MM":"Myanmar","NA":"Namibia","NR":"Nauru","NP":"Nepal","NL":"Netherlands","NC":"New Caledonia","NZ":"New Zealand","NI":"Nicaragua","NE":"Niger","NG":"Nigeria","NU":"Niue","NF":"Norfolk Island","KP":"North Korea","MP":"Northern Mariana Islands","NO":"Norway","OM":"Oman","PK":"Pakistan","PW":"Palau","PS":"Palestinian Authority","PA":"Panama","PG":"Papua New Guinea","PY":"Paraguay","PE":"Peru","PH":"Philippines","PN":"Pitcairn Islands","PL":"Poland","PT":"Portugal","PR":"Puerto Rico","QA":"Qatar","RE":"Réunion","RO":"Romania","RU":"Russia","RW":"Rwanda","BL":"Saint Barthélemy","KN":"Saint Kitts and Nevis","LC":"Saint Lucia","MF":"Saint Martin","PM":"Saint Pierre and Miquelon","VC":"Saint Vincent and the Grenadines","WS":"Samoa","SM":"San Marino","ST":"São Tomé and Príncipe","SA":"Saudi Arabia","SN":"Senegal","RS":"Serbia","SC":"Seychelles","SL":"Sierra Leone","SG":"Singapore","SX":"Sint Maarten","SK":"Slovakia","SI":"Slovenia","SB":"Solomon Islands","SO":"Somalia","ZA":"South Africa","GS":"South Georgia and South Sandwich Islands","SS":"South Sudan","ES":"Spain","LK":"Sri Lanka","SH":"St Helena, Ascension, Tristan da Cunha","SD":"Sudan","SR":"Suriname","SJ":"Svalbard","SZ":"Swaziland","SE":"Sweden","CH":"Switzerland","SY":"Syria","TW":"Taiwan","TJ":"Tajikistan","TZ":"Tanzania","TH":"Thailand","TL":"Timor-Leste","TG":"Togo","TK":"Tokelau","TO":"Tonga","TT":"Trinidad and Tobago","TN":"Tunisia","TR":"Turkey","TM":"Turkmenistan","TC":"Turks and Caicos Islands","TV":"Tuvalu","UM":"U.S. Outlying Islands","VI":"U.S. Virgin Islands","UG":"Uganda","UA":"Ukraine","AE":"United Arab Emirates","GB":"United Kingdom","US":"United States","UY":"Uruguay","UZ":"Uzbekistan","VU":"Vanuatu","VA":"Vatican City","VE":"Venezuela","VN":"Vietnam","WF":"Wallis and Futuna","YE":"Yemen","ZM":"Zambia","ZW":"Zimbabwe"}
```

## <a name="next-steps"></a>後續步驟

如需當地語系化範例，請參閱下列文章：

- [在 Azure Active Directory B2C 中使用自訂原則進行語言自訂](custom-policy-localization.md)
- [在 Azure Active Directory B2C 中使用使用者流程進行語言自訂](user-flow-language-customization.md)