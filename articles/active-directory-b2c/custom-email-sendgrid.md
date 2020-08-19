---
title: 使用 SendGrid 進行自訂電子郵件驗證
titleSuffix: Azure AD B2C
description: 瞭解如何整合 SendGrid，以自訂在使用者註冊以使用您的已啟用 Azure AD B2C 的應用程式時，傳送給客戶的驗證電子郵件。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/18/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 952659746bbb99108c6177166ad60ad2272cbce6
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88584932"
---
# <a name="custom-email-verification-with-sendgrid"></a>使用 SendGrid 進行自訂電子郵件驗證

使用 Azure Active Directory B2C (Azure AD B2C) 中的自訂電子郵件，將自訂電子郵件傳送給註冊以使用您應用程式的使用者。 您可以使用 [DisplayControls](display-controls.md) (目前處於預覽狀態) 和協力廠商電子郵件提供者 SendGrid，使用您自己的電子郵件範本和 *寄件者：* 位址和主旨，以及支援當地語系化和自訂單次密碼 (OTP) 設定。

自訂電子郵件驗證需要使用協力廠商電子郵件提供者，例如 [SendGrid](https://sendgrid.com)、 [Mailjet](https://Mailjet.com)或 [SparkPost](https://sparkpost.com)、自訂 REST API 或任何 HTTP 型電子郵件提供者 (包括您自己的) 。 本文說明如何設定使用 SendGrid 的解決方案。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-sendgrid-account"></a>建立 SendGrid 帳戶

如果您還沒有此帳戶，請從設定 SendGrid 帳戶開始， (Azure 客戶每個月可以將25000的免費電子郵件解除鎖定) 。 如需安裝指示，請參閱[如何使用 SendGrid 搭配 Azure 傳送電子郵件](../sendgrid-dotnet-how-to-send-email.md)的[建立 SendGrid 帳戶](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account)一節。

請務必完成您 [建立 SENDGRID API 金鑰](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key)的區段。 記錄 API 金鑰，以供後續步驟使用。

## <a name="create-azure-ad-b2c-policy-key"></a>建立 Azure AD B2C 原則金鑰

接下來，將 SendGrid API 金鑰儲存在您要參考之原則的 Azure AD B2C 原則金鑰中。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 確定您使用的目錄包含您的 Azure AD B2C 租用戶。 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選擇您的 Azure AD B2C 目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 在 [概觀] 頁面上，選取 [識別體驗架構]。
1. 選取 [原則金鑰]，然後選取 [新增]。
1. 在 [ **選項**] 中選擇 [ **手動**]。
1. 輸入原則金鑰的 [名稱]。 例如： `SendGridSecret` 。 金鑰名稱前面會自動新增前置詞 `B2C_1A_`。
1. 在 [祕密] 中，輸入您先前記錄的用戶端密碼。
1. 針對 [金鑰使用方法] 選取 [簽章]。
1. 選取 [建立]。

## <a name="create-sendgrid-template"></a>建立 SendGrid 範本

建立 SendGrid 帳戶並 SendGrid 儲存在 Azure AD B2C 原則金鑰中的 API 金鑰時，請建立 SendGrid [動態交易式範本](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)。

1. 在 SendGrid 網站上，開啟 [ [交易式範本](https://sendgrid.com/dynamic_templates) ] 頁面，然後選取 [ **建立範本**]。
1. 輸入唯一的範本名稱 `Verification email` ，例如，然後選取 [ **儲存**]。
1. 若要開始編輯新的範本，請選取 [新增 **版本**]。
1. 選取 [程式 **代碼編輯器** ]，然後 **繼續**進行。
1. 在 HTML 編輯器中，貼上下列 HTML 範本，或使用您自己的範本。 `{{otp}}`和 `{{email}}` 參數將會以單次密碼值和使用者電子郵件地址動態取代。

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
       <!-- Template B O365 -->
       <style>
           table td {border-collapse:collapse;margin:0;padding:0;}
       </style>
    </head>
    <body dir="ltr" lang="en">
       <table width="100%" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
            <tr>
               <td valign="top" width="50%"></td>
               <td valign="top">
                  <!-- Email Header -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en" style="border-left:1px solid #e3e3e3;border-right: 1px solid #e3e3e3;">
                   <tr style="background-color: #0072C6;">
                       <td width="1" style="background:#0072C6; border-top:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-top:1px solid #e3e3e3;border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="310" valign="middle" style="border-top:1px solid #e3e3e3; border-bottom:1px solid #e3e3e3;padding:12px 0;">
                           <h1 style="line-height:20pt;font-family:Segoe UI Light; font-size:18pt; color:#ffffff; font-weight:normal;">
                            <span id="HeaderPlaceholder_UserVerificationEmailHeader"><font color="#FFFFFF">Verify your email address</font></span>
                           </h1>
                       </td>
                       <td width="24" style="border-top: 1px solid #e3e3e3;border-bottom: 1px solid #e3e3e3;">&nbsp;</td>
                   </tr>
                  </table>
                  <!-- Email Content -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
                   <tr>
                       <td width="1" style="background:#e3e3e3;"></td>
                       <td width="24">&nbsp;</td>
                       <td id="PageBody" width="640" valign="top" colspan="2" style="border-bottom:1px solid #e3e3e3;padding:10px 0 20px;border-bottom-style:hidden;">
                           <table cellpadding="0" cellspacing="0" border="0">
                               <tr>
                                   <td width="630" style="font-size:10pt; line-height:13pt; color:#000;">
                                       <table cellpadding="0" cellspacing="0" border="0" width="100%" style="" dir="ltr" lang="en">
                                           <tr>
                                               <td>

       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{email}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{otp}}</span>
       </div>
       <br>
       <br>

                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
                                                   Sincerely,
                                                   </div>
                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; font-style:italic; color:#333;">
                                                       Contoso
                                                   </div>
                                               </td>
                                           </tr>
                                       </table>
                                   </td>
                               </tr>
                           </table>

                       </td>

                       <td width="1">&nbsp;</td>
                       <td width="1"></td>
                       <td width="1">&nbsp;</td>
                       <td width="1" valign="top"></td>
                       <td width="29">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3;"></td>
                   </tr>
                   <tr>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td id="PageFooterContainer" width="585" valign="top" colspan="6" style="border-bottom:1px solid #e3e3e3;padding:0px;">


                       </td>

                       <td width="29" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                   </tr>
                  </table>

               </td>
               <td valign="top" width="50%"></td>
           </tr>
       </table>
    <img src="https://mucp.api.account.microsoft.com/m/v2/v?d=AIAACWEPFYXYIUTJIJVV4ST7XLBHVI5MLLYBKJAVXHBDTBHUM5VBSVVPTTVRWDFIXJ5JQTHYOH5TUYIPO4ZAFRFK52UAMIS3UNIPPI7ZJNDZPRXD5VEJBN4H6RO3SPTBS6AJEEAJOUYL4APQX5RJUJOWGPKUABY&amp;i=AIAACL23GD2PFRFEY5YVM2XQLM5YYWMHFDZOCDXUI2B4LM7ETZQO473CVF22PT6WPGR5IIE6TCS6VGEKO5OZIONJWCDMRKWQQVNP5VBYAINF3S7STKYOVDJ4JF2XEW4QQVNHMAPQNHFV3KMR3V3BA4I36B6BO7L4VQUHQOI64EOWPLMG5RB3SIMEDEHPILXTF73ZYD3JT6MYOLAZJG7PJJCAXCZCQOEFVH5VCW2KBQOKRYISWQLRWAT7IINZ3EFGQI2CY2EMK3FQOXM7UI3R7CZ6D73IKDI" width="1" height="1"></body>
    </html>
    ```

1. 展開左側的 [ **設定** ]，然後在 [ **電子郵件**主旨] 中輸入 `{{subject}}` 。
1. 選取 [ **儲存範本**]。
1. 選取 [上一步] 箭號，以返回 [ **交易式範本** ] 頁面。
1. 記錄您為了在後續步驟中所建立的範本 **識別碼** 。 例如： `d-989077fbba9746e89f3f6411f596fb96` 。 當您 [新增宣告轉換](#add-the-claims-transformation)時，可以指定此識別碼。

## <a name="add-azure-ad-b2c-claim-types"></a>新增 Azure AD B2C 宣告類型

在您的原則中，將下列宣告類型新增至 `<ClaimsSchema>` 中的元素 `<BuildingBlocks>` 。

需要這些宣告類型，才能使用單次密碼 (OTP) 碼來產生及驗證電子郵件地址。

```xml
<ClaimType Id="Otp">
  <DisplayName>Secondary One-time password</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="emailRequestBody">
  <DisplayName>SendGrid request body</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="VerificationCode">
  <DisplayName>Secondary Verification Code</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter your email verification code</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

## <a name="add-the-claims-transformation"></a>新增宣告轉換

接下來，您需要宣告轉換來輸出 JSON 字串宣告，此宣告將會是傳送至 SendGrid 的要求主體。

JSON 物件的結構是以輸入參數的點標記法，以及 InputClaims 的 TransformationClaimTypes 來定義。 點標記法中的數位暗示陣列。 這些值來自于 InputClaims 的值和輸入參數 ' "Value" 屬性。 如需 JSON 宣告轉換的詳細資訊，請參閱 [json 宣告轉換](json-transformations.md)。

將下列宣告轉換加入至 `<ClaimsTransformations>` 中的元素 `<BuildingBlocks>` 。 對宣告轉換 XML 進行下列更新：

* `template_id`使用您稍早在[建立 SendGrid 範本](#create-sendgrid-template)中建立的 SENDGRID 交易範本識別碼，更新 >inputparameter 值。
* 更新 `from.email` 位址值。 請使用有效的電子郵件地址，以協助防止驗證電子郵件被標示為垃圾郵件。
* `personalizations.0.dynamic_template_data.subject`使用適用于您組織的主旨行，更新主旨行輸入參數的值。

```xml
<ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
  </InputClaims>
  <InputParameters>
    <!-- Update the template_id value with the ID of your SendGrid template. -->
    <InputParameter Id="template_id" DataType="string" Value="d-989077fbba9746e89f3f6411f596fb96"/>
    <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com"/>
    <!-- Update with a subject line appropriate for your organization. -->
    <InputParameter Id="personalizations.0.dynamic_template_data.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="add-datauri-content-definition"></a>新增 DataUri 內容定義

在內的宣告轉換下方 `<BuildingBlocks>` ，新增下列 [ContentDefinition](contentdefinitions.md) 以參考2.0.0 資料 URI 版本：

```xml
<ContentDefinitions>
 <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="create-a-displaycontrol"></a>建立控制項

驗證顯示控制項是用來驗證傳送給使用者之驗證碼的電子郵件地址。

此範例顯示控制項設定為：

1. `email`從使用者收集位址宣告類型。
1. 等候使用者提供 `verificationCode` 傳送給使用者的程式碼給宣告類型。
1. 返回 `email` 自我判斷技術設定檔，其具有此顯示控制項的參考。
1. 使用 `SendCode` 動作來產生 otp 代碼，並將具有 otp 程式碼的電子郵件傳送給使用者。

![傳送驗證碼電子郵件動作](media/custom-email-sendgrid/display-control-verification-email-action-01.png)

在 [內容定義] 下的 [仍在] 中 `<BuildingBlocks>` ，將下列類型的 [ [VerificationControl](display-control-verification.md) ] 加入至您[的](display-controls.md)原則。

```xml
<DisplayControls>
  <DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
    <DisplayClaims>
      <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
      <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
    </DisplayClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="email" />
    </OutputClaims>
    <Actions>
      <Action Id="SendCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendOtp" />
        </ValidationClaimsExchange>
      </Action>
      <Action Id="VerifyCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
        </ValidationClaimsExchange>
      </Action>
    </Actions>
  </DisplayControl>
</DisplayControls>
```

## <a name="add-otp-technical-profiles"></a>新增 OTP 技術設定檔

`GenerateOtp`技術設定檔會產生電子郵件地址的程式碼。 `VerifyOtp`技術設定檔會驗證與電子郵件地址相關聯的程式碼。 您可以變更格式的設定以及一次性密碼的到期時間。 如需 OTP 技術設定檔的詳細資訊，請參閱 [定義一次性密碼技術設定檔](one-time-password-technical-profile.md)。

將下列技術設定檔新增至 `<ClaimsProviders>` 元素。

```xml
<ClaimsProvider>
  <DisplayName>One time password technical profiles</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="GenerateOtp">
      <DisplayName>Generate one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">GenerateCode</Item>
        <Item Key="CodeExpirationInSeconds">1200</Item>
        <Item Key="CodeLength">6</Item>
        <Item Key="CharacterSet">0-9</Item>
        <Item Key="ReuseSameCode">true</Item>
        <Item Key="MaxNumAttempts">5</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="otp" PartnerClaimType="otpGenerated" />
      </OutputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="VerifyOtp">
      <DisplayName>Verify one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">VerifyCode</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="otpToVerify" />
      </InputClaims>
    </TechnicalProfile>
   </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="add-a-rest-api-technical-profile"></a>新增 REST API 技術設定檔

此 REST API 技術設定檔會使用) 的 SendGrid 格式產生電子郵件內容 (。 如需 RESTful 技術設定檔的詳細資訊，請參閱 [定義 RESTful 技術設定檔](restful-technical-profile.md)。

如同 OTP 技術設定檔，請將下列技術設定檔新增至 `<ClaimsProviders>` 元素。

```xml
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SendOtp">
      <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">emailRequestBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridSecret" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateEmailRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="emailRequestBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>建立控制項的參考

在最後一個步驟中，將參考新增至您所建立的控制項。 以下列程式取代您現有 `LocalAccountSignUpWithLogonEmail` 和自我判斷的 `LocalAccountDiscoveryUsingEmailAddress` 技術設定檔。 如果您使用舊版的 Azure AD B2C 原則。 這些技術設定檔會 `DisplayClaims` 與控制項的參考一起使用。

如需詳細資訊，請參閱 [自我判斷技術設定檔](restful-technical-profile.md) 和顯示 [控制項](display-controls.md)。

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceed the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceed the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
        <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      </DisplayClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="LocalAccountDiscoveryUsingEmailAddress">
      <Metadata>
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceed the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceed the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
      </DisplayClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="optional-localize-your-email"></a>參數將您的電子郵件當地語系化

若要將電子郵件當地語系化，您必須將當地語系化的字串傳送至 SendGrid 或您的電子郵件提供者。 例如，您可以當地語系化電子郵件主旨、主體、程式碼訊息或電子郵件的簽章。 若要這樣做，您可以使用 [GetLocalizedStringsTransformation](string-transformations.md) 宣告轉換，將當地語系化的字串複製到宣告類型。 `GenerateEmailRequestBody`宣告轉換會產生 JSON 承載，並使用包含當地語系化字串的輸入宣告。

1. 在您的原則中，定義下列字串宣告： subject、message、codeIntro 和 signature。
1. 定義 [GetLocalizedStringsTransformation](string-transformations.md) 宣告轉換，以將當地語系化的字串值取代為步驟1中的宣告。
1. 將 `GenerateEmailRequestBody` 宣告轉換變更為使用輸入宣告與下列 XML 程式碼片段。
1. 更新您的 SendGrid 範本，以使用動態參數取代將由 Azure AD B2C 當地語系化的所有字串。

    ```xml
    <ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
        <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
        <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
        <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
        <InputClaim ClaimTypeReferenceId="subject" TransformationClaimType="personalizations.0.dynamic_template_data.subject" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
        <InputClaim ClaimTypeReferenceId="message" TransformationClaimType="personalizations.0.dynamic_template_data.message" />
        <InputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="personalizations.0.dynamic_template_data.codeIntro" />
        <InputClaim ClaimTypeReferenceId="signature" TransformationClaimType="personalizations.0.dynamic_template_data.signature" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="template_id" DataType="string" Value="d-1234567890" />
        <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
    ```

1. 新增下列 [當地語系化](localization.md) 元素。

    ```xml
    <Localization Enabled="true">
      <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
        <SupportedLanguage>en</SupportedLanguage>
        <SupportedLanguage>es</SupportedLanguage>
      </SupportedLanguages>
      <LocalizedResources Id="api.custom-email.en">
        <LocalizedStrings>
          <!--Email template parameters-->
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for validating the account</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
        </LocalizedStrings>
      </LocalizedResources>
      <LocalizedResources Id="api.custom-email.es">
        <LocalizedStrings>
          <!--Email template parameters-->
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sinceramente</LocalizedString>
        </LocalizedStrings>
      </LocalizedResources>
    </Localization>
    ```

1. 藉由更新 [ContentDefinitions](contentdefinitions.md) 元素，加入 LocalizedResources 元素的參考。

    ```XML
    <ContentDefinitions>
      <ContentDefinition Id="api.localaccountsignup">
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
        <LocalizedResourcesReferences MergeBehavior="Prepend">
          <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
          <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
        </LocalizedResourcesReferences>
      </ContentDefinition>
      <ContentDefinition Id="api.localaccountpasswordreset">
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
        <LocalizedResourcesReferences MergeBehavior="Prepend">
          <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
          <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
        </LocalizedResourcesReferences>
      </ContentDefinition>
    </ContentDefinitions>
    ```

1. 最後，將下列輸入宣告轉換新增至 `LocalAccountSignUpWithLogonEmail` 和 `LocalAccountDiscoveryUsingEmailAddress` 技術設定檔。

    ```XML
    <InputClaimsTransformations>
      <InputClaimsTransformation ReferenceId="GetLocalizedStringsForEmail" />
    </InputClaimsTransformations>
    ```

## <a name="next-steps"></a>後續步驟

您可以在 GitHub 上找到自訂電子郵件驗證原則的範例：

- [自訂電子郵件驗證-DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)
- 如需使用自訂 REST API 或任何 HTTP 型 SMTP 電子郵件提供者的詳細資訊，請參閱 [在 Azure AD B2C 自訂原則中定義 RESTful 技術設定檔](restful-technical-profile.md)。
