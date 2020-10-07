---
title: 在 Azure Private Link 中建立私人端點
description: 在本快速入門中，您會使用 Azure Resource Manager 範本 (ARM 範本) 來建立私人端點。
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: 553d36a86671617417b6b9b1ea47966c3ba3fdf6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "88705804"
---
# <a name="quickstart-create-a-private-endpoint-by-using-an-arm-template"></a>快速入門：使用 ARM 範本建立私人端點

在本快速入門中，您會使用 Azure Resource Manager 範本 (ARM 範本) 來建立私人端點。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

您也可以使用 [Azure 入口網站](create-private-endpoint-portal.md)、[Azure PowerShell](create-private-endpoint-powershell.md) 或 [Azure CLI](create-private-endpoint-cli.md) 來完成本快速入門。

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

您需要具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>檢閱範本

此範本會建立 Azure SQL Database 執行個體的私人端點。

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-private-endpoint-sql/)。

:::code language="json" source="~/quickstart-templates/101-private-endpoint-sql/azuredeploy.json":::

範本中定義了多個 Azure 資源：

- [**Microsoft.Sql/servers**](/azure/templates/microsoft.sql/servers)：含有範例資料庫的 SQL Database 執行個體。
- [**Microsoft.Sql/servers/databases**](/azure/templates/microsoft.sql/servers/databases)：範例資料庫。
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)：私人端點部署所在的虛擬網路。
- [**Microsoft.Network/privateEndpoints**](/azure/templates/microsoft.network/privateendpoints)：要存取 SQL Database 執行個體的私人端點。
- [**Microsoft.Network/privateDnsZones**](/azure/templates/microsoft.network/privatednszones)：用來解析私人端點 IP 位址的區域。
- [**Microsoft.Network/privateDnsZones/virtualNetworkLinks**](/azure/templates/microsoft.network/privatednszones/virtualnetworklinks)
- [**Microsoft.Network/privateEndpoints/privateDnsZoneGroups**](/azure/templates/microsoft.network/privateendpoints/privateDnsZoneGroups)：用來將私人端點與私人 DNS 區域產生關聯的區域群組。
- [**Microsoft.Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses)：用來存取虛擬機器的公用 IP 位址。
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)：虛擬機器的網路介面。
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)：用來測試私人端點與 SQL Database 執行個體之間私人連線的虛擬機器。

## <a name="deploy-the-template"></a>部署範本

以下說明如何將 ARM 範本部署至 Azure：

1. 若要登入 Azure 並開啟範本，請選取 [部署至 Azure]。 此範本會建立私人端點、SQL Database 的執行個體、網路基礎結構，以及要驗證的虛擬機器。

   [![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

2. 選取或建立資源群組。
3. 輸入 SQL 系統管理員的登入和密碼。
4. 輸入虛擬機器系統管理員的使用者名稱和密碼。
5. 閱讀條款及條件聲明。 若同意，請選取 [我同意上方所述的條款及條件] > [購買]。 部署可能需要 20 分鐘或更久的時間才能完成。

## <a name="validate-the-deployment"></a>驗證部署

> [!NOTE]
> ARM 範本會為虛擬機器 myVm<b>{uniqueid}</b> 資源以及為 SQL Database sqlserver<b>{uniqueid}</b> 資源產生唯一的名稱。 請以您產生的值替代 **{uniqueid}** 。

### <a name="connect-to-a-vm-from-the-internet"></a>從網際網路連線至 VM

從網際網路連線至 VM _myVm{uniqueid}_ ，如下所示：

1. 在入口網站的搜尋列中，輸入 _myVm{uniqueid}_ 。

2. 選取 [連接]。 [連線至虛擬機器] 隨即開啟。

3. 選取 [下載 RDP 檔案]。 Azure 會建立一個「遠端桌面通訊協定」( _.rdp_) 檔案，並下載至您的電腦。

4. 開啟下載的 .rdp 檔案。

   a. 如果出現提示，請選取 [連接]。

   b. 輸入您在建立 VM 時所指定的使用者名稱和密碼。

      > [!NOTE]
      > 您可能需要選取 [其他選擇] > [使用不同的帳戶]，以指定您在建立 VM 時輸入的認證。

5. 選取 [確定]。

6. 您可能會在登入過程中收到憑證警告。 如果您收到憑證警告，請選取 [是] 或 [繼續]。

7. 在 VM 桌面出現之後，將其最小化以回到您的本機桌面。

### <a name="access-the-sql-database-server-privately-from-the-vm"></a>從 VM 私下存取 SQL Database 伺服器

以下是使用私人端點從 VM 連線到 SQL Database 伺服器的方法。

1.  在 _myVM{uniqueid}_ 的遠端桌面中，開啟 PowerShell。
2.  輸入下列內容：nslookup sqlserver{uniqueid}.database.windows.net。 
    您將收到如下訊息：

    ```
      Server:  UnKnown
      Address:  168.63.129.16
      Non-authoritative answer:
      Name:    sqlserver.privatelink.database.windows.net
      Address:  10.0.0.5
      Aliases:  sqlserver.database.windows.net
    ```

3.  安裝 SQL Server Management Studio。
4.  在 [連線至伺服器] **** 中，輸入或選取這項資訊：
    - **伺服器類型**：選取 [資料庫引擎]。
    - **伺服器名稱**：選取 [sqlserver{uniqueid}.database.windows.net]。
    - **使用者名稱**：輸入在建立期間提供的使用者名稱。
    - **密碼**：輸入在建立期間提供的密碼。
    - **記住密碼**：選取 [是] **** 。

5.  選取 [連接]。
6.  從左側的功能表中移至 [資料庫]。
7.  (選擇性) 您可以從 _sample-db_ 建立或查詢資訊。
8.  關閉對 _myVm{uniqueid}_ 的遠端桌面連線。

## <a name="clean-up-resources"></a>清除資源

當您不再需要先前為私人端點建立的資源時，請刪除資源群組。 這會移除私人端點和所有相關資源。

呼叫 `Remove-AzResourceGroup` Cmdlet 以刪除資源群組：

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>後續步驟

深入了解 [Azure Private Link](private-link-overview.md)。
