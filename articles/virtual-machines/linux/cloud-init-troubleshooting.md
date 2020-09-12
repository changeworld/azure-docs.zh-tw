---
title: 使用雲端初始化進行疑難排解
description: 針對使用雲端初始布建 Azure VM 進行疑難排解。
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 6412036e3f16e2efb3bbf6669f6a31e9dc6e3584
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89434634"
---
# <a name="troubleshooting-vm-provisioning-with-cloud-init"></a>針對使用雲端初始化的 VM 布建進行疑難排解

如果您已建立一般化的自訂映射，並使用雲端初始化來進行布建，但發現該 VM 未正確建立，您將需要針對您的自訂映射進行疑難排解。

布建的一些問題範例如下：
- VM 停滯在「建立中」達40分鐘，而 VM 建立標示為「失敗」
- `CustomData` 不會被處理
- 暫時磁片無法掛接
- 使用者未建立，或有使用者存取問題
- 網路未正確設定
- 交換檔案或分割區失敗

本文將逐步引導您進行雲端初始操作的疑難排解。 如需更深入的詳細資料，請參閱 [雲端初始化深入探討](./cloud-init-deep-dive.md)。

## <a name="step-1-test-the-deployment-without-customdata"></a>步驟1：不測試部署 `customData`

當 VM 建立時，雲端初始可以接受 `customData` 並傳遞給它。 首先，請確定這不會造成部署的任何問題。 嘗試布建 VM，而不需傳入任何設定。 如果您發現 VM 無法布建，請繼續進行下列步驟，如果您發現您傳遞的設定未套用，請執行 [步驟 4]()。 

## <a name="step-2-review-image-requirements"></a>步驟2：審核映射需求
VM 布建失敗的主要原因是 OS 映射不符合在 Azure 上執行的必要條件。 在 Azure 中嘗試布建映射之前，請先確定您的映射已正確備妥。 


下列文章說明準備 Azure 支援的各種 linux 發行版本的步驟：

- [CentOS 型發行版本](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES 和 openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)
- [其他：非背書的發行版本](create-upload-generic.md)

針對 [支援的 azure 雲端初始映射](./using-cloud-init.md)，Linux 發行版本已具備在 Azure 中正確布建映射所需的所有必要套件和設定。 如果您發現您的 VM 無法從您自己的策劃映射建立，請使用您的選擇性來嘗試已針對雲端 init 設定的支援 Azure Marketplace 映射 `customData` 。 如果 `customData` 搭配 Azure Marketplace 映射正常運作，則可能是策劃映射的問題。

## <a name="step-3-collect--review-vm-logs"></a>步驟3：收集 & 審查 VM 記錄

當 VM 無法布建時，Azure 會顯示 [正在建立] 狀態，20分鐘，然後重新開機 VM，然後在最後將 VM 部署標示為失敗之前等候另20分鐘，最後再將其標示為 `OSProvisioningTimedOut` 錯誤。

當 VM 正在執行時，您將需要來自 VM 的記錄，以瞭解布建失敗的原因。  若要瞭解 VM 布建失敗的原因，請不要停止 VM。 讓 VM 保持執行狀態。 您將需要讓失敗的 VM 處於執行中狀態，才能收集記錄。 若要收集記錄檔，請使用下列其中一種方法：

- [序列主控台](../troubleshooting/serial-console-grub-single-user-mode.md)

- 先[啟用開機診斷](./tutorial-monitor.md#enable-boot-diagnostics)，再建立 VM，然後在開機期間加以[查看](./tutorial-monitor.md#view-boot-diagnostics)。

- [執行 AZ VM Repair](../troubleshooting/repair-linux-vm-using-azure-virtual-machine-repair-commands.md) 以連接並掛接 OS 磁片，這可讓您收集這些記錄：
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
若要開始進行初始疑難排解，請從雲端初始化記錄開始，並瞭解發生失敗的位置，然後使用其他記錄深入探討，並提供其他見解。 
* /var/log/cloud-init.log
* /var/log/cloud-init-output.log
* 串列/開機記錄檔

在所有記錄中，開始搜尋「失敗」、「警告」、「警告」、「err」、「錯誤」、「錯誤」。 建議將設定設定為略過區分大小寫的搜尋。 

> [!TIP]
> 如果您要針對自訂映射進行疑難排解，您應該考慮在映射期間新增使用者。 如果布建無法設定管理使用者，您仍然可以登入作業系統。

## <a name="analyzing-the-logs"></a>分析記錄

以下是每個雲端初始化記錄檔中要尋找的詳細資料。

### <a name="varlogcloud-initlog"></a>/var/log/cloud-init.log

依預設，會將所有具有 debug 或更高優先順序的雲端初始事件寫入至 `/var/log/cloud-init.log` 。 這會提供在雲端初始化初始化期間發生之每個事件的詳細資訊記錄。 

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


當您找到錯誤或警告之後，請在 cloud init 記錄檔中向前讀取，以瞭解在發生錯誤或警告之前，會先嘗試進行什麼雲端初始化。 在許多情況下，雲端 init 將會執行作業系統命令或在錯誤之前執行布建作業，這可提供記錄中出現錯誤原因的深入解析。 下列範例顯示在發生錯誤之前，雲端初始嘗試掛接裝置的許可權。

```output
2019-10-10 04:51:24,010 - util.py[DEBUG]: Running command ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpXXXXX'] with allowed return codes [0] (shell=False, capture=True)
```

如果您有 [序列主控台](../troubleshooting/serial-console-grub-single-user-mode.md)的存取權，您可以嘗試重新執行 cloud init 嘗試執行的命令。

您 `/var/log/cloud-init.log` 也可以在/etc/cloud/cloud.cfg.d/05_logging 中重新設定記錄。 如需有關雲端初始記錄的詳細資訊，請參閱 [雲端初始化檔](https://cloudinit.readthedocs.io/en/latest/topics/logging.html)。 

### <a name="varlogcloud-init-outputlog"></a>/var/log/cloud-init-output.log

您可以從 `stdout` 和在 `stderr` [雲端初始階段](cloud-init-deep-dive.md)中取得資訊。 這通常牽涉到路由表資訊、網路資訊、ssh 主機金鑰驗證資訊， `stdout` 以及 `stderr` 每個階段的時間戳記，以及每個階段的時間戳記。 如有需要， `stderr` 也可以重新設定 `stdout` 記錄 `/etc/cloud/cloud.cfg.d/05_logging.cfg` 。

### <a name="serialboot-logs"></a>串列/開機記錄檔 

Cloud init 具有多個相依性，這些相依性記載于 Azure 上的映射所需的必要條件，例如網路、儲存體、掛接 ISO 的能力，以及掛接和格式化暫存磁片。 其中任何一項都可能會擲回錯誤，並導致雲端初始化失敗。 例如，如果 VM 無法取得 DHCP 租用，則雲端初始將會失敗。

如果您仍然無法找出無法布建雲端初始的原因，您必須瞭解什麼是雲端初始階段，以及何時執行模組。 如需詳細資訊，請參閱深入瞭解 [雲端初始化](cloud-init-deep-dive.md) 。


## <a name="step-4-investigate-why-the-configuration-isnt-being-applied"></a>步驟4：調查為何未套用設定
雲端初始化中的每個失敗都不會導致嚴重的布建失敗。 例如，如果您使用的是 `runcmd` 雲端初始設定中的模組，則它正在執行之命令的非零結束代碼會導致 VM 布建失敗。 這是因為它會在雲端初始化的前3個階段中發生的核心布建功能之後執行。 若要針對設定未套用的原因進行疑難排解，請手動檢查步驟3中的記錄檔和雲端初始模組。 例如：

- `runcmd` -腳本是否會在沒有錯誤的情況下執行？ 從終端機手動執行設定，以確保其如預期般執行。
- 安裝套件-VM 可以存取套件存放庫嗎？
- 您也應該檢查 `customData` 提供給 VM 的資料設定，這是位於 `/var/lib/cloud/instances/<unique-instance-identifier>/user-data.txt` 。


## <a name="next-steps"></a>接下來的步驟

如果您仍然無法找出雲端初始化未執行設定的原因，您需要更仔細地查看每個雲端初始化階段和模組執行時所發生的情況。 如需詳細資訊，請參閱深入瞭解 [雲端初始](./cloud-init-deep-dive.md) 設定。 
