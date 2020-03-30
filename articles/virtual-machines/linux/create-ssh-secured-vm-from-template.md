---
title: 從範本在 Azure 中創建 Linux VM
description: 如何使用 Azure CLI 從 Resource Manager 範本建立 Linux VM
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.openlocfilehash: 581eadc60835b758f67ae616d4413800f1d6d718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969516"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>如何使用 Azure Resource Manager 範本建立 Linux 虛擬機器

瞭解如何使用 Azure 資源管理器範本和 Azure 雲外殼中的 Azure CLI 創建 Linux 虛擬機器 （VM）。 要創建 Windows 虛擬機器，請參閱[從資源管理器範本創建 Windows 虛擬機器](../windows/ps-template.md)。

## <a name="templates-overview"></a>範本概觀

Azure Resource Manager 範本是 JSON 檔案，檔案中定義您的 Azure 解決方案的基礎結構和組態。 透過範本，您可以在整個生命週期中重複部署方案，並確信您的資源會以一致的狀態部署。 要瞭解有關範本格式及其構造方式的更多內容，請參閱[快速入門：使用 Azure 門戶創建和部署 Azure 資源管理器範本](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)。 若要檢視資源類型的 JSON 語法，請參閱[在 Azure Resource Manager 範本中定義資源](/azure/templates/microsoft.compute/allversions)。

## <a name="create-a-virtual-machine"></a>建立虛擬機器

創建 Azure 虛擬機器通常包括兩個步驟：

1. 建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 資源群組必須在虛擬機器之前建立。
1. 建立虛擬機器。

下面的示例從[Azure 快速啟動範本](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json)創建 VM。 此部署僅允許 SSH 身份驗證。 出現提示時，提供您自己 SSH 公開金鑰的值，例如 ~/.ssh/id_rsa.pub** 的內容。 如果您需要建立 SSH 金鑰組，請參閱[如何為 Azure 中的 Linux VM 建立和使用的 SSH 金鑰組](mac-create-ssh-keys.md)。 以下是範本的複本：

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

要運行 CLI 腳本，請選擇 **"嘗試嘗試"** 以打開 Azure 雲外殼。 要粘貼腳本，請按右鍵 shell，然後選擇 **"粘貼**：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the project name (used for generating resource names):" &&
read projectName &&
echo "Enter the administrator username:" &&
read username &&
echo "Enter the SSH public key:" &&
read key &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json --parameters projectName=$projectName adminUsername=$username adminPublicKey="$key" &&
az vm show --resource-group $resourceGroupName --name "$projectName-vm" --show-details --query publicIps --output tsv
```

最後一個 Azure CLI 命令顯示新創建的 VM 的公共 IP 位址。 您需要公共 IP 位址才能連接到虛擬機器。 請參閱本文的下一部分。

在先前範例中，您已指定存放在 GitHub 中的範本。 您也可以下載或建立範本，並用 `--template-file` 參數指定本機路徑。

以下是一些其他資源：

- 若要了解如何開發 Resource Manager 範本，請參閱 [Azure Resource Manager 文件](/azure/azure-resource-manager/)。
- 要查看 Azure 虛擬機器架構，請參閱[Azure 範本引用](/azure/templates/microsoft.compute/allversions)。
- 要查看更多虛擬機器範本示例，請參閱[Azure 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)。

## <a name="connect-to-virtual-machine"></a>連線至虛擬機器

之後您便可以如常透過 SSH 連線至您的 VM。 提供您自己的公用 IP 位址 (透過先前命令取得)：

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>後續步驟

在此範例中，您建立基本的 Linux VM。 有關包含應用程式框架或創建更複雜的環境的更多資源管理器範本，請流覽[Azure 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)。

若要深入了解如何建立範本，請針對您部署的資源類型檢視 JSON 語法和屬性：

- [微軟.網路/網路安全性群組](/azure/templates/microsoft.network/networksecuritygroups)
- [微軟.網路/公共IP位址](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
