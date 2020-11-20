---
title: 在 Azure 上執行 SAP Hana (大型執行個體) 的作業系統升級 | Microsoft Docs
description: 在 Azure 上執行 SAP Hana on Azure (大型執行個體) 的作業系統升級
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7c1b09cdcf987c7ed0e489a8a67f77a4099e39a9
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955458"
---
# <a name="operating-system-upgrade"></a>作業系統升級
本文件會詳述 Hana 大型執行個體作業系統升級的詳細資訊。

>[!NOTE]
>作業系統升級是客戶的責任，Microsoft 作業支援可以指引您在升級期間要注意的重要區域。 規劃升級之前，請先諮詢您的作業系統廠商。

在快速布建的過程中，Microsoft 作業小組會安裝作業系統。
隨時間過去，會需要維護 HLI 單元上的作業系統 (例如：修補、調整、升級等)。

在您對作業系統進行重大變更之前 (例如，將 SP1 升級至 SP2) 之前，您應該先開啟支援票證以洽詢 Microsoft Operations 團隊。

包含在您的票證中：

* 您的 HLI 訂用帳戶識別碼。
* 您的伺服器名稱。
* 您打算套用的修補等級。
* 您打算進行變更的日期。 

建議您在需要的升級前至少一周開啟此票證，讓作業小組知道所需的固件版本。

如需具有不同 Linux 版本之 SAP HANA 版本的支援矩陣，請參閱 [SAP 附註 #2235581](https://launchpad.support.sap.com/#/notes/2235581)。

## <a name="known-issues"></a>已知問題

以下是升級時一些已知的常見問題：
- II 型 SKU 類別的 SKU 進行作業系統升級之後，會移除 Software Foundation 軟體 (SFS)。 您必須在作業系統升級之後重新安裝相容的 SFS。
- 乙太網路卡驅動程式 (ENIC 和 FNIC) 會復原至舊版本。 升級之後，您必須重新安裝相容的驅動程式版本。

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>SAP Hana 大型實例 (類型) 建議的設定

作業系統設定可能會因為修補、系統升級以及客戶所做的變更，而與建議的設定漂移。 此外，Microsoft 還會找出現有系統所需的更新，以確保它們獲得最佳效能和復原能力的最佳設定。 下列指示概述可解決網路效能、系統穩定性和最佳 HANA 效能的建議。

### <a name="compatible-enicfnic-driver-versions"></a>相容的 eNIC/fNIC 驅動程式版本
  為了具有適當的網路效能和系統穩定性，建議您確定已安裝作業系統特定的適當 eNIC 和 fNIC 驅動程式版本，如下列相容性表格所示。 伺服器會傳遞給具有相容版本的客戶。 在某些情況下，在作業系統/核心修補期間，可以將驅動程式回復為預設的驅動程式版本。 確定在作業系統/核心修補作業之後，有適當的驅動程式版本正在執行。
       
      
  |  作業系統廠商    |  作業系統套件版本     |  韌體版本  |  eNIC 驅動程式 |  fNIC 驅動程式 | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   SuSE        |  SLES 12 SP2            |   3.1.3 h           |  2.3.0.40    |   1.6.0.34   |
  |   SuSE        |  SLES 12 SP3            |   3.1.3 h           |  2.3.0.44    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP4            |   3.2.3 i           |  4.0.0.6     |   2.0.0.60   |
  |   SuSE        |  SLES 12 SP2            |   3.2.3 i           |  2.3.0.45    |   1.6.0.37   |
  |   SuSE        |  SLES 12 SP3            |   3.2.3 i           |  2.3.0.43    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP5            |   3.2.3 i           |  4.0.0.8     |   2.0.0.60   |
  |   Red Hat     |  RHEL 7.2               |   3.1.3 h           |  2.3.0.39    |   1.6.0.34   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>驅動程式升級和清除舊 rpm 套件的命令

#### <a name="command-to-check-existing-installed-drivers"></a>用來檢查現有已安裝驅動程式的命令
```
rpm -qa | grep enic/fnic 
```
#### <a name="delete-existing-enicfnic-rpm"></a>刪除現有的 eNIC/fNIC rpm
```
rpm -e <old-rpm-package>
```
#### <a name="install-the-recommended-enicfnic-driver-packages"></a>安裝建議的 eNIC/fNIC 驅動程式套件
```
rpm -ivh <enic/fnic.rpm> 
```

#### <a name="commands-to-confirm-the-installation"></a>確認安裝的命令
```
modinfo enic
modinfo fnic
```

#### <a name="steps-for-enicfnic-drivers-installation-during-os-upgrade"></a>OS 升級期間的 eNIC/fNIC 驅動程式安裝步驟

* 升級作業系統版本
* 移除舊的 rpm 套件
* 根據已安裝的 OS 版本安裝相容的 eNIC/fNIC 驅動程式
* 重新開機系統
* 重新開機之後，請檢查 eNIC/fNIC 版本


### <a name="suse-hlis-grub-update-failure"></a>SuSE Hli GRUB 更新失敗
Azure 上的 SAP HANA 大型實例 (類型 I) 在升級之後可能處於非可開機狀態。 下列程式會修正此問題。
#### <a name="execution-steps"></a>執行步驟


*   執行 `multipath -ll` 命令。
*   取得其大小大約是50G 或使用命令的 LUN 識別碼： `fdisk -l | grep mapper`
*   `/etc/default/grub_installdevice`以 line 更新檔案 `/dev/mapper/<LUN ID>` 。 範例：/dev/mapper/3600a09803830372f483f495242534a56
>[!NOTE]
>LUN 識別碼與伺服器不同。


### <a name="disable-edac"></a>停用 EDAC 
   錯誤偵測和更正 (EDAC) 模組有助於偵測和修正記憶體錯誤。 不過，Azure 上的 SAP HANA 大型執行個體 (類型) 的基礎硬體已在執行相同的功能。 在硬體和作業系統 (作業系統) 層級啟用相同的功能可能會造成衝突，而且可能會導致伺服器偶爾發生未計畫的關機。 因此，建議您停用作業系統的模組。

#### <a name="execution-steps"></a>執行步驟

* 檢查是否已啟用 EDAC 模組。 如果在下列命令中傳回輸出，表示已啟用模組。 
```
lsmod | grep -i edac 
```
* 將下列幾行附加至檔案，以停用模組 `/etc/modprobe.d/blacklist.conf`
```
blacklist sb_edac
blacklist edac_core
```
需要重新開機，才能進行變更。 執行 `lsmod` 命令，並在輸出中確認模組不存在。

### <a name="kernel-parameters"></a>核心參數
   請確定 `transparent_hugepage` 已套用、、和的正確設定 `numa_balancing` `processor.max_cstate` `ignore_ce` `intel_idle.max_cstate` 。

* intel_idle intel_idle.max_cstate = 1
* processor.max_cstate = 1
* transparent_hugepage = 永不
* numa_balancing = 停用
* mce = ignore_ce

#### <a name="execution-steps"></a>執行步驟

* 將這些參數新增至檔案 `GRB_CMDLINE_LINUX` 中的行 `/etc/default/grub`
```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
* 建立新的 grub 檔。
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
* 重新開機系統。


## <a name="next-steps"></a>後續步驟
- 請參閱類型 I 的 SKU 類別作業系統的[備份和還原](hana-overview-high-availability-disaster-recovery.md)。
- 請參閱類型 II SKU 類別 [的修訂3的類型 Ii Sku 類型的 OS 備份](os-backup-type-ii-skus.md) 。
