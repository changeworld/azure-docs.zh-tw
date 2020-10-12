---
title: Azure 虛擬機器上的大型主機重新裝載
description: 使用 (Vm) Microsoft Azure 上的虛擬機器，來重新裝載您的大型主機工作負載（例如 IBM Z 型系統）。
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: d754f27a1510d9db0837eabb96b3a7cf75f76fc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87835522"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Azure 虛擬機器上的大型主機重新裝載

將工作負載從大型主機環境遷移至雲端，可讓您將基礎結構現代化，並經常節省成本。 許多工作負載只要稍微變更程式碼 (如更新資料庫的名稱) 就可以傳輸至 Azure。

一般來說，大型 *主機* 是指大型的電腦系統。 具體來說，目前使用的大部分是 IBM System Z 伺服器或 IBM 隨插即用系統，可執行 MVS、DOS、VSE、OS/390 或 Z/OS。

Azure 虛擬機器 (VM) 用來隔離及管理單一實例上特定應用程式的資源。 IBM z/OS 這類大型主機會使用邏輯分割 (LPAR) 以實現此目的。 大型主機可能會針對具有相關 COBOL 程式的 CICS 區域使用一個 LPAR，並針對 IBM Db2 資料庫使用個別的 LPAR。 Azure 上的一般多 [層式應用程式](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) 會將 azure vm 部署至虛擬網路，以分割為每個層級的子網。

Azure Vm 可以執行支援隨即轉移案例的大型主機模擬環境和編譯器。 開發和測試通常是從大型主機遷移至 Azure 開發/測試環境的第一個工作負載。 您可以模擬的一般伺服器元件包括線上交易處理 (OLTP) 、批次和資料內嵌系統，如下圖所示。

![Azure 上的模擬環境可讓您執行以 z/OS 為基礎的系統。](media/01-overview.png)

某些大型主機工作負載可以相對輕鬆地遷移至 Azure，而其他則可使用合作夥伴解決方案在 Azure 上重新裝載。 如需有關選擇合作夥伴解決方案的詳細指引， [Azure 大型主機遷移中心](https://azure.microsoft.com/migration/mainframe/) 可以提供協助。

## <a name="mainframe-migration"></a>大型主機移轉

重新裝載、重建、取代或淘汰？ IaaS 或 PaaS？ 若要為您的大型主機應用程式判斷正確的遷移策略，請參閱 Azure 架構中心中的 [大型主機遷移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)  指南。

## <a name="micro-focus-rehosting-platform"></a>微焦點重新裝載平臺

微型焦點企業伺服器是最大型的大型主機重新裝載平臺之一。 您可以使用它在 Azure 上成本較低的 x86 平臺上執行 z/OS 工作負載。

開始進行之前：

- [在 Azure 上安裝企業伺服器和企業開發人員](./microfocus/set-up-micro-focus-azure.md)
- [為 Azure 上的企業開發人員設定 CICS BankDemo](./microfocus/demo.md)
- [在 Azure 上的 Docker 容器中執行企業伺服器](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>Azure 上的 TmaxSoft OpenFrame

TmaxSoft OpenFrame 是一種熱門的大型主機重新裝載解決方案，用於隨即轉移案例中。 Azure 上的 OpenFrame 環境適用于開發、示範、測試或生產工作負載。

開始進行之前：

- [開始使用 TmaxSoft OpenFrame](./tmaxsoft/get-started.md)
- [下載電子書](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12。0

IBM Z 開發與測試環境 (IBM zD&T) 設定 Azure 上的非生產環境，您可以用來進行以 Z/OS 為基礎的應用程式開發、測試和示範。

Azure 上的模擬環境可以透過應用程式開發人員控制的散發 (ADCDs) 來裝載不同類型的 Z 實例。 您可以在 Azure 和 Azure Stack 上執行 zD&T Personal Edition、zD&T Parallel Sysplex 和 zD&T Enterprise Edition。

開始進行之前：

- [在 Azure 上設定 IBM zD&T 12。0](./ibm/install-ibm-z-environment.md)
- [在 zD&T 上設定 ADCD](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>Azure 上的 IBM DB2 pureScale

IBM DB2 pureScale 環境提供適用于 Azure 的資料庫叢集。 它與原始環境不同，但它提供類似的可用性和規模，如同 IBM DB2 for z/OS 在平行 Sysplex 設定中執行。

若要開始使用，請參閱 [Azure 上的 IBM DB2 pureScale](../../linux/ibm-db2-purescale-azure.md)。

## <a name="considerations"></a>考量

當您將大型主機工作負載遷移到 Azure 基礎結構即服務 (IaaS) 時，您可以選擇數種類型的隨選、可調整的計算資源，包括 Azure Vm。 Azure 提供一系列的 [Linux](../../linux/overview.md) 和 [Windows](../../windows/overview.md) vm。

### <a name="compute"></a>計算

相較于大型主機容量，Azure 計算能力的比較。 如果您想要將大型主機工作負載移至 Azure，請比較每秒1000000指令的大型主機計量 (MIPS) 至虛擬 Cpu。 

瞭解如何 [將大型主機計算移至 Azure](./concepts/mainframe-compute-azure.md)。

### <a name="high-availability-and-failover"></a>高可用性和容錯移轉

Azure 提供以承諾為基礎的服務等級協定 (Sla) 。 預設值為多個九，而且可以使用服務的本機或異地複寫來優化 Sla。 完整 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 說明保證的 Azure 整體可用性。

使用 Azure IaaS （例如 VM）時，特定的系統函數會提供容錯移轉支援，例如容錯移轉叢集實例和可用性設定組。 當您使用 Azure 平臺即服務 (PaaS) 資源時，此平臺會自動處理容錯移轉。 範例包括 [Azure SQL Database](../../../azure-sql/database/sql-database-paas-overview.md) 和 [Azure Cosmos DB](../../../cosmos-db/introduction.md)。

### <a name="scalability"></a>延展性

大型主機通常會擴大，而雲端環境則會相應放大。Azure 提供各種 [Linux](../../sizes.md) 和 [Windows](../../sizes.md) 大小，以符合您的需求。 雲端也會向上或向下調整，以符合確切的使用者規格。 以使用量為基礎的計費模型，依需求 [調整](/azure/architecture/best-practices/auto-scaling) 計算能力、儲存體和服務。

### <a name="storage"></a>儲存體

在雲端中，您有許多彈性、可擴充的儲存體選項，而且您只需支付所需的費用。 [Azure 儲存體](../../../storage/common/storage-introduction.md)提供可大幅調整的資料物件存放區、雲端檔案系統服務、可靠的訊息存放區，以及 NoSQL 存放區。 針對 VM，受控和非受控磁碟可提供安全的永續性磁碟儲存體。

瞭解如何 [將大型主機儲存體移至 Azure](./concepts/mainframe-storage-azure.md)。

### <a name="backup-and-recovery"></a>備份與復原

維護您自己的嚴重損壞修復網站可能是相當昂貴的主張。 Azure 具有簡單且符合成本效益的[選項，適用](../../../site-recovery/site-recovery-overview.md)于本機或區域層級的[備份](../../../backup/backup-overview.md)、復原和[冗余](../../../storage/common/storage-redundancy.md)，或透過地理位置冗余。

## <a name="azure-government-for-mainframe-migrations"></a>適用于大型主機遷移的 Azure Government

許多公用磁區實體很樂意將其大型主機應用程式移至更新式、彈性的平臺。 Microsoft Azure Government 是全域 Microsoft Azure 平臺的實體分隔實例，是針對聯邦、州和地方政府系統所封裝。 它提供世界級的安全性、保護及合規性服務，專為美國政府機構及其合作夥伴提供。

Azure Government 獲得暫時性的授權單位，可操作 (P-ATO) ，以針對需要這類環境的系統 FedRAMP 高影響。

若要開始使用，請下載 [適用于大型主機應用程式的 Microsoft Azure Government 雲端](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/)。

## <a name="next-steps"></a>後續步驟

要求我們的 [合作夥伴](partner-workloads.md) 協助您遷移或重新裝載您的大型主機應用程式。 

另請參閱：

- [關於大型主機主題的白皮書](mainframe-white-papers.md)
- [大型主機遷移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [疑難排解](../../troubleshooting/index.yml)
- [將大型主機揭開至 Azure 遷移](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md