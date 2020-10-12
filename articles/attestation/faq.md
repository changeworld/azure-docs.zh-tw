---
title: 常見問題集
description: Microsoft Azure 證明相關常見問題的解答
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 39f628845bdc9d54b48b1c8037f4a506a9d5c00a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89236613"
---
# <a name="frequently-asked-questions-for-microsoft-azure-attestation"></a>Microsoft Azure 證明的常見問題

本文提供關於 [Azure 證明](overview.md)的一些最常見問題的解答。

如果您的 Azure 問題未在本文中解決，您也可以在 [ [Azure 支援] 頁面](https://azure.microsoft.com/support/options/)上提交 Azure 支援要求。

## <a name="what-is-azure-pck-caching-service-and-its-role-in-enclave-attestation"></a>什麼是 Azure PCK 快取服務及其在記憶體保護區證明中的角色

Azure PCK 快取服務會定義 azure 機密運算的 Azure 安全性基準， [ (ACC) ](../confidential-computing/overview.md) 來自 Intel 的節點，並快取資料。 在 (Tee) 驗證信任的執行環境時，Azure 證明將會進一步使用快取的資訊。  

Azure PCK 快取服務：
   - 提供高可用性 
   - 減少對外部託管服務和網際網路連線的相依性。
   - 提取 intel 憑證、Crl、信任的運算基底的最新版本， (TCB) 資訊，並將 ACC 節點的報價記憶體保護區識別。 如此一來，服務就會在驗證 Tee 時，確認 azure 證明所要參考的 Azure 安全性基準，以大幅降低因為 Intel 憑證失效或撤銷而產生的證明失敗  

## <a name="is-sgx-attestation-supported-by-azure-attestation-in-non-azure-environments"></a>Azure 證明在非 Azure 環境中支援的 SGX 證明

Azure 證明取決於 Azure PCK 快取服務所指定的安全性基準，以驗證 Tee。 Azure PCK 快取服務目前設計為僅支援 Azure 機密運算節點。 

## <a name="what-validations-does-azure-attestation-perform-for-attesting-sgx-enclaves"></a>Azure 證明針對證明 SGX 記憶體保護區執行了哪些驗證

Azure 證明是一種整合架構，可從遠端證明不同類型的 Tee。 Azure 證明：

   - 驗證已簽署記憶體保護區報價的受根信任是否屬於 Intel。
   - 驗證記憶體保護區報價是否符合 azure PCK 快取服務所定義的 Azure 安全性基準。
   - 驗證證明要求物件中記憶體保護區保存資料 (EHD) 的 SHA256 雜湊是否符合記憶體保護區引號中 reportData 欄位的前32個位元組。
   - 可讓客戶建立證明提供者並設定自訂原則。 除了上述驗證之外，Azure 證明也會根據原則評估記憶體保護區報價。 原則會定義記憶體保護區的授權規則，也會規定產生證明權杖的發佈規則。 若要確認預定的軟體是否正在記憶體保護區中執行，客戶可以新增授權規則，以確認記憶體保護區引號中的 **mrsigner** 和 **mrenclave** 欄位是否符合客戶二進位檔的值。

## <a name="how-can-a-verifier-obtain-the-collateral-for-sgx-attestation-supported-by-azure-attestation"></a>驗證器如何取得 Azure 證明所支援的 SGX 證明的相關宣傳

一般情況下，針對 Intel 作為根信任的證明模型，證明用戶端會與記憶體保護區 Api 交談以提取記憶體保護區辨識項。 記憶體保護區 Api 會在內部呼叫 Intel PCK 快取服務，以提取要證明之節點的 Intel 憑證。 憑證是用來簽署記憶體保護區的辨識項，進而產生遠端 attestable 的附屬品。  

您可以針對 Azure 證明執行相同的進程。 不過，若要利用 Azure PCK 快取服務所提供的優點，在安裝 ACC 虛擬機器之後，建議您安裝 [AZURE DCAP 程式庫](https://www.nuget.org/packages/Microsoft.Azure.DCAP)。 根據與 Intel 的合約，安裝 Azure DCAP 程式庫時，產生記憶體保護區辨識項的要求會從 Intel PCK 快取服務重新導向至 Azure PCK 快取服務。 Windows 和 Linux 架構的環境中支援 Azure DCAP 程式庫。

## <a name="how-to-shift-to-azure-attestation-from-other-attestation-models"></a>如何從其他證明模型轉移至 Azure 證明

- 安裝 Azure 機密計算虛擬機器之後，請將 Azure DCAP 程式庫安裝 ([Windows/](https://www.nuget.org/packages/Microsoft.Azure.DCAP/) [Linux](https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/a/az-dcap-client/)) ，以利用 Azure PCK 快取服務所提供的優點。
- 您必須撰寫遠端證明用戶端，以取得記憶體保護區辨識項，並將要求傳送至 Azure 證明。 請參閱程式 [代碼範例](/samples/browse/?expanded=azure&terms=attestation) 以取得參考 
- 證明要求可以傳送至預設提供者或自訂證明提供者的 REST API 端點 
- Azure 證明 Api 會受到 Azure AD 驗證的保護。 因此，叫用證明 Api 的用戶端必須能夠取得並傳遞證明要求中有效的 Azure AD 存取權杖 

## <a name="how-can-the-relying-party-verify-the-integrity-of-attestation-token"></a>信賴憑證者如何驗證證明權杖的完整性

Azure 證明所產生的證明權杖會使用自我簽署憑證進行簽署。 憑證是透過 [OpenID 中繼資料端點](/rest/api/attestation/metadataconfiguration/get)來公開。 信賴憑證者可以從這個端點取出簽署憑證，並執行證明權杖的簽章驗證。 證明權杖的有效時間為8小時。 

## <a name="how-to-identify-the-certificate-to-be-used-for-signature-verification-from-the-openid-metadata-endpoint"></a>如何識別要用於從 OpenID 中繼資料端點進行簽章驗證的憑證

OpenID 中繼資料端點中公開的多個憑證對應至不同的使用案例 (例如，Azure 證明支援的 SGX 證明) 。 根據 [RFC 7515](https://tools.ietf.org/html/rfc7515)所指定的標準，具有金鑰識別碼 (小孩) 與證明權杖標頭中的 *小孩* 參數相符的憑證，將用於簽章驗證。 如果找不到相符的 **小孩** ，則應該嘗試由 OpenID 中繼資料端點公開的所有憑證。

## <a name="is-it-possible-for-the-relying-party-to-share-secrets-with-the-validated-trusted-execution-environments-tees"></a>信賴憑證者是否有可能與已驗證的信任執行環境共用秘密 (Tee) 

在記憶體保護區內產生的公開金鑰可以在用戶端傳送到 Azure 證明之證明要求物件的記憶體保護區保留資料 (EHD) 屬性中表示。 確認 EHD 的 SHA256 雜湊是否以引號的 reportData 欄位表示，Azure 證明在證明權杖中包含 EHD。 信賴憑證者可以使用來自已驗證證明回應的 EHD 來加密密碼，並與記憶體保護區共用。 如需詳細資訊，請參閱 [Azure 證明基本概念](basic-concepts.md) 。
