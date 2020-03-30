---
title: Azure VMware 解決方案（按雲簡單 - 為 Oracle RAC 優化雲簡單私有雲）
description: 描述如何部署新群集並優化針對 Oracle 實際應用程式群集 （RAC） 安裝和配置的 VM
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 733a225c66040cb2ab819f041647120c8b63b6a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77016012"
---
# <a name="optimize-your-cloudsimple-private-cloud-for-installing-oracle-rac"></a>優化雲簡單私有雲，以安裝 Oracle RAC

您可以在雲簡單私有雲環境中部署 Oracle 實際應用程式群集 （RAC）。 本指南介紹如何部署新群集和優化 Oracle RAC 解決方案的 VM。 完成本主題中的步驟後，可以安裝和配置 Oracle RAC。

## <a name="storage-policy"></a>存儲策略

成功實施 Oracle RAC 需要群集中的足夠數量的節點。  在 vSAN 存儲策略中，容容失敗 （FTT） 應用於用於存儲資料庫、日誌和重做磁片的資料磁片。  有效容忍故障所需的節點數為 2N+1，其中 N 是 FTT 的值。

示例：如果所需的 FTT 為 2，則群集中的節點總數必須為 2*2+1 = 5。

## <a name="overview-of-deployment"></a>部署概觀

以下各節介紹如何為 Oracle RAC 設置雲簡單私有雲環境。

1. 磁片配置的最佳做法
2. 部署雲簡單私有雲 vSphere 群集
3. 為 Oracle RAC 設置網路
4. 設置 vSAN 存儲策略
5. 創建 Oracle VM 並創建共用 VM 磁片
6. 設置 VM 到主機的關聯規則

## <a name="best-practices-for-disk-configuration"></a>磁片配置的最佳做法

Oracle RAC 虛擬機器有多個磁片，用於特定功能。  共用磁片安裝在 Oracle RAC 群集使用的所有虛擬機器上。  作業系統和軟體安裝磁片僅安裝在單個虛擬機器上。  

![Oracle RAC 虛擬機器磁片概述](media/oracle-vm-disks-overview.png)

下面的示例使用下表中定義的磁片。

| 磁碟                                      | 目的                                       | 共用磁片 |
|-------------------------------------------|-----------------------------------------------|-------------|
| OS                                        | 作業系統磁碟                         | 否          |
| 網 格                                      | Oracle 網格軟體的安裝位置     | 否          |
| DATABASE                                  | 安裝 Oracle 資料庫軟體的位置 | 否          |
| 奧拉霍馬                                   | Oracle 資料庫二進位檔案的基本位置    | 否          |
| 資料1， 資料2， 資料3， 資料4                | 存儲 Oracle 資料庫檔案的磁片   | 是         |
| 重做 1、重做 2、重做 3、重做 4、重做 5、重做6  | 重做日誌磁片                                | 是         |
| OCR1、 OCR2、 OCR3、 OCR4、 OCR5              | 投票磁片                                  | 是         |
| FRA1， FRA2                                | 快速恢復區域磁片                      | 是         |

![Oracle 虛擬機器磁片配置](media/oracle-vmdk.png)

### <a name="virtual-machine-configuration"></a>虛擬機器組態

* 每個虛擬機器都配置了四個 SCSI 控制器。
* SCSI 控制器類型設置為 VMware 半虛擬。
* 創建了多個虛擬磁片 （.vmdk）。
* 磁片安裝在不同的 SCSI 控制器上。
* 為共用群集磁片設置了多寫入器共用類型。
* vSAN 存儲策略的定義是確保磁片的高可用性。

### <a name="operating-system-and-software-disk-configuration"></a>作業系統和軟體磁片配置

每個 Oracle 虛擬機器都配置了多個磁片，用於主機作業系統、交換、軟體安裝和其他作業系統功能。  這些磁片不會在虛擬機器之間共用。  

* 每個虛擬機器的三個磁片配置為虛擬磁片並安裝在 Oracle RAC 虛擬機器上。
    * OS 磁碟
    * 用於存儲 Oracle 網格的磁片安裝檔
    * 用於存儲 Oracle 資料庫安裝檔的磁片
* 磁片可以配置為**精簡預配**。
* 每個磁片都安裝在第一個 SCSI 控制器 （SCSI0） 上。  
* 共用設置為 **"無共用**"。
* 冗余在存儲上使用 vSAN 策略定義。  

![Oracle RAC 資料磁片組配置](media/oracle-vm-os-disks.png)

### <a name="data-disk-configuration"></a>資料磁片配置

資料磁片主要用於存儲資料庫檔案。  

* 四個磁片配置為虛擬磁片，並安裝在所有 Oracle RAC 虛擬機器上。
* 每個磁片都安裝在不同的 SCSI 控制器上。
* 每個虛擬磁片都配置為 **"厚備配"已歸零**。  
* 共用設置為**多寫入器**。  
* 磁片必須配置為自動存儲管理 （ASM） 磁片組。  
* 冗余在存儲上使用 vSAN 策略定義。  
* ASM 冗余設置為**外部**冗余。

![Oracle RAC 資料磁片組配置](media/oracle-vm-data-disks.png)

### <a name="redo-log-disk-configuration"></a>重做日誌磁片配置

重做日誌檔用於存儲對資料庫所做的更改的副本。  當任何故障後需要恢復資料時，將使用日誌檔。

* 重做日誌磁片必須配置為多個磁片組。  
* 所有 Oracle RAC 虛擬機器上創建並裝載了六個磁片。
* 磁片安裝在不同的 SCSI 控制器上
* 每個虛擬磁片都配置為 **"厚備配"已歸零**。
* 共用設置為**多寫入器**。  
* 磁片必須配置為兩個 ASM 磁片組。
* 每個 ASM 磁片組包含三個磁片，它們位於不同的 SCSI 控制器上。  
* ASM 冗余設置為 **"正常**冗余"。
* 在兩個 ASM 重做日誌組創建五個重做日誌檔

```
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
```

![Oracle RAC 重做日誌磁片組配置](media/oracle-vm-redo-log-disks.png)

### <a name="oracle-voting-disk-configuration"></a>Oracle 投票磁片配置

投票磁片提供仲裁磁碟功能作為額外的通信通道，以避免任何分裂的大腦情況。

* 在所有 Oracle RAC 虛擬機器上創建並裝載五個磁片。
* 磁片安裝在一個 SCSI 控制器上
* 每個虛擬磁片都配置為 **"厚備配"已歸零**。
* 共用設置為**多寫入器**。  
* 磁片必須配置為 ASM 磁片組。  
* ASM 冗余設置為**高**冗余。

![Oracle RAC 投票磁片組配置](media/oracle-vm-voting-disks.png)

### <a name="oracle-fast-recovery-area-disk-configuration-optional"></a>Oracle 快速恢復區域磁片配置（可選）

快速恢復區域 （FRA） 是由 Oracle ASM 磁片組管理的檔案系統。  FRA 為備份和恢復檔提供共用存儲位置。 Oracle 在快速恢復區域中創建存檔的日誌和閃回日誌。 Oracle 恢復管理器 （RMAN） 可以選擇將其備份組和映射副本存儲在快速恢復區域中，並在媒體恢復期間還原檔時使用它。

* 在所有 Oracle RAC 虛擬機器上創建並裝載兩個磁片。
* 磁片安裝在不同的 SCSI 控制器上
* 每個虛擬磁片都配置為 **"厚備配"已歸零**。
* 共用設置為**多寫入器**。  
* 磁片必須配置為 ASM 磁片組。  
* ASM 冗余設置為**外部**冗余。

![Oracle RAC 投票磁片組配置](media/oracle-vm-fra-disks.png)

## <a name="deploy-cloudsimple-private-cloud-vsphere-cluster"></a>部署雲簡單私有雲 vSphere 群集

要在私有雲上部署 vSphere 群集，請按照以下步驟操作：

1. 從雲簡單門戶創建[私有雲](create-private-cloud.md)。 CloudSimple 在新創建的私有雲中創建名為"雲擁有者"的預設 vCenter 使用者。 有關預設私有雲使用者和許可權模型的詳細資訊，請參閱[瞭解私有雲許可權模型](learn-private-cloud-permissions.md)。  此步驟為私有雲創建主管理群集。

2. 從雲簡單門戶，使用新群集[擴展私有雲](expand-private-cloud.md)。  此群集將用於部署 Oracle RAC。  根據所需的容錯（最少三個節點）選擇節點數。

## <a name="set-up-networking-for-oracle-rac"></a>為 Oracle RAC 設置網路

1. 在私有雲中，[創建兩個 VLAN，](create-vlan-subnet.md)一個用於 Oracle 公共網路，一個用於 Oracle 私人網路絡，並分配適當的子網 CIDR。
2. 創建 VLAN 後，[在私有雲 vCenter 上創建分散式埠組](create-vlan-subnet.md#use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere)。
3. 在 Oracle 環境的管理群集上設置[DHCP 和 DNS 伺服器虛擬機器](dns-dhcp-setup.md)。
4. 在私有雲中安裝[的 DNS 伺服器上配置 DNS 轉發](on-premises-dns-setup.md#create-a-conditional-forwarder)。

## <a name="set-up-vsan-storage-policies"></a>設置 vSAN 存儲策略

vSAN 策略定義 VM 磁片上存儲的資料的允許和磁片條帶化的故障。  創建存儲策略必須在創建 VM 時應用於 VM 磁片。

1. [登錄到私有雲的 vSphere 用戶端](https://docs.azure.cloudsimple.com/vsphere-access)。
2. 從頂部功能表中，選擇 **"策略和設定檔**"。
3. 從左側功能表中，選擇**VM 存儲策略**，然後選擇 **"創建 VM 存儲策略**"。
4. 輸入策略的有意義的名稱，然後按一下 **"下一步**"。
5. 在 **"策略結構**"部分中，選擇**啟用 vSAN 存儲的規則**，然後按一下 **"下一步**"。
6. 在**vSAN** > **可用性**部分中，為網站容差選擇 **"無**"。 對於無法容忍的故障，選擇所需 FTT 的**RAID - 鏡像**選項。
    ![vSAN](media/oracle-rac-storage-wizard-vsan.png)設置 。
7. 在 **"高級"** 部分中，選擇每個物件的磁片條帶數。 對於物件空間預留，選擇 **"粗配置**"。 選擇**禁用物件校驗和**。 按一下 **"下一步**"。
8. 按照螢幕上的說明查看相容的 vSAN 資料存儲的清單、查看設置並完成設置。

## <a name="create-oracle-vms-and-create-shared-vm-disks-for-oracle"></a>創建 Oracle VM 並為 Oracle 創建共用 VM 磁片

要為 Oracle 創建 VM，請克隆現有 VM 或創建新 VM。  本節介紹如何創建新 VM，然後在安裝基本作業系統後克隆它以創建第二個 VM。  創建 VM 後，可以創建向它們添加磁片。  Oracle 群集使用共用磁片來存儲、資料、日誌和重做日誌。

### <a name="create-vms"></a>建立 VM

1. 在 vCenter 中，按一下 **"主機和群集"** 圖示。 選擇為 Oracle 創建的群集。
2. 按右鍵群集並選擇 **"新虛擬機器**"。
3. 選擇 **"創建新虛擬機器**"，然後按一下"**下一步**"。
4. 命名電腦，選擇 Oracle VM 的位置，然後按一下 **"下一步**"。
5. 選擇群集資源，然後按一下 **"下一步**"。
6. 選擇群集的 vSAN 資料存儲，然後按一下 **"下一步**"。
7. 保留預設 ESXi 6.5 相容性選擇，然後按一下 **"下一步**"。
8. 為要創建的 VM 選擇 ISO 的客體作業系統，然後按一下"**下一步**"。
9. 選擇安裝作業系統所需的硬碟大小。
10. 要將應用程式安裝在其他設備上，請按一下"**添加新設備**"。
11. 選擇網路選項並分配為公共網路創建的分散式埠組。
12. 要添加其他網路介面，請按一下"**添加新設備**"並選擇為私人網路絡創建的分散式埠組。
13. 對於新 DC/DVD 光碟機，請選擇包含首選作業系統安裝的 ISO 的資料存儲 ISO 檔。 選擇您以前上載到 ISO 和範本資料夾的檔，然後按一下"**確定**"。
14. 查看設置並按一下 **"確定"** 以創建新的 VM。
15. 打開 VM 電源。 安裝作業系統和所需的任何更新

安裝作業系統後，可以克隆第二個 VM。 按右鍵 VM 條目並選擇 和 克隆選項。

### <a name="create-shared-disks-for-vms"></a>為 VM 創建共用磁片

Oracle 使用共用磁片來存儲資料、日誌和重做日誌檔。  您可以在 vCenter 上創建共用磁片，並將其安裝在兩個 VM 上。  為了獲得更高的性能，請將資料磁片放在不同的 SCSI 控制器上，下面將演示如何在 vCenter 上創建共用磁片，然後將其附加到虛擬機器。 vCenter 快閃記憶體用戶端用於修改 VM 屬性。

#### <a name="create-disks-on-the-first-vm"></a>在第一個 VM 上創建磁片

1. 在 vCenter 中，按右鍵其中一個 Oracle VM 並選擇 **"編輯設置**"。
2. 在新設備部分中，選擇**SCSI 控制器**，然後按一下"**添加**"。
3. 在新設備部分中，選擇 **"新建硬碟**"，然後按一下"**添加**"。
4. 展開新硬碟的屬性。
5. 指定硬碟的大小。
6. 指定 VM 存儲策略是您之前定義的 vSAN 存儲策略。
7. 選擇 vSAN 資料存儲上的位置作為資料夾。 該位置有助於流覽磁片並將磁片附加到第二個 VM。
8. 對於磁片預配，選擇 **"密集預配"，即求歸**零。
9. 要共用，請指定**多寫入器**。
10. 對於虛擬裝置節點，請選擇步驟 2 中創建的新 SCSI 控制器。

    ![在第一個 VM 上創建磁片](media/oracle-rac-new-hard-disk.png)

對於 Oracle 資料、日誌和重做日誌檔所需的所有新磁片，重複步驟 2 = 10。

#### <a name="attach-disks-to-second-vm"></a>將磁片連接到第二個 VM

1. 在 vCenter 中，按右鍵其中一個 Oracle VM 並選擇 **"編輯設置**"。
2. 在新設備部分中，選擇**SCSI 控制器**，然後按一下"**添加**"。
3. 在新設備部分中，選擇 **"現有硬碟"，** 然後按一下"**添加**"。
4. 流覽到為第一個 VM 創建磁片的位置，然後選擇 VMDK 檔。
5. 指定 VM 存儲策略是您之前定義的 vSAN 存儲策略。
6. 對於磁片預配，選擇 **"密集預配"，即求歸**零。
7. 要共用，請指定**多寫入器**。
8. 對於虛擬裝置節點，請選擇步驟 2 中創建的新 SCSI 控制器。

    ![在第一個 VM 上創建磁片](media/oracle-rac-existing-hard-disk.png)

對於 Oracle 資料、日誌和重做日誌檔所需的所有新磁片，重複步驟 2 + 7。

## <a name="set-up-vm-host-affinity-rules"></a>設置 VM 主機關聯規則

VM 到主機的關聯規則可確保 VM 在所需的主機上運行。  您可以在 vCenter 上定義規則，以確保 Oracle VM 在主機上運行，具有足夠的資源，並滿足任何特定的許可要求。

1. 在雲簡單門戶[中，上報](escalate-private-cloud-privileges.md)雲擁有者使用者的許可權。
2. [登錄到私有雲的 vSphere 用戶端](https://docs.azure.cloudsimple.com/vsphere-access)。
3. 在 vSphere 用戶端中，選擇部署 Oracle VM 的群集，然後按一下"**配置**"。
4. 在"配置"下，選擇**VM/主機組**。
5. 按一下**+**。
6. 添加 VM 組。 選擇**VM 組**作為類型。 輸入組的名稱。 選擇 VM，然後按一下 **"確定"** 以創建組。
6. 添加主機組。 選擇**主機組**作為類型。 輸入組的名稱。 選擇 VM 將在其中運行的主機，然後按一下 **"確定"** 以創建組。
7. 要創建規則，請按一下**VM/主機規則**。
8. 按一下**+**。
9. 輸入規則的名稱，並選中 **"啟用**"。
10. 對於規則類型，選擇 **"虛擬機器以承載**"。
11. 選擇包含 Oracle VM 的 VM 組。
12. 選擇**必須在此組中的主機上運行**。
13. 選擇您創建的主機組。
14. 按一下 **"確定"** 以創建規則。

## <a name="references"></a>參考

* [關於 vSAN 策略](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-08911FD3-2462-4C1C-AE81-0D4DBC8F7990.html)
* [用於共用 VMDK 的 VMware 多寫入器屬性](https://docs.vmware.com/en/VMware-Cloud-on-AWS/solutions/VMware-Cloud-on-AWS.df6735f8b729fee463802083d46fdc75/GUID-A7642A82B3D6C5F7806DB40A3F2766D9.html)
