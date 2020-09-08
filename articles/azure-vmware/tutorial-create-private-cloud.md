---
title: 教學課程 - 在 Azure 中部署 vSphere 叢集
description: 了解如何使用 Azure VMWare 解決方案在 Azure 中部署 vSphere 叢集
ms.topic: tutorial
ms.date: 09/07/2020
ms.openlocfilehash: 69a29a459ba283bb34169112ac2fa174ac6a14af
ms.sourcegitcommit: 8791f69d44150767807d215cafc4076f3ed43f9f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/08/2020
ms.locfileid: "89512352"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud-in-azure"></a>教學課程：在 Azure 中部署 Azure VMware 解決方案私人雲端

Azure VMware 解決方案讓您能夠在 Azure 中部署 vSphere 叢集。 初始部署的最小值為三部主機。 您可以一次加入一部額外的主機，每個叢集最多 16 部主機。 

由於 Azure VMware 解決方案不允許您在啟動時使用內部部署 vCenter 來管理私人雲端，所以必須執行額外的組態，並連線到本機 vCenter 執行個體、虛擬網路等等。 本教學課程涵蓋這些程序和相關的必要條件。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立 Azure VMware 解決方案私人雲端
> * 確認已部署私人雲端

## <a name="prerequisites"></a>Prerequisites

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 適當的系統管理權和權限可建立私人雲端。
- 請確定您已設定適當的網路，如[教學課程：](tutorial-network-checklist.md)網路檢查清單中所述。

## <a name="register-the-resource-provider"></a>註冊資源提供者

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="create-a-private-cloud"></a>建立私人雲端

您可以使用 [Azure 入口網站](#azure-portal)，或使用 [Azure CLI](#azure-cli) 來建立 Azure VMware 解決方案私人雲端。

### <a name="azure-portal"></a>Azure 入口網站

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-avs-private-cloud-azure-portal-steps.md)]

### <a name="azure-cli"></a>Azure CLI

您可以使用 Azure Cloud Shell 來使用 Azure CLI，而不是使用 Azure 入口網站來建立 Azure VMware 解決方案私人雲端。 Azure Cloud Shell 是一個免費的互動式殼層，具有預先安裝和設定的通用 Azure 工具，可與您的帳戶搭配使用。 

#### <a name="open-azure-cloud-shell"></a>開啟 Azure Cloud Shell

若要開啟 Cloud Shell，請選取程式碼區塊右上角的 [試試看]。 您也可以移至 [https://shell.azure.com/bash](https://shell.azure.com/bash)，從另一個瀏覽器索引標籤啟動 Cloud Shell。 選取 [複製]  即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後按 **Enter** 鍵加以執行。

#### <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group) 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組：

```
azurecli-interactive
az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>建立私人雲端

提供資源組名稱、私人雲端名稱、位置，以及叢集大小。

| 屬性  | 說明  |
| --------- | ------------ |
| **-g** (資源群組名稱)     | 私人雲端資源的資源群組名稱。        |
| **-n** (私人雲端名稱)     | Azure VMware 解決方案私人雲端的名稱。        |
| **--location**     | 用於私人雲端的位置。         |
| **--cluster-size**     | 叢集大小。 最小值為 3。         |
| **--network-block**     | 要用於私人雲端的 CIDR IP 位址網路區塊。 位址區塊不應與其他虛擬網路中使用的位址區塊重疊，而這些虛擬網路位於您的訂用帳戶和內部部署網路中。        |
| **--sku** | SKU 值：AV36 |

```
azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="delete-a-private-cloud-azure-portal"></a>刪除私人雲端 (Azure 入口網站)

如果您有不再需要的 Azure VMware 解決方案私人雲端，可以將其刪除。 刪除私人雲端時，會刪除所有的叢集及其所有元件。

若要這麼做，請瀏覽至 Azure 入口網站中的私人雲端，然後選取 [刪除]。 在確認頁面上，確認私人雲端的名稱，然後選取 [是]。

> [!CAUTION]
> 刪除私人雲端是無法復原的作業。 一旦刪除私人雲端，就無法復原資料，因為此舉會終止所有執行中的工作負載和元件，並終結所有的私人雲端資料和組態設定，包括公用 IP 位址。 

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 建立 Azure VMware 解決方案私人雲端
> * 驗證已部署私人雲端

繼續進行下一個教學課程，以了解如何在設定私人雲端叢集的本機管理時，建立虛擬網路以與私人雲端搭配使用。

> [!div class="nextstepaction"]
> [建立虛擬網路](tutorial-configure-networking.md)
