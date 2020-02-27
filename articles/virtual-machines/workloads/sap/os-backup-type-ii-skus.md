---
title: SAP HANA on Azure (大型執行個體) 類型 II SKU 的作業系統備份和還原 | Microsoft Docs
description: 針對 Azure （大型實例）類型 II Sku 上的 SAP Hana 執行作業系統備份和還原
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 100e1b974e54d8c0065194bc7beb18f458011434
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616874"
---
# <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>修訂版3戳記之類型 II Sku 的 OS 備份和還原

本檔說明針對修訂版3的「HANA 大型實例」**類型 II sku**執行作業系統檔案層級備份和還原的步驟。 

>[!Important]
> **本文不適用於修訂版 4 HANA 大型實例戳記中的類型 II SKU 部署。** 類型 II HANA 大型實例單位的開機 LUN，部署于修訂版 4 HANA 大型實例戳記中。您可以使用儲存體快照集來備份，這是類型 I Sku 已經在修訂3戳記中的情況。


>[!NOTE]
>作業系統備份指令碼使用已預先安裝在伺服器中的 ReaR 軟體。  

Microsoft `Service Management` 小組完成布建之後，根據預設，伺服器會設定兩個備份排程來備份作業系統的檔案系統層級。 您可以使用下列命令來檢查備份作業的排程：
```
#crontab –l
```
您可以使用下列命令隨時變更備份排程：
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>如何進行手動備份？

OS 檔案系統備份已使用**cron 作業**進行排程。 不過，您也可以手動執行作業系統檔案等級備份。 若要執行手動備份，請執行下列命令：

```
#rear -v mkbackup
```
下列畫面顯示顯示範例手動備份：

![方式](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>如何還原備份？

您可以從備份還原完整的備份或個別的檔案。 若要還原，請使用下列命令：

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
還原之後，目前工作目錄中的檔案就會復原。

下列命令顯示從備份檔案 *backup.tar.gz* 還原 */etc/fstab* 檔案
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>從備份還原檔案之後，您需要將檔案複製到想要的位置。

下列螢幕擷取畫面顯示還原完整備份：

![HowtoRestoreaBackup.PNG](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>如何安裝 ReaR 工具並變更設定？ 

Relax-and-Recover (ReaR) 封裝已**預先安裝**在 HANA 大型執行個體的**類型 II SKU** 中，您不需要採取任何動作。 您可以直接開始使用 ReaR 進行作業系統備份。
不過，如果您需要自己安裝封裝，可以按照列出的步驟安裝及設定 ReaR 工具。

若要安裝 **ReaR** 備份封裝，請使用下列命令︰

如果是 **SLES** 作業系統，請使用下列命令：
```
#zypper install <rear rpm package>
```
如果是 **RHEL** 作業系統，請使用下列命令： 
```
#yum install rear -y
```
若要設定 ReaR 工具，您需要在檔案 **/etc/rear/local.conf** 中更新 **OUTPUT_URL** 和 *BACKUP_URL* 參數。
```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

下列螢幕擷取畫面顯示還原完整備份： ![RearToolConfiguration .PNG](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
