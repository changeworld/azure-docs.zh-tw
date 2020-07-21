---
title: 停用或移除布建代理程式
description: 瞭解如何停用或移除 Linux Vm 和映射中的布建代理程式。
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 2a17825d062496e6600966dc7c90b14749507e4d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86494508"
---
# <a name="disable-or-remove-the-linux-agent-from-vms-and-images"></a>從 Vm 和映射停用或移除 Linux 代理程式

移除 Linux 代理程式之前，您必須瞭解在移除 Linux 代理程式之後，VM 將無法執行的動作。

Azure 虛擬機器（VM）[擴充](../extensions/overview.md)功能是小型的應用程式，可在 azure vm 上提供部署後設定和自動化工作，延伸模組則是由 azure 控制平面安裝和管理。 [Azure Linux 代理程式](../extensions/agent-linux.md)的工作是處理平臺擴充命令，並確保 VM 內的擴充功能的正確狀態。

Azure 平台裝載許多擴充功能，包括 VM 設定、監視、安全性和公用程式應用程式等等。 第一個和協力廠商擴充功能有很大的選擇，例如，延伸模組用於下列主要案例的範例：
* 支援第一方的 Azure 服務，例如 Azure 備份、監視、磁片加密、安全性、網站複寫等。
* SSH/密碼重設
* VM 設定-執行自訂腳本、安裝 Chef、Puppet 代理程式等。
* 協力廠商產品，例如 AV 產品、VM 弱點工具、VM 和應用程式監視工具。
* 擴充功能可以與新的 VM 部署配套。 例如，這些擴充功能可以是較大部署的一部分、可以在 VM 佈建時設定應用程式，或者可以在部署之後針對任何支援的擴充功能操作系統來執行。

## <a name="disabling-extension-processing"></a>停用擴充功能處理

視您的需求而定，有數種方式可以停用延伸模組處理，但在繼續之前，您**必須**先移除所有部署到 VM 的延伸模組，例如，使用 AZ CLI，您可以[列出](/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-list)和[刪除](/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-delete)：

```bash
az vm extension delete -g MyResourceGroup --vm-name MyVm -n extension_name
```
> [!Note]
> 
> 如果您未執行上述動作，平臺將會嘗試傳送延伸模組設定和40min 後的超時時間。

### <a name="disable-at-the-control-plane"></a>在控制平面停用
如果您不確定未來是否需要延伸模組，您可以將 Linux 代理程式保留在 VM 上，然後停用平臺的擴充處理功能。 此選項適用于 `Microsoft.Compute` api 版本 `2018-06-01` 或更高版本，且不相依于安裝的 Linux 代理程式版本。

```bash
az vm update -g <resourceGroup> -n <vmName> --set osProfile.allowExtensionOperations=false
```
您可以使用上述命令，輕鬆地從平臺重新啟用此延伸模組處理，但將其設定為 ' true '。

## <a name="remove-the-linux-agent-from-a-running-vm"></a>從執行中的 VM 移除 Linux 代理程式

請先確定您已從 VM**移除**所有現有的延伸模組，如上所示。

### <a name="step-1-remove-the-azure-linux-agent"></a>步驟1：移除 Azure Linux 代理程式

如果您只是移除 Linux 代理程式，而不是相關聯的設定成品，您可以在日後重新安裝。 以 root 身分執行下列其中一項，以移除 Azure Linux 代理程式：

#### <a name="for-ubuntu-1804"></a>適用于 Ubuntu >= 18.04
```bash
apt -y remove walinuxagent
```

#### <a name="for-redhat--77"></a>針對 Redhat >= 7。7
```bash
yum -y remove WALinuxAgent
```

#### <a name="for-suse"></a>適用于 SUSE
```bash
zypper --non-interactive remove python-azure-agent
```

### <a name="step-2-optional-remove-the-azure-linux-agent-artifacts"></a>步驟2：（選擇性）移除 Azure Linux 代理程式構件
> [!IMPORTANT] 
>
> 您可以移除 Linux 代理程式的所有關聯成品，但這表示您無法在日後重新安裝它。 因此，強烈建議您先考慮停用 Linux 代理程式，只使用上述的來移除 Linux 代理程式。 

如果您知道您將不會再重新安裝 Linux 代理程式，則可以執行下列動作：

#### <a name="for-ubuntu-1804"></a>適用于 Ubuntu >= 18.04
```bash
apt -y purge walinuxagent
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-redhat--77"></a>針對 Redhat >= 7。7
```bash
yum -y remove WALinuxAgent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-suse"></a>適用于 SUSE
```bash
zypper --non-interactive remove python-azure-agent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

## <a name="preparing-an-image-without-the-linux-agent"></a>在不使用 Linux 代理程式的情況下準備映射
如果您的映射已經包含雲端 init，而您想要移除 Linux 代理程式，但仍要使用雲端 init 來進行布建，請執行步驟2（並選擇性步驟3）中的步驟來移除 Azure Linux 代理程式，然後下列程式將會移除雲端初始化設定和快取資料，並準備 VM 以建立自訂映射。

```bash
cloud-init clean --logs --seed 
```

## <a name="deprovision-and-create-an-image"></a>取消布建並建立映射
Linux 代理程式能夠清除部分現有的映射中繼資料，其中的步驟為「waagent-取消布建 + 使用者」，不過，移除之後，您將需要執行如下的動作，並移除其中的任何其他機密資料。

- 移除所有現有的 ssh 主機金鑰

   ```bash
   rm /etc/ssh/ssh_host_*key*
   ```
- 刪除系統管理員帳戶

   ```bash
   touch /var/run/utmp
   userdel -f -r <admin_user_account>
   ```
- 刪除根密碼

   ```bash
   passwd -d root
   ```

完成上述動作之後，您就可以使用 Azure CLI 來建立自訂映射。


**建立一般受控映射**
```bash
az vm deallocate -g <resource_group> -n <vm_name>
az vm generalize -g <resource_group> -n <vm_name>
az image create -g <resource_group> -n <image_name> --source <vm_name>
```

**在共用映射資源庫中建立映射版本**

```bash
az sig image-version create \
    -g $sigResourceGroup 
    --gallery-name $sigName 
    --gallery-image-definition $imageDefName 
    --gallery-image-version 1.0.0 
    --managed-image /subscriptions/00000000-0000-0000-0000-00000000xxxx/resourceGroups/imageGroups/providers/images/MyManagedImage
```
### <a name="creating-a-vm-from-an-image-that-does-not-contain-a-linux-agent"></a>從未包含 Linux 代理程式的映射建立 VM
當您從沒有 Linux 代理程式的映射建立 VM 時，您必須確定 VM 部署設定指出此 VM 上不支援延伸模組。

> [!NOTE] 
> 
> 如果您未執行上述動作，平臺將會嘗試傳送延伸模組設定和40min 後的超時時間。

若要部署已停用延伸模組的 VM，您可以使用 Azure CLI 搭配[--enable-agent](/cli/azure/vm#az-vm-create)。

```bash
az vm create \
    --resource-group $resourceGroup \
    --name $prodVmName \
    --image RedHat:RHEL:8.1-ci:latest \
    --admin-username azadmin \
    --ssh-key-value "$sshPubkeyPath" \
    --enable-agent false
```

或者，您也可以藉由設定，使用 Azure Resource Manager （ARM）範本來執行這項操作 `"provisionVMAgent": false,` 。

```json
"osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "provisionVMAgent": false,
        "ssh": {
            "publicKeys": [
                {
                    "path": "[concat('/home/', parameters('adminUsername'), '/.ssh/authorized_keys')]",
                    "keyData": "[parameters('adminPublicKey')]"
```

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱布建[Linux](provisioning.md)。
