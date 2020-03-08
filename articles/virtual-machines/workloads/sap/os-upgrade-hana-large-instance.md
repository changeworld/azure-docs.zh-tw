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
ms.openlocfilehash: 3a0a5d39a7cb2162186291ea534a623ef45c40d4
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78675619"
---
# <a name="operating-system-upgrade"></a>作業系統升級
本文件會詳述 Hana 大型執行個體作業系統升級的詳細資訊。

>[!NOTE]
>作業系統升級是客戶的責任，Microsoft 作業支援可以引導您瞭解升級期間要監看的重要區域。 規劃升級之前，請先諮詢您的作業系統廠商。

在配置單位布建期間，Microsoft 營運小組會安裝作業系統。
隨時間過去，會需要維護 HLI 單元上的作業系統 (例如：修補、調整、升級等)。

在對作業系統進行重大變更（例如，將 SP1 升級至 SP2）之前，您必須先開啟支援票證來諮詢 Microsoft 營運小組，以取得相關資訊。

包含在您的票證中：

* 您的 HLI 訂用帳戶識別碼。
* 您的伺服器名稱。
* 您打算套用的修補等級。
* 您打算進行變更的日期。 

建議您在合適的升級日期之前，開啟此票證至少一周，因為作業小組會檢查您的伺服器刀鋒視窗上是否需要進行韌體升級。


如需具有不同 Linux 版本之 SAP HANA 版本的支援矩陣，請參閱 [SAP 附註 #2235581](https://launchpad.support.sap.com/#/notes/2235581)。


## <a name="known-issues"></a>已知問題

以下是升級時一些已知的常見問題：
- II 型 SKU 類別的 SKU 進行作業系統升級之後，會移除 Software Foundation 軟體 (SFS)。 在作業系統升級之後，您必須重新安裝相容的 SFS。
- 乙太網路卡驅動程式 (ENIC 和 FNIC) 會復原至舊版本。 升級之後，您必須重新安裝相容的驅動程式版本。

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>SAP Hana 大型實例（類型 I）建議的設定

作業系統設定可能會因為修補、系統升級及客戶所做的變更，而偏離建議的設定。 此外，Microsoft 會識別現有系統所需的更新，以確保它們已針對最佳效能和復原進行優化設定。 下列指示概述解決網路效能、系統穩定性和最佳 HANA 效能的建議。

### <a name="compatible-enicfnic-driver-versions"></a>相容的 eNIC/fNIC 驅動程式版本
  為了擁有適當的網路效能和系統穩定性，建議您確定已依照下列相容性表格中的說明，安裝作業系統特定的適當版本的 eNIC 和 fNIC 驅動程式。 伺服器會傳遞給具有相容版本的客戶。 請注意，在某些情況下，OS/核心修補期間，可以將驅動程式回復為預設的驅動程式版本。 請確定適當的驅動程式版本正在執行 post OS/核心修補作業。
       
      
  |  作業系統廠商    |  OS 套件版本     |  韌體版本  |  eNIC 驅動程式 |  fNIC 驅動程式 | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   SuSE        |  SLES 12 SP2            |   3.1.3 h           |  2.3.0.40    |   1.6.0.34   |
  |   SuSE        |  SLES 12 SP3            |   3.1.3 h           |  2.3.0.44    |   1.6.0.36   |
  |   Red Hat     |  RHEL 7.2               |   3.1.3 h           |  2.3.0.39    |   1.6.0.34   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>驅動程式升級和清除舊的 rpm 套件的命令
```
rpm -U driverpackage.rpm
rpm -e olddriverpackage.rpm
```

#### <a name="commands-to-confirm"></a>要確認的命令
```
modinfo enic
modinfo fnic
```

### <a name="suse-hlis-grub-update-failure"></a>SuSE Hli GRUB 更新失敗
升級之後，Azure 上的 SAP HANA 大型實例（類型 I）可能處於無法啟動的狀態。 下列程式會修正此問題。
#### <a name="execution-steps"></a>執行步驟


*   執行 `multipath -ll` 命令。
*   取得其大小大約為50G 的 LUN 識別碼，或使用命令： `fdisk -l | grep mapper`
*   以行 `/dev/mapper/<LUN ID>`更新 `/etc/default/grub_installdevice` 檔案。 範例：/dev/mapper/3600a09803830372f483f495242534a56
>[!NOTE]
>LUN 識別碼會因伺服器而異。


### <a name="disable-edac"></a>停用 EDAC 
   錯誤偵測和修正（EDAC）模組有助於偵測及修正記憶體錯誤。 不過，Azure 上的 SAP HANA 大型執行個體的基礎硬體（類型 I）已在執行相同的功能。 在硬體和作業系統（OS）層級啟用相同的功能可能會造成衝突，而且可能會導致伺服器偶而非計畫的關機。 因此，建議您從 OS 停用模組。

#### <a name="execution-steps"></a>執行步驟

* 檢查是否已啟用 EDAC 模組。 如果在下列命令中傳回輸出，即表示模組已啟用。 
```
lsmod | grep -i edac 
```
* 將下列幾行附加至檔案，以停用模組 `/etc/modprobe.d/blacklist.conf`
```
blacklist sb_edac
blacklist edac_core
```
需要重新開機才能進行變更。 執行 `lsmod` 命令，並確認此模組不會出現在輸出中。


### <a name="kernel-parameters"></a>核心參數
   請確定已套用 `transparent_hugepage`、`numa_balancing`、`processor.max_cstate`、`ignore_ce` 和 `intel_idle.max_cstate` 的正確設定。

* intel_idle。 max_cstate = 1
* 處理器。 max_cstate = 1
* transparent_hugepage = 永不
* numa_balancing = 停用
* mce = ignore_ce


#### <a name="execution-steps"></a>執行步驟

* 將這些參數新增至檔案中的 `GRB_CMDLINE_LINUX` 行 `/etc/default/grub`
```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
* 建立新的 grub 檔案。
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
* 重新開機系統。


## <a name="next-steps"></a>後續步驟
- 請參閱類型 I 的 SKU 類別作業系統的[備份和還原](hana-overview-high-availability-disaster-recovery.md)。
- 請參閱類型 ii SKU 類別[之修訂版3戳記的類型 Ii sku 的 OS 備份](os-backup-type-ii-skus.md)。
