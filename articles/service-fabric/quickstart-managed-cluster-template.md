---
title: 使用 Azure Resource Manager 部署 Service Fabric 受控叢集 (預覽)
description: 了解如何使用 Azure Resource Manager 範本建立 Service Fabric 受控叢集
ms.topic: quickstart
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: 079e8e3e39bd306332abb43fa2ae1f8cc04e305b
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410392"
---
# <a name="quickstart-deploy-a-service-fabric-managed-cluster-preview-with-an-azure-resource-manager-template"></a>快速入門：使用 Azure Resource Manager 範本部署 Service Fabric 受控叢集 (預覽)

Service Fabric 受控叢集是 Azure Service Fabric 叢集資源模型的演進，可簡化您的部署和叢集管理體驗。 Service Fabric 受控叢集是完全封裝的資源，可讓您部署單一 Service Fabric 叢集資源，而不需要部署組成 Service Fabric 叢集的所有基礎資源。 本文說明如何使用 Azure Resource Manager 範本 (ARM 範本) 在 Azure 中部署 Service Fabric 受控叢集以進行測試。

在本教學課程中部署的三節點基本 SKU 叢集僅供教學用途 (而非生產工作負載)。 如需詳細資訊，請參閱 [Service Fabric 受控叢集 SKU](overview-managed-cluster.md#service-fabric-managed-cluster-skus)。

## <a name="prerequisites"></a>必要條件

開始進行本快速入門之前：

* 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 範本 - Service Fabric 叢集範本](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Basic-SKU-1-NT)。

## <a name="create-a-client-certificate"></a>建立用戶端憑證

Service Fabric 受控叢集會使用用戶端憑證做為存取控制的金鑰。 如果您已有想要用於叢集存取控制的用戶端憑證，您可以跳過此步驟。

如果您需要建立新的用戶端憑證，請遵循[從 Azure Key Vault 設定及擷取憑證](../key-vault/certificates/quick-create-portal.md)中的步驟進行。

記下憑證指紋，因為在下一個步驟中部署範本時，將會需要這項功能。

## <a name="deploy-the-template"></a>部署範本

1. 選取以下影像來登入 Azure 並開啟範本。

      [![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fservice-fabric-cluster-templates%2Fmaster%2FSF-Managed-Basic-SKU-1-NT%2Fazuredeploy.json)

2. 選取或輸入下列值

    在本快速入門中，針對下列範本參數提供您自己的值︰

    * 訂用帳戶：選取 Azure 訂用帳戶。
    * **資源群組**：選取 [建立新的]。 輸入資源群組的唯一名稱 (例如 *myResourceGroup*)，然後選擇 [確定]。
    * **位置**：選取一個位置，例如 [eastus2]。 Service Fabric 受控叢集預覽的支援區域包括 `centraluseuap`、`eastus2euap`、`eastasia`、`northeurope`、`westcentralus` 和 `eastus2`。
    * **叢集名稱**：輸入您叢集的唯一名稱，例如 mysfcluster。
    * **管理使用者名稱**：輸入系統管理員的名稱，以用於叢集中基礎 VM 上的 RDP。
    * **管理員密碼**：輸入系統管理員的密碼，以用於叢集中基礎 VM 上的 RDP。
    * **用戶端憑證指紋**：提供您想要用來存取叢集的用戶端憑證指紋。 如果您沒有憑證，請遵循[設定並擷取憑證](../key-vault/certificates/quick-create-portal.md)來建立自我簽署憑證。
    * **節點類型名稱**：為節點類型輸入唯一的名稱，例如 nt1。
    * **我同意上方所述的條款及條件**：核取此方塊以表示同意。 

3. 選取 [購買]。

4. 您的 Service Fabric 受控叢集需要幾分鐘的時間才會部署。 等待部署順利完成後，再移至後續步驟。

## <a name="validate-the-deployment"></a>驗證部署

### <a name="review-deployed-resources"></a>檢閱已部署的資源

部署完成之後，請在輸出中尋找 Service Fabric Explorer 值，然後在網頁瀏覽器中開啟該網址，以在 Service Fabric Explorer 中檢視您的叢集。 當系統提示您輸入憑證時，請使用範本中所提供用戶端指紋的憑證。

> [!NOTE]
> 您可以在 Azure 入口網站的 [資源群組部署] 索引標籤下找到部署的輸出。

## <a name="clean-up-resources"></a>清除資源

當不再需要時，請刪除 Service Fabric 受控叢集的資源群組。 若要透過入口網站刪除資源群組：

1. 在入口網站頂端的 [搜尋] 方塊中，輸入資源群組的名稱。 當您在搜尋結果中看到本快速入門中使用的資源群組時，請加以選取。
2. 選取 [刪除資源群組]。
3. 在 [輸入資源群組名稱:] 方塊中輸入資源群組的名稱，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已部署 Service Fabric 受控叢集。 若要深入了解如何調整叢集，請參閱：

> [!div class="nextstepaction"]
> [擴增 Service Fabric 受控叢集](tutorial-managed-cluster-scale.md)
