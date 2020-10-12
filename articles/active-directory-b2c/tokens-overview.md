---
title: 權杖概觀 - Azure Active Directory B2C
description: 了解在 Azure Active Directory B2C 中使用的權杖。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 19b65554801a22954499219e43ed021a7cc8c121
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89258430"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>概述 Azure Active Directory B2C 中的權杖

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) 會在處理每個[驗證流程](application-types.md)時發出數種安全性權杖。 本文件說明每種權杖的格式、安全性特性和內容。

## <a name="token-types"></a>權杖類型

Azure AD B2C 支援 [OAuth 2.0 和 OpenID Connect 通訊協定](protocols-overview.md)，而使用權杖進行驗證，並保護資源存取的安全性。 Azure AD B2C 中使用的所有權杖都是 [JSON Web 權杖 (JWT) ](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)，其中包含權杖持有人和主體相關資訊的判斷提示。

與 Azure AD B2C 通訊時會使用下列權杖：

- *識別碼權杖* - 一個 JWT，其中包含的宣告可讓您用來識別應用程式中的使用者。 此權杖可在 HTTP 要求中安全地傳送，以在相同應用程式或服務的兩個元件之間進行通訊。 您可以依需要在 ID 權杖中使用宣告。 這些宣告通常用來顯示帳戶資訊，或決定應用程式中的存取控制。 識別碼權杖會進行簽署，但不會加密。 您的應用程式或 API 在收到識別碼權杖時，必須驗證簽章以證明權杖的真實性。 您的應用程式或 API 也必須驗證權杖中的幾個宣告，以證明其有效性。 應用程式所驗證的宣告會視案例需求而有所不同，但您的應用程式必須在每一種案例中執行一些常見的宣告驗證。
- *存取權杖* - 一個 JWT，其中包含的宣告可讓您用來識別為 API 授與的權限。 存取權杖會進行簽署，但不會加密。 存取權杖可用來提供伺服器 API 和資源的存取權。  您的 API 在收到存取權杖時，必須驗證簽章以證明權杖的真實性。 您的 API 也必須驗證權杖中的幾個宣告，以證明它有效。 應用程式所驗證的宣告會視案例需求而有所不同，但您的應用程式必須在每一種案例中執行一些常見的宣告驗證。
- *重新整理權杖* - 重新整理權杖可用來在 OAuth 2.0 流程中取得新的識別碼權杖和存取權杖。 這些權杖可讓您的應用程式代表使用者長期存取資源，而無須與使用者互動。 重新整理權杖對您的應用程式而言是不透明的。 這些權杖由 Azure AD B2C 所簽發，且只能由 Azure AD B2C 檢查和解譯。 這些權杖屬於長效權杖，但在撰寫您的應用程式時，不應期待重新整理權杖會持續一段特定時間。 重新整理權杖可能會因為各種原因而隨時失效。 讓您的應用程式知道重新整理權杖是否有效的唯一方式，就是對 Azure AD B2C 提出權杖要求以嘗試兌換。 當您兌換重新整理權杖作為新的權杖時，您會在權杖回應中收到新的重新整理權杖。 儲存新的重新整理權杖。 該權杖應取代您先前用於要求中的重新整理權杖。 此動作有助於確保您的重新整理權杖會盡可能長期保持有效。

## <a name="endpoints"></a>端點

[已註冊的應用程式](tutorial-register-applications.md)在收到權杖後，會藉由將要求傳送至下列端點與 Azure AD B2C 進行通訊：

- `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize`
- `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/token`

您的應用程式從 Azure AD B2C 收到的安全性權杖可能來自 `/authorize` 或 `/token` 端點。 從 `/authorize` 端點取得識別碼權杖時，會使用[隱含流程](implicit-flow-single-page-application.md)來完成作業，使用者登入以 JavaScript 為基礎的 Web 應用程式時，常會使用此做法。 從 `/token` 端點取得識別碼權杖時，則會使用[授權碼流程](openid-connect.md#get-a-token)來完成作業，而使權杖不顯示在瀏覽器中。

## <a name="claims"></a>Claims

當您使用 Azure AD B2C 時，您可以精確控制權杖的內容。 您可以設定[使用者流程](user-flow-overview.md)和[自訂原則](custom-policy-overview.md)，以在宣告中傳送傳送應用程式所需的特定使用者資料集。 這些宣告可包含標準屬性，例如 **displayName** 和 **emailAddress**。 您的應用程式可以使用這些宣告來安全地驗證使用者和要求。

識別碼權杖中的宣告不會依任何特定順序傳回。 在識別碼權杖中可以隨時導入新的宣告。 導入新的宣告時，您的應用程式並不會中斷。 您也可以在宣告中加入[自訂使用者屬性](user-flow-custom-attributes.md)。

下表列出您在 Azure AD B2C 所簽發的識別碼權杖和存取權杖中可預期的宣告。

| 名稱 | 宣告 | 範例值 | 描述 |
| ---- | ----- | ------------- | ----------- |
| 適用對象 | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | 識別權杖的預定接收者。 Azure AD B2C 的適用對象為應用程式識別碼。 您的應用程式應驗證此值，並拒絕不相符的權杖。 對象是資源的同義詞。 |
| 簽發者 | `iss` |`https://<tenant-name>.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | 識別負責建構並傳回權杖的 Security Token Service (STS)。 此外也會識別在其中驗證使用者的目錄。 您的應用程式應驗證簽發者宣告，以確定權杖來自適當的端點。 |
| 發出時間 | `iat` | `1438535543` | 簽發權杖的時間 (以新紀元 (Epoch) 時間表示)。 |
| 到期時間 | `exp` | `1438539443` | 權杖失效的時間 (以新紀元 (Epoch) 時間表示)。 您的應用程式應使用此宣告來驗證權杖存留期的有效性。 |
| 生效時間 | `nbf` | `1438535543` | 權杖生效的時間 (以新紀元 (Epoch) 時間表示)。 此時間通常與權杖的簽發時間相同。 您的應用程式應使用此宣告來驗證權杖存留期的有效性。 |
| 版本 | `ver` | `1.0` | Azure AD B2C 所定義的識別碼權杖版本。 |
| 代碼雜湊 | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | 只有在識別碼權杖與 OAuth 2.0 授權碼一起簽發時，權杖才會包含代碼雜湊。 代碼雜湊可用來驗證授權碼的真實性。 如需如何執行此驗證的詳細資訊，請參閱 [OpenID Connect 規格](https://openid.net/specs/openid-connect-core-1_0.html)。  |
| 存取權杖雜湊 | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | 只有在識別碼權杖與 OAuth 2.0 存取權杖一起簽發時，權杖才會包含存取權杖雜湊。 存取權杖雜湊可用來驗證存取權杖的真實性。 如需如何執行此驗證的詳細資訊，請參閱 [OpenID Connect 規格](https://openid.net/specs/openid-connect-core-1_0.html)  |
| Nonce | `nonce` | `12345` | Nonce 是用來緩和權杖重新執行攻擊的策略。 您的應用程式可以使用 `nonce` 查詢參數，以在授權要求中指定 nonce。 您在要求中提供的值將只會在識別碼權杖的 `nonce` 宣告中發出 (未經修改)。 此宣告可讓您的應用程式根據要求中指定的值來驗證值。 您的應用程式應在識別碼權杖驗證程序中執行這項驗證。 |
| 主體 | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | 權杖判斷提示其相關資訊的主體，例如應用程式的使用者。 這個值不可變，而且無法重新指派或重複使用。 它可用來安全地執行授權檢查，例如當權杖用於存取資源時。 根據預設，主體宣告會填入目錄中使用者的物件識別碼。 |
| 驗證內容類別參考 | `acr` | 不適用 | 僅適用於較舊的原則。 |
| 信任架構原則 | `tfp` | `b2c_1_signupsignin1` | 用來取得識別碼權杖的原則名稱。 |
| 驗證期間 | `auth_time` | `1438535543` | 使用者上次輸入認證的時間，以 Epoch 時間表示。 該驗證是全新登入、單一登入 (SSO) 工作階段還是其他登入類型，並不會有任何區別。 `auth_time` 是應用程式 (或使用者) 上次嘗試對 Azure AD B2C 起始驗證的時間。 用於驗證的方法並無區別。 |
| 影響範圍 | `scp` | `Read`| 為存取權杖授與的資源權限。 授與的多個權限會以空格分隔。 |
| 授權的合作對象 | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | 起始要求之用戶端應用程式的應用程式識別碼。 |

## <a name="configuration"></a>組態

下列屬性可用來[管理 Azure AD B2C 所發出之安全性權杖的存留期](configure-tokens.md)︰

- **存取和識別碼權杖存留期 (分鐘)** - 用來存取受保護資源之 OAuth 2.0 持有人權杖的存留期。 預設值是 60 分鐘。 最小值為 5 分鐘 (包含此值)。 最大值為 1440 分鐘 (包含此值)。

- **重新整理權杖存留期 (days)** - 經過這段時間後，重新整理權杖就無法用來取得新的存取權或識別碼權杖。 如果已為應用程式授與 `offline_access` 範圍，則這段時間也會涵蓋取得新重新整理權杖的時間。 預設值為 14 天。 最小值為一天 (包含此值)。 最大值為 90 天 (包含此值)。

- **重新整理權杖滑動視窗存留期 (天)** - 這段時間結束後，無論應用程式取得的最新重新整理權杖的有效期間為何，使用者都必須重新驗證。 唯有將參數設定為 [受限制] 時才能提供。 它必須大於或等於 [重新整理權杖存留期 (天)]  值。 如果將參數設定為 [無限制] ，您便無法提供特定的值。 預設值為 90 天。 最小值為一天 (包含此值)。 最大值為 365 天 (包含此值)。

使用這些屬性可啟用以下使用案例：

- 只要使用者持續使用行動應用程式，便允許他們無限期地維持已登入應用程式的狀態。 您可以在登入使用者流程中將 [重新整理權杖滑動視窗存留期 (天)] 設定為 [無限制]。
- 請設定適當的存取權杖存留期，以滿足業界的安全性和循規規範。

這些設定不適用於密碼重設使用者流程。

## <a name="compatibility"></a>相容性

下列屬性可用來[管理權杖相容性](configure-tokens.md)：

- **簽發者 (iss) 宣告** - 此屬性可識別發出權杖的 Azure AD B2C 租用戶。 預設值是 `https://<domain>/{B2C tenant GUID}/v2.0/`。 `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` 的值包含包含 Azure AD B2C 租用戶和權杖要求中所用使用者流程的識別碼。 如果您的應用程式或程式庫需要 Azure AD B2C 符合 [OpenID Connect Discovery 1.0 規格](https://openid.net/specs/openid-connect-discovery-1_0.html)，請使用此值。

- **主體 (子) 宣告** - 此屬性可識別權杖判斷提示資訊的實體。 預設值為 **ObjectID**，會對權杖中的 `sub` 宣告填入使用者的物件識別碼。 **不支援的值**僅供回溯相容性之用。 建議您盡快切換至 **ObjectID**。

- **代表原則識別碼的宣告** - 此屬性會識別在其中填入權杖要求中所用原則名稱的宣告類型。 預設值是 `tfp`。 `acr` 的值僅供回溯相容性之用。

## <a name="pass-through"></a>傳遞

使用者旅程啟動時，Azure AD B2C 會收到識別提供者所提供的存取權杖。 Azure AD B2C 會使用該權杖來擷取使用者的相關資訊。 您可[在使用者流程中啟用宣告](idp-pass-through-user-flow.md)或[在自訂原則中定義宣告](idp-pass-through-custom.md)，以將權杖傳至您在 Azure AD B2C 中註冊的應用程式。 您的應用程式必須使用 [建議的使用者流程](user-flow-versions.md) ，才能利用傳遞權杖作為宣告。

Azure AD B2C 目前僅支援傳遞 OAuth 2.0 識別提供者的存取權杖，其中包括 Facebook 和 Google。 對於所有其他識別提供者，宣告會傳回空白。

## <a name="validation"></a>驗證

若要驗證權杖，您的應用程式應檢查權杖的簽章和宣告。 視您偏好的語言而定，有許多開放原始碼程式庫可用來驗證 JWT。 建議您探索這些選項，而不要自行實作驗證邏輯。

### <a name="validate-signature"></a>驗證簽章

JWT 包含三個區段：*標頭*、*主體*和*簽章*。 簽章區段可用來驗證權杖的真實性，您的應用程式才得以信任。 Azure AD B2C 權杖是經由業界標準非對稱式加密演算法 (例如 RSA 256) 進行簽署。

權杖標頭包含用來簽署權杖的金鑰和加密方法相關資訊：

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

**alg** 宣告的值是用來簽署權杖的演算法。 **kid** 宣告的值是用來簽署權杖的公開金鑰。 無論何時，Azure AD B2C 都能使用任何一組公開-私密金鑰組來簽署權杖。 Azure AD B2C 會定期輪替一組可能的金鑰。 您應撰寫應用程式以自動處理這些金鑰變更。 檢查 Azure AD B2C 所使用的公開金鑰是否有更新的合理頻率為每 24 小時。 若要處理未預期的金鑰變更，您的應用程式應該撰寫成在收到非預期的 **小孩** 值時重新取出公開金鑰。

Azure AD B2C 具有 OpenID Connect 中繼資料端點。 應用程式可以使用此端點，在執行階段要求 Azure AD B2C 的相關資訊。 這項資訊包括端點、權杖內容和權杖簽署金鑰。 您的 Azure AD B2C 租用戶包含每個原則的 JSON 中繼資料文件。 中繼資料文件是包含幾項實用資訊的 JSON 物件。 中繼資料包含 **jwks_uri**，可指出用來簽署權杖的公開金鑰組的位置。 在此提供該位置，但最好使用中繼資料文件並剖析 **jwks_uri** 以動態擷取該位置：

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/b2c_1_signupsignin1/discovery/v2.0/keys
```
位於此 URL 的 JSON 文件包含特定時刻使用的所有公開金鑰資訊。 您的應用程式可以使用 JWT 標頭中的 `kid` 宣告，以選取 JSON 文件中用來簽署特定權杖的公開金鑰。 接著可以使用正確的公開金鑰和指定的演算法來執行簽章驗證。

`contoso.onmicrosoft.com` 租用戶中適用於 `B2C_1_signupsignin1` 原則的中繼資料文件位於：

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/b2c_1_signupsignin1/v2.0/.well-known/openid-configuration
```

若要判斷哪個原則用來簽署權杖 (以及可於何處要求中繼資料)，您有兩個選項。 首先，原則名稱包含在權杖的 `acr` 宣告中。 您可以將 JWT 主體進行 base 64 解碼，並將產生的 JSON 字串還原序列化，以剖析 JWT 主體中的宣告。 `acr` 宣告是用來簽發權杖的原則名稱。 另一個選項是當您發出要求時在 `state` 參數的值中將原則編碼，然後將其解碼以判斷使用了哪個原則。 任一種方法都有效。

描述如何執行簽章驗證已超出本文的範圍。 有許多開放原始碼程式庫可協助您驗證權杖。

### <a name="validate-claims"></a>驗證宣告

當您的應用程式或 API 收到識別碼權杖時，還應該對識別碼權杖中的宣告執行幾項檢查。 應檢查下列宣告：

- **物件** - 確認識別碼權杖預定要提供給您的應用程式。
- **生效時間**和**到期時間** - 確認識別碼權杖並未過期。
- **簽發者** - 確認權杖已由 Azure AD B2C 簽發給您的應用程式。
- **nonce** - 用來緩解權杖重新執行攻擊的策略。

如需應用程式應執行之驗證的完整清單，請參閱 [OpenID Connect 規格](https://openid.net)。

## <a name="next-steps"></a>後續步驟

深入了解如何[使用存取權杖](access-tokens.md)。

