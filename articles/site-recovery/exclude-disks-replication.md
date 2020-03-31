---
title: 通過 Azure 網站恢復從複製中排除磁片
description: 如何通過 Azure 網站恢復從複製中排除磁片到 Azure。
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 57bf06f0fde85714530c06cbd008db08de7460d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281843"
---
# <a name="exclude-disks-from-disaster-recovery"></a>從災害復原中排除磁片

本文介紹如何在災害復原期間從本地到 Azure 進行[Azure 網站恢復](site-recovery-overview.md)時從複製中排除磁片。 出於多種原因，可能會將磁片排除在複製之外：

- 因此，在排除的磁片上改動的不重要資料不會複製。
- 優化消耗的複製頻寬或目標端資源。
- 通過不復制不需要的資料來節省存儲和網路資源。
- Azure VM 已達到網站恢復複製限制。


## <a name="supported-scenarios"></a>支援的案例

您可以排除表中總結的複製中的磁片。

**Azure 至 Azure** | **VMware 至 Azure** | **Hyper-V 至 Azure** 
--- | --- | ---
是（使用電源外殼） | 是 | 是 

## <a name="exclude-limitations"></a>排除限制

**限制** | **Azure VM** | **VMware VM** | **Hyper-V VM**
--- | --- | ---
**磁碟類型** | 可以從複製中排除基本磁碟。<br/><br/> 不能排除作業系統磁片或動態磁碟。 預設情況下，臨時磁片被排除。 | 可以從複製中排除基本磁碟。<br/><br/> 不能排除作業系統磁片或動態磁碟。 | 可以從複製中排除基本磁碟。<br/><br/> 您無法排除作業系統磁碟。 我們建議不要排除動態磁碟。 網站恢復無法識別來賓 VM 中哪個 VHS 是基本的或動態的。 如果未排除所有從屬動態磁碟區磁片，則受保護的動態磁碟將成為容錯移轉 VM 上的失敗磁片，並且無法訪問該磁片上的資料。
**複製磁片** | 不能排除正在複製的磁片。<br/><br/> 禁用 VM 並重新啟用複製。 |  不能排除正在複製的磁片。 |  不能排除正在複製的磁片。
**移動服務（VMware）** | 無關 | 只能在安裝了移動服務的 VM 上排除磁片。<br/><br/> 這意味著您必須在要排除磁片的 VM 上手動安裝移動服務。不能使用推送安裝機制，因為它僅在啟用複製後安裝移動服務。 | 不相關。
**新增/移除** | 您可以使用託管磁片在 Azure VM 上添加和刪除磁片。 | 啟用複製後，無法添加或刪除磁片。 禁用然後重新啟用複製以添加磁片。 | 啟用複製後，無法添加或刪除磁片。 禁用然後重新啟用複製。
**故障** | 如果應用需要排除的磁片，則需要在容錯移轉後手動創建磁片，以便複製的應用可以運行。<br/><br/> 或者，您可以通過將 Azure 自動化集成到恢復計畫中，在 VM 容錯移轉期間創建磁片。 | 如果排除應用需要磁片，則在容錯移轉後在 Azure 中手動創建磁片。 | 如果排除應用需要磁片，則在容錯移轉後在 Azure 中手動創建磁片。
**手動創建的本地故障回退磁片** | 無關 | **Windows VM**： 在 Azure 中手動創建的磁片不會失敗。 例如，如果故障超過三個磁片，直接在 Azure VM 上創建兩個磁片，則只有故障的三個磁片才會失敗。<br/><br/> **Linux VM**： 在 Azure 中手動創建的磁片失敗。 例如，如果故障超過三個磁片並在 Azure VM 上創建兩個磁片，則所有五個磁片都將失敗。 您無法從容錯回復排除手動建立的磁碟。 | 在 Azure 中手動創建的磁片不會失敗。 例如，如果故障超過三個磁片，直接在 Azure VM 上創建兩個磁片，則只有三個失敗磁片將失敗。
**本地故障回退排除磁片** | 無關 | 如果故障回電腦，故障恢復 VM 磁片配置不包括排除的磁片。 從 VMware 到 Azure 複製排除的磁片在故障恢復 VM 上不可用。 | 當故障回退到原始 Hyper-V 位置時，故障回退 VM 磁片配置與原始源 VM 磁片的配置相同。 從 Hyper-V 網站到 Azure 複製的磁片在故障恢復 VM 上可用。



## <a name="typical-scenarios"></a>典型方案

非常適合排除的資料改動示例包括寫入分頁檔 （pagefile.sys） 和寫入 Microsoft SQL Server 的 tempdb 檔。 根據工作負載和儲存子系統，分頁和 tempdb 檔可以註冊大量改動。 將這種類型的資料複製到 Azure 是資源密集型的。

- 要優化具有單個虛擬磁片（包括作業系統和分頁檔的 VM）的複製，可以：
    1. 將單一虛擬磁碟分割成兩個虛擬磁碟。 一個虛擬磁碟具有作業系統，另一個具有分頁檔。
    2. 排除分頁檔磁碟不要複寫。

- 要優化包含 Microsoft SQL Server tempdb 檔和系統資料庫檔的磁片的複製，可以：
    1. 將系統資料庫和 tempdb 放在兩個不同的磁碟上。
    2. 排除 tempdb 磁碟不要複寫。

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>範例 1：排除 SQL Server tempdb 磁碟

讓我們來看看如何處理源 SQL Server Windows VM 的磁片排除、容錯移轉和容錯移轉 - [SalesDB]，我們希望為此排除 tempdb。 

### <a name="exclude-disks-from-replication"></a>從複寫排除磁碟

我們在源 Windows VM 銷售DB 上具有這些磁片。

**磁碟名稱** | **來賓 OS 磁片** | **磁碟機代號** | **磁片資料類型**
--- | --- | --- | ---
DB-Disk0-OS | 磁片0 | C:\ | 作業系統磁片。
DB-Disk1| Disk1 | D:\ | SQL 系統資料庫和使用者資料庫1。
DB-Disk2 (已排除磁碟不要保護) | Disk2 | E:\ | 暫存檔案。
DB-Disk3 (已排除磁碟不要保護) | Disk3 | F:\ | SQL tempdb 資料庫。<br/><br/> 資料夾路徑 - F：[MSSQL]資料*。 在容錯移轉之前記下資料夾路徑。
DB-Disk4 | Disk4 |G:\ | 使用者資料庫 2

1. 我們啟用 SalesDB VM 的複製。
2. 我們將 Disk2 和 Disk3 排除在複製之外，因為這些磁片上的資料改動是暫時的。 


### <a name="handle-disks-during-failover"></a>容錯移轉期間處理磁片

由於磁片未複製，因此當您容錯移轉到 Azure 時，這些磁片在容錯移轉後創建的 Azure VM 上不存在。 Azure VM 在此表中匯總了磁片。

**來賓 OS 磁片** | **磁碟機代號** | **磁片資料類型**
--- | --- | ---
磁片0 | C:\ | 作業系統磁片。
Disk1 | E:\ | 暫存儲存位置<br/><br/>Azure 將添加此磁片。 由於 Disk2 和 Disk3 從複製中排除，因此 E： 是可用清單中的第一個磁碟機號。 Azure 會將 E: 指派給暫時存放磁碟區。 複製磁片的其他磁碟機號保持不變。
Disk2 | D:\ | SQL 系統資料庫和使用者資料庫 1
Disk3 | G:\ | 使用者資料庫 2

在我們的示例中，由於 Disk3（SQL tempdb 磁片）從複製中排除，並且在 Azure VM 上不可用，因此 SQL 服務處於已停止狀態，並且需要 F：_MSSQL_Data 路徑。 您可以通過多種方式創建此路徑： 

- 在容錯移轉後添加新磁片，並分配 tempdb 資料夾路徑。
- 使用現有的暫存磁碟來設定 tempdb 資料夾路徑。

#### <a name="add-a-new-disk-after-failover"></a>容錯移轉後添加新磁片

1. 容錯移轉之前，記下 SQL tempdb.mdf 和 tempdb.ldf 的路徑。
2. 從 Azure 門戶向容錯移轉 Azure VM 添加新磁片。 磁片的大小（或更大）應與源 SQL tempdb 磁片 （Disk3） 相同。
3. 登錄到 Azure VM。
4. 從磁碟管理 (diskmgmt.msc) 主控台，初始化並格式化剛新增的磁碟。
5. 分配 SQL tempdb 磁片 （F：） 使用的相同磁碟機號
6. 在 F: 磁碟區上建立 tempdb 資料夾 (F:\MSSQL\Data)。
7. 從服務主控台啟動 SQL 服務。

#### <a name="use-an-existing-temporary-storage-disk"></a>使用現有臨時存儲磁片 

1. 開啟命令提示字元。
2. 從命令提示字元，在復原模式下執行 SQL Server。

        Net start MSSQLSERVER /f / T3608

3. 執行下列 sqlcmd，將 tempdb 路徑變更為新的路徑。

        sqlcmd -A -S SalesDB        **Use your SQL DBname**
        USE master;     
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');       
        GO


4. 停止 Microsoft SQL Server 服務。

        Net stop MSSQLSERVER
5. 啟動 Microsoft SQL Server 服務。

        Net start MSSQLSERVER



### <a name="vmware-vms-disks-during-failback-to-original-location"></a>VMware VM：在故障回退到原始位置期間磁片

現在，讓我們看看當您故障回原始本地位置時，如何處理 VMware VM 上的磁片。

- **在 Azure 中創建的磁片**：由於我們的示例使用 Windows VM，因此在回滾或重新保護 VM 時，在 Azure 中手動創建的磁片不會複製回您的網站。
- **Azure 中的臨時存儲磁片**：臨時存儲磁片不會複製回本地主機。
- **排除的磁片**：從 VMware 到 Azure 複製排除的磁片在故障倒退後不在本地 VM 上可用。

在將 VMware VM 故障退回原始位置之前，Azure VM 磁片設置如下所示。

**來賓 OS 磁片** | **磁碟機代號** | **磁片資料類型**
--- | --- | ---
磁片0 | C:\ | 作業系統磁片。
Disk1 | E:\ | 臨時存儲。
Disk2 | D:\ | SQL 系統資料庫和使用者資料庫1。
Disk3 | G:\ | 使用者資料庫2。

故障倒退後，原始位置中的 VMware VM 在表中匯總了磁片。

**來賓 OS 磁片** | **磁碟機代號** | **磁片資料類型**
--- | --- | ---
磁片0 | C:\ | 作業系統磁片。
Disk1 | D:\ | SQL 系統資料庫和使用者資料庫1。
Disk2 | G:\ | 使用者資料庫2。


### <a name="hyper-v-vms-disks-during-failback-to-original-location"></a>超 VM：故障回退到原始位置期間的磁片

現在，讓我們看看當您故障回原始本地位置時如何處理 Hyper-V VM 上的磁片。

- **在 Azure 中創建的磁片**：在 Azure 中手動創建的磁片在故障恢復或重新保護 VM 時不會複製回網站。
- **Azure 中的臨時存儲磁片**：臨時存儲磁片不會複製回本地主機。
- **排除磁片**：故障後 VM 磁片配置與原始 VM 磁片配置相同。 從 Hyper-V 複製到 Azure 的磁片在故障恢復 VM 上可用。

在將 Hyper-VM 故障台故障回原始位置之前，Azure VM 磁片設置如下所示。

**來賓 OS 磁片** | **磁碟機代號** | **磁片資料類型**
--- | --- | ---
磁片0 | C:\ | 作業系統磁片。
Disk1 | E:\ | 臨時存儲。
Disk2 | D:\ | SQL 系統資料庫和使用者資料庫1。
Disk3 | G:\ | 使用者資料庫2。

在計畫容錯移轉（容錯移轉）從 Azure 到本地 Hyper-V 後，原始位置中的 Hyper-V VM 在表中匯總了磁片。

**磁片名稱** | **客體 OS 磁碟#** | **磁碟機代號** | **磁片資料類型**
 --- | --- | --- | ---
DB-Disk0-OS | 磁片0 |   C:\ | 作業系統磁片。
DB-Disk1 | Disk1 | D:\ | SQL 系統資料庫和使用者資料庫1。
DB-Disk2 (排除的磁碟) | Disk2 | E:\ | 暫存檔案。
DB-Disk3 (排除的磁碟) | Disk3 | F:\ | SQL tempdb 資料庫<br/><br/> 資料夾路徑（F：\MSSQL_資料\)）。
DB-Disk4 | Disk4 | G:\ | 使用者資料庫 2


## <a name="example-2-exclude-the-paging-file-disk"></a>示例 2：排除分頁檔磁片

讓我們來看看如何處理源 Windows VM 的磁片排除、容錯移轉和容錯移轉，為此我們希望在 D 磁碟機和備用磁碟機上排除 pagefile.sys 檔磁片。


### <a name="paging-file-on-the-d-drive"></a>D 磁碟機上的分頁檔

我們在源 VM 上具有這些磁片。

**磁碟名稱** | **來賓 OS 磁片** | **磁碟機代號** | **磁片資料類型**
--- | --- | --- | ---
DB-Disk0-OS | 磁片0 | C:\ | 作業系統磁碟
DB-Disk1（從複製中排除） | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | 使用者資料 1
DB-Disk3 | Disk3 | F:\ | 使用者資料 2

我們在源 VM 上的分頁檔設置如下所示：

![來源虛擬機器上的分頁檔設定](./media/exclude-disks-replication/pagefile-d-drive-source-vm.png)

1. 我們啟用 VM 的複製。
2. 我們將 DB-Disk1 排除在複製之外。

#### <a name="disks-after-failover"></a>容錯移轉後的磁片

容錯移轉後，Azure VM 已在表中匯總磁片。

**磁碟名稱** | **客體作業系統磁碟#** | **磁碟機代號** | **磁碟上的資料類型**
--- | --- | --- | ---
DB-Disk0-OS | 磁片0 | C:\ | 作業系統磁碟
DB-Disk1 | Disk1 | D:\ | 臨時存儲/分頁檔.sys <br/><br/> 因為 DB 磁片 1 （D：）排除後，D：是可用清單中的第一個磁碟機號。<br/><br/> Azure 會將 D: 指派給暫時存放磁碟區。<br/><br/> 由於 D： 可用，因此 VM 分頁檔設置保持不變）。
DB-Disk2 | Disk2 | E:\ | 使用者資料 1
DB-Disk3 | Disk3 | F:\ | 使用者資料 2

我們在 Azure VM 上的分頁檔設置如下所示：

![Azure 虛擬機器上的分頁檔設定](./media/exclude-disks-replication/pagefile-azure-vm-after-failover.png)

### <a name="paging-file-on-another-drive-not-d"></a>在另一個磁碟機上分頁檔（不是 D：）

讓我們看一下分頁檔不在 D 磁碟機上的示例。  

我們在源 VM 上具有這些磁片。

**磁碟名稱** | **來賓 OS 磁片** | **磁碟機代號** | **磁片資料類型**
--- | --- | --- | ---
DB-Disk0-OS | 磁片0 | C:\ | 作業系統磁碟
DB-Disk1（從複製中排除） | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | 使用者資料 1
DB-Disk3 | Disk3 | F:\ | 使用者資料 2

我們在本地 VM 上的分頁檔設置如下所示：

![內部部署虛擬機器上的分頁檔設定](./media/exclude-disks-replication/pagefile-g-drive-source-vm.png)

1. 我們啟用 VM 的複製。
2. 我們將 DB-Disk1 排除在複製之外。

#### <a name="disks-after-failover"></a>容錯移轉後的磁片

容錯移轉後，Azure VM 已在表中匯總磁片。

**磁碟名稱** | **客體 OS 磁碟#** | **磁碟機代號** | **磁片資料類型**
--- | --- | --- | ---
DB-Disk0-OS | 磁片0  |C:\ | 作業系統磁碟
DB-Disk1 | Disk1 | D:\ | 暫存儲存位置<br/><br/> 因為 D: 是可用清單中的第一個磁碟機代號，Azure 會將 D: 指派給暫時存放磁碟區。<br/><br/> 對於所有複寫的磁碟，磁碟機代號維持不變。<br/><br/> 由於 G： 磁片不可用，系統將使用分頁檔的 C： 磁碟機。
DB-Disk2 | Disk2 | E:\ | 使用者資料 1
DB-Disk3 | Disk3 | F:\ | 使用者資料 2

我們在 Azure VM 上的分頁檔設置如下所示：

![Azure 虛擬機器上的分頁檔設定](./media/exclude-disks-replication/pagefile-azure-vm-after-failover-2.png)


## <a name="next-steps"></a>後續步驟

- 詳細瞭解臨時存儲磁片的指南：
    - [瞭解如何](https://blogs.technet.microsoft.com/dataplatforminsider/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)在 Azure VM 中使用 SSD 來存儲 SQL Server TempDB 和緩衝集區擴展
    - [查看](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance)Azure VM 中 SQL Server 的性能最佳實踐。
- 在您的部署設定完成並開始執行之後，請 [深入了解](failover-failback-overview.md) 不同類型的容錯移轉。

