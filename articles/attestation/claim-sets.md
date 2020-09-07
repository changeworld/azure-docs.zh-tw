---
title: Azure 證明宣告集
description: Azure 證明的宣告集。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 555dccbd3c2dfe61bac5891514deface6f8a877d
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236864"
---
# <a name="claim-sets"></a>宣告集

在使用 Microsoft Azure 證明來證明記憶體保護區的程序中，所產生的宣告可分為以下類別：

- **連入宣告**：剖析證明證據之後，Microsoft Azure 證明產生的宣告。

- **連出宣告**：由 Azure 證明以輸出形式建立的宣告。 其包含所有最終應在證明權杖中的宣告。

- **屬性宣告**：由 Azure 證明以輸出形式建立的宣告。 其中包含所有代表證明權杖屬性的宣告，例如報表的編碼方式、報表的有效期間等等。

下列是由 JWT RFC 定義，並由 Azure 證明在回應物件中使用的宣告：

- **"iss" (簽發者) 宣告**："Iss" (簽發者) 宣告會識別發出 JWT 的主體。 此宣告的處理方式通常取決於應用程式。 "iss" 值是區分大小寫的字串，其中包含 StringOrURI 值。
- **"iat" (簽發時間) 宣告**："iat" (簽發時間) 宣告會識別發出 JWT 的時間。 此宣告可用於判斷 JWT 的存留期。 其值「必須」是包含 NumericDate 值的數字。
- **"exp" (到期時間) 宣告**："exp" (到期時間) 宣告會識別到期時間，等於或晚於此時間都不得接受 JWT 以進行處理。 若要處理 "exp" 宣告，目前的日期/時間「必須」早於 "exp" 宣告中所列的到期日期/時間。

  注意:考慮到時鐘誤差，簽發時間 (iat) 中會加入 5 分鐘的緩衝時間。
- **"nbf" (生效時間) 宣告**："nbf" (生效時間) 宣告會識別生效時間，在此時間之前「不得」接受 JWT 以進行處理。 若要處理 "nbf" 宣告，目前的日期/時間「必須」晚於或等同 "nbf" 宣告中所列的「生效時間」日期/時間。
  注意:考慮到時鐘誤差，簽發時間 (iat) 中會加入 5 分鐘的緩衝時間。

## <a name="claims-issued-by-azure-attestation-in-sgx-enclaves"></a>Azure 證明在 SGX 記憶體保護區中發出的宣告

### <a name="incoming-claims"></a>連入宣告 

- **$is-debuggable**：布林值，指出記憶體保護區是否已啟用偵錯
- **$sgx-mrsigner**：引用中 "mrsigner" 欄位的十六進位編碼值
- **$sgx-mrenclave**：引用中 "mrenclave" 欄位的十六進位編碼值
- **$product-id**
- **$svn**：引用中已編碼的安全性版本號碼 
- **$tee**：記憶體保護區的類型 

### <a name="outgoing-claims"></a>連出宣告

- **is-debuggable**：布林值，指出記憶體保護區是否已啟用偵錯
- **sgx-mrsigner**：引用中 "mrsigner" 欄位的十六進位編碼值
- **sgx-mrenclave**：引用中 "mrenclave" 欄位的十六進位編碼值
- **product-id**
- **svn**：引用中已編碼的安全性版本號碼 
- **tee**：記憶體保護區的類型 
- **maa-ehd**：證明要求中所指定「記憶體保護區保留資料」的 Base64Url 編碼版本 
- **aas-ehd**：證明要求中所指定「記憶體保護區保留資料」的 Base64Url 編碼版本 

## <a name="claims-issued-by-azure-attestation-in-vbs-enclaves"></a>Azure 證明在 VBS 記憶體保護區中發出的宣告

### <a name="incoming-claims-can-also-be-used-as-outgoing-claims"></a>連入宣告 (也可以作為連出宣告)

- **aikValidated**：布林值，包含證明識別金鑰 (AIK) 憑證是否已驗證的資訊。
- **aikPubHash**：包含 base64 的字串 (SHA256(DER 格式的 AIK 公開金鑰))。
- **tpmVersion**： 包含信賴平台模組 (TPM) 主要版本的整數值。
- **secureBootEnabled**：布林值，指出是否已啟用安全開機。
- **iommuEnabled**：布林值，指出是否已啟用輸入輸出記憶體管理單位 (Iommu)。
- **bootDebuggingDisabled**：布林值，指出是否已停用開機偵錯。
- **notSafeMode**：布林值，指出 Windows 是否在安全模式下執行。
- **notWinPE**：布林值，指出 Windows 是否在 WinPE 模式下執行。
- **vbsEnabled**：布林值，指出是否已啟用 VBS。
- **vbsReportPresent**：布林值，指出是否可以使用 VBS 記憶體保護區報表。
- **enclaveAuthorId**：字串值，包含記憶體保護區作者識別碼 (記憶體保護區主要模組的作者識別碼) 的 Base64Url 編碼值。
- **enclaveImageId**：字串值，包含記憶體保護區映像識別碼 (記憶體保護區主要模組的映像識別碼) 的 Base64Url 編碼值。
- **enclaveOwnerId**：字串值，包含記憶體保護區擁有者識別碼 (記憶體保護區擁有者的識別碼) 的 Base64Url 編碼值。
- **enclaveFamilyId**：字串值，包含記憶體保護區系列識別碼的 Base64Url 編碼值。記憶體保護區主要模組的系列識別碼。
- **enclaveSvn**：整數值，包含記憶體保護區主要模組的安全性版本號碼。
- **enclavePlatformSvn**：整數值，包含記憶體保護區所在平台的安全性版本號碼。
- **enclaveFlags**：enclaveFlags 宣告是一個整數值，其中包含描述記憶體保護區執行階段原則的旗標。
  
### <a name="outgoing-claims"></a>連出宣告

- **policy_hash**：字串值，包含 BASE64URL 所計算原則文字的 SHA256 雜湊 (SHA256(BASE64URL(UTF8(原則文字))))。
- **policy_signer**：包含 JWK 的字串值，該 JWK 具有已簽署原則標頭中的公開金鑰或憑證鏈結。
- **ver (版本)** ：包含報表版本的字串值。 目前為 1.0。
- **cnf (確認) 宣告**："cnf" 宣告會用來識別擁有權證明金鑰。 如 RFC 7800 中所定義，確認宣告包含已證明之記憶體保護區金鑰的公開部分，並以 JSON Web 金鑰 (JWK) 物件 (RFC 7517) 表示。
- **rp_data (信賴憑證者資料)** ：在要求中指定的信賴憑證者資料 (如果有的話)，由信賴憑證者當做 nonce 使用，以保證報告的有效期限。
- **"jti" (JWT 識別碼) 宣告**："jti" (JWT 識別碼) 宣告會提供 JWT 的唯一識別碼。 識別碼值的指派方式會確保不會意外將相同的值指派給不同的資料物件。

### <a name="property-claims"></a>屬性宣告

- **report_validity_in_minutes**：表示權杖有效時間長度的整數宣告。
  - **預設值 (時間)** ：一天 (以分鐘為單位)。
  - **最大值 (時間)** ：一年 (以分鐘為單位)。
- **omit_x5c**：布林值宣告，指出 Azure 證明是否應省略用來提供服務真確性證明的憑證。 若為 true，則會將 x5t 新增至證明權杖。 如果為 false (預設值)，則會將 x5c 新增至證明權杖。

## <a name="next-steps"></a>後續步驟
- [如何撰寫和簽署證明原則](author-sign-policy.md)
- [使用 PowerShell 設定 Azure 證明](quickstart-powershell.md)
