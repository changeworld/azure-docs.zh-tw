---
title: 權杖總覽-Azure Active Directory B2C
description: 瞭解 Azure Active Directory B2C 中使用的權杖。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78186483"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的權杖總覽

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) 會在處理每個[驗證流程](application-types.md)時發出數種安全性權杖。 本文件說明每種權杖的格式、安全性特性和內容。

## <a name="token-types"></a>權杖類型

Azure AD B2C 支援[OAuth 2.0 和 OpenID connect 通訊協定](protocols-overview.md)，這會使用權杖進行驗證，並安全地存取資源。 Azure AD B2C 中使用的所有權杖都是[JSON web 權杖（jwt）](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) ，其中包含持有人和權杖主體的相關資訊判斷提示。

下列權杖會用於與 Azure AD B2C 的通訊：

- *識別碼權杖*-包含宣告的 JWT，您可以用來識別應用程式中的使用者。 此權杖會安全地傳送在 HTTP 要求中，以在相同應用程式或服務的兩個元件之間進行通訊。 您可以依需要在 ID 權杖中使用宣告。 它們通常用來顯示帳戶資訊，或在應用程式中進行存取控制決策。 識別碼權杖會經過簽署，但不會加密。 當您的應用程式或 API 收到識別碼權杖時，它必須驗證簽章以證明權杖是真實的。 您的應用程式或 API 也必須驗證權杖中的幾個宣告，以證明它是有效的。 根據案例需求，應用程式所驗證的宣告可能有所不同，但您的應用程式必須在每個案例中執行一些常見的宣告驗證。
- *存取權杖*-包含宣告的 JWT，您可以用來識別授與 api 的許可權。 存取權杖已簽署，但不會加密。 存取權杖是用來提供 Api 和資源伺服器的存取權。  當您的 API 收到存取權杖時，它必須 驗證簽章 以證明權杖的真實性。 您的 API 也必須驗證權杖中的幾個宣告，以證明它有效。 根據案例需求，應用程式所驗證的宣告可能有所不同，但您的應用程式必須在每個案例中執行一些常見的宣告驗證。
- 重新整理*權杖*-重新整理權杖可用來在 OAuth 2.0 流程中取得新的識別碼權杖和存取權杖。 它們可讓您的應用程式代表使用者長期存取資源，而不需要與使用者互動。 重新整理權杖對您的應用程式而言是不透明的。 這些是由 Azure AD B2C 發行，而且只能由 Azure AD B2C 檢查和解讀。 它們是長期的，但您的應用程式不應該在預期重新整理權杖將持續一段特定時間的情況下寫入。 重新整理權杖可能會因為各種原因而隨時失效。 讓您的應用程式知道重新整理權杖是否有效的唯一方式，就是嘗試藉由提出 Azure AD B2C 的權杖要求來兌換。 當您兌換新權杖的重新整理權杖時，您會在權杖回應中收到新的重新整理權杖。 儲存新的重新整理權杖。 它會取代您先前在要求中使用的重新整理權杖。 此動作可協助確保您的重新整理權杖盡可能保持有效。

## <a name="endpoints"></a>端點

[已註冊的應用程式](tutorial-register-applications.md)會藉由將要求傳送至這些端點，來接收權杖並與 Azure AD B2C 通訊：

- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize`
- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token`

您的應用程式從 Azure AD B2C 接收的安全性權杖可以來自`/authorize`或`/token`端點。 從`/authorize`端點取得識別碼權杖時，會使用[隱含流程](implicit-flow-single-page-application.md)來完成，這通常用於登入以 JavaScript 為基礎的 web 應用程式的使用者。 從`/token`端點取得 ID 權杖時，會使用[授權碼流程](openid-connect.md#get-a-token)來完成，這會讓權杖在瀏覽器中隱藏。

## <a name="claims"></a>Claims

當您使用 Azure AD B2C 時，您可以精確控制權杖的內容。 您可以設定[使用者流程](user-flow-overview.md)和[自訂原則](custom-policy-overview.md)，以在您的應用程式所需的宣告中傳送特定的使用者資料集。 這些宣告可以包含標準屬性（例如**displayName**和**emailAddress**）。 您的應用程式可以使用這些宣告來安全地驗證使用者和要求。

識別碼權杖中的宣告不會以任何特定順序傳回。 可以隨時在識別碼權杖中引進新的宣告。 當引進新的宣告時，您的應用程式不應該中斷。 您也可以在宣告中包含[自訂使用者屬性](user-flow-custom-attributes.md)。

下表列出 Azure AD B2C 所簽發的識別碼權杖和存取權杖中，您可以預期的宣告。

| 名稱 | 宣告 | 範例值 | 描述 |
| ---- | ----- | ------------- | ----------- |
| 適用對象 | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | 識別權杖的預定接收者。 針對 Azure AD B2C，物件是應用程式識別碼。 您的應用程式應驗證此值，並在權杖不相符時予以拒絕。 對象是資源的同義詞。 |
| 簽發者 | `iss` |`https://{tenant}.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | 識別負責建構並傳回權杖的 Security Token Service (STS)。 它也會識別使用者已驗證的目錄。 您的應用程式應該驗證簽發者宣告，以確保權杖來自適當的端點。 |
| 發出時間 | `iat` | `1438535543` | 簽發權杖的時間 (以新紀元 (Epoch) 時間表示)。 |
| 到期時間 | `exp` | `1438539443` | 權杖失效的時間 (以新紀元 (Epoch) 時間表示)。 您的應用程式應該使用此宣告來驗證權杖存留期的有效性。 |
| 生效時間 | `nbf` | `1438535543` | 權杖生效的時間 (以新紀元 (Epoch) 時間表示)。 此時間通常與發出權杖的時間相同。 您的應用程式應該使用此宣告來驗證權杖存留期的有效性。 |
| 版本 | `ver` | `1.0` | 識別碼權杖的版本，如 Azure AD B2C 所定義。 |
| 代碼雜湊 | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | 只有當令牌與 OAuth 2.0 授權碼一起發行時，識別碼權杖中才會包含程式碼雜湊。 代碼雜湊可用來驗證授權碼的真實性。 如需有關如何執行此驗證的詳細資訊，請參閱[OpenID connect 規格](https://openid.net/specs/openid-connect-core-1_0.html)。  |
| 存取權杖雜湊 | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | 只有在權杖與 OAuth 2.0 存取權杖一起發行時，才會包含在識別碼權杖中的存取權杖雜湊。 存取權杖雜湊可用來驗證存取權杖的真實性。 如需有關如何執行此驗證的詳細資訊，請參閱[OpenID connect 規格](https://openid.net/specs/openid-connect-core-1_0.html)  |
| Nonce | `nonce` | `12345` | Nonce 是用來緩和權杖重新執行攻擊的策略。 您的應用程式可以使用`nonce`查詢參數，在授權要求中指定 nonce。 您在要求中提供的值只會在 ID 權杖`nonce`的宣告中發出未修改的。 此宣告可讓您的應用程式根據要求中指定的值來驗證值。 您的應用程式應該在識別碼權杖驗證過程中執行此驗證。 |
| 主體 | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | 權杖判斷提示資訊的主體，例如應用程式的使用者。 這個值不可變，而且無法重新指派或重複使用。 它可用來安全地執行授權檢查，例如當權杖用於存取資源時。 根據預設，主體宣告會填入目錄中使用者的物件識別碼。 |
| 驗證內容類別參考 | `acr` | 不適用 | 僅適用于較舊的原則。 |
| 信任架構原則 | `tfp` | `b2c_1_signupsignin1` | 用來取得識別碼權杖的原則名稱。 |
| 驗證期間 | `auth_time` | `1438535543` | 使用者上次輸入認證的時間（以 epoch 時程表示）。 該驗證為全新登入、單一登入（SSO）會話，或另一個登入類型，因此不會有任何辨識。 `auth_time`是最後一次應用程式（或使用者）起始對 Azure AD B2C 的驗證嘗試。 用來驗證的方法並不區分。 |
| 影響範圍 | `scp` | `Read`| 授與存取權杖資源的許可權。 會以空格分隔多個授與的許可權。 |
| 授權的合作對象 | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | 起始要求之用戶端應用程式的應用程式識別碼****。 |

## <a name="configuration"></a>設定

下列屬性可用來管理 Azure AD B2C 所發出[之安全性權杖的存留期](configure-tokens.md)：

- **存取和識別碼權杖存留期 (分鐘)** - 用來存取受保護資源之 OAuth 2.0 持有人權杖的存留期。 預設值是 60 分鐘。 最小值（含）為5分鐘。 最大值（含）為1440分鐘。

- 重新整理**權杖存留期（天）** -重新整理權杖可用來取得新存取權或識別碼權杖的最大時間週期。 如果您的應用程式已被授與`offline_access`範圍，則時間週期也會涵蓋取得新的重新整理權杖。 預設值為 14 天。 最小值（含）為一天。 最大值（含）為90天。

- 重新整理**權杖滑動視窗存留期（天）** -在這段時間過後，不論應用程式取得的最新重新整理權杖的有效期間，使用者都會被迫重新驗證。 唯有將參數設定為 [受限制] **** 時才能提供。 它必須大於或等於 [重新整理權杖存留期 (天)] **** 值。 如果將參數設定為 [無限制] ****，您便無法提供特定的值。 預設值為90天。 最小值（含）為一天。 最大值（含）為365天。

使用這些屬性可啟用以下使用案例：

- 只要使用者持續使用行動應用程式，便允許他們無限期地維持已登入應用程式的狀態。 您可以在登入使用者流程中將 [重新整理權杖滑動視窗存留期 (天)]**** 設定為 [無限制]****。
- 請設定適當的存取權杖存留期，以滿足業界的安全性和循規規範。

這些設定不適用於密碼重設使用者流程。

## <a name="compatibility"></a>相容性

下列屬性可用來[管理權杖相容性](configure-tokens.md)：

- **簽發者 (iss) 宣告** - 此屬性可識別發出權杖的 Azure AD B2C 租用戶。 預設值是 `https://<domain>/{B2C tenant GUID}/v2.0/`。 的值`https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/`包含權杖要求中所用的 Azure AD B2C 租使用者和使用者流程的識別碼。 如果您的應用程式或程式庫需要 Azure AD B2C 符合[OpenID Connect 探索1.0 規格](https://openid.net/specs/openid-connect-discovery-1_0.html)的規範，請使用此值。

- **主體 (子) 宣告** - 此屬性可識別權杖判斷提示資訊的實體。 預設值為**ObjectID**，它會在權杖`sub`中以使用者的物件識別碼填入宣告。 [**不支援**] 的值僅針對回溯相容性而提供。 我們建議您儘快切換至**ObjectID** （）。

- **代表原則識別碼**的宣告-此屬性會識別要在其中填入權杖要求中所用原則名稱的宣告類型。 預設值是 `tfp`。 僅針對後`acr`向相容性提供的值。

## <a name="pass-through"></a>傳遞

當使用者旅程圖啟動時，Azure AD B2C 會從身分識別提供者接收存取權杖。 Azure AD B2C 會使用該權杖來擷取使用者的相關資訊。 您在[使用者流程中啟用](idp-pass-through-user-flow.md)宣告，或在[自訂原則中定義](idp-pass-through-custom.md)宣告，以將權杖傳遞至您在 Azure AD B2C 中註冊的應用程式。 您的應用程式必須使用[v2 使用者流程](user-flow-versions.md)，才能利用傳遞權杖做為宣告。

Azure AD B2C 目前僅支援傳遞 OAuth 2.0 身分識別提供者的存取權杖，其中包括 Facebook 和 Google。 對於所有其他識別提供者，宣告會傳回空白。

## <a name="validation"></a>驗證

若要驗證權杖，您的應用程式應該同時檢查權杖的簽章和宣告。 視您偏好的語言而定，許多開放原始碼程式庫可用於驗證 Jwt。 建議您探索這些選項，而不是執行自己的驗證邏輯。

### <a name="validate-signature"></a>驗證簽章

JWT 包含三個區段、一個*標頭*、一個*主體*和一個簽*章。* 簽章區段可用來驗證權杖的真實性，讓您的應用程式可以信任它。 Azure AD B2C 權杖是經由業界標準非對稱式加密演算法 (例如 RSA 256) 進行簽署。

權杖標頭包含用來簽署權杖的金鑰和加密方法相關資訊：

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

**Alg**宣告的值是用來簽署權杖的演算法。 **小孩**宣告的值是用來簽署權杖的公開金鑰。 在任何指定的時間，Azure AD B2C 都可以使用一組公開/私密金鑰配對的任何一個來簽署權杖。 Azure AD B2C 定期旋轉一組可能的金鑰。 您的應用程式應該要撰寫成自動處理這些金鑰變更。 檢查 Azure AD B2C 所使用之公開金鑰更新的合理頻率是每24小時一次。

Azure AD B2C 具有 OpenID Connect 中繼資料端點。 使用此端點，應用程式可以在執行時間要求 Azure AD B2C 的相關資訊。 這項資訊包括端點、權杖內容和權杖簽署金鑰。 您的 Azure AD B2C 租使用者包含每個原則的 JSON 元資料檔案。 中繼資料文件是包含幾項實用資訊的 JSON 物件。 中繼資料包含**jwks_uri**，它會提供用來簽署權杖之公開金鑰集合的位置。 這裡提供該位置，但最好使用元資料檔案和剖析**jwks_uri**，以動態方式提取位置：

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_signupsignin1
```
位於此 URL 的 JSON 文件包含特定時刻使用的所有公開金鑰資訊。 您的應用程式可以使用 JWT 標頭中的 `kid` 宣告，以選取 JSON 文件中用來簽署特定權杖的公開金鑰。 接著可以使用正確的公開金鑰和指定的演算法來執行簽章驗證。

`contoso.onmicrosoft.com`租使用者中`B2C_1_signupsignin1`原則的元資料檔案位於：

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_signupsignin1
```

若要判斷用來簽署權杖的原則（以及要前往何處要求中繼資料），您有兩個選項。 首先，原則名稱包含在權杖的 `acr` 宣告中。 您可以將 JWT 主體進行 base 64 解碼，並將產生的 JSON 字串還原序列化，以剖析 JWT 主體中的宣告。 `acr`宣告是用來簽發權杖的原則名稱。 另一個選項是當您發出要求時，在`state`參數的值中將原則編碼，然後將它解碼以判斷使用了哪個原則。 任一種方法都有效。

描述如何執行簽章驗證已超出本文的範圍。 有許多開放原始碼程式庫可協助您驗證權杖。

### <a name="validate-claims"></a>驗證宣告

當您的應用程式或 API 收到識別碼權杖時，它也應該對識別碼權杖中的宣告執行數個檢查。 應檢查下列宣告：

- **物件**-確認 ID 權杖預定要提供給您的應用程式。
- **不早**于**到期時間**-確認識別碼權杖尚未過期。
- **簽發者**-藉由 Azure AD B2C，確認權杖已簽發給您的應用程式。
- **nonce** -權杖重新執行攻擊的策略緩和措施。

如需您的應用程式應執行之驗證的完整清單，請參閱[OpenID connect 規格](https://openid.net)。

## <a name="next-steps"></a>後續步驟

深入了解如何[使用存取權杖](access-tokens.md)。

