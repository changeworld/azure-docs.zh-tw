---
title: 大型機在 Azure 虛擬機器上重新託管
description: 使用 Microsoft Azure 上的虛擬機器 （VM） 重新託管大型機工作負載，例如基於 IBM Z 的系統。
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: ccdeefabeedfca4959239696361ccce0bc6c1c78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76289793"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>大型機在 Azure 虛擬機器上重新託管

將工作負載從大型機環境遷移到雲使您能夠實現基礎架構現代化，並經常節省成本。 許多工作負載只要稍微變更程式碼 (如更新資料庫的名稱) 就可以傳輸至 Azure。

一般來說，*術語大型機*意味著一個大型電腦系統。 具體來說，目前使用的絕大多數是 IBM 系統 Z 伺服器或 IBM 插頭相容系統，這些系統運行 MVS、DOS、VSE、OS/390 或 z/OS。

Azure 虛擬機器 （VM） 用於隔離和管理單個實例上特定應用程式的資源。 IBM z/OS 等大型機為此使用邏輯分區 （LPARS）。 大型機可能使用一個 LPAR 用於具有關聯 COBOL 程式的 CICS 區域，對 IBM Db2 資料庫使用單獨的 LPAR。 Azure[上的典型 n 層應用程式](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)將 Azure VM 部署到虛擬網路，該虛擬網路可以跨段到每個層的子網中。

Azure VM 可以運行支援升降和移位方案的大型機模擬環境和編譯器。 開發和測試通常是從大型機遷移到 Azure 開發/測試環境的第一個工作負載之一。 可以類比的常見伺服器元件包括連線事務進程 （OLTP）、批次處理和資料引入系統，如下圖所示。

![Azure 上的模擬環境使您能夠運行基於 z/OS 的系統。](media/01-overview.png)

某些大型機工作負荷可以相對輕鬆地遷移到 Azure，而其他工作負荷可以使用合作夥伴解決方案在 Azure 上重新託管。 有關選擇合作夥伴解決方案的詳細資訊[，Azure 大型機遷移中心](https://azure.microsoft.com/migration/mainframe/)可以提供説明。

## <a name="mainframe-migration"></a>大型主機移轉

重新託管、重建、替換或停用？ IaaS 或 PaaS？ 要確定大型機應用程式的正確遷移策略，請參閱 Azure 體系結構中心中的[大型機遷移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)指南。

## <a name="micro-focus-rehosting-platform"></a>微焦點重新託管平臺

微焦點企業伺服器是可用的最大的大型主機重新託管平臺之一。 您可以使用它在 Azure 上較便宜的 x86 平臺上運行 z/OS 工作負荷。

開始進行之前：

- [在 Azure 上安裝企業伺服器和企業開發人員](./microfocus/set-up-micro-focus-azure.md)
- [在 Azure 上為企業開發人員設置 CICS 銀行演示](./microfocus/demo.md)
- [在 Azure 上的 Docker 容器中運行企業伺服器](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>在 Azure 上使用 TmaxSoft 打開框架

TmaxSoft OpenFrame 是一種流行的大型主機重新託管解決方案，用於升降和換檔方案。 Azure 上的 OpenFrame 環境適用于開發、演示、測試或生產工作負荷。

開始進行之前：

- [使用 TmaxSoft 開放幀入門](./tmaxsoft/get-started.md)
- [下載電子書](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12.0

IBM Z 開發和測試環境（IBM zD&T）在 Azure 上設置了一個非生產環境，可用於基於 z/OS 的應用程式的開發、測試和演示。

Azure 上的模擬環境可以通過應用程式開發人員控制的分發（ADCD）承載不同類型的 Z 實例。 您可以在 Azure 和 Azure 堆疊上運行 zD&T 個人版、zD&T 並行系統版和 zD&T 企業版。

開始進行之前：

- [在 Azure 上設置 IBM zD&T 12.0](./ibm/install-ibm-z-environment.md)
- [在 zD&T 上設置 ADCD](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>Azure 上的 IBM DB2 pureScale

IBM DB2 純 Scale 環境為 Azure 提供了資料庫群集。 它與原始環境不同，但它提供與 IBM DB2 類似的可用性和擴展，用於在並行系統設置中運行的 z/OS。

要開始，請參閱[Azure 上的 IBM DB2 純標。](/azure/virtual-machines/linux/ibm-db2-purescale-azure)

## <a name="considerations"></a>考量

將大型機工作負荷作為服務 （IaaS） 遷移到 Azure 基礎結構時，可以從多種類型的按需可擴展計算資源（包括 Azure VM）中進行選擇。 Azure 提供一系列[Linux](/azure/virtual-machines/linux/overview)和[Windows](/azure/virtual-machines/windows/overview) VM。

### <a name="compute"></a>計算

Azure 計算能力與大型機的容量相比是有利的。 如果您正在考慮將大型機工作負載移動到 Azure，請將每秒 100 萬條指令 （MIPS） 的大型機指標與虛擬 CPU 進行比較。 

瞭解如何[將大型機計算移動到 Azure](./concepts/mainframe-compute-azure.md)。

### <a name="high-availability-and-failover"></a>高可用性和容錯移轉

Azure 提供基於承諾的服務等級協定 （SL）。 多個九的可用性是預設的，SLA 可以通過本地或基於地理位置的服務複製進行優化。 完整 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 說明保證的 Azure 整體可用性。

使用 Azure IaaS（如 VM），特定的系統功能提供容錯移轉支援，例如容錯移轉叢集實例和可用性集。 使用 Azure 平臺作為服務 （PaaS） 資源時，該平臺會自動處理容錯移轉。 示例包括[Azure SQL 資料庫](/azure/sql-database/sql-database-technical-overview)和 Azure[宇宙資料庫](/azure/cosmos-db/introduction)。

### <a name="scalability"></a>延展性

大型機通常向上擴展，而雲環境則橫向擴展。Azure 提供一系列[Linux](/azure/virtual-machines/linux/sizes)和[Windows](/azure/virtual-machines/windows/sizes)大小，以滿足您的需求。 雲還可以向上或向下擴展，以匹配確切的使用者規格。 在基於使用方式的計費模型下，計算功率、存儲和服務可按需[擴展](/azure/architecture/best-practices/auto-scaling)。

### <a name="storage"></a>存放裝置

在雲中，您擁有一系列靈活、可擴展的存儲選項，並且您只需根據所需內容付費。 [Azure 儲存體](/azure/storage/common/storage-introduction)提供可大幅調整的資料物件存放區、雲端檔案系統服務、可靠的訊息存放區，以及 NoSQL 存放區。 針對 VM，受控和非受控磁碟可提供安全的永續性磁碟儲存體。

瞭解如何[將大型機存儲移動到 Azure](./concepts/mainframe-storage-azure.md)。

### <a name="backup-and-recovery"></a>備份與復原

維護自己的災害復原網站可能是一個昂貴的提議。 Azure 具有易於實現且經濟高效的選項，用於本地或區域級別的[備份](/azure/backup/backup-introduction-to-azure-backup)、[恢復](/azure/site-recovery/site-recovery-overview)和[冗余](/azure/storage/common/storage-redundancy)，或通過異地冗余。

## <a name="azure-government-for-mainframe-migrations"></a>用於大型機遷移的 Azure 政府

許多公共部門實體希望將其大型機應用程式遷移到更現代、更靈活的平臺。 Microsoft Azure 政府是全域 Microsoft Azure 平臺的物理分離實例，為聯邦、州和地方政府系統打包。 它專門為美國政府機構及其合作夥伴提供世界一流的安全、保護和合規服務。

Azure 政府為 FedRAMP 高影響系統贏得了臨時運營授權 （P-ATO），適用于需要此類環境的系統。

要開始，請下載[用於大型機應用程式的 Microsoft Azure 政府雲](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/)。

## <a name="next-steps"></a>後續步驟

請我們的[合作夥伴](partner-workloads.md)説明您遷移或重新託管大型機應用程式。 

另請參閱：

- [有關大型機主題的白皮書](mainframe-white-papers.md)
- [大型機遷移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [疑難排解](/azure/virtual-machines/troubleshooting/)
- [將大型機與 Azure 遷移解密](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
