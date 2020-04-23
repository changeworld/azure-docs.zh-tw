---
title: Azure VMware 解決方案(按雲端簡單 - Oracle RAC 最佳化雲端的簡易)
description: 描述如何部署新叢集並最佳化針對 Oracle 實際應用程式叢集 (RAC) 安裝和設定的 VM
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b945beaa7497e1ad19315bacf1284dd0cbc24d6a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868066"
---
# <a name="optimize-your-cloudsimple-private-cloud-for-installing-oracle-rac"></a>優化雲簡單私有雲,以安裝 Oracle RAC

您可以在雲端簡單私有雲端環境中部署 Oracle 實際應用程式群集 (RAC)。 本指南介紹如何部署新群集和最佳化 Oracle RAC 解決方案的 VM。 完成本主題中的步驟後,可以安裝和配置 Oracle RAC。

## <a name="storage-policy"></a>儲存原則

成功實施 Oracle RAC 需要群集中的足夠數量的節點。  在 vSAN 儲存原則中,容容失敗 (FTT) 應用於用於儲存資料庫、日誌和重做磁碟的數據磁碟。  有效容忍故障所需的節點數為 2N+1,其中 N 是 FTT 的值。

示例:如果所需的 FTT 為 2,則群集中的節點總數必須為 2*2+1 = 5。

## <a name="overview-of-deployment"></a>部署概觀

以下各節介紹如何為 Oracle RAC 設置雲端簡單私有雲端環境。

1. 磁碟設定的最佳做法
2. 部署雲簡單私有雲 vSphere 群集
3. 為 Oracle RAC 設定網路
4. 設定 vSAN 儲存原則
5. 建立 Oracle VM 並建立分享 VM 磁碟
6. 設定 VM 到主機的關聯規則

## <a name="best-practices-for-disk-configuration"></a>磁碟設定的最佳做法

Oracle RAC 虛擬機器有多個磁碟,用於特定功能。  共用磁碟安裝在 Oracle RAC 群集使用的所有虛擬機器上。  操作系統和軟體安裝磁碟僅安裝在單個虛擬機器上。  

![Oracle RAC 虛擬機器磁碟概述](media/oracle-vm-disks-overview.png)

下面的範例使用下表中定義的磁碟。

| 磁碟                                      | 目的                                       | 共用磁碟 |
|-------------------------------------------|-----------------------------------------------|-------------|
| OS                                        | 作業系統磁碟                         | 否          |
| 格                                      | Oracle 網格軟體的安裝位置     | 否          |
| DATABASE                                  | 安裝 Oracle 資料庫軟體的位置 | 否          |
| 奧拉霍馬                                   | Oracle 資料庫二進位檔案的基本位置    | 否          |
| 資料1, 資料2, 資料3, 資料4                | 儲存 Oracle 資料庫檔案的磁碟   | 是         |
| 重做 1、重做 2、重做 3、重做 4、重做 5、重做6  | 重做紀錄磁碟                                | 是         |
| OCR1, OCR2, OCR3, OCR4, OCR5              | 投票磁碟                                  | 是         |
| FRA1, FRA2                                | 快速復原區域磁碟                      | 是         |

![Oracle 虛擬機器磁碟設定](media/oracle-vmdk.png)

### <a name="virtual-machine-configuration"></a>虛擬機器組態

* 每個虛擬機都配置了四個 SCSI 控制器。
* SCSI 控制器類型設定為 VMware 半虛擬。
* 創建了多個虛擬磁碟 (.vmdk)。
* 磁碟安裝在不同的 SCSI 控制器上。
* 為共用群集磁碟設置了多寫入器共享類型。
* vSAN 儲存原則的定義是確保磁碟的高可用性。

### <a name="operating-system-and-software-disk-configuration"></a>作業系統與軟體磁碟設定

每個 Oracle 虛擬機器都配置了多個磁碟,用於主機作業系統、交換、軟體安裝和其他作業系統功能。  這些磁碟不會在虛擬機器之間共用。  

* 每個虛擬機的三個磁碟配置為虛擬磁碟並安裝在 Oracle RAC 虛擬機器上。
    * OS 磁碟
    * 儲存 Oracle 網格的磁碟安裝檔案
    * 儲存 Oracle 資料庫安裝檔案的磁碟
* 磁碟可以設定為**精簡預配**。
* 每個磁碟都安裝在第一個 SCSI 控制器 (SCSI0) 上。  
* 共用設置為 **「無共用**」。
* 冗餘在存儲上使用 vSAN 策略定義。  

![Oracle RAC 資料磁碟組設定](media/oracle-vm-os-disks.png)

### <a name="data-disk-configuration"></a>資料磁碟設定

數據磁碟主要用於存儲資料庫檔。  

* 四個磁碟配置為虛擬磁碟,並安裝在所有 Oracle RAC 虛擬機器上。
* 每個磁碟都安裝在不同的 SCSI 控制器上。
* 每個虛擬磁碟都設定為 **「厚備配」 已歸零**。  
* 共用設定為**多寫入器**。  
* 磁碟必須配置為自動儲存管理 (ASM) 磁碟組。  
* 冗餘在存儲上使用 vSAN 策略定義。  
* ASM 冗餘設置為**外部**冗餘。

![Oracle RAC 資料磁碟組設定](media/oracle-vm-data-disks.png)

### <a name="redo-log-disk-configuration"></a>重做紀錄磁碟設定

重做日誌檔用於儲存對資料庫所做的更改的複本。  當任何故障后需要恢復數據時,將使用日誌檔。

* 重做日誌磁碟必須配置為多個磁碟組。  
* 所有 Oracle RAC 虛擬機器上創建並裝載了六個磁碟。
* 磁碟安裝在不同的 SCSI 控制器上
* 每個虛擬磁碟都設定為 **「厚備配」 已歸零**。
* 共用設定為**多寫入器**。  
* 磁碟必須配置為兩個 ASM 磁碟組。
* 每個 ASM 磁碟組包含三個磁碟,它們位於不同的 SCSI 控制器上。  
* ASM 冗餘設置為 **「正常**冗餘」。
* 在兩個 ASM 重做紀錄組建立五個重做紀錄檔

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

![Oracle RAC 重做紀錄磁碟組設定](media/oracle-vm-redo-log-disks.png)

### <a name="oracle-voting-disk-configuration"></a>Oracle 投票磁碟設定

投票磁碟提供仲裁磁碟功能作為額外的通信通道,以避免任何分裂的大腦情況。

* 在所有 Oracle RAC 虛擬機器上創建並裝載五個磁碟。
* 磁碟安裝在 SCSI 控制器上
* 每個虛擬磁碟都設定為 **「厚備配」 已歸零**。
* 共用設定為**多寫入器**。  
* 磁碟必須配置為 ASM 磁碟組。  
* ASM 冗餘設置為**高**冗餘。

![Oracle RAC 投票磁碟組設定](media/oracle-vm-voting-disks.png)

### <a name="oracle-fast-recovery-area-disk-configuration-optional"></a>Oracle 快速復原區域磁碟設定 (可選)

快速復原區域 (FRA) 是由 Oracle ASM 磁碟組管理的檔案系統。  FRA 為備份和恢復檔提供共用存儲位置。 Oracle 在快速恢復區域中創建存檔的日誌和快閃回日誌。 Oracle 恢復管理員 (RMAN) 可以選擇將其備份集和映射複本儲存在快速復原區域中,並在媒體恢復期間還原檔時使用它。

* 在所有 Oracle RAC 虛擬機器上創建並裝載兩個磁碟。
* 磁碟安裝在不同的 SCSI 控制器上
* 每個虛擬磁碟都設定為 **「厚備配」 已歸零**。
* 共用設定為**多寫入器**。  
* 磁碟必須配置為 ASM 磁碟組。  
* ASM 冗餘設置為**外部**冗餘。

![Oracle RAC 投票磁碟組設定](media/oracle-vm-fra-disks.png)

## <a name="deploy-cloudsimple-private-cloud-vsphere-cluster"></a>部署雲簡單私有雲 vSphere 群集

要在私有雲上部署 vSphere 群集,請按照以下步驟操作:

1. 從雲端簡單門戶建立[私有雲](create-private-cloud.md)。 CloudSimple 在新創建的私有雲中創建名為"云所有者"的預設 vCenter 使用者。 有關預設私有雲使用者和許可權模型的詳細資訊,請參閱[瞭解私有雲端許可權模型](learn-private-cloud-permissions.md)。  此步驟為私有雲創建主管理群集。

2. 從雲簡單門戶,使用新群集[擴展私有雲](expand-private-cloud.md)。  此群集將用於部署 Oracle RAC。  根據所需的容錯(最少三個節點)選擇節點數。

## <a name="set-up-networking-for-oracle-rac"></a>為 Oracle RAC 設定網路

1. 在私有雲中,[創建兩個 VLAN,](create-vlan-subnet.md)一個用於 Oracle 公共網路,一個用於 Oracle 專用網路,並分配適當的子網路 CIDR。
2. 建立 VLAN 後,[在私有雲 vCenter 上建立分散式連接埠組](create-vlan-subnet.md#use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere)。
3. 在 Oracle 環境管理叢集上設定[DHCP 與 DNS 伺服器虛擬機器](dns-dhcp-setup.md)。
4. 在私有雲端中安裝[的 DNS 伺服器上設定 DNS 轉寄](on-premises-dns-setup.md#create-a-conditional-forwarder)。

## <a name="set-up-vsan-storage-policies"></a>設定 vSAN 儲存原則

vSAN 策略定義 VM 磁碟上儲存的數據的允許和磁碟條帶化的故障。  創建儲存策略必須在創建 VM 時應用於 VM 磁碟。

1. [登入到私有雲的 vSphere 用戶端](https://docs.microsoft.com/azure/vmware-cloudsimple/vcenter-access)。
2. 從頂部功能表中,選擇 **「策略和設定檔**」。
3. 從左側選單中,選擇**VM 儲存策略**,然後選擇 **「創建 VM 儲存政策**」 。。
4. 輸入策略的有意義的名稱,然後單擊 **「下一步**」。。
5. 在 **「策略結構**」部分中,選擇**啟用 vSAN 儲存的規則**,然後單擊 **「下一步**」。
6. 在**vSAN** > **可用性**部分中,為網站容差選擇 **「無**」。。 對於無法容忍的故障,選擇所需 FTT 的**RAID - 鏡像**選項。
    ![vSAN](media/oracle-rac-storage-wizard-vsan.png)設定 。
7. 在 **「高級」** 部分中,選擇每個物件的磁碟條帶數。 對於物件空間預留,選擇 **「粗配置**」。。 選擇**關閉物件檢查與**。 按下 **「下一步**」。
8. 按照螢幕上的說明查看相容的 vSAN 資料儲存的清單、查看設置並完成設置。

## <a name="create-oracle-vms-and-create-shared-vm-disks-for-oracle"></a>建立 Oracle VM 為 Oracle 建立分享 VM 磁碟

要為 Oracle 創建 VM,請複製現有 VM 或創建新 VM。  本節介紹如何創建新 VM,然後在安裝基本操作系統后克隆它以創建第二個 VM。  創建 VM 後,可以創建向它們添加磁碟。  Oracle 群集使用共用磁碟來存儲、數據、日誌和重做日誌。

### <a name="create-vms"></a>建立 VM

1. 在 vCenter 中,按下 **「主機和群集」** 圖示。 選擇為 Oracle 建立的叢集。
2. 右鍵按一下群集並選擇 **「新虛擬機器**」。
3. 選擇 **「創建新虛擬機**」,然後按**一下「 下一步**」 。
4. 命名電腦,選擇 Oracle VM 的位置,然後按下 **「下一步**」 。。
5. 選擇群集資源,然後單擊 **「下一步**」。。
6. 選擇群集的 vSAN 資料存儲,然後按下 **「下一步**」 。。
7. 保留預設 ESXi 6.5 相容性選擇,然後單擊 **「下一步**」 。。
8. 為要創建的 VM 選擇 ISO 的來賓作業系統,然後單擊「**下一步**」。
9. 選擇安裝作業系統所需的硬碟大小。
10. 要將應用程式安裝在其他設備上,請單擊「**添加新設備**」。
11. 選擇網路選項並分配為公共網路創建的分散式埠組。
12. 要添加其他網路介面,請按一下「**添加新裝置**」並選擇為專用網路創建的分散式埠組。
13. 對於新 DC/DVD 驅動程式,請選擇包含首選作業系統安裝的 ISO 的數據存儲 ISO 檔案。 選擇您以前上傳到 ISO 和樣本資料夾的檔案,然後按下「**確定**」。
14. 查看設定並按下 **「確定」** 以建立新的 VM。
15. 打開 VM 電源。 安裝作業系統與所需的更新

安裝作業系統後,可以克隆第二個 VM。 右鍵按一下 VM 條目並選擇和複製項選項。

### <a name="create-shared-disks-for-vms"></a>為 VM 建立分享磁碟

Oracle 使用共用磁碟來存儲數據、日誌和重做日誌檔。  您可以在 vCenter 上創建共用磁碟,並將其安裝在兩個 VM 上。  為了獲得更高的性能,請將數據磁碟放在不同的 SCSI 控制器上,下面將演示如何在 vCenter 上創建共用磁碟,然後將其附加到虛擬機器。 vCenter 快閃記憶體用戶端用於修改 VM 屬性。

#### <a name="create-disks-on-the-first-vm"></a>在 VM 建立磁碟

1. 在 vCenter 中,右鍵單擊其中一個 Oracle VM 並選擇 **「編輯設置**」。
2. 在新裝置部分中,選擇**SCSI控制器**,然後按下「**添加**」。。
3. 在新裝置部分中,選擇 **「新建硬碟**」,然後按一下「**添加**」。
4. 展開新硬碟的屬性。
5. 指定硬碟的大小。
6. 指定 VM 儲存原則是您之前定義的 vSAN 儲存原則。
7. 選擇 vSAN 資料儲存上的位置作為資料夾。 該位置有助於流覽磁碟並將磁碟附加到第二個 VM。
8. 對於磁碟預配,選擇 **「密集預配」,即求歸**零。
9. 要分享,請指定**多寫入器**。
10. 對於虛擬設備節點,請選擇步驟 2 中創建的新 SCSI 控制器。

    ![在 VM 建立磁碟](media/oracle-rac-new-hard-disk.png)

對於 Oracle 資料、日誌和重做日誌檔所需的所有新磁碟,重複步驟 2 = 10。

#### <a name="attach-disks-to-second-vm"></a>將磁碟連線到第二個 VM

1. 在 vCenter 中,右鍵單擊其中一個 Oracle VM 並選擇 **「編輯設置**」。
2. 在新裝置部分中,選擇**SCSI控制器**,然後按下「**添加**」。。
3. 在新裝置部分中,選擇 **「現有硬碟」,** 然後按下「**添加**」。
4. 瀏覽到第一個 VM 創建磁碟的位置,然後選擇 VMDK 檔案。
5. 指定 VM 儲存原則是您之前定義的 vSAN 儲存原則。
6. 對於磁碟預配,選擇 **「密集預配」,即求歸**零。
7. 要分享,請指定**多寫入器**。
8. 對於虛擬設備節點,請選擇步驟 2 中創建的新 SCSI 控制器。

    ![在 VM 建立磁碟](media/oracle-rac-existing-hard-disk.png)

對於 Oracle 資料、日誌和重做日誌檔所需的所有新磁碟,重複步驟 2 + 7。

## <a name="set-up-vm-host-affinity-rules"></a>設定 VM 主機關聯規則

VM 到主機的關聯規則可確保 VM 在所需的主機上運行。  您可以在 vCenter 上定義規則,以確保 Oracle VM 在主機上運行,具有足夠的資源,並滿足任何特定的許可要求。

1. 在雲簡單門戶[中,上報](escalate-private-cloud-privileges.md)雲所有者用戶的許可權。
2. [登入到私有雲的 vSphere 用戶端](https://docs.azure.cloudsimple.com/vsphere-access)。
3. 在 vSphere 用戶端中,選擇部署 Oracle VM 的群集,然後單擊「**配置**」 。
4. 在「設定」下,選擇**VM/主機組**。
5. 按一**+** 下 。
6. 添加 VM 組。 選擇**VM 組**作為類型。 輸入組的名稱。 選擇 VM,然後單擊 **「 確定」** 以建立群組。
6. 添加主機組。 選擇**主機組**作為類型。 輸入組的名稱。 選擇 VM 將在其中運行的主機,然後單擊 **「確定」** 以創建組。
7. 要建立規則,請按**下 VM/主機規則**。
8. 按一**+** 下 。
9. 輸入規則的名稱,並選中 **「啟用**」。。
10. 對於規則類型,選擇 **「虛擬機器以承載**」。
11. 選擇包含 Oracle VM 的 VM 組。
12. 選擇**必須在此群組中的主機上執行**。
13. 選擇您創建的主機組。
14. 按下 **「確定」** 以建立規則。

## <a name="references"></a>參考

* [關於 vSAN 政策](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-08911FD3-2462-4C1C-AE81-0D4DBC8F7990.html)
* [分享 VMDK 的 VMware 多寫入器屬性](https://docs.vmware.com/en/VMware-Cloud-on-AWS/solutions/VMware-Cloud-on-AWS.df6735f8b729fee463802083d46fdc75/GUID-A7642A82B3D6C5F7806DB40A3F2766D9.html)
