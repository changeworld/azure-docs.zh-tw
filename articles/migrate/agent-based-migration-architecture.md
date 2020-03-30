---
title: Azure 遷移伺服器遷移中基於代理的遷移
description: 提供 Azure 遷移中基於代理的 VMware VM 遷移的概述。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: raynew
ms.openlocfilehash: d345d707cbf58f48466c3bd830d93250d13397c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425847"
---
# <a name="agent-based-migration-architecture"></a>代理程式移轉架構

本文概述了使用[Azure 遷移：伺服器遷移](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具基於代理複製 VMware VM 的體系結構和過程。

使用 Azure 遷移：伺服器遷移，可以使用幾個選項複製 VMware VM：

- 使用基於代理的複製遷移 VM，如本文所述。
- 使用無代理複製遷移 VMware VM。 這將遷移 VM，而無需在 VM 上安裝任何內容。

詳細瞭解如何為 VMware VM[選擇和比較](server-migrate-overview.md)遷移方法。 


## <a name="agent-based-migration"></a>基於代理的遷移

基於代理的遷移用於將本地 VMware VM 和物理伺服器遷移到 Azure。 它還可用於遷移其他本地虛擬化伺服器，以及私有和公共雲 VM，包括 AWS 實例和 GCP VM。 Azure 遷移中基於代理的遷移使用[Azure 網站恢復](../site-recovery/site-recovery-overview.md)服務中的一些後端功能。


## <a name="architectural-components"></a>架構元件

該圖說明瞭基於代理的遷移所涉及的元件。

![架構](./media/agent-based-replication-architecture/architecture.png)

該表總結了用於基於代理的遷移的元件。

**元件** | **詳細資料** | **安裝**
--- | --- | ---
**複製設備** | 複製設備（佈建服務器/進程伺服器）是一臺本地電腦，充當本地環境和伺服器遷移之間的橋樑。 設備發現本地電腦清單，以便伺服器遷移可以協調複製和遷移。 產品有兩個元件：<br/><br/> **佈建服務器**：連接到伺服器遷移並協調複製。<br/> **進程伺服器**：處理資料複製。 進程伺服器接收電腦資料、壓縮和加密電腦資料並將其發送到 Azure。 在 Azure 中，伺服器遷移將資料寫入託管磁片。 | 預設情況下，進程伺服器與複製設備上的佈建服務器一起安裝。
**行動服務** | 移動服務是安裝在要複製和遷移的每台電腦上的代理。 它將複製資料從電腦發送到進程伺服器。 | 不同版本的移動服務的安裝檔位於複製設備上。 您可以根據要複製的電腦的作業系統和版本下載並安裝所需的代理。

## <a name="mobility-service-installation"></a>行動服務安裝

您可以使用下列方法來部署「行動服務」︰

- **推送安裝**：當您啟用電腦保護時，進程伺服器將安裝移動服務。 
- **手動安裝**：您可以通過 UI 或命令提示符在每台電腦上手動安裝移動服務。

移動服務與複製設備和複製的電腦進行通信。 如果在複製設備、進程伺服器或要複製的電腦上運行防毒軟體，則應從掃描中排除以下資料夾：


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86)\Microsoft Azure Site Recovery
- C：\程式資料\ASR\代理（在安裝了移動服務的 Windows 電腦上）

## <a name="replication-process"></a>複寫程序

1. 為電腦啟用複製時，將開始初始複製到 Azure。
2. 在初始複製期間，移動服務從電腦磁片讀取資料，並將其發送到進程伺服器。
3. 此資料用於在 Azure 訂閱中設置磁片副本的種子。 
4. 初始複寫完成之後，就會開始將差異變更複寫到 Azure。 複製是塊級和幾乎連續的。
4. 移動服務通過與作業系統的儲存子系統集成來攔截寫入磁片記憶體。 此方法避免複製電腦上的磁片 I/O 操作，以便進行增量複製。 
5. 電腦的跟蹤更改將發送到入站埠 HTTPS 9443 上的進程伺服器。 您可以修改此連接埠。 進程伺服器壓縮並加密它，並將其發送到 Azure。 

## <a name="ports"></a>連接埠

**裝置** | **連接**
--- | --- 
**複製機器** | 在 VM 上運行的移動服務與埠 HTTPS 443 入站上的本地複製設備進行通信，以便進行複製管理。<br/><br/> 電腦將複製資料發送到埠 HTTPS 9443 入站上的進程伺服器。 您可以修改此連接埠。
**複製設備** | 複製設備通過埠 HTTPS 443 出站協調使用 Azure 進行複製。
**處理序伺服器** | 處理伺服器會透過輸出連接埠 443 接收複寫資料、將其最佳化並加密，然後傳送至 Azure 儲存體。


## <a name="performance-and-scaling"></a>效能及延展性

預設情況下，部署同時回合組態伺服器和進程伺服器的單個複製設備。 如果只複製幾台電腦，則此部署就足夠了。 但是，如果要複製和遷移數百台電腦，則單個進程伺服器可能無法處理所有複製流量。 在這種情況下，您可以部署其他橫向擴展進程伺服器。

### <a name="plan-vmware-deployment"></a>規劃 VMware 部署

如果要複製 VMware VM，可以使用[VMware 的網站恢復部署規劃器](../site-recovery/site-recovery-deployment-planner.md)來説明確定性能要求，包括每日資料更改率以及所需的進程伺服器。

### <a name="replication-appliance-capacity"></a>複製設備容量

使用此表中的值確定部署中是否需要其他進程伺服器。

- 如果每日更改率（流失率）超過 2 TB，請部署其他進程伺服器。
- 如果要複製 200 多台電腦，請部署其他複製設備。

**Cpu** | **記憶體** | **自由空間資料緩存** | **流失率** | **複製限制**
--- | --- | --- | --- | ---
8 個 vCPU (2 個插槽 * 4 核心 \@ 2.5GHz) | 16 GB | 300 GB | 500 GB 或更少 | < 100 部機器 
12 個 vCPU (2 個插槽 * 6 核心 \@ 2.5GHz) | 18 GB | 600 GB | 501 GB 至 1 TB | 100-150台機器。
16 個 vCPU (2 個插槽 * 8 核心 \@ 2.5GHz) | 32 G1 |  1 TB | 1 TB 至 2 TB | 151-200台機器。

### <a name="sizing-scale-out-process-servers"></a>調整橫向擴展進程伺服器的規模

如果需要部署橫向擴展進程伺服器，請使用此表確定伺服器大小調整。

**處理序伺服器** | **資料緩存的可用空間** | **流失率** | **複製限制**
--- | --- | --- | --- 
4 個 vCPU (2 個插槽 * 2 核心 \@ 2.5 GHz)，8 GB 記憶體 | 300 GB | 250 GB 或更少 | 多達 85 台機器 
8 個 vCPU (2 個插槽 * 4 核心 \@ 2.5 GHz)，12 GB 記憶體 | 600 GB | 251 GB 至 1 TB    | 86-150台機器。
12 個 vCPU（2 個插槽\@+ 6 個內核 2.5 GHz），24 GB 記憶體 | 1 TB | 1-2 TB | 151-225台機器。

## <a name="throttle-upload-bandwidth"></a>限制上傳頻寬。

複寫至 Azure 的 VMware 流量會經過特定的處理序伺服器。 您可以通過限制作為進程伺服器運行的電腦上頻寬來限制上載輸送量。 您可以使用此登錄機碼影響頻寬：

- HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM 登錄值可指定用於磁碟資料傳輸 (初始或差異複寫) 的執行緒數目。 較高的值可增加用於複寫的網路頻寬。 預設值為 4。 最大值為 32。 監視流量，將此值最佳化。
- 此外，您可以限制進程伺服器電腦上的頻寬，如下所示：

    1. 在進程伺服器電腦上，打開 Azure 備份 MMC 管理單元。 桌面或資料夾 C：程式檔\Microsoft Azure 恢復服務代理\bin 上有一個快捷方式。 
    2. 在嵌入式管理單元中，選取 [變更內容]****。
    3. 在 **"限制"中**，選擇**啟用備份操作的互聯網頻寬使用限制**。 設定工作和非工作時數的限制。 有效範圍是 512 Kbps 到 1,023 Mbps。


## <a name="next-steps"></a>後續步驟

嘗試[VMware](tutorial-migrate-vmware-agent.md)或[物理伺服器](tutorial-migrate-physical-virtual-machines.md)[基於代理的遷移](tutorial-migrate-vmware-agent.md)。
