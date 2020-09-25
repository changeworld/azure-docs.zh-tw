---
title: 適用于 Azure 證明 (VBS) 通訊協定的虛擬化安全性
description: VBS 證明通訊協定
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: e5cc3b5fb7ca38df196119de12d346f5d0346b58
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91342257"
---
# <a name="virtualization-based-security-vbs-attestation-protocol"></a>以虛擬化為基礎的安全性 (VBS) 證明通訊協定 

為了讓 Microsoft Azure 證明提供強大的安全性保證，它所報告的資料是真實的，因此必須從與啟動的虛擬程式和安全核心的啟動中建立一鏈信任。 為了達到此目的，Azure 證明必須證明機器的開機狀態，才能在安全記憶體保護區中建立信任。 作業系統、管理程式和安全核心二進位檔必須由正確的官方 Microsoft 授權單位簽署，並以安全的方式進行設定。 一旦系結信賴平臺模組之間的信任 (TPM) 和虛擬程式的健康情況，我們就可以信任測量開機記錄檔中提供的 VBS IDKS。 有了這項功能，我們就可以驗證記憶體保護區所產生的金鑰組，並 mint 一份證明報告，該報告系結了該金鑰的信任，並包含其他宣告，例如安全性等級和開機證明屬性。

## <a name="protocol-messages"></a>通訊協定訊息

### <a name="init-message"></a>初始訊息

#### <a name="direction"></a>方向

用戶端 > 的 Azure 證明

#### <a name="payload"></a>Payload

```
{
  "type": "aikcert"
}
```

"type" (ASCII string) ：表示要求的證明類型。 目前只支援 "aikcert"。

### <a name="challenge-message"></a>挑戰訊息

#### <a name="direction"></a>方向

Azure 證明-> 用戶端

#### <a name="payload"></a>Payload

```
{

  "challenge": "<BASE64URL(CHALLENGE)>",
  
  "service_context": "<BASE64URL(SERVICECONTEXT)>"
  
}
```

**挑戰** (BASE64URL (的八位) # A3：服務所發出的隨機值。

**service_coNtext** (BASE64URL (八位) # A3：此服務所建立的不透明、加密的內容，包括其他人、挑戰和該挑戰的到期時間。


### <a name="request-message"></a>要求訊息

#### <a name="direction"></a>方向

用戶端 > 的 Azure 證明 

#### <a name="payload"></a>Payload

```
{

  "request": "<JWS>"
  
}
```

**要求** (JWS) ：要求是由 JSON Web 簽章所組成 (JWS) 結構。 受 JWS 保護的標頭和 JWS 承載如下所示。 如同任何 JWS 結構，最終的值是由下列各項所組成：

BASE64URL (UTF8 (JWS 保護的標頭) # A3 | |'.' ||

BASE64URL (JWS 承載) | |'.' ||

BASE64URL (JWS Signature) 

##### <a name="jws-protected-header"></a>JWS 保護的標頭

```
{
  "alg": "PS256",
  "typ": "attReq"
  // no "kid" parameter as the key specified by attest_key MUST sign this JWS to prove possession.
}
```

##### <a name="jws-payload"></a>JWS 承載

JWS 裝載的類型可以是 basic 或 VBS。 當證明辨識項不包含 VBS 資料時，就會使用基本。

基本範例

``` 
{
  "att_type": "basic",
  "att_data": {
    "rp_id": "<URL>",
    "rp_data": "<BASE64URL(RPCUSTOMDATA)>",
    "challenge": "<BASE64URL(CHALLENGE)>",
    "tpm_att_data": {
      "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>",
      "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>",
      "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>",
      "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>",
      "aik_cert": "<BASE64URL(AIKCERTIFICATE)>",
      // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517).
      "aik_pub": {
        "kty": "RSA",
        "n": "<Base64urlUInt(MODULUS)>",
        "e": "<Base64urlUInt(EXPONENT)>"
      },
      "current_claim": "<BASE64URL(CURRENTCLAIM)>",
      "boot_claim": "<BASE64URL(BOOTCLAIM)>"
    },
    // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517).
    "attest_key": {
      "kty": "RSA",
      "n": "<Base64urlUInt(MODULUS)>",
      "e": "<Base64urlUInt(EXPONENT)>"
    },
    "custom_claims": [
      {
        "name": "<name>",
        "value": "<value>",
        "value_type": "<value_type>"
      },
      {
        "name": "<name>",
        "value": "<value>",
        "value_type": "<value_type>"
      }
    ],
    "service_context": "<BASE64URL(SERVICECONTEXT)>"
  }
}
```

VBS 範例

``` 
{
  "att_type": "vbs",
  "att_data": {
    "report_signed": {
      "rp_id": "<URL>",
      "rp_data": "<BASE64URL(RPCUSTOMDATA)>",
      "challenge": "<BASE64URL(CHALLENGE)>",
      "tpm_att_data": {
        "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>",
        "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>",
        "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>",
        "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>",
        "aik_cert": "<BASE64URL(AIKCERTIFICATE)>",
        // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517).
        "aik_pub": {
          "kty": "RSA",
          "n": "<Base64urlUInt(MODULUS)>",
          "e": "<Base64urlUInt(EXPONENT)>"
        },
        "current_claim": "<BASE64URL(CURRENTCLAIM)>",
        "boot_claim": "<BASE64URL(BOOTCLAIM)>"
      },
      // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517).
      "attest_key": {
        "kty": "RSA",
        "n": "<Base64urlUInt(MODULUS)>",
        "e": "<Base64urlUInt(EXPONENT)>"
      },
      "custom_claims": [
        {
          "name": "<name>",
          "value": "<value>",
          "value_type": "<value_type>"
        },
        {
          "name": "<name>",
          "value": "<value>",
          "value_type": "<value_type>"
        }
      ],
      "service_context": "<BASE64URL(SERVICECONTEXT)>"
    },
    "vbs_report": "<BASE64URL(REPORT)>"
  }
}
``` 

**rp_id** (StringOrURI) ：信賴憑證者識別碼。 由服務在電腦識別碼宣告的計算中使用

**rp_data** (BASE64URL (八位) # A3：信賴憑證者傳遞的不透明資料。 這通常是由信賴憑證者作為 nonce，以保證報表的有效期限。

**挑戰** (BASE64URL (八位) # A3：服務所發出的隨機值

**tpm_att_data**： tpm 相關證明資料

- **srtm_boot_log (BASE64URL (八位) # B3 **： srtm boot log as 函式 Tbsi_Get_TCG_Log_Ex （具有記錄類型 = TBS_TCGLOG_SRTM_BOOT）

- **srtm_resume_log (BASE64URL (八位) # B3 **： srtm resume 記錄檔是由具有記錄類型 = Tbsi_Get_TCG_Log_Ex 的函式 TBS_TCGLOG_SRTM_RESUME 所抓取

- **drtm_boot_log (BASE64URL (八位) # B3 **： drtm boot log as 函式 Tbsi_Get_TCG_Log_Ex （具有記錄類型 = TBS_TCGLOG_DRTM_BOOT）

- **drtm_resume_log (BASE64URL (八位) # B3 **： drtm resume 記錄檔是由具有記錄類型 = Tbsi_Get_TCG_Log_Ex 的函式 TBS_TCGLOG_DRTM_RESUME 所抓取

- **aik_cert (BASE64URL (八位) # B3 **：以 property = NCRYPT_CERTIFICATE_PROPERTY 的函式 NCryptGetProperty 所傳回的 aik x.509 憑證

- **aik_pub**： aik 的公開部分，表示為 JSON Web 金鑰 (JWK) 物件 (RFC 7517) 

- **current_claim (BASE64URL (八位) # B3 **：以 dwClaimType = NCRYPT_CLAIM_PLATFORM 的函數 NCryptCreateClaim 傳回的目前 PCR 狀態證明宣告，而參數 NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK 設定為包含所有 PCRs。 服務所傳送的挑戰也應用於此宣告的計算

- **boot_claim (BASE64URL (八位) # B3 **：開機時的 PCR 狀態證明宣告，如同 NCryptCreateClaim with dwClaimType = NCRYPT_CLAIM_PLATFORM 和參數 NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK 設定為包含 all PCRs

**vbs 報表** (BASE64URL (的八位) # A3：依函數 EnclaveGetAttestationReport 所傳回的 vbs 記憶體保護區證明報告。 EnclaveData 參數必須是 report_signed (值的 512 SHA-1 雜湊，包括左括弧和右大括弧) 。 雜湊函數輸入是 UTF8 (report_signed) 

**attest_key**：記憶體保護區金鑰的公開部分，表示為 JSON Web 金鑰 (JWK) 物件 (RFC 7517) 

**custom_claims**：傳送至服務的自訂記憶體保護區宣告陣列，此服務可由原則進行評估。 宣告

- **名稱 (字串) **：宣告的名稱。 此名稱將會附加至證明服務所決定的 url (以避免衝突) 且串連的字串會變成可在原則中使用的宣告類型

- **值 (字串) **：宣告的值

- **value_type (字串) **：宣告值的資料類型

**service_coNtext** (BASE64URL (八位) # A3：此服務所建立的不透明、加密的內容，包括其他人、挑戰和該挑戰的到期時間。

### <a name="report-message"></a>報告訊息

#### <a name="direction"></a>方向

Azure 證明-> 用戶端

#### <a name="payload"></a>Payload

```
{
  "report": "<JWT>"
}
```

**報表** (jwt) ： JSON Web 權杖中的證明報告 (JWT) 格式 (RFC 7519) 。
