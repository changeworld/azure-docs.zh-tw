---
title: 教學課程 - 在 Azure 中部署 vSphere 叢集
description: 了解如何使用 Azure VMWare 解決方案 (AVS) 在 Azure 中部署 vSphere 叢集
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: fc753f43563650357cf43c102e94f0057b62a406
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873742"
---
# <a name="tutorial-deploy-an-avs-private-cloud-in-azure"></a>教學課程：在 Azure 中部署 AVS 私人雲端

Azure VMware 解決方案 (AVS) 讓您能夠在 Azure 中部署 vSphere 叢集。 初始部署的最小值為三部主機。 您可以一次加入一部額外的主機，每個叢集最多 16 部主機。 

由於 AVS 不允許您在啟動時使用內部部署 vCenter 來管理私人雲端，所以必須執行額外的組態，並連線到本機 vCenter 執行個體、虛擬網路等等。 本教學課程系列將涵蓋這些程序和相關的必要條件。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立 AVS 私人雲端
> * 確認已部署私人雲端

## <a name="prerequisites"></a>Prerequisites

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 適當的系統管理權和權限可建立私人雲端。
- 請確定您已設定適當的網路，如[教學課程：](tutorial-network-checklist.md)網路檢查清單中所述。

## <a name="register-the-resource-provider"></a>註冊資源提供者

您必須先註冊資源提供者，才能使用 Azure VMware 解決方案。 下列範例會向您的訂用帳戶註冊資源提供者。

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

如需註冊資源提供者的其他方式，請參閱 [Azure 資源提供者和類型](../azure-resource-manager/management/resource-providers-and-types.md)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-a-private-cloud"></a>建立私人雲端

您可以使用 [Azure 入口網站](#azure-portal)，或使用 [Azure CLI](#azure-cli) 來建立 AVS 私人雲端。

### <a name="azure-portal"></a>Azure 入口網站

在 Azure 入口網站中，選取 [建立新資源]。 在 [搜尋 Marketplace] 文字方塊中，輸入 `Azure VMware Solution`，然後從清單中選取 [Azure VMware 解決方案]。 在 [Azure VMware 解決方案] 視窗中，選取 [建立]

在 [基本概念] 索引標籤上，輸入欄位的值。 下表顯示屬性的詳細清單。

| 欄位   | 值  |
| ---| --- |
| **訂用帳戶** | 您計畫用於部署的訂用帳戶。|
| **資源群組** | 私人雲端資源的資源群組。 |
| **位置** | 選取一個位置，例如 [美國東部]。|
| **資源名稱** | 您的 AVS 私人雲端名稱。 |
| **SKU** | 選取下列 SKU 值：AV36 |
| **主控件** | 這是要新增至私人雲端叢集的主機數目。 預設值是 3。 您可在部署之後提高或降低此值。  |
| **vCenter 管理密碼** | 輸入雲端管理員密碼。 |
| **NSX-T 管理員密碼** | 輸入 NSX-T 管理員密碼。 |
| **位址封鎖** | 為私人雲端輸入 CIDR 網路的 IP 位址區塊。 例如 10.175.0.0/22。 |

:::image type="content" source="./media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="建立私人雲端" border="true":::

在完成後，選取 [檢閱+建立]。 在下一個畫面上，確認輸入的資訊。 如果資訊都正確無誤，請選取 [建立]。

> [!NOTE]
> 這個步驟需要大約 2 小時。 

### <a name="azure-cli"></a>Azure CLI

或者，您可以使用 Azure CLI 在 Azure 中建立 AVS 私人雲端。 若要這麼做，您可以使用 Azure Cloud Shell，下列步驟將示範如何執行這項操作。

#### <a name="open-azure-cloud-shell"></a>開啟 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看] 即可。 您也可以移至 https://shell.azure.com/bash ，從另一個瀏覽器索引標籤啟動 Cloud Shell。 選取 [複製] 即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後按 **Enter** 鍵加以執行。

#### <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group) 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組：

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>建立私人雲端

若要建立 AVS 私人雲端，您必須提供資源組名稱、私人雲端名稱、位置，以及叢集大小


|屬性  |描述  |
|---------|---------|
|資源群組名稱     | 您要部署私人雲端之資源群組的名稱。        |
|私人雲端名稱     | 私人雲端的名稱。        |
|Location     | 用於私人雲端的位置         |
|叢集大小     | 叢集大小。 最小值為 3。         |
|網路區塊     | 要用於私人雲端的 CIDR 範圍。 建議此值在內部部署環境和 Azure 環境中均為唯一值。        |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22
```

## <a name="verify-deployment-was-successful"></a>確認部署成功

瀏覽至您建立的資源群組，然後選取您的私人雲端，當部署完成時，您會看到下列畫面，以及**成功**的狀態。

:::image type="content" source="./media/tutorial-create-private-cloud/validate-deployment.png" alt-text="驗證已部署私人雲端" border="true":::

## <a name="delete-a-private-cloud"></a>刪除私人雲端

如果您確定不再需要 AVS 私用雲端，可以將其刪除。 刪除私人雲端時，會刪除所有的叢集及其所有元件。

若要這麼做，請瀏覽至 Azure 入口網站中的私人雲端，然後選取 [刪除]。 在確認頁面上，確認私人雲端的名稱，然後選取 [是]。

> [!CAUTION]
> 刪除私人雲端是無法復原的作業。 一旦刪除私人雲端，就無法復原資料，因為此舉會終止所有執行中的工作負載和元件，並終結所有的私人雲端資料和組態設定，包括公用 IP 位址。 

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 建立 AVS 私人雲端
> * 驗證已部署私人雲端

繼續進行下一個教學課程，以了解如何在設定私人雲端叢集的本機管理時，建立虛擬網路以與私人雲端搭配使用。

> [!div class="nextstepaction"]
> [建立虛擬網路](tutorial-configure-networking.md)
