---
title: 自訂電子郵件驗證
titleSuffix: Azure AD B2C
description: 瞭解如何自訂在客戶註冊使用啟用 Azure AD B2C 的應用程式時發送給客戶的驗證電子郵件。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6cc0508a63f26b955ac5e0ebf3ef58a184a35997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671638"
---
# <a name="custom-email-verification-in-azure-active-directory-b2c"></a>Azure 活動目錄 B2C 中的自訂電子郵件驗證

使用 Azure 活動目錄 B2C（Azure AD B2C）中的自訂電子郵件向註冊使用應用程式的使用者發送自訂電子郵件。 通過使用[DisplayControls（](display-controls.md)當前預覽版）和協力廠商電子郵件供應商，您可以使用自己的電子郵件範本和*From：* 位址和主題，以及支援當地語系化和自訂一次性密碼 （OTP） 設置。

自訂電子郵件驗證需要使用協力廠商電子郵件供應商（如[SendGrid](https://sendgrid.com)或[SparkPost、](https://sparkpost.com)自訂 REST API 或任何基於 HTTP 的電子郵件供應商（包括您自己的）。 本文介紹設置使用 SendGrid 的解決方案。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-sendgrid-account"></a>建立 SendGrid 帳戶

如果還沒有，請先設置 SendGrid 帳戶（Azure 客戶每月可以解鎖 25，000 封免費電子郵件）。 有關設置說明，請參閱創建"如何使用 Azure 使用[SendGrid 發送電子郵件](../sendgrid-dotnet-how-to-send-email.md)的["發送網格帳戶"](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account)部分。

請務必完成[創建 SendGrid API 金鑰](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key)的部分。 記錄 API 金鑰，以便在後面的步驟中使用。

## <a name="create-azure-ad-b2c-policy-key"></a>創建 Azure AD B2C 策略金鑰

接下來，將 SendGrid API 金鑰存儲在 Azure AD B2C 策略金鑰中，供策略參考。

1. 登錄到 Azure[門戶](https://portal.azure.com/)。
1. 請確保使用的是包含 Azure AD B2C 租戶的目錄。 選擇頂部功能表中的**目錄 + 訂閱**篩選器，然後選擇 Azure AD B2C 目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
1. 在 [概觀] 頁面上，選取 [識別體驗架構]****。
1. 選取 [原則金鑰]****，然後選取 [新增]****。
1. 針對 [選項]**** 選擇 `Manual`。
1. 輸入原則金鑰的 [名稱]****。 例如： `SendGridSecret` 。 金鑰名稱前面會自動新增前置詞 `B2C_1A_`。
1. 在 [祕密]**** 中，輸入您先前記錄的用戶端密碼。
1. 針對 [金鑰使用方法]****，選取 `Signature`。
1. 選取 [建立]****。

## <a name="create-sendgrid-template"></a>創建發送網格範本

使用創建 SendGrid 帳戶並將 SendGrid API 金鑰存儲在 Azure AD B2C 策略金鑰中，創建 SendGrid[動態事務範本](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)。

1. 在 SendGrid 網站上，打開[事務範本](https://sendgrid.com/dynamic_templates)頁面並選擇"**創建範本**"。
1. 輸入一個唯一的`Verification email`範本名稱，然後選擇 **"保存**"。
1. 要開始編輯新範本，請選擇"**添加版本**"。
1. 選擇**代碼編輯器**，然後**繼續**。
1. 在 HTML 編輯器中，粘貼以下 HTML 範本或使用您自己的範本。 和`{{otp}}``{{email}}`參數將動態替換為一次性密碼值和使用者電子郵件地址。

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

1. 展開左側的**設置**，對於 **"電子郵件主題"，** 請`{{subject}}`輸入 。
1. 選擇 **"保存範本**"。
1. 通過選擇後退箭頭返回到**事務範本**頁面。
1. 記錄在後面的步驟中創建的範本的**ID。** 例如： `d-989077fbba9746e89f3f6411f596fb96` 。 在[添加聲明轉換](#add-the-claims-transformation)時指定此 ID。

## <a name="add-azure-ad-b2c-claim-types"></a>添加 Azure AD B2C 聲明類型

在策略中，將以下聲明類型添加到 中`<ClaimsSchema>``<BuildingBlocks>`的元素。

使用一次性密碼 （OTP） 代碼生成和驗證電子郵件地址，需要這些聲明類型。

```XML
<ClaimType Id="Otp">
  <DisplayName>Secondary One-time password</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="sendGridReqBody">
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

## <a name="add-the-claims-transformation"></a>添加聲明轉換

接下來，您需要聲明轉換來輸出 JSON 字串聲明，該聲明將成為發送到 SendGrid 的請求的正文。

JSON 物件的結構由輸入參數的點符號中的指示和輸入聲明的轉換聲明類型定義。 點標記法中的數位表示陣列。 這些值來自輸入要求的值和輸入參數的"值"屬性。 有關 JSON 聲明轉換的詳細資訊，請參閱[JSON 聲明轉換](json-transformations.md)。

將以下聲明轉換添加到`<ClaimsTransformations>``<BuildingBlocks>`中的元素。 對聲明轉換 XML 進行以下更新：

* 使用之前`template_id`在[創建 SendGrid 範本](#create-sendgrid-template)中創建的 SendGrid 事務範本的 ID 更新輸入參數值。
* 更新`from.email`位址值。 使用有效的電子郵件地址説明防止驗證電子郵件被標記為垃圾郵件。
* 使用適合您的組織的主題行`personalizations.0.dynamic_template_data.subject`更新主題行輸入參數的值。

```XML
<ClaimsTransformation Id="GenerateSendGridRequestBody" TransformationMethod="GenerateJson">
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
    <OutputClaim ClaimTypeReferenceId="sendGridReqBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="add-datauri-content-definition"></a>添加 DataUri 內容定義

下面的聲明轉換在`<BuildingBlocks>`中添加以下內容[定義](contentdefinitions.md)以引用版本 2.0.0 資料 URI：

```XML
<ContentDefinitions>
 <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="create-a-displaycontrol"></a>創建顯示控制

驗證顯示控制項用於使用發送給使用者的驗證碼驗證電子郵件地址。

此示例顯示控制項配置為：

1. 從使用者`email`收集位址聲明類型。
1. 等待使用者提供`verificationCode`聲明類型，併發送到使用者的代碼。
1. `email`返回回具有對此顯示控制項引用的自斷言技術設定檔。
1. 使用`SendCode`該操作，生成 OTP 代碼，並向使用者發送帶有 OTP 代碼的電子郵件。

![發送驗證碼電子郵件操作](media/custom-email/display-control-verification-email-action-01.png)

在內容定義下，仍在`<BuildingBlocks>`中，向策略添加以下類型的["驗證控制"](display-control-verification.md)的[顯示控制](display-controls.md)。

```XML
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
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
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

## <a name="add-otp-technical-profiles"></a>添加 OTP 技術設定檔

技術`GenerateOtp`設定檔生成電子郵件地址的代碼。 技術`VerifyOtp`設定檔驗證與電子郵件地址關聯的代碼。 您可以更改格式的配置和一次性密碼的過期。 有關 OTP 技術設定檔的詳細資訊，請參閱[定義一次性密碼技術設定檔](one-time-password-technical-profile.md)。

將以下技術設定檔添加到元素`<ClaimsProviders>`。

```XML
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

## <a name="add-a-rest-api-technical-profile"></a>添加 REST API 技術設定檔

此 REST API 技術設定檔生成電子郵件內容（使用 SendGrid 格式）。 有關 RESTful 技術設定檔的詳細資訊，請參閱[定義 RESTful 技術設定檔](restful-technical-profile.md)。

與 OTP 技術設定檔一樣，向`<ClaimsProviders>`元素添加以下技術設定檔。

```XML
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SendGrid">
      <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridSecret" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>引用顯示控制項

在最後一步中，添加對您創建的顯示控制項的引用。 如果使用早期版本的`LocalAccountSignUpWithLogonEmail`Azure AD B2C 策略，請將現有自斷言技術設定檔替換為以下內容。 此技術設定檔與`DisplayClaims`顯示器控制一起使用。

有關詳細資訊，請參閱[自斷言技術設定檔](restful-technical-profile.md)和[顯示控制](display-controls.md)。

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <DisplayName>Email signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
        <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
        <Item Key="language.button_continue">Create</Item>
        
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceed the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceed the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
        <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      </DisplayClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" Required="true" />
        <OutputClaim ClaimTypeReferenceId="objectId" />
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" />
        <OutputClaim ClaimTypeReferenceId="newUser" />
      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
      </ValidationTechnicalProfiles>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="optional-localize-your-email"></a>[可選]當地語系化您的電子郵件

要當地語系化電子郵件，您必須向 SendGrid 或電子郵件供應商發送當地語系化字串。 例如，當地語系化電子郵件主題、正文、代碼消息或電子郵件簽名。 為此，可以使用["獲取當地語系化字串轉換"](string-transformations.md)聲明轉換將當地語系化字串複製到聲明類型中。 在`GenerateSendGridRequestBody`生成 JSON 負載的聲明轉換中，使用包含當地語系化字串的輸入聲明。

1. 在策略中定義以下字串聲明：主題、消息、代碼簡介和簽名。
1. 定義[獲取當地語系化字串轉換](string-transformations.md)聲明轉換，以從步驟 1 替換當地語系化的字串值到聲明中。
1. 更改`GenerateSendGridRequestBody`聲明轉換以將輸入聲明與以下 XML 程式碼片段一起使用。
1. 更新 SendGrind 範本以使用動態參數代替 Azure AD B2C 當地語系化的所有字串。

```XML
<ClaimsTransformation Id="GenerateSendGridRequestBody" TransformationMethod="GenerateJson">
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
    <OutputClaim ClaimTypeReferenceId="sendGridReqBody" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="next-steps"></a>後續步驟

您可以在 GitHub 上找到自訂電子郵件驗證策略的示例：

[自訂電子郵件驗證 - 顯示控制](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)

有關使用自訂 REST API 或任何基於 HTTP 的 SMTP 電子郵件提供程式的資訊，請參閱[在 Azure AD B2C 自訂策略中定義 RESTful 技術設定檔](restful-technical-profile.md)。
