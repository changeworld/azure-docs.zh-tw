---
title: 針對 Azure VM 的 SSH 連線問題進行疑難排解 | Microsoft Docs
description: 如何針對執行 Linux 的 Azure 虛擬機器進行「SSH 連線失敗」或「SSH 連線被拒」等問題的疑難排解。
keywords: ssh 連線被拒, ssh 錯誤, azure ssh, ssh 連線失敗
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 05/30/2017
ms.author: genli
ms.openlocfilehash: f221a0bdf579dbbf42ecf64e18803decfb718456
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80060653"
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>針對 SSH 連線至 Azure Linux VM 失敗、發生錯誤或被拒進行疑難排解
本文可協助您找出並更正當您嘗試連接到 Linux 虛擬機器 (VM) 時，因為安全殼層 (SSH) 錯誤、SSH 連線失敗或 SSH 被拒而發生的問題。 您可以使用 Azure 入口網站、Azure CLI 或適用於 Linux 的 VM 存取擴充功能，針對連線問題進行疑難排解並予以解決。


如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。

## <a name="quick-troubleshooting-steps"></a>快速疑難排解步驟
在每個疑難排解步驟完成之後，請嘗試重新連接到 VM。

1. [重設 SSH 設定](#reset-config)。
2. [重設使用者的認證](#reset-credentials)。
3. 確認[網路安全性群組](../../virtual-network/security-overview.md)規則允許 SSH 流量。
   * 請確定[網路安全性群組規則](#security-rules)存在，以允許 SSH 流量（預設為 TCP 通訊埠22）。
   * 若未使用 Azure 負載平衡器，則無法使用連接埠重新導向 / 對應功能。
4. 檢查[VM 資源健康狀態](../../resource-health/resource-health-overview.md)。
   * 確定 VM 回報告為狀況良好。
   * 如果您已[啟用開機診斷](boot-diagnostics.md)，請確認 VM 並未在記錄中回報開機錯誤。
5. [重新開機 VM](#restart-vm)。
6. 重新[部署 VM](#redeploy-vm)。

如需更詳細的疑難排解步驟與說明，請繼續閱讀。

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>針對 SSH 連線問題進行疑難排解的可用方法
您可以使用下列方法之一重設認證或 SSH 組態：

* [Azure 入口網站](#use-the-azure-portal) - 適用於您需要快速重設 SSH 組態或 SSH 金鑰，而且未安裝 Azure 工具時。
* [AZURE Vm 序列主控台](https://aka.ms/serialconsolelinux)-無論 SSH 設定為何，VM 序列主控台都會正常執行，並可為您提供 VM 的互動式主控台。 事實上，「無法 SSH」的情況特別是序列主控台設計來協助解決的。 請參閱下列詳細資訊。
* [Azure CLI](#use-the-azure-cli) - 如果您已在命令列上，請快速重設 SSH 設定或認證。 如果您正在使用傳統 VM，可以使用 [Azure 傳統 CLI](#use-the-azure-classic-cli)。
* [Azure VMAccessForLinux 擴充功能](#use-the-vmaccess-extension) - 建立並重複使用 json 定義檔案，以重設 SSH 組態或使用者認證。

在每個疑難排解步驟完成之後，請再次嘗試連接到 VM。 如果仍然無法連線，請嘗試下一個步驟。

## <a name="use-the-azure-portal"></a>使用 Azure 入口網站
Azure 入口網站可供快速重設 SSH 組態或使用者認證，而不需在本機電腦上安裝任何工具。

若要開始，請在 Azure 入口網站中選取您的 VM。 向下捲動至 [支援 + 疑難排解]**** 區段，然後選取 [重設密碼]****，如下列範例所示︰

![在 Azure 入口網站中重設 SSH 組態或認證](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a><a id="reset-config" />重設 SSH 組態
若要重設 SSH 組態，請在 [模式]**** 區段中選取 `Reset configuration only`，如前面的螢幕擷取畫面所示，然後選取 [更新]****。 完成此動作後，嘗試再次存取您的 VM。

### <a name="reset-ssh-credentials-for-a-user"></a><a id="reset-credentials" />重設使用者的 SSH 認證
若要重設現有使用者的認證，請在 [模式]**** 區段中選取 `Reset SSH public key` 或 `Reset password`，如前面的螢幕擷取畫面所示。 指定使用者名稱和 SSH 金鑰或新的密碼，然後選取 [更新]****。

您也可以經由此功能表，在此 VM 上建立具備 sudo 權限的使用者。 輸入新的使用者名稱和相關聯的密碼或 SSH 金鑰，然後選取 [更新]****。

### <a name="check-security-rules"></a><a id="security-rules" />檢查安全性規則

使用[IP 流量驗證](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md)來確認網路安全性群組中的規則是否會封鎖虛擬機器的流量。 您也可以檢閱有效的安全性群組規則，以確保輸入「允許」NSG 規則存在並已針對 SSH 連接埠 (預設值 22) 設定優先順序。 如需詳細資訊，請參閱[使用有效安全性規則對 VM 流量流程進行疑難排解](../../virtual-network/diagnose-network-traffic-filter-problem.md)。

### <a name="check-routing"></a>檢查路由

使用網路監看員的[下一個躍點](../../network-watcher/network-watcher-check-next-hop-portal.md)功能，確認路由不會防止流量從虛擬機器往返路由傳送。 您也可以檢閱有效路由，以查看網路介面的所有有效路由。 如需詳細資訊，請參閱[使用有效路由來針對 VM 流量流程進行疑難排解](../../virtual-network/diagnose-network-routing-problem.md)。

## <a name="use-the-azure-vm-serial-console"></a>使用 Azure VM 序列主控台
[AZURE VM 序列主控台](./serial-console-linux.md)可讓您存取 Linux 虛擬機器的文字型主控台。 您可以使用主控台，在互動式 shell 中針對 SSH 連線進行疑難排解。 請確定您已符合使用序列主控台的[必要條件](./serial-console-linux.md#prerequisites)，並嘗試下列命令以進一步疑難排解您的 SSH 連線能力。

### <a name="check-that-ssh-is-running"></a>檢查 SSH 是否正在執行
您可以使用下列命令來確認 SSH 是否正在您的 VM 上執行：

```console
ps -aux | grep ssh
```

如果有任何輸出，SSH 就會啟動並執行。

### <a name="check-which-port-ssh-is-running-on"></a>檢查 SSH 正在哪個埠上執行

您可以使用下列命令來檢查 SSH 執行所在的埠：

```console
sudo grep Port /etc/ssh/sshd_config
```

您的輸出看起來會像這樣：

```output
Port 22
```

## <a name="use-the-azure-cli"></a>使用 Azure CLI
如果尚未安裝，請安裝最新的 [Azure CLI](/cli/azure/install-az-cli2) 並使用 [az login](/cli/azure/reference-index) 來登入 Azure 帳戶。

如果您已建立並上傳自訂 Linux 磁碟映像，請確定已安裝 [Microsoft Azure Linux 代理程式](../extensions/agent-linux.md) 2.0.5 版或更新版本。 若為使用資源庫映像建立的 VM，系統已經為您安裝和設定此存取擴充功能。

### <a name="reset-ssh-configuration"></a>重設 SSH 組態
您可以一開始先嘗試將 SSH 組態重設為預設值，然後將虛擬機器上的 SSH 伺服器重新開機。 這不會變更使用者帳戶名稱、密碼或 SSH 金鑰。
下列範例會使用 [az vm user reset-ssh](/cli/azure/vm/user)，在 `myResourceGroup` 中名為 `myVM` 的虛擬機器上重設 SSH 組態。 使用您自己的值，如下所示︰

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>重設使用者的 SSH 認證
下列範例會使用 [az vm user update](/cli/azure/vm/user)，在 `myResourceGroup` 中名為 `myVM` 的虛擬機器上，將 `myUsername` 的認證重設為 `myPassword` 中指定的值。 使用您自己的值，如下所示︰

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

如果使用 SSH 金鑰驗證，您可以針對指定的使用者重設 SSH 金鑰。 下列範例會使用 **az vm access set-linux-user**，在 `myResourceGroup` 中名為 `myVM` 的 VM 上，針對名為 `myUsername` 的使用者更新儲存在 `~/.ssh/id_rsa.pub` 中的 SSH 金鑰。 使用您自己的值，如下所示︰

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>使用 VMAccess 擴充功能
適用于 Linux 的 VM 存取延伸模組會讀取 json 檔案，該檔案會定義要執行的動作。這些動作包括重設 SSHD、重設 SSH 金鑰或新增使用者。 您仍可使用 Azure CLI 來呼叫 VMAccess 擴充功能，但您可以視需要將 json 檔案重複使用於多個 VM。 這種方法可讓您建立 json 檔案的儲存機制，以便之後針對特定案例進行呼叫。

### <a name="reset-sshd"></a>重設 SSHD
使用下列內容，建立名為 `settings.json` 的檔案︰

```json
{
    "reset_ssh":"True"
}
```

使用 Azure CLI，接著呼叫 `VMAccessForLinux` 擴充功能，藉由指定 json 檔案來重設 SSH 連線。 下列範例會使用 [az vm extension set](/cli/azure/vm/extension)，在 `myResourceGroup` 中名為 `myVM` 的虛擬機器上重設 SSHD。 使用您自己的值，如下所示︰

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>重設使用者的 SSH 認證
如果 SSHD 似乎能正常運作，您可以針對指定的使用者重設認證。 若要重設使用者的密碼，請建立名為 `settings.json` 的檔案。 下列範例會將 `myUsername` 的認證重設為在 `myPassword` 中指定的值。 使用您自己的值，將下列幾行程式碼輸入到 `settings.json` 檔案中︰

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

或者，若要重設使用者的 SSH 金鑰，請先建立名為 `settings.json` 的檔案。 下列範例會在 `myResourceGroup` 中名為 `myVM` 的 VM 上，將 `myUsername` 的認證重設為在 `myPassword` 中指定的值。 使用您自己的值，將下列幾行程式碼輸入到 `settings.json` 檔案中︰

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

建立 json 檔案之後，使用 Azure CLI 來呼叫 `VMAccessForLinux` 擴充功能，藉由指定 json 檔案來重設 SSH 使用者認證。 下列範例會在 `myResourceGroup` 中名為 `myVM` 的 VM 上重設認證。 使用您自己的值，如下所示︰

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-classic-cli"></a>使用 Azure 傳統 CLI
如果尚未安裝，請 [安裝 Azure 傳統 CLI 並連線至您的 Azure 訂用帳戶](../../cli-install-nodejs.md)。 確定您使用的是 Resource Manager 模式，如下所示：

```azurecli
azure config mode arm
```

如果您已建立並上傳自訂 Linux 磁碟映像，請確定已安裝 [Microsoft Azure Linux 代理程式](../extensions/agent-linux.md) 2.0.5 版或更新版本。 若為使用資源庫映像建立的 VM，系統已經為您安裝和設定此存取擴充功能。

### <a name="reset-ssh-configuration"></a>重設 SSH 組態
SSHD 組態本身可能設定錯誤，或服務發生錯誤。 您可以重設 SSH 以確定 SSH 組態本身有效。 重設 SSHD 應該是您所採取的第一個疑難排解步驟。

下列範例會在名為 `myResourceGroup` 的資源群組中名為 `myVM` 的 VM 上重設 SSHD。 使用您自己的 VM 和資源群組名稱，如下所示︰

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>重設使用者的 SSH 認證
如果 SSHD 似乎能正常運作，您可以針對指定的使用者重設密碼。 下列範例會在 `myResourceGroup` 中名為 `myVM` 的 VM 上，將 `myUsername` 的認證重設為在 `myPassword` 中指定的值。 使用您自己的值，如下所示︰

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

如果使用 SSH 金鑰驗證，您可以針對指定的使用者重設 SSH 金鑰。 下列範例會在 `myResourceGroup` 中名為 `myVM` 的 VM 上，針對名為 `myUsername` 的使用者，更新 `~/.ssh/id_rsa.pub` 中儲存的 SSH 金鑰。 使用您自己的值，如下所示︰

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```

## <a name="restart-a-vm"></a><a id="restart-vm" />重新啟動 VM
如果您重設 SSH 組態和使用者認證，或在執行此作業時發生錯誤，您可以嘗試重新啟動 VM 以處理基礎計算問題。

### <a name="azure-portal"></a>Azure 入口網站
若要使用 Azure 入口網站來重新啟動 VM，請選取您的 VM，然後選取 [重新啟動]****，如下列範例所示︰

![在 Azure 入口網站中重新啟動 VM](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
下列範例會使用 [az vm restart](/cli/azure/vm) 來重新啟動名為 `myResourceGroup` 的資源群組中名為 `myVM` 的 VM。 使用您自己的值，如下所示︰

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Azure 傳統 CLI

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

下列範例會重新啟動名為 `myResourceGroup` 的資源群組中名為 `myVM` 的 VM。 使用您自己的值，如下所示︰

```console
azure vm restart --resource-group myResourceGroup --name myVM
```

## <a name="redeploy-a-vm"></a><a id="redeploy-vm" />重新部署 VM
您可以將 VM 重新部署到 Azure 中的另一個節點，這可能會修正任何的基礎網路問題。 如需重新部署 VM 的相關資訊，請參閱[將虛擬機器重新部署至新的 Azure 節點](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

> [!NOTE]
> 此作業完成之後，會遺失暫時磁碟資料，並且會更新與虛擬機器關聯的動態 IP 位址。
>
>

### <a name="azure-portal"></a>Azure 入口網站
若要使用 Azure 入口網站重新部署 VM，請選取您的 VM 並向下捲動至 [支援 + 疑難排解]**** 區段。 選取 [重新部署]****，如下列範例所示︰

![在 Azure 入口網站中重新部署 VM](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
下列範例會使用 [az vm redeploy](/cli/azure/vm) 來重新部署名為 `myResourceGroup` 的資源群組中名為 `myVM` 的 VM。 使用您自己的值，如下所示︰

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Azure 傳統 CLI

下列範例會重新部署名為 `myResourceGroup` 的資源群組中名為 `myVM` 的 VM。 使用您自己的值，如下所示︰

```console
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>使用傳統部署模型建立的 VM

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

請嘗試下列步驟，來解決使用傳統部署模型所建立之 VM 中最常見的 SSH 連線失敗。 在每個步驟完成後，請嘗試重新連接至 VM。

* 透過 [Azure 入口網站](https://portal.azure.com)，重設遠端存取。 在 Azure 入口網站上選取您的 VM，然後選取 [重設遠端...]****。
* 重新啟動 VM。 在 [Azure 入口網站](https://portal.azure.com)上選取您的 VM，然後選取 [重新啟動]****。

* 將 VM 重新部署到新的 Azure 節點。 如需如何重新部署 VM 的資訊，請參閱[將虛擬機器重新部署至新的 Azure 節點](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

    此作業完成之後，暫時磁碟機資料將會遺失，且將會更新與虛擬機器相關聯的動態 IP 位址。
* 請依循[如何為 Linux 虛擬機器重設密碼或 SSH](../linux/classic/reset-access-classic.md) 中的指示進行，以：

  * 重設密碼或 SSH 金鑰。
  * 建立 *sudo* 使用者帳戶。
  * 重設 SSH 組態。
* 檢查 VM 的資源健康狀態是否有任何平台問題。<br>
     選取您的 VM，並向下滾動**設定**[  >  **檢查健全狀況**]。

## <a name="additional-resources"></a>其他資源
* 如果在依循這些步驟之後仍無法以 SSH 連線到您的 VM，請參閱[更詳細的疑難排解步驟](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)以檢閱其他的步驟來解決您的問題。
* 如需疑難排解應用程式存取的詳細資訊，請參閱[針對存取在 Azure 虛擬機器上執行的應用程式進行疑難排解](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* 如需針對使用傳統部署模型所建立之虛擬機器進行疑難排解的詳細資訊，請參閱[如何為 Linux 虛擬機器重設密碼或 SSH](../linux/classic/reset-access-classic.md)。
