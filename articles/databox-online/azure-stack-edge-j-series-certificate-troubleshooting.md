---
title: 使用 GPU 進行 Azure Stack Edge Pro 的憑證疑難排解 |Microsoft Docs
description: 說明針對 Azure Stack Edge Pro GPU 裝置的憑證錯誤進行疑難排解。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 11/17/2020
ms.author: alkohli
ms.openlocfilehash: 18e90bcfe7238ae998ac677a9d7eebef0ffa8939
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874248"
---
# <a name="troubleshooting-certificate-errors"></a>針對憑證錯誤進行疑難排解

本文提供將憑證安裝至 Azure Stack Edge Pro 裝置時，常見憑證錯誤的疑難排解。

## <a name="common-certificate-errors"></a>常見的憑證錯誤

下表顯示常見的憑證錯誤，以及這些錯誤和可能解決方案的詳細資訊：

> [!NOTE]
> 出現 &#8220;{0} ， {1} ，...，{n} &#8221; 指出位置參數。 位置參數會根據您所使用的憑證來取得值。

| 錯誤碼 | 描述 |
|---|---|
| CertificateManagement_UntrustedCertificate | 具有主體名稱的憑證 {0} 違反了憑證鏈。 上傳此憑證之前，請先上傳簽署鏈憑證。|
| CertificateManagement_DeviceNotRegistered| 您的裝置未啟用。 您只能在啟用之後上傳支援憑證。|
| CertificateManagement_ExpiredCertificate | 具有類型的憑證 {0} 已過期或即將到期。 檢查憑證到期日，並在需要時帶入新的憑證。|
| CertificateManagement_FormatMismatch | 不支援憑證格式。 檢查憑證格式，如有需要，請帶入新的憑證。  預期 {0} ，找到 {1} 。 |
| CertificateManagement_GenericError | 無法執行憑證管理操作。 請在幾分鐘內重試此操作。 如果問題持續發生，請連絡 Microsoft 支援服務。 |
| CertificateManagement_HttpsBindingFailure | 主體名稱的憑證 {0} 無法建立安全的 HTTPs 通道。 檢查您已上傳的憑證，並視需要帶出新的憑證。 裝置節點憑證發生此錯誤。|
| CertificateManagement_IncorrectKeyCertSignKeyUsage | 具有主體名稱的憑證 {0} 不應使用金鑰使用憑證簽署。 檢查憑證的金鑰使用方式，如有需要，請帶入新的憑證。 簽署鏈憑證發生此錯誤。 |
| CertificateManagement_IncorrectKeyNumber | 具有主體名稱的憑證 {0} 有不正確的金鑰編號 {1} 。 檢查憑證的金鑰編號，如有需要，請帶入新的憑證。|
| CertificateManagement_InvalidP7b | 上傳的憑證檔案無效。  檢查憑證格式，如有需要，請帶入新的憑證。|
| CertificateManagement_KeyAlgorithmNotRSA | 此憑證不使用 RSA 金鑰演算法。 僅支援 RSA 憑證。 |
| CertificateManagement_KeySizeNotSufficient | 具有主體名稱的憑證 {0} 的金鑰大小不足 {1} 。 最小金鑰大小為4096。|
| CertificateManagement_MissingClientOid | 具有主體名稱的憑證沒有 {0} 用戶端驗證 OID。 檢查您的憑證屬性，並在需要時帶入新的憑證。|
| CertificateManagement_MissingDigitalSignatureKeyUsage | 具有主體名稱的憑證沒有 {0} 金鑰使用方式的數位簽章。 檢查您的憑證屬性，並在需要時帶入新的憑證。 |
| CertificateManagement_MissingKeyCertSignKeyUsage | 具有主體名稱的憑證 {0} 未在金鑰使用方式中簽署憑證。 檢查您的憑證屬性，並在需要時帶入新的憑證。|
| CertificateManagement_MissingKeyEnciphermentKeyUsage | 具有主體名稱的憑證沒有金鑰 {0} 使用方式的金鑰加密。 檢查您的憑證屬性，並在需要時帶入新的憑證。 |
| CertificateManagement_MissingServerOid | 具有主體名稱的憑證沒有 {0} 伺服器驗證 OID。 檢查您的憑證屬性，並在需要時帶入新的憑證。|
| CertificateManagement_NameMismatch | 憑證類型不符。 預期的範圍： {0} ，找到 {1} 。 上傳適當的憑證。|
| CertificateManagement_NoPrivateKeyPresent | 具有主體名稱的憑證 {0} 沒有私密金鑰存在。 上傳具有私密金鑰的 .pfx 憑證。|
| CertificateManagement_NoRSACryptoPrivateKey | 無法存取具有主體名稱的憑證私密金鑰 {0} 。 請確定您使用的是支援的憑證。 僅支援 Microsoft RSA/Schannel 密碼編譯提供者。 |
| CertificateManagement_NotSelfSignedCertificate | 具有主體名稱的憑證 {0} 不是自我簽署的。 根憑證應該自我簽署 |
| CertificateManagement_NotSupportedOnVirtualAppliance | 虛擬裝置不支援這項操作。 此錯誤表示只有在策略性雲端設備中執行的資料箱閘道，才會進行簽署。 透過 Windows PowerShell 管理裝置時，會發生此錯誤。|
| CertificateManagement_SelfSignedCertificate | 具有主體名稱的憑證 {0} 是自我簽署的。 上傳已正確簽署的憑證。|
| CertificateManagement_SignatureAlgorithmSha1 | 具有主體名稱的憑證 {0} 具有不支援的簽章演算法。 SHA1-不支援 RSA。 |
| CertificateManagement_SubjectNamesInvalid | 具有主體名稱的憑證沒有 {0} 憑證的正確主體名稱或主體別名 {1} 。 檢查您已上傳的憑證，並視需要帶出新的憑證。 您也應該檢查 DNS 名稱，以符合 SAN 名稱。|
| CertificateManagement_UnreadableCertificate | 無法讀取類型的憑證 {0} 。 當憑證無法讀取或損毀時，就會發生此錯誤。 帶入新的憑證。|
| CertificateSubjectNotFound | 找不到具有主體名稱的憑證 {0} 。 帶入新的憑證。|
| CertificateRotationGenericFailure | 一或多個憑證輪替失敗。 請在幾分鐘後重試。 如果問題持續發生，請連絡 Microsoft 支援服務。|
| CertificateImportFailure | {0}未在節點上匯入具有指紋的憑證 {1} 。 如果問題持續發生，請連絡 Microsoft 支援服務。 |
| CertificateApplyFailure | 具有指紋的憑證 {0} 未套用於節點 {1} 。 如果問題持續發生，請連絡 Microsoft 支援服務。|
| NodeNotReachable | 無法驗證的憑證 {0} 。 檢查系統硬體和軟體健康情況。|
| 取得錯誤碼 | 無法存取此憑證的私密金鑰。 請確定您使用的是支援的憑證。 僅支援 Microsoft RSA/Schannel 密碼編譯提供者。 |


## <a name="next-steps"></a>後續步驟

[憑證需求](azure-stack-edge-j-series-certificate-requirements.md)