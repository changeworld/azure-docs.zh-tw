---
title: Azure Migrate Server 遷移中的代理程式型遷移
description: 提供 Azure Migrate 中以代理程式為基礎的 VMware VM 遷移概述。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: raynew
ms.openlocfilehash: 90e499b436a3ae44fa29cec1138d939a106a4db7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91357160"
---
# <a name="agent-based-migration-architecture"></a>代理程式移轉架構

本文概述使用 [Azure Migrate： Server 遷移](migrate-services-overview.md#azure-migrate-server-assessment-tool) 工具的 VMware vm 以代理程式為基礎的複寫所使用的架構和程式。

使用 Azure Migrate：伺服器遷移，您可以使用幾個選項來複寫 VMware Vm：

- 使用以代理程式為基礎的複寫來遷移 Vm，如本文中所述。
- 使用無代理程式複寫來遷移 VMware Vm。 這會遷移 Vm，而不需要在其上安裝任何專案。

深入瞭解如何 [選取和比較](server-migrate-overview.md) VMware vm 的遷移方法。 


## <a name="agent-based-migration"></a>以代理程式為基礎的遷移

以代理程式為基礎的遷移可用來將內部部署 VMware Vm 和實體伺服器遷移至 Azure。 它也可以用來遷移其他內部部署虛擬化伺服器，以及私人和公用雲端 Vm，包括 AWS 實例和 GCP Vm。 Azure Migrate 中以代理程式為基礎的遷移使用 [Azure Site Recovery](../site-recovery/site-recovery-overview.md) 服務的某些後端功能。


## <a name="architectural-components"></a>架構元件

此圖說明以代理程式為基礎的遷移相關元件。

![下圖顯示以代理程式為基礎之遷移的元件，如表格中所述。](./media/agent-based-replication-architecture/architecture.png)

此表格摘要說明代理程式型遷移所用的元件。

**元件** | **詳細資料** | **安裝**
--- | --- | ---
**複寫設備** | 複寫設備 (設定伺服器/進程伺服器) 是內部部署電腦，可做為內部部署環境與伺服器遷移之間的橋樑。 設備會探索內部部署機器清查，讓伺服器遷移可以協調複寫和遷移。 設備有兩個元件：<br/><br/> 設定**伺服器**：連接至伺服器遷移並協調複寫。<br/> **進程伺服器**：處理資料複寫。 進程伺服器會接收電腦資料、將其壓縮和加密，然後傳送至 Azure。 在 Azure 中，伺服器遷移會將資料寫入受控磁片。 | 依預設，進程伺服器會與複寫設備上的設定伺服器一起安裝。
**行動服務** | 行動服務是在您想要複寫和遷移的每部電腦上安裝的代理程式。 它會將複寫資料從電腦傳送至進程伺服器。 | 不同行動服務版本的安裝檔案位於複寫設備上。 您可以根據您想要複寫之機器的作業系統和版本，下載並安裝所需的代理程式。

## <a name="mobility-service-installation"></a>行動服務安裝

您可以使用下列方法來部署「行動服務」︰

- **推入安裝**：當您啟用機器的保護時，進程伺服器會安裝行動服務。 
- **手動安裝**：您可以透過 UI 或命令提示字元，在每部電腦上手動安裝行動服務。

行動服務與複寫設備和複寫的機器通訊。 如果您在複寫設備、進程伺服器或正在複寫的機器上執行防毒軟體，則應該排除下列資料夾進行掃描：


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86)\Microsoft Azure Site Recovery
- 在安裝行動服務的 Windows 電腦上 C:\ProgramData\ASR\agent () 

## <a name="replication-process"></a>複寫程序

1. 當您啟用機器的複寫時，就會開始對 Azure 的初始複寫。
2. 在初始複寫期間，行動服務會從機器磁片讀取資料，並將資料傳送至進程伺服器。
3. 這項資料是用來植入 Azure 訂用帳戶中的磁片複本。 
4. 初始複寫完成之後，就會開始將差異變更複寫到 Azure。 複寫是區塊層級和近連續的。
4. 行動服務藉由與作業系統的儲存子系統整合，來攔截寫入磁片記憶體。 此方法可避免複寫電腦上的磁片 i/o 作業，以進行增量複寫。 
5. 電腦的追蹤變更會在輸入埠 HTTPS 9443 上傳送至進程伺服器。 您可以修改此連接埠。 進程伺服器會壓縮和加密它，並將其傳送至 Azure。 

## <a name="ports"></a>連接埠

**裝置** | **[連接]**
--- | --- 
**複寫機器** | 在 Vm 上執行的行動服務會與埠 HTTPS 443 輸入的內部部署複寫設備通訊，以進行複寫管理。<br/><br/> 機器會以埠 HTTPS 9443 輸入將複寫資料傳送至進程伺服器。 您可以修改此連接埠。
**複寫設備** | 複寫設備會透過埠 HTTPS 443 輸出來協調 Azure 的複寫。
**進程伺服器** | 處理伺服器會透過輸出連接埠 443 接收複寫資料、將其最佳化並加密，然後傳送至 Azure 儲存體。


## <a name="performance-and-scaling"></a>效能及延展性

根據預設，您會部署可同時執行設定伺服器和進程伺服器的單一複寫設備。 如果您只是要複寫一些機器，此部署就已足夠。 但是，如果您要複寫和遷移數百部機器，單一進程伺服器可能無法處理所有的複寫流量。 在此情況下，您可以部署額外的相應放大進程伺服器。

### <a name="plan-vmware-deployment"></a>規劃 VMware 部署

如果您要複寫 VMware Vm，可以使用 VMware 的 [Site Recovery 部署規劃工具](../site-recovery/site-recovery-deployment-planner.md)，以協助判斷效能需求，包括每日資料變更率，以及您需要的進程伺服器。

### <a name="replication-appliance-capacity"></a>複寫設備容量

您可以使用此表中的值來判斷您的部署中是否需要額外的進程伺服器。

- 如果您的每日變更率 (流失率) 超過 2 TB，請部署額外的進程伺服器。
- 如果您要複寫200部以上的機器，請部署額外的複寫設備。

**CPU** | **記憶體** | **可用空間-資料快取** | **流失率** | **複寫限制**
--- | --- | --- | --- | ---
8 個 vCPU (2 個插槽 * 4 核心 \@ 2.5GHz) | 16 GB | 300 GB | 500 GB 或更少 | < 100 部機器 
12 個 vCPU (2 個插槽 * 6 核心 \@ 2.5GHz) | 18 GB | 600 GB | 501 GB 至 1 TB | 100-150 機器。
16 個 vCPU (2 個插槽 * 8 核心 \@ 2.5GHz) | 32 G1 |  1 TB | 1 TB 至 2 TB | 151-200 機器。

### <a name="sizing-scale-out-process-servers"></a>調整相應放大進程伺服器的大小

如果您需要部署相應放大進程伺服器，請使用此資料表來找出伺服器調整大小。

**進程伺服器** | **資料快取的可用空間** | **流失率** | **複寫限制**
--- | --- | --- | --- 
4 個 vCPU (2 個插槽 * 2 核心 \@ 2.5 GHz)，8 GB 記憶體 | 300 GB | 250 GB 或更少 | 最多85電腦 
8 個 vCPU (2 個插槽 * 4 核心 \@ 2.5 GHz)，12 GB 記憶體 | 600 GB | 251 GB 至 1 TB    | 86-150 機器。
12個 vcpu (2 個通訊端 * 6 核心 \@ 2.5 GHz) ，24 GB 記憶體 | 1 TB | 1-2 TB | 151-225 機器。

## <a name="throttle-upload-bandwidth"></a>節流上傳頻寬。

複寫至 Azure 的 VMware 流量會經過特定的處理序伺服器。 您可以藉由在以進程伺服器形式執行的電腦上節流頻寬來限制上傳輸送量。 您可以使用此登錄機碼來影響頻寬：

- HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM 登錄值可指定用於磁碟資料傳輸 (初始或差異複寫) 的執行緒數目。 較高的值可增加用於複寫的網路頻寬。 預設值為四。 最大值為32。 監視流量，將此值最佳化。
- 此外，您可以節流處理伺服器電腦上的頻寬，如下所示：

    1. 在進程伺服器電腦上，開啟 Azure 備份 MMC 嵌入式管理單元。 桌面上的快捷方式，或在 C:\Program Files\Microsoft Azure Recovery Services Agent\bin. 的資料夾中。 
    2. 在嵌入式管理單元中，選取 [變更內容]****。
    3. 在 [ **節流**] 中，選取 [ **啟用備份作業的網際網路頻寬使用節流**設定]。 設定工作和非工作時數的限制。 有效範圍是 512 Kbps 到 1,023 Mbps。


## <a name="next-steps"></a>後續步驟

針對[VMware](tutorial-migrate-vmware-agent.md)或[實體伺服器](tutorial-migrate-physical-virtual-machines.md)試試以代理程式為[基礎的遷移](tutorial-migrate-vmware-agent.md)。
