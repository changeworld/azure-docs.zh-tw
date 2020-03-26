---
title: 專用硬體安全模組 (HSM) 是什麼？ - Azure 專用硬體安全模組 (HSM) | Microsoft Docs
description: Azure 專用硬體安全模組 (HSM) 可在 Azure 內提供符合 FIPS 140-2 等級 3 認證的金鑰儲存功能概觀
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc, seodec18
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: dd5ce117645ef2b368bbf8f0e441770d6e746b5b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "70881086"
---
# <a name="what-is-azure-dedicated-hsm"></a>什麼是 Azure 專用硬體安全模組 (HSM)？

Azure 專用硬體安全模組 (HSM) 是一種 Azure 服務，可在 Azure 中提供密碼編譯金鑰儲存功能。 專用 HSM 符合最嚴格的安全性需求。 若客戶需要通過 FIPS 140-2 等級 3 驗證的裝置，以及完整且專屬的 HSM 設備控制權，則它是理想的解決方案。 

 HSM 裝置可跨數個 Azure 區域進行全域部署。 這些可輕易佈建為一對裝置，並設定高可用性。 HSM 裝置也可跨區域進行佈建，以確保能提供區域層級的容錯移轉。 Microsoft 使用 Gemalto 中的 [SafeNet Luna 網路 HSM 7 (A790 型號)](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/) 設備來提供專用 HSM 服務。 此裝置會提供最高層級的效能及密碼編譯整合選項。 

HSM 裝置在佈建後，會直接連線到客戶的虛擬網路。 當您設定點對站或站對站 VPN 連線時，也可使用內部部署應用程式和管理工具來存取這些裝置。 客戶可從 Gemalto 的支援入口網站取得設定及管理 HSM 裝置所需的軟體和文件。

## <a name="why-use-azure-dedicated-hsm"></a>為何使用 Azure 專用 HSM？

### <a name="fips-140-2-level-3-compliance"></a>符合 FIPS 140-2 等級 3 的規範

許多組織都有嚴格的業界規範，規定密碼編譯金鑰儲存體需符合 [FIPS 140-2 等級 3](https://csrc.nist.gov/publications/detail/fips/140/2/final) 的需求。 Microsoft 的多租用戶 Azure Key Vault 服務目前只提供 FIPS 140-2 等級 2 認證。 針對金融服務產業、政府機關和其他必須符合 FIPS 140-2 等級 3 需求的其他產業，Azure 專用硬體安全模組 (HSM) 可滿足他們的實際需求。

### <a name="single-tenant-devices"></a>單一租用戶裝置

我們有許多客戶會要求對密碼編譯儲存體裝置採用單一租用戶。 Azure 專用 HSM 服務可讓他們從 Microsoft 的其中一個全域散發資料中心佈建實體裝置。 對客戶進行佈建後，就只有該客戶能夠存取裝置。

### <a name="full-administrative-control"></a>完整的系統管理控制權

許多客戶都需要完整的系統管理控制權，以及用於系統管理的裝置唯一存取權。 裝置佈建後，將只有該客戶具有裝置的系統管理或應用程式層級存取權。

 在客戶第一次存取裝置後，Microsoft 即無系統管理控制權，因為此時客戶已變更密碼。 從此時間點開始，客戶就是具有完整系統管理控制權和應用程式管理能力的單一租用戶。 Microsoft 可透過序列埠連線擁有監視層級的遙測資料存取權 (不是系統管理員角色)。 其中涵蓋對硬體監視器的存取，例如溫度、電源供應器健康情況、風扇健康情況等。 
 
 客戶可以視需要自行停用此監視。 不過，如果他們加以停用，就不會收到 Microsoft 提供的主動式健康情況警示。

### <a name="high-performance"></a>高效能

選擇以 Gemalto 裝置執行這項服務，是基於諸多考量。 此裝置可支援廣泛的密碼編譯演算法、多種作業系統和多種 API。 針對 RSA 2048，已部署的特定模型可提供每秒 10,000 個作業數的絕佳效能。 其支援 10 個用於唯一應用程式執行個體的分割區。 此裝置是低延遲、高容量及高輸送量的裝置。

### <a name="unique-cloud-based-offering"></a>唯一的雲端式供應項目

Microsoft 在獨特的客戶群之間發現了特定需求。 他們是唯一提供專用 HSM 服務給新客戶的雲端提供者，HSM 服務已通過 FIPS 140-2 等級 3 的驗證，並在此層級上提供雲端式應用程式和內部部署應用程式的整合。

## <a name="is-azure-dedicated-hsm-right-for-you"></a>Azure 專用硬體安全模組 (HSM) 適合您嗎？

Azure 專用 HSM 是特製化服務，專為特定類型的大型組織處理獨特需求。 因此，我們預期大部分的 Azure 客戶並不符合使用這項服務的條件。 許多組織會發現 Azure Key Vault 服務更適合他們，而且符合成本效益。 為協助判斷您的需求是否適用此服務，我們整理出下列準則。

### <a name="best-fit"></a>最適用

「隨即轉移」案例最適用 Azure 專用 HSM，因為此時需要 HSM 裝置的唯一直接存取權。 範例包括：

- 將應用程式從內部部署遷移至 Azure 虛擬機器
- 將應用程式從 Amazon AWS EC2 移轉至使用 AWS 雲端 HSM 傳統服務 (Amazon 不會提供此服務給新客戶) 的虛擬機器
- 在 Azure 虛擬機器中執行 Apache/Ngnix SSL 卸載、Oracle TDE 和 ADCS 等拆封授權軟體 

### <a name="not-a-fit"></a>不適用

Azure 專用 HSM 不適用於下列類型的案例：可使用客戶管理金鑰進行加密的 Microsoft 雲端服務不可與 Azure 專用 HSM 整合，這些服務包括 Azure 資訊保護、Azure 磁碟加密、Azure Data Lake Store、Azure 儲存體、Azure SQL Database 和 Office 365 客戶金鑰等。

### <a name="it-depends"></a>視情況而定

Azure 專用 HSM 是否適用於您，必須綜合考量您可能的需求和您所做的折衷。 例如，FIPS 140-2 等級 3 的需求。 這項需求很常見，而目前符合此需求的選項只有專用 HSM。 如果與這些明文規定的需求無關，則通常會從 Azure Key Vault 與專用 HSM 中擇一使用。 決定之前請先評估您的需求。

您必須權衡選項的情況包括： 

- 在新客戶的 Azure 虛擬機器中執行新程式碼
- Azure 虛擬機器中的 SQL Server TDE
- Azure 儲存體用戶端加密
- SQL Server 和 Azure SQL DB Always Encrypted

## <a name="next-steps"></a>後續步驟

這是高度特殊化的服務。 因此，我們建議您應充分了解此文件集所說明的重要概念，包括定價、支援和服務等級協定。 

[Gemalto 整合指南](https://safenet.gemalto.com/partners/microsoft/)可協助您將 HSM 佈建到現有的虛擬網路環境中。 此外也有操作指南可協助您判斷如何設定部署架構。

* [高可用性](high-availability.md)
* [實體安全性](physical-security.md)
* [網路功能](networking.md)
* [支援能力](supportability.md)
* [監視](monitoring.md)
