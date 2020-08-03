---
title: 使用 SSH 金鑰連接到 Linux Vm
description: 瞭解如何從 Windows 電腦產生和使用 SSH 金鑰，以連線到 Azure 上的 Linux 虛擬機器。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.date: 07/09/2020
ms.topic: how-to
ms.author: cynthn
ms.openlocfilehash: dcb5277773be1fc45a3d2b0901cf9fda177b7054
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2020
ms.locfileid: "87512647"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>如何在 Azure 上搭配 Windows 使用 SSH 金鑰

本文適用于想要[建立](#create-an-ssh-key-pair)及使用*安全 shell* （SSH）金鑰的 Windows[使用者，以連線到 Azure](#connect-to-your-vm)中的 Linux 虛擬機器（vm）。 在入口網站中建立 Vm 時，您也可以[在 Azure 入口網站中產生並儲存 SSH 金鑰](../ssh-keys-portal.md)。


若要從 Linux 或 macOS 用戶端使用 SSH 金鑰，請參閱[快速](mac-create-ssh-keys.md)。 如需 SSH 的詳細總覽，請參閱[詳細步驟：建立和管理 ssh 金鑰以在 Azure 中對 LINUX VM 進行驗證](create-ssh-keys-detailed.md)。

## <a name="overview-of-ssh-and-keys"></a>SSH 和金鑰的概觀

[SSH](https://www.ssh.com/ssh/)是一種加密的連線通訊協定，允許透過不安全的連接進行安全登入。 SSH 是 Azure 中裝載 Linux VM 的預設連線通訊協定。 雖然 SSH 本身提供加密的連線，但使用密碼搭配 SSH 仍然會使 VM 容易遭受暴力密碼破解攻擊。 建議您使用公開-私密金鑰組（也稱為*SSH 金鑰*），透過 SSH 連線至 VM。 

公開/私密金鑰組就像是您的大門鎖定。 鎖定會公開給**公用**，任何具有正確金鑰的人都可以開啟門。 金鑰是**私**用的，而且只會提供給您信任的人員，因為它可以用來將門解除鎖定。 

- 當您建立 VM 時，*公開金鑰*會放在您的 Linux VM 上。 

- 「私密金鑰」** 會保留在您的本機系統上。 保護此私密金鑰。 不要共用它。

當您連線到 Linux VM 時，VM 會測試 SSH 用戶端，以確保其具有正確的私密金鑰。 如果用戶端具有私密金鑰，則會獲得 VM 的存取權。 

視您組織的安全性原則而定，您可以重複使用單一金鑰組來存取多個 Azure Vm 和服務。 您不需要為每個 VM 使用個別的金鑰組。 

您的公開金鑰可以與任何人共用，但只有您（或您的本機安全性基礎結構）才能存取您的私密金鑰。

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-clients"></a>SSH 用戶端

最新版本的 Windows 10 包含[OpenSSH 用戶端命令](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/)，可用來建立和使用 ssh 金鑰，以及從 PowerShell 或命令提示字元進行 ssh 連線。 這是從 Windows 電腦建立與 Linux VM 的 SSH 連線最簡單的方式。 

您也可以使用[Azure Cloud Shell](../../cloud-shell/overview.md)中的 Bash 來連接到您的 VM。 您可以使用[網頁瀏覽器](https://shell.azure.com/bash)中的 Cloud Shell、 [Azure 入口網站](https://portal.azure.com)，或在使用[Azure 帳戶延伸](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)模組的 Visual Studio Code 中作為終端機。

您也可以安裝[適用于 Linux 的 Windows 子系統](https://docs.microsoft.com/windows/wsl/about)，以透過 SSH 連線至您的 VM，並在 Bash shell 中使用其他原生 Linux 工具。

## <a name="create-an-ssh-key-pair"></a>建立 SSH 金鑰組

使用命令建立 SSH 金鑰組 `ssh-keygen` 。 輸入檔案名，或使用括弧中所顯示的預設值（例如 `C:\Users\username/.ssh/id_rsa` ）。  輸入檔案的複雜密碼，如果您不想要使用複雜密碼，請將複雜密碼保留空白。 

```powershell
ssh-keygen -m PEM -t rsa -b 4096
```

## <a name="create-a-vm-using-your-key"></a>使用您的金鑰建立 VM

若要建立使用 SSH 金鑰進行驗證的 Linux VM，請在建立 VM 時提供您的 SSH 公開金鑰。

使用 Azure CLI，您可以使用和參數來指定公用金鑰的路徑和檔案名 `az vm create` `--ssh-key-value` 。

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVM \
   --image UbuntuLTS\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

透過 PowerShell，使用 `New-AzVM` ，並將 SSH 金鑰新增至 VM 設定。 如需範例，請參閱[快速入門：使用 PowerShell 在 Azure 中建立 Linux 虛擬機器](quick-create-powershell.md)。

如果您使用入口網站進行許多部署，您可能會想要將您的公開金鑰上傳至 Azure，從入口網站建立 VM 時，可以輕鬆地選取該金鑰。 如需詳細資訊，請參閱[上傳 SSH 金鑰](../ssh-keys-portal.md#upload-an-ssh-key)。


## <a name="connect-to-your-vm"></a>連接到您的 VM

公開金鑰已部署到您的 Azure VM 且私密金鑰儲存在本機系統中，此時可以使用 VM 的 IP 位址或 DNS 名稱，透過 SSH 連線到您的 VM。 將下列命令中的*azureuser*和*10.111.12.123*取代為系統管理員使用者名稱、IP 位址（或完整功能變數名稱），以及您的私密金鑰的路徑：

```bash
ssh -i ~/.ssh/id_rsa.pub azureuser@10.111.12.123
```

如果您在建立金鑰組時設定了複雜密碼，請在出現提示時輸入複雜密碼。

如果 VM 使用 Just-In-Time 存取原則，您必須先要求權限，才能連線到 VM。 如需 Just-In-Time 原則的詳細資訊，請參閱[使用 Just-In-Time 原則管理虛擬機器存取](../../security-center/security-center-just-in-time.md)。


## <a name="next-steps"></a>後續步驟

- 如需 Azure 入口網站中 SSH 金鑰的詳細資訊，請參閱在入口網站中建立 Vm 時要使用[的 Azure 入口網站中產生和儲存 ssh 金鑰](../ssh-keys-portal.md)。

- 如需使用 SSH 金鑰的詳細步驟、選項和 advanced 範例，請參閱[建立 ssh 金鑰組的詳細步驟](create-ssh-keys-detailed.md)。

- 您也可以在 Azure Cloud Shell 使用 PowerShell 來產生 SSH 金鑰，並建立 Linux VM 的 SSH 連線。 請參閱 [PowerShell 快速入門](../../cloud-shell/quickstart-powershell.md#ssh)。

- 如果您無法使用 SSH 連線到 Linux VM，請參閱[針對 Azure Linux VM 的 SSH 連線進行疑難排解](../troubleshooting/troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json)。
