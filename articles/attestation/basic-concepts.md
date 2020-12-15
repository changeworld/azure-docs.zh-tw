---
title: Azure 證明基本概念
description: Azure 證明的基本概念。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 8ae5bcf103bbb2d2b952fa647ba591e49002f2ff
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96921621"
---
# <a name="basic-concepts"></a>基本概念

以下是一些與 Microsoft Azure 證明相關的基本概念。

## <a name="json-web-token-jwt"></a>JSON Web 權杖 (JWT)

[JSON Web 權杖](https://jwt.io/) (JWT) 是開放式標準 [RFC7519](https://tools.ietf.org/html/rfc7519) 方法，可透過 JavaScript 物件標記法 (JSON) 物件的形式，安全地在合作夥伴之間傳輸資訊。 您可以驗證並信任此資訊，因為其已經過數位簽署。 JWT 可以使用祕密或公開/私密金鑰組來簽署。

## <a name="json-web-key-jwk"></a>JSON Web 金鑰 (JWK)

[JSON Web 金鑰](https://tools.ietf.org/html/rfc7517) (JWK) 是代表密碼編譯金鑰的 JSON 資料結構。 此規格也會定義 JWK 集的 JSON 資料結構來代表一組 JWK。

## <a name="attestation-provider"></a>證明提供者

證明提供者屬名為 Microsoft.Attestation 的 Azure 資源提供者。 資源提供者是提供 Azure 證明 REST 合約並使用 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 部署的服務端點。 每個證明提供者都會遵循特定且可探索的原則。 

證明提供者會搭配每個證明類型的預設原則來建立 (請注意，VBS 記憶體保護區沒有預設原則)。 如需有關 SGX 預設原則的詳細資訊，請參閱[證明原則的範例](policy-examples.md)。

### <a name="regional-default-provider"></a>區域預設提供者

Azure 證明會在每個區域中提供預設的提供者。 客戶可以選擇使用預設提供者進行證明，或利用自訂原則建立自己的提供者。 所有 Azure AD 使用者都可以存取預設提供者，但無法改變與預設提供者相關聯的原則。

| 區域 | 證明 URI | 
|--|--|
| 英國南部 | `https://shareduks.uks.attest.azure.net` | 
| 美國東部 2 | `https://sharedeus2.eus2.attest.azure.net` | 
| 美國中部 | `https://sharedcus.cus.attest.azure.net` | 
| 美國東部| `https://sharedeus.eus.attest.azure.net` | 
| 加拿大中部 | `https://sharedcac.cac.attest.azure.net` | 

## <a name="attestation-request"></a>證明要求

證明要求是用戶端應用程式傳送給證明提供者的已序列化 JSON 物件。 SGX 記憶體保護區的要求物件有兩個屬性： 
- “Quote” – "Quote" 屬性的值是字串，其中包含證明引用的 Base64URL 編碼標記法
- "EnclaveHeldData" – "EnclaveHeldData" 屬性的值是字串，其中包含「記憶體保護區保留資料」的 Base64URL 編碼標記法。

Azure 證明會驗證所提供的 "Quote"，然後確保所提供記憶體保護區保留資料的 SHA256 雜湊，會以引用中 reportData 欄位的前 32 個位元組表示。 

## <a name="attestation-policy"></a>證明原則

證明原則會用來處理證明的證據，而且可由客戶進行設定。 Azure 證明的核心是原則引擎，用來處理構成證據的宣告。 原則會用來判斷 Azure 證明是否應根據證明發出證明權杖，以及是否要因此為證明方背書。 因此，若無法傳遞所有原則，則不會發出任何 JWT 權杖。

如果證明提供者中的預設的原則不符合需求，客戶可以在 Azure 證明支援的任何區域中建立自訂原則。 原則管理是 Azure 證明提供給客戶的一項重要功能。 原則會針對各個證明類型制定，可用來識別記憶體保護區或將宣告新增至輸出權杖，或修改輸出權杖中的宣告。 

若要了解預設原則的內容和範例，請參閱[證明原則的範例](policy-examples.md)。

## <a name="benefits-of-policy-signing"></a>原則簽署的優點

證明原則最終會決定是否要由 Azure 證明發行證明權杖。 原則也會決定要在證明權杖中產生的宣告。 因此，最重要的是，服務所評估的原則事實上就是系統管理員所撰寫的原則，而且該原則尚未受到外部實體影響或修改。 

信任模型會定義證明提供者的授權模型，以定義和更新原則。  支援的模型有兩種 – 一種以 Azure AD 授權為基礎，另一種以客戶管理的密碼編譯金鑰擁有權為基礎 (稱為「隔離模型」)。  隔離模型會啟用 Azure 證明來確保客戶提交的原則未遭到篡改。

在隔離模型中，系統管理員會建立證明提供者，以在檔案中指定一組受信任的簽署 x.509 憑證。 然後，系統管理員就可以將已簽署的原則新增到證明提供者。 處理證明要求時，Azure 證明會使用標頭中 “jwk” 或 “x5c” 參數所代表的公開金鑰來驗證原則的簽章。  Azure 證明也會驗證要求標頭中的公開金鑰是否在與證明提供者相關聯的信任簽署憑證清單中。 如此一來，信賴憑證者 (Azure 證明) 就可以信任使用已知 X.509 憑證簽署的原則。 

如需範例，請參閱[原則簽署者憑證的範例](policy-signer-examples.md)。

## <a name="attestation-token"></a>證明權杖

Azure 證明回應會是一個 JSON 字串，其值包含 JWT。 Azure 證明會封裝宣告並產生已簽署的 JWT。 簽署作業會使用自我簽署憑證來執行，其主體名稱會符合證明提供者的 AttestUri 元素。

取得 OpenID 中繼資料 API 會傳回 OpenID 設定回應，與 [OpenID Connect 探索通訊協定](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig)所指定的一樣。 API 會從 Azure 證明所使用的簽署憑證中擷取相關中繼資料。

針對 SGX 記憶體保護區產生的 JWT 範例：

```
{
  “alg”: “RS256”,
  “jku”: “https://tradewinds.us.attest.azure.net/certs”,
  “kid”: “f1lIjBlb6jUHEUp1/Nh6BNUHc6vwiUyMKKhReZeEpGc=”,
  “typ”: “JWT”
}.{
  “maa-ehd”: <input enclave held data>,
  “exp”: 1568187398,
  “iat”: 1568158598,
  “is-debuggable”: false,
  “iss”: “https://tradewinds.us.attest.azure.net”,
  “nbf”: 1568158598,
  “product-id”: 4639,
  “sgx-mrenclave”: “”,
  “sgx-mrsigner”: “”,
  “svn”: 0,
  “tee”: “sgx”
}.[Signature]
```
“exp”、“iat”、“iss”、“nbf” 等宣告會由 [JWT RFC](https://tools.ietf.org/html/rfc7517) 所定義，其餘則是由 Azure 證明產生。 如需詳細資訊，請參閱 [Azure 證明所發行的宣告](claim-sets.md)。

## <a name="encryption-of-data-at-rest"></a>待用資料加密

為了保護客戶資料，Azure 證明會將資料保存在 Azure 儲存體中。 Azure 儲存體會在待用資料寫入資料中心時提供加密，並將其解密供客戶存取。 此加密會使用 Microsoft 受控加密金鑰進行。 

除了保護 Azure 儲存體中的資料，Azure 證明也會利用 Azure 磁碟加密 (ADE) 來加密服務 VM。 針對在 Azure 機密計算環境的記憶體保護區中執行的 Azure 證明，目前不支援 ADE 延伸模組。 在這種情況下，若要防止資料儲存在記憶體中，系統會停用分頁檔。 

客戶資料不會保存在 Azure 證明執行個體本機硬碟中。


## <a name="next-steps"></a>後續步驟

- [如何撰寫和簽署證明原則](author-sign-policy.md)
- [使用 PowerShell 設定 Azure 證明](quickstart-powershell.md)
