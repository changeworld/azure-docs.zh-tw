---
title: 使用 Azure Site Recovery 從複寫排除磁碟
description: 如何使用 Azure Site Recovery 從 Azure 的複寫排除磁碟。
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 5a8d52bd0cc40b45f92039c537a1b3b63f0bec61
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135690"
---
# <a name="exclude-disks-from-disaster-recovery"></a>從災害復原排除磁碟

本文描述如何在內部部署到 Azure 的災害復原期間，使用 [Azure Site Recovery](site-recovery-overview.md) 從複寫排除磁碟。 您可能會基於多種原因，而從複寫排除磁碟：

- 這樣一來，就不會複寫在受排除磁碟上變換的不重要資料。
- 將使用的複寫頻寬或目標端資源進行最佳化。
- 藉由不複寫不需要的資料來節省儲存體和網路資源。
- Azure VM 已達到 Site Recovery 複寫限制。


## <a name="supported-scenarios"></a>支援的案例

您可從複寫排除磁碟，如下表摘要所示。

**Azure 至 Azure** | **VMware 至 Azure** | **Hyper-V 至 Azure** | **實體伺服器至 Azure**
--- | --- | --- | ---
是 | 是 | 是 | 是

## <a name="exclude-limitations"></a>排除限制

**限制** | **Azure VM** | **VMware VM** | **Hyper-V VM**
--- | --- | ---
**磁碟類型** | 您可從複寫排除基本磁碟。<br/><br/> 您無法排除作業系統磁碟或動態磁碟。 暫存磁碟會根據預設排除。 | 您可從複寫排除基本磁碟。<br/><br/> 您無法排除作業系統磁碟或動態磁碟。 | 您可從複寫排除基本磁碟。<br/><br/> 您無法排除作業系統磁碟。 我們建議不要排除動態磁碟。 Site Recovery 無法識別客體 VM 中的 VHS 是基本或動態。 如果未排除所有相依動態磁碟區磁碟，則受保護的動態磁碟會在容錯移轉 VM 上成為失敗磁碟，且該磁碟上的資料無法存取。
**複寫磁碟** | 您無法排除正在複寫的磁碟。<br/><br/> 請停用並重新啟用 VM 的複寫。 |  您無法排除正在複寫的磁碟。 |  您無法排除正在複寫的磁碟。
**行動服務 (VMware)** | 不相關 | 您只能排除 VM 上已安裝行動服務的磁碟。<br/><br/> 這表示必須在要排除磁碟的 VM 上手動安裝行動服務。您無法使用推送安裝機制，因為其只會在啟用複寫後安裝行動服務。 | 不相關。
**新增/移除** | 您可使用受控磁碟在已啟用複寫的 Azure VM 上新增受控磁碟。 您無法移除已啟用複寫的 Azure VM 上磁碟。 | 啟用複寫後，您將無法新增或移除磁碟。 請停用再重新啟用複寫以新增磁碟。 | 啟用複寫後，您將無法新增或移除磁碟。 請停用再重新啟用複寫。
**容錯移轉** | 如果應用程式需要已排除的磁碟，則您必須在容錯移轉後手動建立磁碟，讓複寫的應用程式能夠執行。<br/><br/> 或者，您也可以藉由將 Azure 自動化整合至復原方案，在機器容錯移轉期間建立磁碟。 | 如果您排除應用程式所需的磁碟，則請在容錯移轉後在 Azure 中手動建立。 | 如果您排除應用程式所需的磁碟，則請在容錯移轉後在 Azure 中手動建立。
**內部部署容錯回復 - 手動建立的磁碟** | 不相關 | **Windows VM**：以手動方式在 Azure 中建立的磁碟將不會容錯回復。 例如，如果您容錯移轉三個磁碟，並直接在 Azure VM 上建立兩個磁碟，則只有三個已容錯移轉的磁碟會容錯回復。<br/><br/> **Linux VM**：以手動方式在 Azure 中建立的磁碟會容錯回復。 例如，如果您容錯移轉三個磁碟，並在 Azure 上建立兩個磁碟，則五個磁碟全都將容錯回復。 您無法從容錯回復排除手動建立的磁碟。 | 以手動方式在 Azure 中建立的磁碟將不會容錯回復。 例如，如果您容錯移轉三個磁碟，並直接在 Azure VM 上建立兩個磁碟，則只有三個已容錯移轉的磁碟將容錯回復。
**內部部署容錯回復 - 排除的磁碟** | 不相關 | 如果您容錯回復至原始電腦，則容錯回復 VM 磁碟設定不會包含排除的磁碟。 從 WMware 到 Azure 複寫排除的磁碟不會出現在容錯回復 VM 上。 | 容錯回復至原始 Hyper-V 位置後，容錯回復 VM 磁碟設定會維持與原始來源 VM 磁碟設定相同。 從 Hyper-V 網站到 Azure 複寫排除的磁碟，不會出現在容錯回復 VM 上。



## <a name="typical-scenarios"></a>典型案例

資料變換就是很好的排除候選範例，包括對分頁檔 (pagefile.sys) 的寫入，以及對 Microsoft SQL Server tempdb 檔案的寫入。 根據工作負載和儲存體子系統，分頁檔和 tempdb 檔案可註冊大量變換。 將這類型的資料複寫至 Azure，會耗用大量資源。

- 若要使用包含作業系統和分頁檔的單一虛擬磁碟，以將 VM 的複寫最佳化，您可以：
    1. 將單一虛擬磁碟分割成兩個虛擬磁碟。 一個虛擬磁碟具有作業系統，另一個具有分頁檔。
    2. 排除分頁檔磁碟不要複寫。

- 若要將同時具有 Microsoft SQL Server tempdb 檔案和系統資料庫檔案的磁碟複寫最佳化，您可以︰
    1. 將系統資料庫和 tempdb 放在兩個不同的磁碟上。
    2. 排除 tempdb 磁碟不要複寫。

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>範例 1：排除 SQL Server tempdb 磁碟

讓我們看一下如何處理磁碟排除、容錯移轉和來源 SQL Server Windows VM **SalesDB*** (想要為其排除 tempdb 的 VM) 的容錯移轉。 

### <a name="exclude-disks-from-replication"></a>從複寫排除磁碟

我們在來源 Windows VM SalesDB 上有這些磁碟。

**磁碟名稱** | **客體 OS 磁碟** | **磁碟機代號** | **磁碟資料類型**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | 作業系統磁碟。
DB-Disk1| Disk1 | D:\ | SQL 系統資料庫和使用者資料庫 1。
DB-Disk2 (已排除磁碟不要保護) | Disk2 | E:\ | 暫存檔案。
DB-Disk3 (已排除磁碟不要保護) | Disk3 | F:\ | SQL tempdb 資料庫。<br/><br/> 資料夾路徑 - F:\MSSQL\Data\。 請在容錯移轉前記下資料夾路徑。
DB-Disk4 | Disk4 |G:\ | 使用者資料庫 2

1. 我們會啟用 SalesDB VM 的複寫。
2. 我們會從複寫排除 Disk2 和 Disk3，因為這些磁碟上的資料變換為暫時性。 


### <a name="handle-disks-during-failover"></a>在容錯移轉期間處理磁碟

因為磁碟不會複寫，所以在您容錯移轉至 Azure 時，這些磁碟不會出現在容錯移轉後才建立的 Azure VM 上。 Azure VM 會在下表中摘要說明磁碟。

**客體 OS 磁碟** | **磁碟機代號** | **磁碟資料類型**
--- | --- | ---
Disk0 | C:\ | 作業系統磁碟。
Disk1 | E:\ | 暫存位置<br/><br/>Azure 會新增此磁碟。 因為已從複寫排除 Disk2 和 Disk3，E: 是可用清單中的第一個磁碟機代號。 Azure 會將 E: 指派給暫時存放磁碟區。 複寫磁碟的其他磁碟機代號則會維持不變。
Disk2 | D:\ | SQL 系統資料庫和使用者資料庫 1
Disk3 | G:\ | 使用者資料庫 2

在範例中，因為 SQL tempdb 磁碟 Disk3 已從複寫排除並在 Azure VM 上無法使用，所以 SQL 服務會進入停止狀態，且需要 F:\MSSQL\Data 路徑。 您可使用幾種方式來建立此路徑： 

- 在容錯移轉後新增磁碟並指派 tempdb 資料夾路徑。
- 使用現有的暫存磁碟來設定 tempdb 資料夾路徑。

#### <a name="add-a-new-disk-after-failover"></a>在容錯移轉後新增磁碟

1. 容錯移轉之前，記下 SQL tempdb.mdf 和 tempdb.ldf 的路徑。
2. 從 Azure 入口網站將新的磁碟新增至容錯移轉 Azure VM。 磁碟的大小應該與來源 SQL tempdb 磁碟 (Disk 3) 大小相同 (或較大)。
3. 登入 Azure VM。
4. 從磁碟管理 (diskmgmt.msc) 主控台，初始化並格式化剛新增的磁碟。
5. 指派 SQL tempdb 磁碟所使用的相同磁碟機代號 (F:)
6. 在 F: 磁碟區上建立 tempdb 資料夾 (F:\MSSQL\Data)。
7. 從服務主控台啟動 SQL 服務。

#### <a name="use-an-existing-temporary-storage-disk"></a>使用現有的暫存磁碟 

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



### <a name="vmware-vms-disks-during-failback-to-original-location"></a>VMware VM：容錯回復至原始位置期間的磁碟

現在讓我們看一下在容錯回復至原始內部部署位置時，如何處理 VMware VM 上的磁碟。

- **在 Azure 中建立的磁碟**：因為範例使用 Windows VM，所以當容錯回復或重新保護 VM 時，您在 Azure 中手動建立的磁碟不會複寫回網站。
- **Azure 中的暫存磁碟**：暫存磁碟不會複寫至內部部署主機。
- **排除的磁碟**：從 WMware 到 Azure 複寫排除的磁碟，在容錯回復後不會出現在內部部署 VM 上。

將 VMware VM 容錯回復至原始位置之前，Azure VM 磁碟的設定如下所示。

**客體 OS 磁碟** | **磁碟機代號** | **磁碟資料類型**
--- | --- | ---
Disk0 | C:\ | 作業系統磁碟。
Disk1 | E:\ | 暫存位置。
Disk2 | D:\ | SQL 系統資料庫和使用者資料庫 1。
Disk3 | G:\ | 使用者資料庫 2。

容錯回復後，原始位置中的 VMware VM 會在表格中摘要說明磁碟。

**客體 OS 磁碟** | **磁碟機代號** | **磁碟資料類型**
--- | --- | ---
Disk0 | C:\ | 作業系統磁碟。
Disk1 | D:\ | SQL 系統資料庫和使用者資料庫 1。
Disk2 | G:\ | 使用者資料庫 2。


### <a name="hyper-v-vms-disks-during-failback-to-original-location"></a>Hyper-V VM：容錯回復至原始位置期間的磁碟

現在讓我們看一下在容錯回復至原始內部部署位置時，如何處理 Hyper-V VM 上的磁碟。

- **在 Azure 中建立的磁碟**：當容錯回復或重新保護 VM 時，您在 Azure 中手動建立的磁碟不會複寫回網站。
- **Azure 中的暫存磁碟**：暫存磁碟不會複寫至內部部署主機。
- **排除的磁碟**：容錯回復後，VM 磁碟設定會與原始 VM 磁碟設定相同。 從 Hyper-V 到 Azure 複寫排除的磁碟會出現在容錯回復 VM 上。

將 Hyper-V VM 容錯回復至原始位置之前，Azure VM 磁碟的設定如下所示。

**客體 OS 磁碟** | **磁碟機代號** | **磁碟資料類型**
--- | --- | ---
Disk0 | C:\ | 作業系統磁碟。
Disk1 | E:\ | 暫存位置。
Disk2 | D:\ | SQL 系統資料庫和使用者資料庫 1。
Disk3 | G:\ | 使用者資料庫 2。

在進行從 Azure 到內部部署 Hyper-V 的已規劃容錯移轉 (容錯回復) 之後，原始位置中的 Hyper-V VM 會在表格中摘要說明磁碟。

**磁碟名稱** | **客體 OS 磁碟#** | **磁碟機代號** | **磁碟資料類型**
 --- | --- | --- | ---
DB-Disk0-OS | Disk0 |   C:\ | 作業系統磁碟。
DB-Disk1 | Disk1 | D:\ | SQL 系統資料庫和使用者資料庫 1。
DB-Disk2 (排除的磁碟) | Disk2 | E:\ | 暫存檔案。
DB-Disk3 (排除的磁碟) | Disk3 | F:\ | SQL tempdb 資料庫<br/><br/> 資料夾路徑 (F:\MSSQL\Data\)。
DB-Disk4 | Disk4 | G:\ | 使用者資料庫 2


## <a name="example-2-exclude-the-paging-file-disk"></a>範例 2：排除分頁檔磁碟

讓我們看一下如何處理磁碟排除、容錯移轉和來源 Windows VM (我們想要為其在 D 磁碟機和替代磁碟機上排除 pagefile.sys 檔案磁碟) 的容錯移轉。


### <a name="paging-file-on-the-d-drive"></a>D 磁碟機上的分頁檔

來源 VM 上有這些磁碟。

**磁碟名稱** | **客體 OS 磁碟** | **磁碟機代號** | **磁碟資料類型**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | 作業系統磁碟
DB-Disk1 (從複寫排除) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | 使用者資料 1
DB-Disk3 | Disk3 | F:\ | 使用者資料 2

來源 VM 上的分頁檔設定如下所示：

![來源虛擬機器上的分頁檔設定](./media/exclude-disks-replication/pagefile-d-drive-source-vm.png)

1. 我們會啟用 VM 的複寫。
2. 我們會從複寫排除 DB-Disk1。

#### <a name="disks-after-failover"></a>容錯移轉後的磁碟

容錯移轉後，Azure VM 會在下表中摘要說明磁碟。

**磁碟名稱** | **客體作業系統磁碟#** | **磁碟機代號** | **磁碟上的資料類型**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | 作業系統磁碟
DB-Disk1 | Disk1 | D:\ | 暫存位置/pagefile.sys <br/><br/> 因為已排除 DB-Disk1 (D:)，D: 是可用清單中的第一個磁碟機代號。<br/><br/> Azure 會將 D: 指派給暫時存放磁碟區。<br/><br/> 因為可使用 D:，所以 VM 分頁檔設定會維持不變)。
DB-Disk2 | Disk2 | E:\ | 使用者資料 1
DB-Disk3 | Disk3 | F:\ | 使用者資料 2

Azure VM 上的分頁檔設定如下所示：

![Azure 虛擬機器上的分頁檔設定](./media/exclude-disks-replication/pagefile-azure-vm-after-failover.png)

### <a name="paging-file-on-another-drive-not-d"></a>另一部磁碟機上的分頁檔 (不是 D:)

讓我們看一下分頁檔不在 D 磁碟機上的範例。  

來源 VM 上有這些磁碟。

**磁碟名稱** | **客體 OS 磁碟** | **磁碟機代號** | **磁碟資料類型**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | 作業系統磁碟
DB-Disk1 (從複寫排除) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | 使用者資料 1
DB-Disk3 | Disk3 | F:\ | 使用者資料 2

內部部署 VM 上的分頁檔設定如下所示：

![內部部署虛擬機器上的分頁檔設定](./media/exclude-disks-replication/pagefile-g-drive-source-vm.png)

1. 我們會啟用 VM 的複寫。
2. 我們會從複寫排除 DB-Disk1。

#### <a name="disks-after-failover"></a>容錯移轉後的磁碟

容錯移轉後，Azure VM 會在下表中摘要說明磁碟。

**磁碟名稱** | **客體 OS 磁碟#** | **磁碟機代號** | **磁碟資料類型**
--- | --- | --- | ---
DB-Disk0-OS | Disk0  |C:\ | 作業系統磁碟
DB-Disk1 | Disk1 | D:\ | 暫存位置<br/><br/> 因為 D: 是可用清單中的第一個磁碟機代號，Azure 會將 D: 指派給暫時存放磁碟區。<br/><br/> 對於所有複寫的磁碟，磁碟機代號維持不變。<br/><br/> 因為 G: 磁碟無法使用，所以系統會針對分頁檔使用 C: 磁碟機。
DB-Disk2 | Disk2 | E:\ | 使用者資料 1
DB-Disk3 | Disk3 | F:\ | 使用者資料 2

Azure VM 上的分頁檔設定如下所示：

![Azure 虛擬機器上的分頁檔設定](./media/exclude-disks-replication/pagefile-azure-vm-after-failover-2.png)


## <a name="next-steps"></a>後續步驟

- 深入了解暫存磁碟的指導方針：
    - [深入了解](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)使用 Azure VM 中的 SSD 來儲存 SQL Server TempDB 和緩衝集區延伸模組
    - [檢閱](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md) Azure VM 中的 SQL Server 效能最佳做法。
- 在您的部署設定完成並開始執行之後，請 [深入了解](failover-failback-overview.md) 不同類型的容錯移轉。
