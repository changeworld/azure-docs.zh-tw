---
title: Azure 證明概觀
description: 概述用於證明可信執行環境 (TEE) 的 Microsoft Azure 證明解決方案
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: references_regions
ms.openlocfilehash: 2ee906b406f5fd09fc870626f1905541a4270c66
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92670540"
---
# <a name="microsoft-azure-attestation-preview"></a>Microsoft Azure 證明 (預覽)

Microsoft Azure 證明 (預覽) 是用於證明可信執行環境 (TEE) 的解決方案，例如 [Intel® Software Guard Extensions](https://www.intel.com/content/www/us/en/architecture-and-technology/software-guard-extensions.html) (SGX) 記憶體保護區 (enclave) 和[虛擬化型安全性](/windows-hardware/design/device-experiences/oem-vbs) (VBS) 記憶體保護區 (enclave) 之類的環境。 記憶體保護區證明是確認記憶體保護區安全且值得信賴的程序。

證明程序會證明軟體二進位檔已在信任的平台上正確地具現化。 然後，遠端信賴憑證者就可以放心地相信，只有這類預期的軟體會在受信任的硬體上執行。 Azure 證明是經過統整且面向客戶的證明服務與架構。

Azure 證明提供最新的安全性典範，例如 [Azure 機密運算](../confidential-computing/overview.md)和智慧邊緣保護。 客戶一直希望能夠獨立驗證機器的位置、該機器上的虛擬機器 (VM) 狀態，以及該 VM 上所執行記憶體保護區內的環境。 Azure 證明可支援這些要求，以及許多額外的客戶要求。

Azure 證明會從計算實體接收證據、將其轉換成一組宣告、針對可設定的原則進行驗證，然後為宣告式應用程式 (例如信賴憑證者和稽核授權單位) 產生密碼編譯證明。

## <a name="use-cases"></a>使用案例

Azure 證明可為多個環境和特殊使用案例提供完整的證明服務。

### <a name="sgx-attestation"></a>SGX 證明

SGX 指的是硬體等級的隔離，可在某些 Intel CPU 模型上受到支援。 SGX 可讓程式碼在已清理過的區間 (稱為 SGX 記憶體保護區) 中執行。 接著，硬體會管理存取權和記憶體權限，以確保能透過適當隔離來將攻擊面最小化。

用戶端應用程式可以設計為使用 SGX 記憶體保護區，方法是委派要在這些記憶體保護區內進行的安全性敏感工作。 如此一來，這類應用程式就可以使用 Azure 證明，定期在記憶體保護區中建立信任及其存取敏感性資料的能力。

### <a name="vbs-attestation"></a>VBS 證明

VBS 是記憶體保護區的軟體型架構，其以 Hyper-V 為基礎來保護記憶體。 VBS 會阻止主機管理員程式碼，以及本機和雲端服務管理員存取 VBS 記憶體保護區中的資料或影響其執行。

與 SGX 技術類似，Azure 證明可針對已設定的原則來驗證 VBS 記憶體保護區，並發出認證聲明作為有效性證明。

### <a name="open-enclave"></a>Open Enclave
[Open Enclave](https://openenclave.io/sdk/) (OE) 是程式庫的集合，其目標是為開發人員建立一個已統整的記憶體保護區抽象概念，進而建立以 TEE 為基礎的應用程式。 其提供通用的安全應用程式模型，可將平台特定性降至最低。 Microsoft 將其視為將硬體型記憶體保護區技術 (例如 SGX) 大眾化，以及提升 Azure 功能的重要墊腳石。

OE 會將驗證記憶體保護區證據的特定需求標準化。 這讓 OE 成為極適合向 Azure 證明取用證明的工具。

## <a name="azure-attestation-can-run-in-a-tee"></a>Azure 證明可在 TEE 中執行

Azure 證明對機密計算案例而言很重要，因為其會執行下列動作：

- 確認記憶體保護區證據是否有效。
- 針對客戶定義的原則評估記憶體保護區證據。
- 管理及儲存租用戶特有的原則。
- 產生並簽署信賴憑證者用來與記憶體保護區互動的權杖。

Azure 證明是為了在兩種環境類型中執行而建立：
- 在已啟用 SGX 的 TEE 中執行 Azure 證明。
- 在非 TEE 中環境執行 Azure 證明。

Azure 證明的客戶已表達希望 Microsoft 不要介入信任運算基礎 (TCB) 操作的要求。 這是為了防止 Microsoft 實體 (例如 VM 管理員、主機管理員和 Microsoft 開發人員) 修改證明要求、原則和 Azure 證明發行的權杖。 Azure 證明也是為了在 TEE 中執行而建立，其中 Azure 證明的功能 (例如引用 (quote) 驗證、權杖產生及權杖簽署) 會移至 SGX 記憶體保護區中。

## <a name="why-use-azure-attestation"></a>為何要使用 Azure 證明

Azure 證明是證明 TEE 的慣用選擇，因為其提供下列優點： 

- 可證明多個 TEE (例如 SGX 記憶體保護區和 VBS 記憶體保護區) 的統一架構
- 多租用戶服務，可讓您設定自訂證明提供者和原則來限制權杖產生
- 提供預設提供者來進行證明，使用者無須進行任何設定
- 在 SGX 記憶體保護區中搭配使用時可保護其資料
- 高可用性服務，提供服務等級協定 (SLA)

## <a name="business-continuity-and-disaster-recovery-bcdr-support"></a>商務持續性和災害復原 (BCDR) 支援

適用於 Azure 證明的[商務持續性和災害復原](../best-practices-availability-paired-regions.md) (BCDR) 可減輕因重大可用性問題或區域中災難事件所造成的服務中斷問題。

以下是 BCDR 目前支援的區域
- 美國東部 2 => 與美國中部配對。
- 美國中部 => 與美國東部 2 配對。

在兩個區域中部署的叢集將會在正常情況下獨立運作。 當一個區域發生錯誤或中斷時，下列作業就會開始運作：

- Azure 證明 BCDR 會提供無縫容錯移轉，客戶不需要採取任何額外的步驟來進行復原
- 該區域的 [Azure 流量管理員](../traffic-manager/index.yml)會偵測到健康情況探查已降級，並將端點切換至配對的區域
- 現有的連線將無法使用，而且會收到內部伺服器錯誤或逾時問題
- 所有控制平面作業將會遭到封鎖。 客戶將無法在主要區域中建立證明提供者和更新原則
- 所有資料平面作業 (包括證明) 呼叫都會繼續在主要區域中運作

## <a name="next-steps"></a>後續步驟
- 了解 [Azure 證明基本概念](basic-concepts.md)
- [如何撰寫和簽署證明原則](author-sign-policy.md)
- [使用 PowerShell 設定 Azure 證明](quickstart-powershell.md)