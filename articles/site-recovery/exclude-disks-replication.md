---
title: 使用 Azure Site Recovery 從複寫中排除磁片
description: 如何使用 Azure Site Recovery 將磁片從複寫排除到 Azure。
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: abecc19cac57a4a95d01b7a7ec076259088b101b
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900273"
---
# <a name="exclude-disks-from-disaster-recovery"></a>排除磁片免于嚴重損壞修復

本文說明如何在使用[Azure Site Recovery](site-recovery-overview.md)從內部部署到 Azure 的嚴重損壞修復期間，排除磁片不進行複寫。 您可能會因為許多原因而將磁片從複寫中排除：

- 因此不會複寫排除的磁片上所變換的不重要資料。
- 將使用的複寫頻寬或目標端資源優化。
- 藉由不復寫不需要的資料來儲存儲存體和網路資源。
- Azure Vm 已達到 Site Recovery 複寫限制。


## <a name="supported-scenarios"></a>支援的案例

您可以從複寫中排除磁片，如下表摘要所示。

**Azure 至 Azure** | **VMware 至 Azure** | **Hyper-V 至 Azure** 
--- | --- | ---
是 | 是 | 是 

## <a name="exclude-limitations"></a>排除限制

**局限** | **Azure VM** | **VMware VM** | **Hyper-V VM**
--- | --- | ---
**磁碟類型** | 您可以從複寫中排除基本磁碟。<br/><br/> 您無法排除作業系統磁片或動態磁碟。 預設會排除暫存磁片。 | 您可以從複寫中排除基本磁碟。<br/><br/> 您無法排除作業系統磁片或動態磁碟。 | 您可以從複寫中排除基本磁碟。<br/><br/> 您無法排除作業系統磁碟。 我們建議不要排除動態磁碟。 Site Recovery 無法識別來賓 VM 中的「基本」或「動態」 VHS。 如果未排除所有的相依動態磁碟區磁片，受保護的動態磁碟會在容錯移轉 VM 上成為失敗的磁片，而且該磁片上的資料無法存取。
**複寫磁片** | 您無法排除正在複寫的磁片。<br/><br/> 停用並重新啟用 VM 的複寫。 |  您無法排除正在複寫的磁片。 |  您無法排除正在複寫的磁片。
**行動服務（VMware）** | 無關 | 您只能在已安裝行動服務的 Vm 上排除磁片。<br/><br/> 這表示您必須在想要排除磁片的 Vm 上手動安裝行動服務。您無法使用推送安裝機制，因為它只會在啟用複寫後才安裝行動服務。 | 無關。
**新增/移除** | 您可以在具有受控磁片的已啟用複寫的 Azure Vm 上新增受控磁片。 您無法移除已啟用複寫的 Azure Vm 上的磁片。 | 啟用複寫之後，即無法新增或移除磁片。 停用再重新啟用複寫以新增磁片。 | 啟用複寫之後，即無法新增或移除磁片。 停用再重新啟用複寫。
**容錯移轉** | 如果應用程式需要您排除的磁片，在容錯移轉之後，您必須手動建立磁片，才能執行複寫的應用程式。<br/><br/> 或者，您也可以藉由將 Azure 自動化整合至復原方案，在 VM 容錯移轉期間建立磁片。 | 如果您排除應用程式所需的磁片，請在容錯移轉之後，在 Azure 中手動建立它。 | 如果您排除應用程式所需的磁片，請在容錯移轉之後，在 Azure 中手動建立它。
**內部部署容錯回復-手動建立的磁片** | 無關 | **Windows vm**：在 Azure 中手動建立的磁片不會容錯回復。 例如，如果您故障切換三個磁片，並直接在 Azure VM 上建立兩個磁片，則只有三個已故障轉的磁片會容錯回復。<br/><br/> **Linux vm**：在 Azure 中手動建立的磁片會容錯回復。 例如，如果您故障切換三個磁片，並在 Azure VM 上建立兩個磁片，則所有五個都會容錯回復。 您無法從容錯回復排除手動建立的磁碟。 | 在 Azure 中手動建立的磁片不會容錯回復。 例如，如果您故障切換三個磁片，並直接在 Azure VM 上建立兩個磁片，則只有三個已容錯回復的磁片會容錯回復。
**內部部署容錯回復-排除的磁片** | 無關 | 如果您容錯回復至原始電腦，容錯回復 VM 磁片設定不會包含已排除的磁片。 從 VMware 到 Azure 複寫排除的磁片，無法在容錯回復 VM 上使用。 | 容錯回復到原始的 Hyper-v 位置時，容錯回復 VM 磁片設定會與原始來源 VM 磁片的設定保持相同。 從 Hyper-v 網站排除到 Azure 複寫的磁片，可在容錯回復 VM 上取得。



## <a name="typical-scenarios"></a>一般案例

資料變換的範例是很棒的排除專案，包括寫入分頁檔案（檔頁面），以及寫入 Microsoft SQL Server 的 tempdb 檔案。 根據工作負載和儲存子系統，分頁和 tempdb 檔案可以註冊大量變換。 將此類型的資料複寫至 Azure 會耗用大量資源。

- 若要使用包含作業系統和分頁檔案的單一虛擬磁片來優化 VM 的複寫，您可以：
    1. 將單一虛擬磁碟分割成兩個虛擬磁碟。 一個虛擬磁碟具有作業系統，另一個具有分頁檔。
    2. 排除分頁檔磁碟不要複寫。

- 若要優化同時包含 Microsoft SQL Server tempdb 檔案和系統資料庫檔案的磁片複寫，您可以：
    1. 將系統資料庫和 tempdb 放在兩個不同的磁碟上。
    2. 排除 tempdb 磁碟不要複寫。

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>範例 1：排除 SQL Server tempdb 磁碟

讓我們看看如何處理來源 SQL Server Windows VM-* * SalesDB * * * 的磁片排除、容錯移轉和容錯移轉，而我們想要排除 tempdb。 

### <a name="exclude-disks-from-replication"></a>從複寫排除磁碟

我們在來源 Windows VM SalesDB 上有這些磁片。

**磁碟名稱** | **客體作業系統磁片** | **磁碟機代號** | **磁片資料類型**
--- | --- | --- | ---
DB-Disk0-OS | Disk0.vhdx | C:\ | 作業系統磁片。
DB-Disk1| Disk1 | D:\ | SQL 系統資料庫和使用者 Database1。
DB-Disk2 (已排除磁碟不要保護) | Disk2 | E:\ | 暫存檔案。
DB-Disk3 (已排除磁碟不要保護) | Disk3 | F:\ | SQL tempdb 資料庫。<br/><br/> 資料夾路徑-F:\MSSQL\Data\。 請記下 [容錯移轉前的資料夾路徑]。
DB-Disk4 | Disk4 |G:\ | 使用者資料庫 2

1. 我們會為 SalesDB VM 啟用複寫。
2. 我們會從複寫中排除 Disk2 和 Disk3，因為這些磁片上的資料變換是暫時性的。 


### <a name="handle-disks-during-failover"></a>在容錯移轉期間處理磁片

由於磁片不會複寫，因此當您容錯移轉至 Azure 時，這些磁片不會出現在容錯移轉之後建立的 Azure VM 上。 Azure VM 具有下表摘要說明的磁片。

**客體作業系統磁片** | **磁碟機代號** | **磁片資料類型**
--- | --- | ---
Disk0.vhdx | C:\ | 作業系統磁片。
Disk1 | E:\ | 暫存儲存位置<br/><br/>Azure 會新增此磁片。 因為 Disk2 和 Disk3 已從複寫中排除，所以 E：是可用清單中的第一個磁碟機號。 Azure 會將 E: 指派給暫時存放磁碟區。 複寫磁片的其他磁碟機號維持不變。
Disk2 | D:\ | SQL 系統資料庫和使用者資料庫 1
Disk3 | G:\ | 使用者資料庫 2

在我們的範例中，因為 SQL tempdb 磁片已從複寫中排除，且在 Azure VM 上無法使用，所以 SQL 服務會處於停止狀態，而且需要 F:\MSSQL\Data 路徑。 您可以用幾種方式建立此路徑： 

- 在容錯移轉之後新增磁片，並指派 tempdb 資料夾路徑。
- 使用現有的暫存磁碟來設定 tempdb 資料夾路徑。

#### <a name="add-a-new-disk-after-failover"></a>在容錯移轉之後新增磁片

1. 容錯移轉之前，記下 SQL tempdb.mdf 和 tempdb.ldf 的路徑。
2. 從 Azure 入口網站，將新的磁片新增至容錯移轉 Azure VM。 磁片應該與來源 SQL tempdb 磁片（Disk3）大小相同（或更大）。
3. 登入 Azure VM。
4. 從磁碟管理 (diskmgmt.msc) 主控台，初始化並格式化剛新增的磁碟。
5. 指派 SQL tempdb 磁片所使用的相同磁碟機號（F：）
6. 在 F: 磁碟區上建立 tempdb 資料夾 (F:\MSSQL\Data)。
7. 從服務主控台啟動 SQL 服務。

#### <a name="use-an-existing-temporary-storage-disk"></a>使用現有的暫存儲存體磁片 

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



### <a name="vmware-vms-disks-during-failback-to-original-location"></a>VMware Vm：容錯回復至原始位置期間的磁片

現在讓我們看看如何在容錯回復至原始內部部署位置時，處理 VMware Vm 上的磁片。

- **在 azure 中建立的磁片**：因為我們的範例使用 Windows VM，所以當您容錯回復或重新保護 VM 時，您在 Azure 中手動建立的磁片不會複寫回您的網站。
- **Azure 中的暫存儲存體磁片**：暫存儲存體磁片不會複寫回內部部署主機。
- **排除的磁片**：容錯回復後，無法在內部部署 VM 上使用從 VMware 到 Azure 複寫排除的磁片。

將 VMware Vm 容錯回復至原始位置之前，Azure VM 磁片設定如下所示。

**客體作業系統磁片** | **磁碟機代號** | **磁片資料類型**
--- | --- | ---
Disk0.vhdx | C:\ | 作業系統磁片。
Disk1 | E:\ | 暫存儲存體。
Disk2 | D:\ | SQL 系統資料庫和使用者 Database1。
Disk3 | G:\ | 使用者 Database2。

容錯回復之後，原始位置中的 VMware VM 會在資料表中摘要說明磁片。

**客體作業系統磁片** | **磁碟機代號** | **磁片資料類型**
--- | --- | ---
Disk0.vhdx | C:\ | 作業系統磁片。
Disk1 | D:\ | SQL 系統資料庫和使用者 Database1。
Disk2 | G:\ | 使用者 Database2。


### <a name="hyper-v-vms-disks-during-failback-to-original-location"></a>Hyper-v Vm：容錯回復至原始位置期間的磁片

現在讓我們看看如何在容錯回復至原始內部部署位置時，處理 Hyper-v Vm 上的磁片。

- **在 azure 中建立的磁片**：當您容錯回復或重新保護 VM 時，您在 azure 中手動建立的磁片不會複寫回您的網站。
- **Azure 中的暫存儲存體磁片**：暫存儲存體磁片不會複寫回內部部署主機。
- **排除的磁片**：容錯回復之後，vm 磁片設定與原始 VM 磁片設定相同。 從 Hyper-v 到 Azure 的複寫所排除的磁片，可在容錯回復 VM 上取得。

在您將 Hyper-v Vm 容錯回復至原始位置之前，Azure VM 磁片設定如下所示。

**客體作業系統磁片** | **磁碟機代號** | **磁片資料類型**
--- | --- | ---
Disk0.vhdx | C:\ | 作業系統磁片。
Disk1 | E:\ | 暫存儲存體。
Disk2 | D:\ | SQL 系統資料庫和使用者 Database1。
Disk3 | G:\ | 使用者 Database2。

在規劃的容錯移轉（容錯回復）從 Azure 到內部部署 Hyper-v 之後，原始位置中的 Hyper-v VM 會在資料表中摘要說明磁片。

**磁片名稱** | **客體 OS 磁碟#** | **磁碟機代號** | **磁片資料類型**
 --- | --- | --- | ---
DB-Disk0-OS | Disk0.vhdx |   C:\ | 作業系統磁片。
DB-Disk1 | Disk1 | D:\ | SQL 系統資料庫和使用者 Database1。
DB-Disk2 (排除的磁碟) | Disk2 | E:\ | 暫存檔案。
DB-Disk3 (排除的磁碟) | Disk3 | F:\ | SQL tempdb 資料庫<br/><br/> 資料夾路徑（F:\MSSQL\Data\)。
DB-Disk4 | Disk4 | G:\ | 使用者資料庫 2


## <a name="example-2-exclude-the-paging-file-disk"></a>範例2：排除分頁檔案磁片

讓我們看看如何處理來源 Windows VM 的磁片排除、容錯移轉和容錯移轉，我們想要在 D 磁片磁碟機和替代磁片磁碟機上排除分頁檔磁片。


### <a name="paging-file-on-the-d-drive"></a>D 磁片磁碟機上的分頁檔案

來源 VM 上有這些磁片。

**磁碟名稱** | **客體作業系統磁片** | **磁碟機代號** | **磁片資料類型**
--- | --- | --- | ---
DB-Disk0-OS | Disk0.vhdx | C:\ | 作業系統磁碟
DB-Disk1 （從複寫中排除） | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | 使用者資料 1
DB-Disk3 | Disk3 | F:\ | 使用者資料 2

來源 VM 上的分頁檔設定如下所示：

![來源虛擬機器上的分頁檔設定](./media/exclude-disks-replication/pagefile-d-drive-source-vm.png)

1. 我們會為 VM 啟用複寫。
2. 我們會從複寫中排除資料庫 Disk1。

#### <a name="disks-after-failover"></a>容錯移轉後的磁片

容錯移轉之後，Azure VM 會在資料表中摘要說明磁片。

**磁碟名稱** | **客體作業系統磁碟#** | **磁碟機代號** | **磁碟上的資料類型**
--- | --- | --- | ---
DB-Disk0-OS | Disk0.vhdx | C:\ | 作業系統磁碟
DB-Disk1 | Disk1 | D:\ | 暫存儲存體/分頁檔 <br/><br/> 因為 DB-Disk1 （D：）已排除，D：是可用清單中的第一個磁碟機號。<br/><br/> Azure 會將 D: 指派給暫時存放磁碟區。<br/><br/> 因為可以使用 D：，所以 VM 分頁檔設定會維持不變）。
DB-Disk2 | Disk2 | E:\ | 使用者資料 1
DB-Disk3 | Disk3 | F:\ | 使用者資料 2

Azure VM 上的分頁檔設定如下所示：

![Azure 虛擬機器上的分頁檔設定](./media/exclude-disks-replication/pagefile-azure-vm-after-failover.png)

### <a name="paging-file-on-another-drive-not-d"></a>另一個磁片磁碟機上的分頁檔案（不是 D：）

讓我們看看分頁檔案不在 D 磁片磁碟機上的範例。  

來源 VM 上有這些磁片。

**磁碟名稱** | **客體作業系統磁片** | **磁碟機代號** | **磁片資料類型**
--- | --- | --- | ---
DB-Disk0-OS | Disk0.vhdx | C:\ | 作業系統磁碟
DB-Disk1 （從複寫中排除） | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | 使用者資料 1
DB-Disk3 | Disk3 | F:\ | 使用者資料 2

我們在內部部署 VM 上的分頁檔設定如下所示：

![內部部署虛擬機器上的分頁檔設定](./media/exclude-disks-replication/pagefile-g-drive-source-vm.png)

1. 我們會為 VM 啟用複寫。
2. 我們會從複寫中排除資料庫 Disk1。

#### <a name="disks-after-failover"></a>容錯移轉後的磁片

容錯移轉之後，Azure VM 會在資料表中摘要說明磁片。

**磁碟名稱** | **客體 OS 磁碟#** | **磁碟機代號** | **磁片資料類型**
--- | --- | --- | ---
DB-Disk0-OS | Disk0.vhdx  |C:\ | 作業系統磁碟
DB-Disk1 | Disk1 | D:\ | 暫存儲存位置<br/><br/> 因為 D: 是可用清單中的第一個磁碟機代號，Azure 會將 D: 指派給暫時存放磁碟區。<br/><br/> 對於所有複寫的磁碟，磁碟機代號維持不變。<br/><br/> 由於 G：磁片無法使用，因此系統會使用 C：磁片磁碟機來進行分頁檔。
DB-Disk2 | Disk2 | E:\ | 使用者資料 1
DB-Disk3 | Disk3 | F:\ | 使用者資料 2

Azure VM 上的分頁檔設定如下所示：

![Azure 虛擬機器上的分頁檔設定](./media/exclude-disks-replication/pagefile-azure-vm-after-failover-2.png)


## <a name="next-steps"></a>後續步驟

- 深入瞭解暫存儲存體磁片的指導方針：
    - [瞭解](https://blogs.technet.microsoft.com/dataplatforminsider/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)如何在 Azure vm 中使用 ssd 來儲存 SQL Server TempDB 和緩衝集區延伸模組
    - [查看](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance)Azure vm 中 SQL Server 的效能最佳作法。
- 在您的部署設定完成並開始執行之後，請 [深入了解](failover-failback-overview.md) 不同類型的容錯移轉。

