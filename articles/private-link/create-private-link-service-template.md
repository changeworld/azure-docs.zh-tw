---
title: 在 Azure Private Link 中建立私人連結服務
description: 在本快速入門中，您將使用 Azure Resource Manager 範本來建立私人連結服務。
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/29/2020
ms.author: allensu
ms.openlocfilehash: c9ed628501e8fa02b816a1564b91620404dfc379
ms.sourcegitcommit: 1383842d1ea4044e1e90bd3ca8a7dc9f1b439a54
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/16/2020
ms.locfileid: "84817626"
---
# <a name="quickstart-create-a-private-link-service-by-using-an-azure-resource-manager-template"></a>快速入門：使用 Azure Resource Manager 範本建立私人連結服務

在本快速入門中，您將使用 Azure Resource Manager 範本來建立私人連結服務。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

您也可以使用 [Azure 入口網站](create-private-link-service-portal.md)、[Azure PowerShell](create-private-link-service-powershell.md) 或 [Azure CLI](create-private-link-service-cli.md) 來完成本快速入門。

## <a name="prerequisite"></a>必要條件

您需要具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-private-link-service"></a>建立私人連結服務

此範本會建立私人連結服務。

### <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/)。

:::code language="json" source="~/quickstart-templates/101-privatelink-service/azuredeploy.json" range="001-432" highlight="263-289":::

範本中定義了多個 Azure 資源：

- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)：每部虛擬機器都有一個虛擬網路。
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers)：此負載平衡器會公開裝載服務的虛擬機器。
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)：網路介面有兩個，每部虛擬機器各一個。
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)：虛擬機器有兩部，一部會裝載服務，一部會測試與私人端點的連線。
- [**Microsoft.Compute/virtualMachines/extensions**](/azure/templates/Microsoft.Compute/virtualMachines/extensions)：安裝網頁伺服器的延伸模組。
- [**Microsoft.Network/privateLinkServices**](/azure/templates/microsoft.network/privateLinkServices)：公開服務的私人連結服務。
- [**Microsoft.Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses)：公用 IP 位址有兩個，每部虛擬機器各一個。
- [**Microsoft.Network/privateendpoints**](/azure/templates/microsoft.network/privateendpoints)：用來存取服務的私人端點。

### <a name="deploy-the-template"></a>部署範本

以下是將 Azure Resource Manager 範本部署至 Azure 的方法：

1. 若要登入 Azure 並開啟範本，請選取 [部署至 Azure]。 此範本會建立虛擬機器、標準負載平衡器、私人連結服務、私人端點、網路，以及要驗證的虛擬機器。

   [![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

2. 選取或建立資源群組。
3. 輸入虛擬機器系統管理員的使用者名稱和密碼。
4. 閱讀條款及條件聲明。 若同意，請選取 [我同意上方所述的條款及條件] > [購買]。

## <a name="validate-the-deployment"></a>驗證部署

> [!NOTE]
> Azure Resource Manager 範本會為虛擬機器 myConsumerVm<b>{uniqueid}</b> 資源產生唯一的名稱。 請以您產生的值替代 **{uniqueid}** 。

### <a name="connect-to-a-vm-from-the-internet"></a>從網際網路連線至 VM

從網際網路連線至 VM _myConsumerVm{uniqueid}_ ，如下所示：

1.  在入口網站的搜尋列中，輸入 _myConsumerVm{uniqueid}_ 。

2.  選取 [連接]。 [連線至虛擬機器] 隨即開啟。

3.  選取 [下載 RDP 檔案]。 Azure 會建立一個「遠端桌面通訊協定」( _.rdp_) 檔案，並下載至您的電腦。

4.  開啟下載的 .rdp 檔案。

    a. 如果出現提示，請選取 [連接]。

    b. 輸入您在建立 VM 時所指定的使用者名稱和密碼。
    
    > [!NOTE]
    > 您可能需要選取 [其他選擇] > [使用不同的帳戶]，以指定您在建立 VM 時輸入的認證。

5.  選取 [確定]。

6.  您可能會在登入過程中收到憑證警告。 如果您收到憑證警告，請選取 [是] 或 [繼續]。

7.  在 VM 桌面出現之後，將其最小化以回到您的本機桌面。

### <a name="access-the-http-service-privately-from-the-vm"></a>從 VM 私下存取 HTTP 服務

以下是使用私人端點從 VM 連線到 HTTP 服務的方法。

1.  移至 _myConsumerVm{uniqueid}_ 的遠端桌面。
2.  開啟瀏覽器，然後輸入私人端點位址： http://10.0.0.5/ 。
3.  預設的 IIS 頁面隨即出現。

## <a name="clean-up-resources"></a>清除資源

當您不再需要先前為私人連結服務建立的資源時，請刪除資源群組。 這會移除私人連結服務和所有相關資源。

呼叫 `Remove-AzResourceGroup` Cmdlet 以刪除資源群組：

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>後續步驟

深入了解 [Azure Private Link](private-link-overview.md)。
