---
title: 權杖概述 - Azure 活動目錄 B2C
description: 瞭解 Azure 活動目錄 B2C 中使用的權杖。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cbbd083a6b62733d71c316af95dffaa188b28955
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186483"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Azure 活動目錄 B2C 中的權杖概述

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) 會在處理每個[驗證流程](application-types.md)時發出數種安全性權杖。 本文件說明每種權杖的格式、安全性特性和內容。

## <a name="token-types"></a>權杖類型

Azure AD B2C 支援[OAuth 2.0 和 OpenID 連線協定](protocols-overview.md)，該協定使用權杖進行身份驗證並安全訪問資源。 Azure AD B2C 中使用的所有權杖都是[JSON Web 權杖 （JWT），](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)其中包含有關標記者和權杖主題的資訊斷言。

以下權杖用於與 Azure AD B2C 通信：

- *ID 權杖*- 包含可用於標識應用程式中使用者的聲明的 JWT。 此權杖在 HTTP 要求中安全地發送，以便在同一應用程式或服務的兩個元件之間通信。 您可以依需要在 ID 權杖中使用宣告。 它們通常用於在應用程式中顯示帳戶資訊或做出存取控制決策。 ID 權杖已簽名，但未加密。 當應用程式或 API 收到 ID 權杖時，它必須驗證簽名以證明該權杖是可信的。 應用程式或 API 還必須驗證權杖中的一些聲明，以證明它有效。 根據方案要求，應用程式驗證的聲明可能會有所不同，但應用程式必須在每種方案中執行一些常見的聲明驗證。
- *訪問權杖*- 包含可用於標識 API 授予的許可權的聲明的 JWT。 訪問權杖已簽名，但未加密。 訪問權杖用於提供對 API 和資源伺服器的訪問。  當您的 API 收到存取權杖時，它必須 驗證簽章 以證明權杖的真實性。 您的 API 也必須驗證權杖中的幾個宣告，以證明它有效。 根據方案要求，應用程式驗證的聲明可能會有所不同，但應用程式必須在每種方案中執行一些常見的聲明驗證。
- *刷新權杖*- 刷新權杖用於獲取 OAuth 2.0 流中的新 ID 權杖和訪問權杖。 它們為您的應用程式提供代表使用者長期訪問資源的機會，而無需與這些使用者進行交互。 刷新權杖對應用程式不透明。 它們由 Azure AD B2C 頒發，只能由 Azure AD B2C 檢查和解釋。 它們是長期存在的，但應用程式不應以期望刷新權杖持續特定時間段來編寫。 重新整理權杖可能會因為各種原因而隨時失效。 應用程式瞭解刷新權杖是否有效的唯一方法是嘗試通過向 Azure AD B2C 發出權杖請求來兌換它。 當您為新權杖兌換刷新權杖時，您將在權杖回應中收到新的刷新權杖。 保存新的刷新權杖。 它替換以前在請求中使用的刷新權杖。 此操作有助於確保刷新權杖盡可能長時間保持有效。

## <a name="endpoints"></a>端點

[註冊的應用程式](tutorial-register-applications.md)通過向這些終結點發送請求來接收權杖並與 Azure AD B2C 通信：

- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize`
- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token`

應用程式從 Azure AD B2C 接收的安全權杖可能來自 或`/authorize``/token`終結點。 從`/authorize`終結點獲取 ID 權杖時，它使用[隱式流](implicit-flow-single-page-application.md)完成，該流通常用於登錄到基於 JavaScript 的 Web 應用程式的使用者。 從`/token`終結點獲取 ID 權杖時，將使用[授權代碼流](openid-connect.md#get-a-token)完成，該流使權杖隱藏在瀏覽器中。

## <a name="claims"></a>Claims

當您使用 Azure AD B2C 時，您可以精確控制權杖的內容。 您可以配置[使用者流](user-flow-overview.md)和[自訂策略](custom-policy-overview.md)，以在應用程式所需的聲明中發送某些使用者資料集。 這些聲明可以包括標準屬性，如**顯示名稱**和**電子郵件地址**。 您的應用程式可以使用這些宣告來安全地驗證使用者和要求。

ID 權杖中的聲明不會按任何特定順序返回。 可以隨時在 ID 權杖中引入新聲明。 引入新聲明時，應用程式不應中斷。 您還可以在聲明中包括[自訂使用者屬性](user-flow-custom-attributes.md)。

下表列出了在 Azure AD B2C 頒發的 ID 權杖和訪問權杖中可以期待的聲明。

| 名稱 | 宣告 | 範例值 | 描述 |
| ---- | ----- | ------------- | ----------- |
| 適用對象 | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | 識別權杖的預定接收者。 對於 Azure AD B2C，訪問群體是應用程式 ID。 應用程式應驗證此值，並在權杖不匹配時拒絕該權杖。 對象是資源的同義詞。 |
| 簽發者 | `iss` |`https://{tenant}.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | 識別負責建構並傳回權杖的 Security Token Service (STS)。 它還標識使用者在其中進行身份驗證的目錄。 應用程式應驗證頒發者聲明，以確保權杖來自相應的終結點。 |
| 發出時間 | `iat` | `1438535543` | 簽發權杖的時間 (以新紀元 (Epoch) 時間表示)。 |
| 到期時間 | `exp` | `1438539443` | 權杖失效的時間 (以新紀元 (Epoch) 時間表示)。 您的應用程式應使用此聲明來驗證權杖存留期的有效性。 |
| 生效時間 | `nbf` | `1438535543` | 權杖生效的時間 (以新紀元 (Epoch) 時間表示)。 此時間通常與頒發權杖的時間相同。 您的應用程式應使用此聲明來驗證權杖存留期的有效性。 |
| 版本 | `ver` | `1.0` | 由 Azure AD B2C 定義的 ID 權杖的版本。 |
| 代碼雜湊 | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | 僅當令牌與 OAuth 2.0 授權代碼一起發出時，ID 權杖中包含的代碼雜湊。 代碼雜湊可用來驗證授權碼的真實性。 有關如何執行此驗證的詳細資訊，請參閱[OpenID 連接規範](https://openid.net/specs/openid-connect-core-1_0.html)。  |
| 存取權杖雜湊 | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | 僅當令牌與 OAuth 2.0 訪問權杖一起頒發時，ID 權杖中包含的訪問權杖雜湊。 存取權杖雜湊可用來驗證存取權杖的真實性。 有關如何執行此驗證的詳細資訊，請參閱[OpenID 連接規範](https://openid.net/specs/openid-connect-core-1_0.html)  |
| Nonce | `nonce` | `12345` | Nonce 是用來緩和權杖重新執行攻擊的策略。 應用程式可以使用`nonce`查詢參數在授權請求中指定 nonce。 僅在 ID 權杖`nonce`的聲明中未更改您請求中提供的值。 此聲明允許應用程式根據請求上指定的值驗證該值。 應用程式應在 ID 權杖驗證過程中執行此驗證。 |
| 主體 | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | 權杖斷言資訊的主體，例如應用程式的使用者。 這個值不可變，而且無法重新指派或重複使用。 它可用來安全地執行授權檢查，例如當權杖用於存取資源時。 根據預設，主體宣告會填入目錄中使用者的物件識別碼。 |
| 驗證內容類別參考 | `acr` | 不適用 | 僅與較舊的策略一起使用。 |
| 信任架構原則 | `tfp` | `b2c_1_signupsignin1` | 用於獲取 ID 權杖的策略的名稱。 |
| 驗證期間 | `auth_time` | `1438535543` | 使用者上次輸入憑據的時間，以紀元時程表示。 該身份驗證是新的登錄、單個登錄 （SSO） 會話或其他登錄類型之間沒有區別。 是`auth_time`應用程式（或使用者）上次對 Azure AD B2C 發起身份驗證嘗試。 用於身份驗證的方法沒有區分。 |
| 影響範圍 | `scp` | `Read`| 授予訪問權杖的資源的許可權。 多個授予的許可權由空格分隔。 |
| 授權的合作對象 | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | 起始要求之用戶端應用程式的應用程式識別碼****。 |

## <a name="configuration"></a>組態

以下屬性用於管理 Azure AD B2C 發出[的安全權杖的存留期](configure-tokens.md)：

- **存取和識別碼權杖存留期 (分鐘)** - 用來存取受保護資源之 OAuth 2.0 持有人權杖的存留期。 預設值是 60 分鐘。 最短（含）為 5 分鐘。 最大（含）為 1440 分鐘。

- **刷新權杖存留期（天）** - 刷新權杖可用於獲取新訪問或 ID 權杖的最長時間段。 如果應用程式已被授予作用域，`offline_access`則時間段還包括獲取新的刷新權杖。 預設值為 14 天。 最低（含）為一天。 最長（含）為 90 天。

- **刷新權杖滑動視窗存留期（天）** - 在此時間段過後，無論應用程式獲取的最新刷新權杖的有效性期限如何，使用者都被迫重新進行身份驗證。 唯有將參數設定為 [受限制] **** 時才能提供。 它必須大於或等於 [重新整理權杖存留期 (天)] **** 值。 如果將參數設定為 [無限制] ****，您便無法提供特定的值。 預設值為 90 天。 最低（含）為一天。 最長（含）為365天。

使用這些屬性可啟用以下使用案例：

- 只要使用者持續使用行動應用程式，便允許他們無限期地維持已登入應用程式的狀態。 您可以在登入使用者流程中將 [重新整理權杖滑動視窗存留期 (天)]**** 設定為 [無限制]****。
- 請設定適當的存取權杖存留期，以滿足業界的安全性和循規規範。

這些設定不適用於密碼重設使用者流程。

## <a name="compatibility"></a>相容性

以下屬性用於[管理權杖相容性](configure-tokens.md)：

- **簽發者 (iss) 宣告** - 此屬性可識別發出權杖的 Azure AD B2C 租用戶。 預設值是 `https://<domain>/{B2C tenant GUID}/v2.0/`。 的值`https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/`包括 Azure AD B2C 租戶和權杖請求中使用的使用者流的 ID。 如果應用程式或庫需要 Azure AD B2C 符合[OpenID 連接發現 1.0 規範](https://openid.net/specs/openid-connect-discovery-1_0.html)，請使用此值。

- **主體 (子) 宣告** - 此屬性可識別權杖判斷提示資訊的實體。 預設值為**ObjectID**，它使用使用者的物件`sub`ID 在權杖中填充聲明。 **不支援**的值僅用於向後相容性。 建議您儘快切換到**ObjectID。**

- **表示策略 ID 的聲明**- 此屬性標識在權杖請求中使用的策略名稱填充到其中的聲明類型。 預設值是 `tfp`。 的值`acr`僅提供向後相容性。

## <a name="pass-through"></a>傳遞

當使用者旅程開始時，Azure AD B2C 會從標識提供程式接收訪問權杖。 Azure AD B2C 會使用該權杖來擷取使用者的相關資訊。 在[使用者流中啟用聲明](idp-pass-through-user-flow.md)或在[自訂策略中定義聲明](idp-pass-through-custom.md)，以便將權杖傳遞給在 Azure AD B2C 中註冊的應用程式。 應用程式必須使用[v2 使用者流](user-flow-versions.md)來利用將權杖作為聲明傳遞的優勢。

Azure AD B2C 目前僅支援傳遞 OAuth 2.0 身分識別提供者的存取權杖，其中包括 Facebook 和 Google。 對於所有其他識別提供者，宣告會傳回空白。

## <a name="validation"></a>驗證

要驗證權杖，應用程式應同時檢查權杖的簽名和聲明。 許多開源庫可用於驗證 JWT，具體取決於您的首選語言。 建議您探索這些選項，而不是實現自己的驗證邏輯。

### <a name="validate-signature"></a>驗證簽名

JWT 包含三個段，一個*標題*，一個*正文*和一個*簽名*。 簽名段可用於驗證權杖的真實性，以便應用程式可以信任它。 Azure AD B2C 權杖是經由業界標準非對稱式加密演算法 (例如 RSA 256) 進行簽署。

權杖標頭包含用來簽署權杖的金鑰和加密方法相關資訊：

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

**alg**聲明的值是用於對權杖進行簽名的演算法。 **孩子**聲明的值是用於對權杖進行簽名的公開金鑰。 在任何給定時間，Azure AD B2C 都可以使用一組公開金鑰對中的任何一個對權杖。 Azure AD B2C 定期輪換可能的金鑰集。 應編寫應用程式以自動處理這些金鑰更改。 檢查 Azure AD B2C 使用的公開金鑰更新的合理頻率為 24 小時。

Azure AD B2C 具有 OpenID Connect 中繼資料端點。 使用此終結點，應用程式可以在運行時請求有關 Azure AD B2C 的資訊。 這項資訊包括端點、權杖內容和權杖簽署金鑰。 Azure AD B2C 租戶包含每個策略的 JSON 中繼資料文檔。 中繼資料文件是包含幾項實用資訊的 JSON 物件。 中繼資料包含**jwks_uri**，它提供用於對權杖進行簽名的公開金鑰集的位置。 此處提供了該位置，但最好使用中繼資料文檔和分析**jwks_uri**動態獲取該位置：

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_signupsignin1
```
位於此 URL 的 JSON 文件包含特定時刻使用的所有公開金鑰資訊。 您的應用程式可以使用 JWT 標頭中的 `kid` 宣告，以選取 JSON 文件中用來簽署特定權杖的公開金鑰。 接著可以使用正確的公開金鑰和指定的演算法來執行簽章驗證。

租戶中策略的`B2C_1_signupsignin1``contoso.onmicrosoft.com`中繼資料文檔位於：

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_signupsignin1
```

要確定使用哪個策略對權杖進行簽名（以及請求中繼資料的位置），有兩個選項。 首先，原則名稱包含在權杖的 `acr` 宣告中。 您可以將 JWT 主體進行 base 64 解碼，並將產生的 JSON 字串還原序列化，以剖析 JWT 主體中的宣告。 聲明`acr`是用於頒發權杖的策略的名稱。 另一個選項是在發出請求時對策略在`state`參數值中編碼，然後對其進行解碼以確定使用了哪個策略。 任一種方法都有效。

描述如何執行簽章驗證已超出本文的範圍。 許多開源庫都可以説明您驗證權杖。

### <a name="validate-claims"></a>驗證聲明

當應用程式或 API 收到 ID 權杖時，它還應對 ID 權杖中的聲明執行多個檢查。 應檢查以下聲明：

- **訪問群體**- 驗證 ID 權杖是否打算提供給您的應用程式。
- **之前**和**過期時間**- 驗證 ID 權杖尚未過期。
- **頒發者**- 驗證權杖是否由 Azure AD B2C 頒發給應用程式。
- **nonce** - 權杖重播攻擊緩解的策略。

有關應用程式應執行的驗證的完整清單，請參閱[OpenID 連接規範](https://openid.net)。

## <a name="next-steps"></a>後續步驟

深入了解如何[使用存取權杖](access-tokens.md)。

