---
title: Twilio 使用 Azure Active Directory B2C 驗證應用程式
titleSuffix: Azure AD B2C
description: 瞭解如何使用 Twilio Verify API，在 Azure AD B2C 中整合範例線上付款應用程式。 符合 PSD2 (付款服務指示 2) 透過動態連結和強式客戶驗證的交易需求。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 840d2afa72de290d5534adc766f8634efa6926e8
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86170049"
---
# <a name="integrating-twilio-verify-app-with-azure-active-directory-b2c"></a>整合 Twilio Verify 應用程式與 Azure Active Directory B2C

在本逐步解說中，您將瞭解如何使用 Twilio Verify API，將 Azure Active Directory B2C (Azure AD B2C) 中的範例線上付款應用程式整合在一起。 藉由使用 Twilio 驗證應用程式，Azure AD B2C 客戶可以透過動態連結和強式客戶驗證，符合 PSD2 (付款服務指示詞 2) 交易需求。

## <a name="prerequisites"></a>必要條件

若要開始使用，您需要：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 連結至您 Azure 訂用帳戶的[Azure AD B2C 租](tutorial-create-tenant.md)使用者。
* [試用帳戶](https://www.twilio.com/try-twilio)，網址為 Twilio。

## <a name="scenario-description"></a>案例描述

下列元件構成 Twilio 解決方案：

- .NET PSD2 示範 web 應用程式，可提供登入或註冊的能力，以及執行虛擬高風險交易。
- Azure AD B2C 合併的登入和註冊原則。
- Azure AD B2C 使用與 Twilio 驗證 API 整合的原則 `id_token_hint` 。
- .NET Web 應用程式，裝載 `.well-known` OpenIdConnect 端點以允許驗證 `id_token_hint` 。


    ![twilio 流程](media/partner-twilio/twilio-flow.png)

| 步驟 | 描述 |
|------|------|
| 1     | 使用者會起始登入或註冊 PSD2 示範應用程式。 使用者會透過 Azure AD B2C 合併登入和註冊原則來進行驗證。 權杖會傳回給應用程式。 在註冊時，會使用 SMS/Phone 驗證使用者的電話號碼，並記錄在其 Azure AD B2C 帳戶上。     |
| 2     | 使用者會起始高風險的交易，例如 $50.00 的傳輸。 使用者目前的存取權杖會針對 PolicyId 進行評估，以判斷使用者是否已經透過「逐步執行自訂原則」進行驗證。     |
| 3     | 應用程式會記錄交易值和收款人（$50.00 和 John Doe），並產生已簽署的權杖。 這個 token 稱為 `id_token_hint` ，並包含宣告 `amount:$500, payee:john doe` 。 `id_token_hint`會隨著要求一起傳送至 Azure AD B2C 的自訂原則，該原則會與 Twilio 整合。     |
| 4     | Azure AD B2C 藉由檢查應用程式 OpenId Connect 端點來驗證 id_token_hint 的簽章 `/.well-known` 。 驗證之後，它會從這個權杖中解壓縮宣告，特別是 `amount` 和 `payee` 。 使用者會看到一個頁面，以透過 SMS 訊息驗證其行動電話號碼。     |
| 5     | 使用者透過 SMS 訊息要求驗證其電話號碼，並 Azure AD B2C 對 Twilio 的驗證 API 端點提出 REST API 要求。 它也會傳送交易 `amount` 和 `payee` 作為 PSD2 流程的一部分，以產生 (OTP) 的一次性密碼。 Twilio 會將 SMS 訊息傳送給使用者的已註冊電話號碼。     |
| 6     |  使用者輸入 SMS 訊息中收到的 OTP，然後將它提交給 Azure AD B2C。 Azure AD B2C 使用此 OTP 對 Twilio 的驗證 API 提出 API 要求，以確認 OTP 是否正確。 最後，會向應用程式發出權杖，並以新的 PolicyId 來表示使用者已逐步執行其驗證。    |
|      |      |

## <a name="onboard-with-twilio"></a>使用 Twilio 上架

1. 取得[試用帳戶](https://www.twilio.com/try-twilio)，網址為 Twilio。

2. 在 Twilio 購買電話號碼，如[這篇文章](https://support.twilio.com/hc/articles/223135247-How-to-Search-for-and-Buy-a-Twilio-Phone-Number-from-Console)所述

3. 流覽至 Twilio 主控台的 [[驗證 API](https://www.twilio.com/console/verify/services) ]，並依照[指示](https://www.twilio.com/docs/verify/verifying-transactions-psd2)來建立服務並啟用 PSD2 選項。  

## <a name="configure-the-psd2-demo-app"></a>設定 PSD2 示範應用程式

1. 開啟 B2C-WebAPI-DotNet 解決方案，並將下列值取代為 web.config 中您自己的租使用者特定值：

    ```xml
   <add key="ida:Tenant" value="yourtenant.onmicrosoft.com" />
   <add key="ida:TenantId" value="d6f33888-0000-4c1f-9b50-1590f171fc70" />
   <add key="ida:ClientId" value="6bd98cc8-0000-446a-a05e-b5716ef2651b" />
   <add key="ida:ClientSecret" value="secret" />
   <add key="ida:AadInstance" value="https://yourtenant.b2clogin.com/tfp/{0}/{1}" />
   <add key="ida:RedirectUri" value="https://your hosted psd2 demo app url/" />
   ```

2. Web 應用程式也會裝載 ID token 提示產生器和中繼資料端點。
   - 建立您的簽署憑證，如本[範例說明](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#creating-a-signing-certificate)中所述。
   - 根據您在 web.config 中的憑證，更新下列程式程式碼：
   
   ```xml
   <add key="ida:SigningCertThumbprint" value="4F39D6014818082CBB763E5BA5F230E545212E89" />
   <add key="ida:SigningCertAlgorithm" value="RS256" />
   ```

3. 將示範應用程式上傳至您選擇的主控提供者。  [本範例說明](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#hosting-the-application-in-azure-app-service)中會提供 Azure App Service 的指引，包括上傳憑證的指示。

4. 新增對應至裝載應用程式之 URL 的回復 URL，以更新您的 Azure AD B2C 應用程式註冊。

5. 開啟原則檔案，並將的所有實例取代  `contoso` 為您的租使用者名稱。

6. 尋找 Twilio REST API 技術設定檔 **自訂-SMS-註冊**。 將更新為  `ServiceURL`   您的 Twilio AccountSID，並將 [寄件者數] 設為您購買的電話號碼。

7. 尋找 Twilio REST API 技術設定檔、 **TwilioRestAPI-驗證-步驟 1**   和 **TwilioRestAPI-verify-步驟 2**，並  `ServiceURL`   使用您的 Twilio AccountSID 更新。

## <a name="integrate-with-azure-ad-b2c"></a>與 Azure AD B2C 整合

將原則檔案新增至 Azure AD B2C：

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。

2. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。

3. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。

4. 流覽至**Azure AD B2C**  >  **Identity Experience Framework**  >  **原則金鑰**]。

5. 新增名為**B2cRestTwilioClientId**的新機碼。 選取 [ **手動**]，並提供 Twilio AccountSID 的值。

6. 新增名為**B2cRestTwilioClientSecret**的新機碼。 選取 [ **手動**]，並提供 TWILIO AUTH TOKEN 的值。

7. 將所有原則檔案上傳到您的租使用者。

8. 在 GenerateOTPMessageEnrol 宣告轉換中，為您的註冊 SMS 文字自訂字串。

## <a name="test-the-solution"></a>測試解決方案

* 流覽至您的應用程式，並測試登入、註冊和傳送 Money 動作。

## <a name="next-steps"></a>後續步驟

如需其他資訊，請參閱下列文章：

- 如需[Twilio 整合程式碼範例](https://github.com/azure-ad-b2c/samples/tree/master/policies/twilio-mfa-psd2)，請參閱 GitHub  

- [AAD B2C 中的自訂原則](custom-policy-overview.md)

- [開始使用 AAD B2C 中的自訂原則](custom-policy-get-started.md?tabs=applications)
