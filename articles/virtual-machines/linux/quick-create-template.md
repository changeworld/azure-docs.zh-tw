---
title: 快速入門：使用 Resource Manager 範本建立 Ubuntu Linux VM
description: 在此快速入門中，您會了解如何使用 Resource Manager 範本建立 Linux 虛擬機器
author: cynthn
ms.service: virtual-machines-linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 06/04/2020
ms.author: cynthn
ms.custom: subject-armqs
ms.openlocfilehash: 0e9ce74d62bb45c84f8bca2d71579b05c0ba656a
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2020
ms.locfileid: "84485707"
---
# <a name="quickstart-create-an-ubuntu-linux-virtual-machine-using-a-resource-manager-template"></a>快速入門：使用 Resource Manager 範本建立 Ubuntu Linux 虛擬機器

此快速入門會向您說明如何在 Azure 中使用 Resource Manager 範本部署 Ubuntu Linux 虛擬機器 (VM)。 

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。


## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/)。

:::code language="json" source="~/quickstart-templates/101-vm-simple-linux/azuredeploy.json" range="1-261" highlight="110-260":::


範本中定義了幾個資源：

- [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/Microsoft.Network/virtualNetworks/subnets)：建立子網路。
- [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts)：建立儲存體帳戶。
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/Microsoft.Network/publicIPAddresses)：建立公用 IP 位址。
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/Microsoft.Network/networkSecurityGroups)：建立網路安全性群組。
- [**Microsoft.Network/virtualNetworks**](/azure/templates/Microsoft.Network/virtualNetworks)：建立虛擬網路。
- [**Microsoft.Network/networkInterfaces**](/azure/templates/Microsoft.Network/networkInterfaces)：建立 NIC。
- [**Microsoft.Compute/virtualMachines**](/azure/templates/Microsoft.Compute/virtualMachines)：建立虛擬機器。



## <a name="deploy-the-template"></a>部署範本

1. 選取以下影像來登入 Azure 並開啟範本。 此範本會建立金鑰保存庫和祕密。

    [![部署至 Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-linux%2fazuredeploy.json)

1. 選取或輸入下列值。 使用預設值 (如果有的話)。

    - **訂用帳戶**：選取 Azure 訂用帳戶。
    - **資源群組**：從下拉式清單選取現有的群組，或選取 [新建] 並輸入資源群組的唯一名稱，然後按一下 [確定]。
    - **位置**：選取位置。  例如，**美國中部**。
    - **系統管理員使用者名稱**：提供使用者名稱，例如 *azureuser*。
    - **驗證類型**：您可以選擇使用 SSH 金鑰或密碼。
    - **系統管理員密碼或金鑰** (視您選擇的驗證類型而定)：
        - 如果您選擇 **password**，密碼長度至少必須有 12 個字元，且必須符合[定義的複雜性需求](faq.md#what-are-the-password-requirements-when-creating-a-vm)。
        - 如果您選擇 **sshPublicKey**，請貼上公開金鑰的內容。
    - **DNS 標籤首碼**：輸入要作為 DNS 標籤一部分使用的唯一識別碼。
    - **Ubuntu OS 版本**：選取您想要在 VM 上執行的 Ubuntu 版本。
    - **位置**：預設值為與資源群組相同的位置 (如果其已存在)。
    - **VM 大小**：選取要用於 VM 的[大小](sizes.md)。
    - **虛擬網路名稱**：要用於 vNet 的名稱。
    - **子網路名稱**：VM 應使用之子網路的名稱。
    - **網路安全性群組名稱**：NSG 的名稱。
1. 選取 [檢閱 + 建立]。 驗證完成後，選取 [建立] 以建立及部署 VM。


Azure 入口網站用於部署範本。 除了 Azure 入口網站以外，您也可以使用 Azure CLI、Azure PowerShell 與 REST API。 若要了解其他部署方法，請參閱[部署範本](../../azure-resource-manager/templates/deploy-cli.md)。

## <a name="review-deployed-resources"></a>檢閱已部署的資源

您可以使用 Azure 入口網站檢查已建立的 VM 與其他資源。 部署完成之後，請選取 [前往資源群組] 以查看 VM 與其他資源。


## <a name="clean-up-resources"></a>清除資源

如果不再需要，請刪除資源群組，這會刪除資源群組中的 VM 與所有資源。 

1. 選取 [資源群組]。
1. 在資源群組的頁面上，選取 [刪除]。
1. 出現提示時，請輸入資源群組的名稱，然後選取 [刪除]。


## <a name="next-steps"></a>後續步驟

在此快速入門中，您已使用 Resource Manager 範本部署一個簡單的虛擬機器。 若要深入了解 Azure 虛擬機器，請繼續 Linux VM 的教學課程。


> [!div class="nextstepaction"]
> [Azure Linux 虛擬機器教學課程](./tutorial-manage-vm.md)