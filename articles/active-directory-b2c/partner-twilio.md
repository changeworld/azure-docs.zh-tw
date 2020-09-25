---
title: Twilio 使用 Azure Active Directory B2C 驗證應用程式
titleSuffix: Azure AD B2C
description: 瞭解如何使用 Twilio Verify API，在 Azure AD B2C 中整合範例 online 付款應用程式。 透過動態連結和強式客戶驗證，遵守 PSD2 (付款服務指示詞 2) 交易需求。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 953653a758577ed3d48ca2d81403b4cb363ea294
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91259063"
---
# <a name="integrating-twilio-verify-app-with-azure-active-directory-b2c"></a>整合 Twilio 驗證應用程式與 Azure Active Directory B2C

在本逐步解說中，您將瞭解如何使用 Twilio Verify API，在 Azure Active Directory B2C (Azure AD B2C) 中整合範例 online 付款應用程式。 藉由使用 Twilio Verify App，Azure AD B2C 客戶可以透過動態連結和強式客戶驗證，遵守 PSD2 (付款服務指示詞 2) 交易需求。

## <a name="prerequisites"></a>Prerequisites

若要開始使用，您需要：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 連結至 Azure 訂用帳戶的[Azure AD B2C 租](tutorial-create-tenant.md)使用者。
* Twilio 上的 [試用帳戶](https://www.twilio.com/try-twilio) 。

## <a name="scenario-description"></a>案例描述

下列元件構成 Twilio 方案：

- .NET [PSD2 示範 web 應用程式](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App)，可提供登入或註冊的能力，以及執行虛擬高風險的交易。
- Azure AD B2C 合併登 [入和註冊原則](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy)。
- Azure AD B2C 與 Twilio Verify API 搭配使用的原則 `id_token_hint` 。
- .NET Web 應用程式，裝載 `.well-known` OpenIdConnect 端點以允許驗證 `id_token_hint` 。


    ![twilio 流程](media/partner-twilio/twilio-flow.png)

| 步驟 | 說明 |
|------|------|
| 1     | 使用者起始登入或註冊 PSD2 示範應用程式。 使用者會透過 Azure AD B2C 合併登入和註冊原則進行驗證。 權杖會傳回至應用程式。 註冊時，會使用 SMS/Phone 驗證使用者的電話號碼，並記錄在其 Azure AD B2C 帳戶上。     |
| 2     | 使用者起始高風險的交易，例如傳送 $50.00。 使用者目前的存取權杖會針對 PolicyId 進行評估，以判斷使用者是否已通過逐步執行自訂原則驗證。     |
| 3     | 應用程式會記錄交易值和簽署人（$50.00 和 John Doe），並產生已簽署的權杖。 此權杖稱為 `id_token_hint` ，並且包含宣告 `amount:$500, payee:john doe` 。 `id_token_hint`會連同要求一起傳送至與 Twilio 整合的 Azure AD B2C 自訂原則。     |
| 4     | Azure AD B2C 藉由檢查應用程式 OpenId Connect 端點來驗證 id_token_hint 的簽章 `/.well-known` 。 驗證之後，它會從這個權杖中解壓縮宣告，而不是 `amount` 和 `payee` 。 使用者會看到一個頁面，以透過 SMS 訊息驗證其行動電話號碼。     |
| 5     | 使用者透過 SMS 訊息要求驗證其電話號碼，Azure AD B2C 對 Twilio 的驗證 API 端點提出 REST API 要求。 它也會在 `amount` `payee` PSD2 過程中傳送交易，以產生單次密碼 (OTP) 。 Twilio 會將 SMS 訊息傳送給使用者的已註冊電話號碼。     |
| 6     |  使用者輸入在其 SMS 訊息中收到的 OTP，並將其提交至 Azure AD B2C。 Azure AD B2C 會使用此 OTP 將 API 要求 Twilio 的驗證 API，以驗證 OTP 是否正確。 最後，會使用新的 PolicyId 來向應用程式發出權杖，以表示使用者已進行驗證。    |
|      |      |

## <a name="onboard-with-twilio"></a>使用 Twilio 上線

1. 取得 [試用帳戶](https://www.twilio.com/try-twilio) ，網址為 Twilio。

2. 如[本文所](https://support.twilio.com/hc/articles/223135247-How-to-Search-for-and-Buy-a-Twilio-Phone-Number-from-Console)述，在 Twilio 購買電話號碼

3. 在 Twilio 主控台流覽至 [驗證 API](https://www.twilio.com/console/verify/services) ，並遵循 [指示](https://www.twilio.com/docs/verify/verifying-transactions-psd2) 來建立服務，並啟用 PSD2 選項。  

## <a name="configure-the-psd2-demo-app"></a>設定 PSD2 示範應用程式

1. 開啟 B2C WebAPI-DotNet 解決方案，並以您自己的租使用者特定值取代 web.config 中的下列值：

    ```xml
   <add key="ida:Tenant" value="yourtenant.onmicrosoft.com" />
   <add key="ida:TenantId" value="d6f33888-0000-4c1f-9b50-1590f171fc70" />
   <add key="ida:ClientId" value="6bd98cc8-0000-446a-a05e-b5716ef2651b" />
   <add key="ida:ClientSecret" value="secret" />
   <add key="ida:AadInstance" value="https://yourtenant.b2clogin.com/tfp/{0}/{1}" />
   <add key="ida:RedirectUri" value="https://your hosted psd2 demo app url/" />
   ```

2. [Web 應用程式](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App)也會裝載識別碼權杖提示產生器和中繼資料端點。
   - 依照此 [範例說明](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#creating-a-signing-certificate)所述，建立您的簽署憑證。
   - 根據您在 web.config 中的憑證來更新下列幾行：
   
   ```xml
   <add key="ida:SigningCertThumbprint" value="4F39D6014818082CBB763E5BA5F230E545212E89" />
   <add key="ida:SigningCertAlgorithm" value="RS256" />
   ```

3. 將示範應用程式上傳至您選擇的主機服務提供者。  [此範例描述](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#hosting-the-application-in-azure-app-service)提供 Azure App Service 的指引，包括上傳憑證的指示。

4. 將回復 URL 新增為與裝載應用程式的 URL 相等的回復 URL，以更新您的 Azure AD B2C 應用程式註冊。

5. 開啟 [原則](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy) 檔案，並將的所有實例取代  `contoso` 為您的租使用者名稱。

6. 尋找 Twilio REST API 技術設定檔 **自訂-SMS-註冊**。 將更新為  `ServiceURL`   您的 Twilio AccountSID，並將 From 號碼更新為您購買的電話號碼。

7. 尋找 Twilio REST API 技術設定檔， **TwilioRestAPI-verify-步驟 1**   和 **TwilioRestAPI-verify-步驟 2**，並  `ServiceURL`   使用您的 Twilio AccountSID 更新。

## <a name="integrate-with-azure-ad-b2c"></a>與 Azure AD B2C 整合

將原則檔新增至 Azure AD B2C：

1. 以 Azure AD B2C 租用戶的全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。

2. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。

3. 選擇 Azure 入口網站左上角的 [所有服務]，搜尋並選取 [Azure AD B2C]。

4. 流覽至**Azure AD B2C**  >  **Identity Experience Framework**  >  **原則金鑰**。

5. 新增名稱為 **B2cRestTwilioClientId**的新機碼。 選取 [ **手動**]，並提供 Twilio AccountSID 的值。

6. 新增名稱為 **B2cRestTwilioClientSecret**的新機碼。 選取 [ **手動**]，然後提供 TWILIO AUTH TOKEN 的值。

7. 將所有原則檔案上傳至您的租使用者。

8. 針對您的註冊 SMS 文字，自訂 GenerateOTPMessageEnrol 宣告轉換中的字串。

## <a name="test-the-solution"></a>測試解決方案

* 流覽至您的應用程式，並測試登入、註冊和傳送 Money 動作。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章：

- 請參閱 GitHub 以取得 [Twilio 整合程式碼範例](https://github.com/azure-ad-b2c/samples/tree/master/policies/twilio-mfa-psd2)  

- [AAD B2C 中的自訂原則](custom-policy-overview.md)

- [AAD B2C 中的自訂原則入門](custom-policy-get-started.md?tabs=applications)
