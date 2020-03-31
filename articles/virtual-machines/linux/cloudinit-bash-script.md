---
title: 使用雲 init 在 Azure 上的 Linux VM 中運行 bash 腳本
description: 如何透過 Azure CLI 在建立期間使用 cloud-init 在 Linux 虛擬機器中執行 Bash 指令碼
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: e2f19ceb6c7f19ba749b46a3553036587be6a71a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969222"
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>使用 cloud-init 在 Azure 上的 Linux 虛擬機器中執行 Bash 指令碼
本文會示範如何在 Azure 佈建期間，使用 [cloud-init](https://cloudinit.readthedocs.io) 在 Linux 虛擬機器 (VM) 上或虛擬機器擴展集 (VMSS) 上執行現有的 Bash 指令碼。 一旦 Azure 佈建資源，這些 cloud-init 指令碼就會在初次開機時執行。 如需深入了解 cloud-init 如何以原生方式在 Azure 和支援的 Linux 散發版本中運作，請參閱 [cloud-init 概觀](using-cloud-init.md)

## <a name="run-a-bash-script-with-cloud-init"></a>使用 cloud-init 執行 Bash 指令碼
透過 cloud-init，您不需要將現有指令碼轉換成雲端組態，cloud-init 接受多種輸入類型，其中一個就是 Bash 指令碼。

如果您已使用 Linux 自訂指令碼的 Azure 延伸模組來執行指令碼，您可以將其移轉為使用 cloud-init。 但是，Azure 延伸模組有整合的指令碼失敗警示報告，而 cloud-init 映像部署則是「不會」在指令碼失敗時失敗。

若要在實際操作中查看這項功能，可建立簡單的 Bash 指令碼進行測試。 如同 cloud-init 的 `#cloud-config` 檔案，此指令碼必須位於您執行 AzureCLI 命令來佈建虛擬機器的位置。  針對此案例，在 Cloud Shell 中 (而不是本機電腦上) 建立該檔案。 您可以使用任何您想要的編輯器。 輸入 `sensible-editor simple_bash.sh` 可建立檔案，並查看可用的編輯器清單。 建議首先選擇使用 **nano** 編輯器。 請確定已正確複製整個 cloud-init 檔案，特別是第一行。  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

部署此映像前，您必須使用 [az group create](/cli/azure/group) 命令建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 下面的示例在*東部*位置創建名為*myResourceGroup*的資源組。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

現在，請使用 [az vm create](/cli/azure/vm) 建立虛擬機器並以 `--custom-data simple_bash.sh` 指定 Bash 指令碼，如下所示：

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>驗證已執行的 Bash 指令碼
以 SSH 連線到顯示於由上述命令所產生之輸出中的 VM 公用 IP 位址。 輸入您自己的 **publicIpAddress**，如下所示︰

```bash
ssh <publicIpAddress>
```

變更為 **/tmp** 目錄，並確認 myScript.txt 檔案已存在且其中的文字正確。  如果不存在，您可以檢查 **/var/log/cloud-init.log** 以取得詳細資訊。  搜尋下列項目：

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>後續步驟
如需其他設定變更的 cloud-init 範例，請參閱下列文件：
 
- [將其他 Linux 使用者新增至虛擬機器](cloudinit-add-user.md)
- [執行套件管理員以便在初次開機時更新現有的套件](cloudinit-update-vm.md)
- [變更虛擬機器本機的主機名稱](cloudinit-update-vm-hostname.md) 
- [安裝應用程式套件、更新組態檔案，以及插入金鑰](tutorial-automate-vm-deployment.md)
