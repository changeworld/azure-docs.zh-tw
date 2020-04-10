---
title: 在 Azure 上執行 SAP Hana (大型執行個體) 的作業系統升級 | Microsoft Docs
description: 在 Azure 上執行 SAP Hana on Azure (大型執行個體) 的作業系統升級
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7fea0f74a90bc7b786a9b302d6282f9fb70e5412
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991478"
---
# <a name="operating-system-upgrade"></a>作業系統升級
本文件會詳述 Hana 大型執行個體作業系統升級的詳細資訊。

>[!NOTE]
>操作系統升級是客戶的責任,Microsoft 運營支援可以引導您到升級期間需要注意的關鍵領域。 規劃升級之前，請先諮詢您的作業系統廠商。

在 HLI 單元預配期間,Microsoft 操作團隊將安裝作業系統。
隨時間過去，會需要維護 HLI 單元上的作業系統 (例如：修補、調整、升級等)。

在對作業系統進行重大更改(例如,將 SP1 升級到 SP2)之前,您需要通過打開支援票證來諮詢 Microsoft 運營團隊。

包括您的機票:

* 您的 HLI 訂用帳戶識別碼。
* 您的伺服器名稱。
* 您打算套用的修補等級。
* 您打算進行變更的日期。 

建議您在合適的升級日期之前，開啟此票證至少一周，因為作業小組會檢查您的伺服器刀鋒視窗上是否需要進行韌體升級。


如需具有不同 Linux 版本之 SAP HANA 版本的支援矩陣，請參閱 [SAP 附註 #2235581](https://launchpad.support.sap.com/#/notes/2235581)。


## <a name="known-issues"></a>已知問題

以下是升級時一些已知的常見問題：
- II 型 SKU 類別的 SKU 進行作業系統升級之後，會移除 Software Foundation 軟體 (SFS)。 在作業系統升級後,您需要重新安裝相容的 SFS。
- 乙太網路卡驅動程式 (ENIC 和 FNIC) 會復原至舊版本。 升級後,您需要重新安裝驅動程式的相容版本。

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>SAP HANA 大型實體(I 型)推薦配置

由於修補、系統升級和客戶所做的更改,操作系統配置可能會隨著時間的推移而偏離建議的設置。 此外,Microsoft 還會識別現有系統所需的更新,以確保它們以最佳配置,以實現最佳性能和彈性。 按照說明概述了解決網路性能、系統穩定性和最佳 HANA 性能的建議。

### <a name="compatible-enicfnic-driver-versions"></a>相容 eNIC/fNIC 驅動程式版本
  為了具有適當的網路性能和系統穩定性,建議確保安裝特定於作業系統的 eNIC 和 fNIC 驅動程式的適當版本,如下相容性表所示。 伺服器以相容版本交付給客戶。 請注意,在某些情況下,在 OS/Kernel 修補期間,驅動程式可以回滾到預設驅動程式版本。 確保適當的驅動程式版本在運行後 OS/內核修補操作。
       
      
  |  作業系統供應商    |  作業系統套件版本     |  韌體版本  |  eNIC 驅動程式 |  fNIC 驅動程式 | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   Suse        |  SLES 12 SP2            |   3.1.3 小時           |  2.3.0.40    |   1.6.0.34   |
  |   Suse        |  SLES 12 SP3            |   3.1.3 小時           |  2.3.0.44    |   1.6.0.36   |
  |   Suse        |  SLES 12 SP4            |   3.2.3b           |  2.3.0.47    |   2.0.0.54   |
  |   Red Hat     |  RHEL 7.2               |   3.1.3 小時           |  2.3.0.39    |   1.6.0.34   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>驅動程式升級和清潔舊 rpm 套件的指令
```
rpm -U driverpackage.rpm
rpm -e olddriverpackage.rpm
```

#### <a name="commands-to-confirm"></a>要確認的指令
```
modinfo enic
modinfo fnic
```

### <a name="suse-hlis-grub-update-failure"></a>SuSE HLIS GRUB 更新失敗
升級後,Azure HANA 大型實例(類型 I)上的 SAP 可能處於不可啟動狀態。 下面的過程修復了此問題。
#### <a name="execution-steps"></a>執行步驟


*   執行`multipath -ll`命令。
*   取得其大小約為 50G 的 LUN ID 或使用以下指令:`fdisk -l | grep mapper`
*   使用`/etc/default/grub_installdevice`列`/dev/mapper/<LUN ID>`更新檔案 。 範例: /dev/映射器/3600a09803830372f483f495242534a56
>[!NOTE]
>LUN ID 因伺服器而異。


### <a name="disable-edac"></a>關閉 EDAC 
   錯誤檢測和更正 (EDAC) 模組有助於檢測和更正記憶體錯誤。 但是,Azure 大型實例(類型 I)上的 SAP HANA 的基礎硬體已經執行了相同的功能。 在硬體和作業系統 (OS) 級別啟用相同的功能可能會導致衝突,並可能導致伺服器偶爾意外、計畫外停機。 因此,建議從操作系統禁用模組。

#### <a name="execution-steps"></a>執行步驟

* 檢查是否啟用了 EDAC 模組。 如果以以下命令返回輸出,則表示模組已啟用。 
```
lsmod | grep -i edac 
```
* 通過將以下行附加到檔案中來禁用模組`/etc/modprobe.d/blacklist.conf`
```
blacklist sb_edac
blacklist edac_core
```
需要重新啟動才能進行更改。 執行`lsmod`命令並驗證輸出中不存在模組。


### <a name="kernel-parameters"></a>核心參數
   請確保`transparent_hugepage`應用了、`numa_balancing``processor.max_cstate``ignore_ce``intel_idle.max_cstate`和的正確設置。

* intel_idle.max_cstate=1
* 處理器.max_cstate=1
* transparent_hugepage=從不
* numa_balancing=禁用
* mce_ignore_ce


#### <a name="execution-steps"></a>執行步驟

* 將這些參數加入檔案中的`GRB_CMDLINE_LINUX`列`/etc/default/grub`
```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
* 建立新的 grub 檔。
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
* 重新啟動系統。


## <a name="next-steps"></a>後續步驟
- 請參閱類型 I 的 SKU 類別作業系統的[備份和還原](hana-overview-high-availability-disaster-recovery.md)。
- [有關第 II 類修訂版 3 戳的 II 類 SKU,請參閱作業系統備份](os-backup-type-ii-skus.md)。
