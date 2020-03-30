---
title: Hyper-V 遷移在 Azure 遷移中的工作方式如何？
description: 瞭解 Azure 遷移的超 V 遷移
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 8bca88fc63a7fc04a22d2a68adbe59259b07f50e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185869"
---
# <a name="how-does-hyper-v-replication-work"></a>Hyper-V 複製如何工作？

本文概述了使用 Azure 遷移伺服器遷移工具遷移 Hyper-VM 時使用的體系結構和過程。

[Azure Migrate](migrate-services-overview.md) 提供集中式中樞，可追蹤內部部署應用程式和工作負載及私人/公用雲端 VM 的探索、評量和移轉 (以 Azure 為目標)。 該中樞能提供 Azure Migrate 工具以進行評估和移轉，也提供協力廠商獨立軟體廠商 (ISV) 的供應項目。

## <a name="agentless-migration"></a>無代理遷移

Azure 遷移伺服器遷移工具使用針對 Hyper-V 優化的遷移工作流為本地超 VM 提供無代理複製。 您僅在 Hyper-V 主機或叢集節點上安裝軟體代理。 不需要在超 VM 上安裝任何內容。

## <a name="server-migration-and-azure-site-recovery"></a>伺服器遷移和 Azure 網站恢復

Azure 遷移伺服器遷移是一種工具，用於將本地工作負荷和基於雲的 VM 遷移到 Azure。 網站恢復是一種災害復原工具。 這些工具共用一些用於資料複製的通用技術元件，但用途不同。 


## <a name="architectural-components"></a>架構元件

![架構](./media/hyper-v-replication-architecture/architecture.png)



**元件** | **部署** | 
--- | --- 
**複製提供程式** | Microsoft Azure 網站恢復提供程式安裝在 Hyper-V 主機上，並在 Azure 遷移伺服器遷移中註冊。<br/> 提供程式協調對超 VM 的複製。
**復原服務代理程式** | Microsoft Azure 恢復服務代理處理資料複製。 它與提供程式一起將資料從超 VM 複製到 Azure。<br/> 複製的資料將上載到 Azure 訂閱中的存儲帳戶。 伺服器遷移工具處理複製的資料，並將其應用於訂閱中的副本磁片。 複製副本磁片用於在遷移時創建 Azure VM。

- 元件由單個設置檔安裝，該檔從門戶中的 Azure 遷移伺服器遷移下載。
- 提供程式和應用程式使用出站 HTTPS 埠 443 連接與 Azure 遷移伺服器遷移進行通信。
- 來自供應商和代理的通信是安全和加密的。


## <a name="replication-process"></a>複寫程序

1. 啟用 Hyper-V VM 的複製時，開始初始複製。
2. 將拍攝超 V VM 快照。
3. VM 上的 VHD 逐個複製，直到它們全部複製到 Azure。 初始複製時間取決於 VM 大小和網路頻寬。
4. 使用 Hyper-V 副本跟蹤初始複製期間發生的磁片更改，並存儲在日誌檔（hrl 檔）中。
    - 日誌檔與磁片位於同一資料夾中。
    - 每個磁片都有一個發送到輔助存儲的關聯 hrl 檔。
    - 當初始複寫正在進行時，快照和記錄檔會取用磁碟資源。
4. 初始複製完成後，VM 快照將被刪除，增量複製開始。
5. 增量磁片更改在 hrl 檔中跟蹤。 複製日誌由恢復服務代理定期上載到 Azure 存儲帳戶。


## <a name="performance-and-scaling"></a>效能及延展性

Hyper-V 的複製性能受以下因素的影響：VM 大小、VM 的資料更改率（更改率）、用於日誌檔存儲的 Hyper-V 主機上的可用空間、複製資料的上載頻寬以及 Azure 中的目標存儲。

- 如果同時複製多台電腦，請使用 Hyper-V 的[Azure 網站恢復部署規劃器](../site-recovery/hyper-v-deployment-planner-overview.md)來説明優化複製。
- 規劃 Hyper-V 複製，並根據容量在 Azure 存儲帳戶上分發複製。

### <a name="control-upload-throughput"></a>控制上傳輸送量

您可以限制用於將資料上載到每個 Hyper-V 主機上的 Azure 的頻寬量。 小心。 如果將值設置得太低，將對複製產生負面影響，並延遲遷移。


1. 登錄到 Hyper-V 主機或叢集節點。
2. 運行**C：\程式檔\微軟 Azure 恢復服務代理\bin_wabadmin.msc，** 以打開 Windows Azure 備份 MMC 管理單元。
3. 在嵌入式管理單元中，選取 [變更內容]****。
4. 在 **"限制"中**，選擇**啟用備份操作的互聯網頻寬使用限制**。 設定工作和非工作時數的限制。 有效範圍是 512 Kbps 到 1,023 Mbps。
I

### <a name="influence-upload-efficiency"></a>影響上傳效率

如果您有用於複製的備用頻寬，並且希望增加上載，則可以增加為上載任務分配的執行緒數，如下所示：

1. 使用 Regedit 打開註冊表。
2. 導航到關鍵HKEY_LOCAL_MACHINE_軟體\微軟\Windows Azure 備份\複製\上傳執行緒PerVM
3. 增加用於為每個複製 VM 的資料上載的執行緒數的值。 預設值為 4，最大值為 32。 




## <a name="next-steps"></a>後續步驟

使用 Azure 遷移伺服器遷移嘗試[Hyper-V 遷移](tutorial-migrate-hyper-v.md)。
