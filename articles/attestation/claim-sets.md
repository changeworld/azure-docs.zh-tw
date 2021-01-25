---
title: Azure 證明宣告集
description: Azure 證明的宣告集。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: eb08bb262806cb662822a75898196546a5c1058e
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762540"
---
# <a name="claim-sets"></a>宣告集

在使用 Microsoft Azure 證明來證明記憶體保護區的程序中，所產生的宣告可分為以下類別：

- 連 **入宣告**：剖析證明辨識項之後 Microsoft Azure 證明產生的宣告，並且可供原則作者用來定義自訂原則中的授權規則

- 連 **出宣告**： Azure 證明所產生的宣告，並包含最後在證明權杖中的所有宣告

- **屬性宣告**：由 Azure 證明以輸出形式建立的宣告。 其中包含所有代表證明權杖屬性的宣告，例如報表的編碼方式、報表的有效期間等等。

### <a name="common-incoming-claims-across-all-attestation-types"></a>所有證明類型之間的一般傳入宣告

以下是 Azure 證明所產生的宣告，而且可以用來定義自訂原則中的授權規則：
- **x-ms-ver**： JWT 架構版本 (應為 "1.0" ) 
- **x-ms 證明類型**：代表證明類型的字串值 
- **x-ms-原則-雜湊**：計算為 BASE64URL (SHA256 (utf8 ( (的 Azure 證明評估原則雜湊 (# A6 # A7 # A8 # A9
- **x-ms-原則簽署者**：具有 "jwk" 成員的 JSON 物件，代表客戶在上傳已簽署的原則時用來簽署其原則的金鑰

下列宣告會被視為已淘汰，但受到完整支援。 建議使用未淘汰的宣告名稱。

已淘汰的宣告 | 建議的宣告 
--- | --- 
ver | x-ms-ver
tee | x-ms-證明類型
maa-policyHash | x-ms-原則-雜湊
policy_hash | x-ms-原則-雜湊
policy_signer | x-ms-原則-簽署者

### <a name="common-outgoing-claims-across-all-attestation-types"></a>所有證明類型的一般傳出宣告

以下是由 [IETF JWT](https://tools.ietf.org/html/rfc7519) 定義並在回應物件中由 Azure 證明使用的宣告：

- **"jti" (JWT 識別碼) 宣告**
- **「iss」 (簽發者) 宣告**
- **"iat" (于) 宣告發出**
- **"exp" (到期時間) 宣告**
- **"nbf" (不在) 宣告之前**

以下是由 [IETF](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9) 所定義的宣告，以及 Azure 證明在回應物件中所使用的宣告：
- **"Nonce 宣稱" (Nonce)**

根據預設，系統會根據傳入的宣告來產生以下宣告
- **x-ms-ver**： JWT 架構版本 (應為 "1.0" ) 
- **x-ms 證明類型**：代表證明類型的字串值 
- **x-ms-原則雜湊**：字串值，包含由 BASE64URL 所計算之原則文字的 SHA256 雜湊， (SHA256 (UTF8 (BASE64URL (utf8 (原則文字) # A6 # A7 # A8 # A9
- **x-ms-原則-簽署者**：包含具有公開金鑰的 JWK 或已簽署原則標頭中的憑證鏈。 x-ms-原則-只有在已簽署原則時才會新增簽署者

## <a name="claims-specific-to-sgx-enclaves"></a>SGX 記憶體保護區特定的宣告

### <a name="incoming-claims-specific-to-sgx-attestation"></a>針對 SGX 證明專屬的傳入宣告

下列宣告是由服務針對 SGX 證明產生的，可用來定義自訂原則中的授權規則：
- **x-ms-sgx-可調試**：布林值，指出記憶體保護區是否已啟用偵錯工具
- **x-毫秒-sgx-產品識別碼**
- **mrsigner**：引號的 "mrsigner" 欄位的十六進位編碼值
- **mrenclave**：引號的 "mrenclave" 欄位的十六進位編碼值
- **x-ms-sgx-svn**：以引號編碼的安全性版本號碼 

### <a name="outgoing-claims-specific-to-sgx-attestation"></a>針對 SGX 證明專屬的傳出宣告

下列宣告是由服務所產生，並且包含在適用于 SGX 證明的回應物件中：
- **x-ms-sgx-可調試**：布林值，指出記憶體保護區是否已啟用偵錯工具
- **x-毫秒-sgx-產品識別碼**
- **mrsigner**：引號的 "mrsigner" 欄位的十六進位編碼值
- **mrenclave**：引號的 "mrenclave" 欄位的十六進位編碼值
- **x-ms-sgx-svn**：以引號編碼的安全性版本號碼 
- **x-ms-sgx-ehd**：記憶體保護區保留的資料格式為 BASE64URL (記憶體保護區保存的資料) 
- **x-ms-sgx-附屬** 元件：描述用來執行證明之附屬品的 JSON 物件。 X--sgx-加值宣告的值是具有下列索引鍵/值組的嵌套 JSON 物件：
    - **qeidcertshash**： QE 身分識別頒發證書的 SHA256 值
    - **qeidcrlhash**： QE 身分識別頒發證書 CRL 清單的 SHA256 值
    - **qeidhash**： QE 身分識別附屬物的 SHA256 值
    - **quotehash**：已評估之引號的 SHA256 值
    - **tcbinfocertshash**： TCB 資訊頒發證書的 SHA256 值
    - **tcbinfocrlhash**： TCB 資訊頒發證書 CRL 清單的 SHA256 值
    - **tcbinfohash**： JSON 物件，描述用來執行證明的材料

下列宣告會被視為已淘汰，但會受到完整支援，未來將繼續包含這些宣告。 建議使用未淘汰的宣告名稱。

已淘汰的宣告 | 建議的宣告
--- | --- 
$is-調試 | x-ms-sgx-可進行調試
$sgx-mrsigner | x-ms-sgx-mrsigner
$sgx-mrenclave | x-ms-sgx-mrenclave
$product-id | x-毫秒-sgx-產品識別碼
$svn | x-ms-sgx-svn
$tee | x-ms-證明類型
maa-ehd | x-ms-sgx-ehd
.aas-ehd | x-ms-sgx-ehd
maa-attestationcollateral | x-ms-sgx-附屬

## <a name="claims-specific-to-trusted-platform-module-tpm-vbs-attestation"></a>信賴平臺模組的特定宣告 (TPM) /VBS 證明

### <a name="incoming-claims-for-tpm-attestation"></a>TPM 證明的傳入宣告

Azure 證明針對 TPM 證明發出的宣告。 宣告的可用性取決於為證明提供的辨識項。

- **aikValidated**：如果證明身分識別金鑰 (AIK) 憑證已驗證，則為包含資訊的布林值
- **aikPubHash**：包含 BASE64 (SHA256 (AIK 公開金鑰（DER 格式）的字串) # A3
- **tpmVersion**：包含信賴平臺模組 (TPM) 主要版本的整數值
- **secureBootEnabled**：布林值，指出是否已啟用安全開機
- **iommuEnabled**：布林值，指出是否已啟用輸入/輸出記憶體管理單位 (Iommu) 
- **bootDebuggingDisabled**：布林值，指出開機偵錯工具是否已停用
- **notSafeMode**：布林值，指出 Windows 是否未在安全模式下執行
- **notWinPE**：布林值，指出 Windows 是否未在 WinPE 模式中執行
- **vbsEnabled**：布林值，指出是否已啟用 VBS
- **vbsReportPresent**：布林值，指出 VBS 記憶體保護區報表是否可用

### <a name="incoming-claims-for-vbs-attestation"></a>VBS 證明的傳入宣告

Azure 證明針對 VBS 證明所發出的宣告是除了提供給 TPM 證明的宣告之外。 宣告的可用性取決於為證明提供的辨識項。

- **enclaveAuthorId**：包含記憶體保護區作者識別碼之 Base64Url 編碼值的字串值-記憶體保護區主要模組的作者識別碼
- **enclaveImageId**：包含記憶體保護區映射識別碼之 Base64Url 編碼值的字串值-記憶體保護區主要模組的映射識別碼
- **enclaveOwnerId**：包含記憶體保護區擁有者識別碼之 Base64Url 編碼值的字串值-記憶體保護區的擁有者識別碼
- **enclaveFamilyId**：包含記憶體保護區系列識別碼之 Base64Url 編碼值的字串值。 記憶體保護區主要模組的系列識別碼
- **enclaveSvn**：包含記憶體保護區主要模組之安全性版本號碼的整數值
- **enclavePlatformSvn**：包含裝載記憶體保護區平臺之安全性版本號碼的整數值
- **enclaveFlags**： enclaveFlags 宣告是一個整數值，其中包含描述記憶體保護區之執行時間原則的旗標。

### <a name="outgoing-claims-specific-to-tpm-and-vbs-attestation"></a>TPM 和 VBS 證明專屬的輸出宣告

- **my.cnf (確認)**： "my.cnf" 宣告用來識別擁有權證明金鑰。 如 RFC 7800 中所定義的確認宣告，包含證明記憶體保護區金鑰的公開部分，表示為 JSON Web 金鑰， (JWK) 物件 (RFC 7517) 
- **rp_data (信賴憑證者資料)**：在要求中指定的信賴憑證者資料（如果有的話）會由信賴憑證者作為 nonce 使用，以保證報告的有效性。 只有在有 rp_data 時，才會新增 rp_data

### <a name="property-claims"></a>屬性宣告

- **report_validity_in_minutes**：表示權杖有效時間長度的整數宣告。
  - **預設值 (時間)** ：一天 (以分鐘為單位)。
  - **最大值 (時間)** ：一年 (以分鐘為單位)。
- **omit_x5c**：布林值宣告，指出 Azure 證明是否應省略用來提供服務真確性證明的憑證。 若為 true，則會將 x5t 新增至證明權杖。 如果為 false (預設值)，則會將 x5c 新增至證明權杖。

## <a name="next-steps"></a>後續步驟
- [如何撰寫和簽署證明原則](author-sign-policy.md)
- [使用 PowerShell 設定 Azure 證明](quickstart-powershell.md)
