---
title: 部署 Service Fabric 受控叢集 (預覽)
description: 在本教學課程中，您將部署 Service Fabric 受控叢集以進行測試。
ms.topic: tutorial
ms.date: 08/27/2020
ms.custom: references_regions
ms.openlocfilehash: 0265a1393c697cbd767de13df1064a5eea957380
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92316177"
---
# <a name="tutorial-deploy-a-service-fabric-managed-cluster-preview"></a>教學課程：部署 Service Fabric 受控叢集 (預覽)

在本教學課程系列中，我們將討論：

> [!div class="checklist"]
> * 如何部署 Service Fabric 受控叢集 
> * [如何擴增 Service Fabric 受控叢集](tutorial-managed-cluster-scale.md)
> * [如何新增及移除 Service Fabric 受控叢集中的節點](tutorial-managed-cluster-add-remove-node-type.md)
> * [如何將應用程式部署到 Service Fabric 受控叢集](tutorial-managed-cluster-deploy-app.md)

本系列的這個部分涵蓋如何：

> [!div class="checklist"]
> * 連線至您的 Azure 帳戶
> * 建立新的資源群組
> * 部署 Service Fabric 受控叢集
> * 將主要節點類型新增至叢集

## <a name="prerequisites"></a>必要條件

開始進行本教學課程之前：

* 如果您還沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* 安裝 [Service Fabric SDK 和 PowerShell 模組](service-fabric-get-started.md)。

* 安裝 [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps?preserve-view=true&view=azps-4.7.0#azservicefabric) (或更新版本)。

## <a name="connect-to-your-azure-account"></a>連線至您的 Azure 帳戶

將 `<your-subscription>` 取代為 Azure 儲存體帳戶的訂用帳戶字串，然後連線：

```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <your-subscription>

```

## <a name="create-a-new-resource-group"></a>建立新的資源群組

接下來，建立受控 Service Fabric 叢集的資源群組，以所需的群組名稱和位置取代 `<your-rg>` 和 `<location>`。

> [!NOTE]
> 公開預覽版支援的區域包括 `centraluseuap`、`eastus2euap`、`eastasia`、`northeurope`、`westcentralus` 和 `eastus2`。

```powershell
$resourceGroup = "myResourceGroup"
$location = "EastUS2"

New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="deploy-a-service-fabric-managed-cluster"></a>部署 Service Fabric 受控叢集

### <a name="create-a-service-fabric-managed-cluster"></a>建立 Service Fabric 受控叢集

在此步驟中，您將使用 New-AzServiceFabricManagedCluster PowerShell 命令來建立 Service Fabric 受控叢集。 下列範例會在名為 myResourceGroup 的資源群組中建立名為 myCluster 的叢集。 在先前的步驟中，我們已在 eastus2 區域建立此資源群組。

在此步驟中，請針對下列參數提供您自己的值︰

* **叢集名稱**：輸入您叢集的唯一名稱，例如 mysfcluster。
* **管理員密碼**：輸入系統管理員的密碼，以用於叢集中基礎 VM 上的 RDP。
* **用戶端憑證指紋**：提供您想要用來存取叢集的用戶端憑證指紋。 如果您沒有憑證，請遵循[設定並擷取憑證](../key-vault/certificates/quick-create-portal.md)來建立自我簽署憑證。
* **叢集 SKU**：指定要部署 [Service Fabric 受控叢集的類型](overview-managed-cluster.md#service-fabric-managed-cluster-skus)。 「基本」SKU 叢集僅適用於測試部署，不允許新增或移除節點類型。

```powershell
$clusterName = "<unique cluster name>"
$password = "Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$clientThumbprint = "<certificate thumbprint>"
$clusterSku = "Standard"

New-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroup -Location $location -ClusterName $clusterName -ClientCertThumbprint $clientThumbprint -ClientCertIsAdmin -AdminPassword $password -Sku $clusterSKU -Verbose
```

### <a name="add-a-primary-node-type-to-the-service-fabric-managed-cluster"></a>將主要節點類型新增至 Service Fabric 受控叢集

在此步驟中，您會將主要節點類型新增至您剛才建立的叢集。 每個 Service Fabric 叢集必須至少有一個主要節點類型。

在此步驟中，請針對下列參數提供您自己的值︰

* **節點類型名稱**：輸入要新增至叢集的節點類型唯一名稱，例如 "NT1"。

> [!NOTE]
> 如果要新增的節點類型是叢集中的第一個或唯一節點類型，則必須使用主要屬性。

```powershell
$nodeType1Name = "NT1"

New-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -Name $nodeType1Name -Primary -InstanceCount 5
```

此命令可能需要數分鐘才能完成。

## <a name="validate-the-deployment"></a>驗證部署

### <a name="review-deployed-resources"></a>檢閱已部署的資源

部署完成之後，請在入口網站的 Service Fabric 受控叢集資源概觀頁面中尋找 Service Fabric Explorer 值。 當系統提示您輸入憑證時，請使用 PowerShell 命令中所提供用戶端指紋的憑證。

## <a name="next-steps"></a>後續步驟

在此步驟中，我們已建立及部署我們的第一個 Service Fabric 受控叢集。 若要深入了解如何調整叢集，請參閱：

> [!div class="nextstepaction"]
> [擴增 Service Fabric 受控叢集](tutorial-managed-cluster-scale.md)