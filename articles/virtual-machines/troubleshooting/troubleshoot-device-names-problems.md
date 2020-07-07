---
title: 在 Azure 中針對 Linux VM 裝置名稱變更進行疑難排解 | Microsoft Docs
description: 說明 Linux VM 裝置名稱變更的原因，以及解決問題的方法。
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 7d8a7e7e88837214042fb8f1c109c0b93bfe771b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "71058212"
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>針對 Linux VM 裝置名稱變更進行疑難排解

本文章說明在您重新啟動 Linux VM 或重新連接資料磁碟之後，裝置名稱會變更的原因。 本文章也提供此問題的解決方案。

## <a name="symptoms"></a>徵狀
在 Microsoft Azure 中執行 Linux VM 時，您可能會遇到下列問題：

- VM 在重新啟動之後無法開機。
- 中斷連結並重新連接資料磁碟時，即會變更磁碟的裝置名稱。
- 因為裝置名稱已經變更，所以使用裝置名稱參考磁碟的應用程式或指令碼就會失敗。

## <a name="cause"></a>原因

Linux 中的裝置路徑不保證會在重新啟動之間保持一致。 裝置名稱是由主要號碼 (字母) 和次要號碼所組成。 當 Linux 儲存裝置驅動程式偵測到新的裝置時，該驅動程式會從裝置的可用範圍中指派主要和次要號碼。 移除裝置時，即會釋放裝置號碼，以供重複使用。

問題發生的原因是因為 Linux 中由 SCSI 子系統所排定的裝置掃描是以非同步方式執行。 因此，裝置路徑名稱可能會在重新啟動之間有所不同。

## <a name="solution"></a>解決方案

若要解決這個問題，請使用永續性命名。 有四種方式可使用永續性命名：依檔案系統標籤、依 UUID、依識別碼，或依路徑。 我們建議針對 Azure Linux VM 使用檔案系統標籤或 UUID。

大部分的散發套件都會提供 `fstab` **nofail** 或 **nobootwait** 參數。 即使磁碟在啟動時無法掛接，這些參數也能讓系統開機。 請檢查散發版本的文件，以取得這些參數的相關資訊。 如需如何在您新增資料磁碟時設定 Linux VM 以使用 UUID 的詳細資訊，請參閱[連接到 Linux VM 以掛接新磁碟](../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk)。

在 VM 上安裝 Azure Linux 代理程式時，代理程式會使用 Udev 規則，在 /dev/disk/azure 路徑下方建構一組符號連結。 應用程式與指令碼會使用 Udev 規則，來識別連接至 VM 的磁碟，以及磁碟類型與磁碟 LUN。

如果您已編輯 fstab，使 VM 無法開機，而且您無法透過 SSH 連線到您的 VM，您可以使用[VM 序列主控台](./serial-console-linux.md)來進入[單一使用者模式](./serial-console-grub-single-user-mode.md)，並修改您的 fstab。

### <a name="identify-disk-luns"></a>識別磁碟 LUN

應用程式會使用 LUN，來尋找所有連接的磁碟並建構符號連結。 Azure Linux 代理程式包含 Udev 規則，可將符號連結從 LUN 設定到裝置：

    $ tree /dev/disk/azure

    /dev/disk/azure
    ├── resource -> ../../sdb
    ├── resource-part1 -> ../../sdb1
    ├── root -> ../../sda
    ├── root-part1 -> ../../sda1
    └── scsi1
        ├── lun0 -> ../../../sdc
        ├── lun0-part1 -> ../../../sdc1
        ├── lun1 -> ../../../sdd
        ├── lun1-part1 -> ../../../sdd1
        ├── lun1-part2 -> ../../../sdd2
        └── lun1-part3 -> ../../../sdd3

使用 `lsscsi` 或類似工具從 Linux 來賓帳戶擷取 LUN 資訊：

      $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

來賓 LUN 資訊會搭配 Azure 訂用帳戶中繼資料使用，以在包含分割區資料的 Azure 儲存體中找出 VHD。 例如，您可以使用 `az` CLI：

    $ az vm show --resource-group testVM --name testVM | jq -r .storageProfile.dataDisks
    [
    {
    "caching": "None",
      "createOption": "empty",
    "diskSizeGb": 1023,
      "image": null,
    "lun": 0,
    "managedDisk": null,
    "name": "testVM-20170619-114353",
    "vhd": {
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-114353.vhd"
    }
    },
    {
    "caching": "None",
    "createOption": "empty",
    "diskSizeGb": 512,
    "image": null,
    "lun": 1,
    "managedDisk": null,
    "name": "testVM-20170619-121516",
    "vhd": {
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-121516.vhd"
      }
      }
    ]

### <a name="discover-filesystem-uuids-by-using-blkid"></a>使用 blkid 探索 filesystem UUID

應用程式與指令碼會讀取 `blkid` 或類似資訊來源的輸出，以在 /dev 路徑中建構符號連結。 輸出會顯示連接到 VM 之所有磁碟的 UUID，以及其相關聯的裝置檔案：

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

Azure Linux 代理程式 Udev 規則會在 /dev/disk/azure 路徑下方建構一組符號連結：

    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1

應用程式會使用該連結來識別開機磁碟裝置和資源 (暫時) 磁碟。 在 Azure 中，應用程式應查看 /dev/disk/azure/root-part1 或 /dev/disk/azure-resource-part1 路徑，以探索這些分割區。

任何來自 `blkid` 清單的其他分割區都會位於資料磁碟上。 應用程式會維護這些分割區的 UUID，並使用路徑在執行階段探索該裝置名稱：

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1


### <a name="get-the-latest-azure-storage-rules"></a>取得最新的 Azure 儲存體規則

若要取得最新的 Azure 儲存體規則，請執行下列命令：

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block

## <a name="see-also"></a>請參閱

如需詳細資訊，請參閱下列文章：

- [Ubuntu：使用 UUID](https://help.ubuntu.com/community/UsingUUID) \(英文\)
- [Red Hat：持續性命名](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)
- [Linux：UUID 可為您做些什麼](https://www.linux.com/news/what-uuids-can-do-you) \(英文\)
- [Udev：現代 Linux 系統中的裝置管理簡介](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system) \(英文\)

