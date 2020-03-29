---
title: 使用雲 init 在 Linux VM 上配置交換分區
description: 如何使用雲 init 在使用 Azure CLI 創建期間在 Linux VM 中配置交換分區
author: rickstercdn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 1247652e536042ee249054d86aed3c3f8e7aa7bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969194"
---
# <a name="use-cloud-init-to-configure-a-swap-partition-on-a-linux-vm"></a>使用雲 init 在 Linux VM 上配置交換分區
本文介紹如何使用[雲 init](https://cloudinit.readthedocs.io)在各種 Linux 發行版本上配置交換分區。 交換分區傳統上由 Linux 代理 （WALA） 配置，具體取決於哪些發行區需要一個。  本文檔將概述使用雲 init 在預配期間按需構建交換分區的過程。  如需深入了解 cloud-init 如何以原生方式在 Azure 和支援的 Linux 散發版本中運作，請參閱 [cloud-init 概觀](using-cloud-init.md)

## <a name="create-swap-partition-for-ubuntu-based-images"></a>為基於 Ubuntu 的圖像創建交換分區
預設情況下，在 Azure 上，Ubuntu 庫映射不會創建交換分區。 要在 VM 預配期間使用雲 init 啟用交換分區配置 - 請參閱 Ubuntu wiki 上的[AzureSwap 分區文檔](https://wiki.ubuntu.com/AzureSwapPartitions)。

## <a name="create-swap-partition-for-red-hat-and-centos-based-images"></a>為紅帽和基於 CentOS 的圖像創建交換分區

在當前名為*cloud_init_swappart.txt*的 shell 中創建檔，並粘貼以下配置。 針對此案例，在 Cloud Shell 中 (而不是本機電腦上) 建立該檔案。 您可以使用任何您想要的編輯器。 輸入 `sensible-editor cloud_init_swappart.txt` 可建立檔案，並查看可用的編輯器清單。 建議首先選擇使用 **nano** 編輯器。 請確定已正確複製整個 cloud-init 檔案，特別是第一行。  

```yaml
#cloud-config
disk_setup:
  ephemeral0:
    table_type: gpt
    layout: [66, [33,82]]
    overwrite: true
fs_setup:
  - device: ephemeral0.1
    filesystem: ext4
  - device: ephemeral0.2
    filesystem: swap
mounts:
  - ["ephemeral0.1", "/mnt"]
  - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
```

部署此映像前，您必須使用 [az group create](/cli/azure/group) 命令建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 下面的示例在*東部*位置創建名為*myResourceGroup*的資源組。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

現在，請使用 [az vm create](/cli/azure/vm) 建立 VM 並以 `--custom-data cloud_init_swappart.txt` 指定 cloud-init 檔案，如下所示：

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swappart.txt \
  --generate-ssh-keys 
```

## <a name="verify-swap-partition-was-created"></a>驗證已創建交換分區
以 SSH 連線到顯示於由上述命令所產生之輸出中的 VM 公用 IP 位址。 輸入您自己的 **publicIpAddress**，如下所示︰

```bash
ssh <publicIpAddress>
```

將 SSH 進入 vm 後，檢查是否創建了交換分區

```bash
swapon -s
```

此命令的輸出如下所示：

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> 如果現有 Azure 映射配置了交換分區，並且想要更改新映射的交換分區配置，則應刪除現有的交換分區。 如需詳細資訊，請參閱＜透過 cloud-init 自訂映像來進行佈建＞的文件。

## <a name="next-steps"></a>後續步驟
如需其他設定變更的 cloud-init 範例，請參閱下列文件：
 
- [將其他 Linux 使用者新增至虛擬機器](cloudinit-add-user.md)
- [執行套件管理員以便在初次開機時更新現有的套件](cloudinit-update-vm.md)
- [變更虛擬機器本機的主機名稱](cloudinit-update-vm-hostname.md) 
- [安裝應用程式套件、更新組態檔案，以及插入金鑰](tutorial-automate-vm-deployment.md)
