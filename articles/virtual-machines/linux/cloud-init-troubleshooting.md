---
title: 使用雲端初始化進行疑難排解
description: 針對使用雲端初始化的 Azure VM 布建進行疑難排解。
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 2bf0443465f0cfd98f8bce93e60f9007ac7503be
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86042064"
---
# <a name="troubleshooting-vm-provisioning-with-cloud-init"></a>使用雲端初始化進行 VM 布建的疑難排解

如果您已建立一般化的自訂映射，使用雲端 init 來進行布建，但發現 VM 未正確建立，您將需要針對自訂映射進行疑難排解。

布建問題的一些範例：
- VM 會停滯在「建立中」40分鐘，且 VM 建立會標示為失敗
- CustomData 不會被處理
- 暫時磁片無法掛接
- 使用者未建立，或發生使用者存取問題
- 未正確設定網路功能
- 交換檔案或分割區失敗

這篇文章會逐步引導您進行雲端 init 的疑難排解。 如需更深入的詳細資料，請參閱[雲端初始化深入探討](https://docs.microsoft.com/azure/virtual-machines/linux/cloud-init-deep-dive)。

## <a name="step-1-test-the-deployment-without-customdata"></a>步驟1：在不 customData 的情況下測試部署

建立 VM 時，Cloud init 可以接受傳遞給它的 customData。 首先，您應該確保這不會導致部署發生問題。 嘗試布建 VM，而不傳入任何設定。 如果您發現 VM 無法布建，請繼續進行下列步驟，如果您發現未套用您所傳遞的設定，請移至[步驟 4]()。 

## <a name="step-2-review-image-requirements"></a>步驟2：審查影像需求
VM 布建失敗的主要原因是 OS 映射無法滿足在 Azure 上執行的必要條件。 請先確定您的映射已正確備妥，再嘗試在 Azure 中布建它們。 


下列文章說明準備 Azure 支援的各種 linux 散發版本的步驟：

- [CentOS 型發行版本](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SLES 和 openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [其他：非背書的發行版本](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

針對[支援的 Azure 雲端 init 映射](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init)，Linux 散發套件已具備所有必要的封裝和設定，可在 Azure 中正確布建映射。 如果您發現您的 VM 無法從自己的策劃映射建立，請嘗試使用您的選擇性 customData 為雲端 init 設定的支援 Azure Marketplace 映射。 如果 customData 與 Azure Marketplace 映射搭配運作正常，則策劃映射可能會有問題。

## <a name="step-3-collect--review-vm-logs"></a>步驟3：收集 & 審查 VM 記錄

當 VM 無法布建時，Azure 會顯示「建立中」狀態（20分鐘），然後重新開機 VM，並在最後將 VM 部署標示為失敗之前等候另20分鐘，最後再將其標示為 `OSProvisioningTimedOut` 錯誤。

當 VM 正在執行時，您將需要來自 VM 的記錄，以瞭解布建失敗的原因。  若要瞭解為何 VM 布建失敗，請勿停止 VM。 讓 VM 保持執行狀態。 您必須將失敗的 VM 保持在執行狀態，才能收集記錄。 若要收集記錄檔，請使用下列其中一種方法：

- [序列主控台](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console-grub-single-user-mode)

- 在建立 VM 之前先[啟用開機診斷](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-monitor#enable-boot-diagnostics)，然後在開機期間加以[查看](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-monitor#view-boot-diagnostics)。

- [執行 AZ VM Repair](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-linux-vm-using-azure-virtual-machine-repair-commands)來連接並掛接 OS 磁片，這可讓您收集這些記錄：
```bash
/var/log/cloud-init*
/var/log/waagent*
/var/log/syslog*
/var/log/rsyslog*
/var/log/messages*
/var/log/kern*
/var/log/dmesg*
/var/log/boot*
```
若要開始進行初始疑難排解，請從雲端 init 記錄開始，並瞭解失敗發生的位置，然後使用其他記錄深入探討，並提供額外的深入解析。 
* /var/log/cloud-init.log
* /var/log/cloud-init-output.log
* 序列/開機記錄檔

在所有記錄中，開始搜尋 "Failed"、"WARNING"、"WARNING"、"err"、"error"、"ERROR"。 建議將設定設為忽略區分大小寫的搜尋。 

> [!TIP]
> 如果您要針對自訂映射進行疑難排解，您應該考慮在映射期間新增使用者。 如果布建無法設定系統管理員使用者，您仍然可以登入作業系統。

## <a name="analyzing-the-logs"></a>分析記錄檔

以下是每個雲端初始化記錄檔中要尋找之內容的詳細資訊。

### <a name="varlogcloud-initlog"></a>/var/log/cloud-init.log

根據預設，所有具有 debug 或以上優先順序的雲端 init 事件都會寫入至 `/var/log/cloud-init.log` 。 這會提供在雲端初始化初始化期間發生之每個事件的詳細資訊記錄。 

例如：

```console
2019-10-10 04:51:25,321 - util.py[DEBUG]: Failed mount of '/dev/sr0' as 'auto': Unexpected error while running command.
Command: ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpLIrklc']
Exit code: 32
Reason: -
Stdout:
Stderr: mount: unknown filesystem type 'udf'
2020-01-31 00:21:53,352 - DataSourceAzure.py[WARNING]: /dev/sr0 was not mountable
```


當您找到錯誤或警告之後，請在雲端 init 記錄中向後閱讀，以瞭解在發生錯誤或警告之前，哪些雲端 init 正在嘗試。 在許多情況下，cloud init 將會執行 OS 命令，或在發生錯誤之前執行布建作業，這可提供錯誤訊息出現在記錄檔中的相關見解。 下列範例顯示雲端 init 在遇到錯誤之前，嘗試裝載裝置的許可權。

```output
2019-10-10 04:51:24,010 - util.py[DEBUG]: Running command ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpXXXXX'] with allowed return codes [0] (shell=False, capture=True)
```

如果您有[序列主控台](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console-grub-single-user-mode)的存取權，您可以嘗試重新執行 cloud init 嘗試執行的命令。

您 `/var/log/cloud-init.log` 也可以在/etc/cloud/cloud.cfg.d/05_logging cfg 中重新設定的記錄。 如需雲端 init 記錄的詳細資訊，請參閱[雲端 init 檔](https://cloudinit.readthedocs.io/en/latest/topics/logging.html)。 

### <a name="varlogcloud-init-outputlog"></a>/var/log/cloud-init-output.log

您可以從 `stdout` 和在 `stderr` [雲端初始化階段](cloud-init-deep-dive.md)中取得的資訊。 這通常牽涉到路由表資訊、網路資訊、ssh 主機金鑰驗證資訊， `stdout` 以及 `stderr` 每個階段的每個階段的時間戳記。 如有需要 `stderr` ， `stdout` 可以從重新設定記錄 `/etc/cloud/cloud.cfg.d/05_logging.cfg` 。

### <a name="serialboot-logs"></a>序列/開機記錄檔 

雲端 init 有多個相依性，這些相依性記載于 Azure 上的映射所需的必要條件，例如網路、儲存體、掛接 ISO 的能力，以及掛接和格式化暫存磁片。 其中任何一項都可能會擲回錯誤，並導致雲端初始化失敗。 例如，如果 VM 無法取得 DHCP 租用，雲端初始化將會失敗。

如果您仍然無法隔離為何無法布建雲端 init，則您必須瞭解什麼是雲端初始化階段，以及何時執行模組。 如需詳細資訊，請參閱深入探索[雲端 init](cloud-init-deep-dive.md) 。


## <a name="step-4-investigate-why-the-configuration-isnt-being-applied"></a>步驟4：調查為何未套用設定
不是雲端初始化中的每個失敗都會導致嚴重的布建失敗。 例如，如果您 `runcmd` 在雲端 init 設定中使用模組，則它正在執行之命令中的非零結束代碼會導致 VM 布建失敗。 這是因為它會在雲端初始化的前3個階段中發生的核心布建功能之後執行。 若要疑難排解設定不適用的原因，請參閱步驟3中的記錄，以及手動檢查雲端初始化模組。 例如：

- `runcmd`-腳本是否執行不會發生錯誤？ 從終端機手動執行設定，以確保其如預期般執行。
- 安裝套件-VM 是否可存取套件存放庫？
- 您也應該檢查 `customData` 提供給 VM 的資料設定，這是位於 `/var/lib/cloud/instances/<unique-instance-identifier>/user-data.txt` 。


## <a name="next-steps"></a>後續步驟

如果您仍然無法隔離為什麼雲端 init 未執行設定，您需要更仔細查看每個雲端初始化階段中發生的情況，以及執行模組的時機。 如需詳細資訊，請參閱深入探索[雲端 init](https://docs.microsoft.com/azure/virtual-machines/linux/cloud-init-deep-dive)設定。 
