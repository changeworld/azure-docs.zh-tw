---
title: 教學課程 - 在虛擬網路中部署 Azure Spring Cloud
description: 在虛擬網路中部署 Azure Spring Cloud (VNet 插入)。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/21/2020
ms.custom: devx-track-java
ms.openlocfilehash: 9d72d60bd3a1ef23b8122b2bc5ba4f0c5c701254
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587718"
---
# <a name="tutorial-deploy-azure-spring-cloud-in-a-virtual-network"></a>教學課程：在虛擬網路中部署 Azure Spring Cloud

**本文適用於：** ✔️ Java ✔️ C#

本教學課程說明如何在虛擬網路中部署 Azure Spring Cloud 執行個體。 此部署有時稱為「VNet 插入」。

此部署可實現下列目標：

* 讓 Azure Spring Cloud 應用程式和服務執行階段可以與公司網路上的網際網路分隔開來。
* 讓 Azure Spring Cloud 可以與內部部署資料中心內的系統或其他虛擬網路中的 Azure 服務互動。
* 讓客戶得以控制 Azure Spring Cloud 的輸入和輸出網路通訊。

## <a name="prerequisites"></a>必要條件

根據 [在 Azure 入口網站上註冊資源提供者](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)的指示或執行下列 Azure CLI 命令，來註冊 Azure Spring Cloud 資源提供者 **Microsoft.AppPlatform** 和 **Microsoft.ContainerService**：

```azurecli
az provider register --namespace Microsoft.AppPlatform
az provider register --namespace Microsoft.ContainerService
```

## <a name="virtual-network-requirements"></a>虛擬網路需求

您作為 Azure Spring Cloud 執行個體部署目的地的虛擬網路必須符合下列需求：

* **位置**：虛擬網路必須位於與 Azure Spring Cloud 執行個體相同的位置。
* 訂用帳戶：虛擬網路必須位於與 Azure Spring Cloud 執行個體相同的訂用帳戶。
* **子網路**：虛擬網路必須包含兩個專用於 Azure Spring Cloud 執行個體的子網路：

    * 一個用於服務執行階段。
    * 一個用於 Spring Boot 微服務應用程式。
    * 這些子網路與 Azure Spring Cloud 執行個體之間有一對一的關聯性。 針對您部署的每個服務執行個體使用新的子網路。 每個子網路只能包含單一服務執行個體。
* **位址空間**：最高 /28 的 CIDR 區塊用於服務執行階段子網路和 Spring Boot 微服務應用程式子網路。
* **路由表**：子網路不能有相關聯的現有路由表。

下列程序描述如何為包含 Azure Spring Cloud 執行個體的虛擬網路進行設定。

## <a name="create-a-virtual-network"></a>建立虛擬網路

如果您已經有可裝載 Azure Spring Cloud 執行個體的虛擬網路，請略過步驟 1、2 和 3。 您可以從步驟 4 開始，以準備虛擬網路的子網路。

1. 在 Azure 入口網站功能表上，選取 [建立資源]。 從 Azure Marketplace 選取 [網路] > [虛擬網路]。

1. 在 [建立虛擬網路] 對話方塊中，輸入或選取下列資訊：

    |設定          |值                                             |
    |-----------------|--------------------------------------------------|
    |訂用帳戶     |選取您的訂用帳戶。                         |
    |資源群組   |選取您的資源群組，或建立新的資源群組。  |
    |Name             |輸入 **azure-spring-cloud-vnet**。                 |
    |Location         |選取 [美國東部]  。                               |

1. 完成時，選取 [下一步:IP 位址]。

1. 在 IPv4 位址空間中，輸入 **10.1.0.0/16**。

1. 選取 [新增子網路]。 接著輸入 **service-runtime-subnet** 作為 **子網路名稱**，並輸入 **10.1.0.0/28** 作為 **子網路位址範圍**。 然後選取 [新增]  。

1. 再次選取 [新增子網路]，然後輸入 [子網路名稱] 和 [子網路位址範圍]。 例如，輸入 **apps-subnet** 和 **10.1.1.0/28**。 然後選取 [新增]  。

1. 選取 [檢閱 + 建立]。 將其他項目保留為預設值，然後選取 [建立]。

## <a name="grant-service-permission-to-the-virtual-network"></a>將服務權限授與給虛擬網路

選取您先前建立的虛擬網路 [azure-spring-cloud-vnet]。

1. 選取 [存取控制 (IAM)]，然後選取 [新增] > [新增角色指派]。

    ![顯示 [存取原則] 畫面的螢幕擷取畫面。](./media/spring-cloud-v-net-injection/access-control.png)

1. 在 [新增角色指派] 對話方塊中，輸入或選取下列資訊：

    |設定  |值                                             |
    |---------|--------------------------------------------------|
    |角色     |選取 [擁有者]。                                 |
    |選取   |輸入 **Azure Spring Cloud 資源提供者**。   |

    接著選取 [Azure Spring Cloud 資源提供者]，然後選取 [儲存]。

    ![顯示選取 Azure Spring Cloud 資源提供者的螢幕擷取畫面。](./media/spring-cloud-v-net-injection/grant-azure-spring-cloud-resource-provider-to-vnet.png)

您也可以執行下列 Azure CLI 命令執行此步驟：

```azurecli
VIRTUAL_NETWORK_RESOURCE_ID=`az network vnet show \
    --name ${NAME_OF_VIRTUAL_NETWORK} \
    --resource-group ${RESOURCE_GROUP_OF_VIRTUAL_NETWORK} \
    --query "id" \
    --output tsv`

az role assignment create \
    --role "Owner" \
    --scope ${VIRTUAL_NETWORK_RESOURCE_ID} \
    --assignee e8de9221-a19c-4c81-b814-fd37c6caf9d2
```

## <a name="deploy-an-azure-spring-cloud-instance"></a>部署 Azure Spring Cloud 執行個體

在虛擬網路中部署 Azure Spring Cloud 執行個體：

1. 開啟 [Azure 入口網站](https://portal.azure.com)。

1. 在頂端的搜尋方塊中，搜尋 **Azure Spring Cloud**。 從結果中選取 **Azure Spring Cloud**。

1. 在 [Azure Spring Cloud] 頁面上，選取 [+ 新增]。

1. 填寫 Azure Spring Cloud [建立] 頁面上的表單。

1. 選取與虛擬網路相同的資源群組和區域。

1. 針對 [服務詳細資料] 底下的 [名稱]，請選取 [azure-spring-cloud-vnet]。

1. 選取 [網路] 索引標籤，然後選取下列值：

    |設定                                |值                                             |
    |---------------------------------------|--------------------------------------------------|
    |在您自己的虛擬網路中部署     |選取 [是]  。                                   |
    |虛擬網路                        |選取 [azure-spring-cloud-vnet]。               |
    |服務執行階段子網路                 |選取 [service-runtime-subnet]。                |
    |Spring Boot 微服務應用程式子網路   |選取 [apps-subnet]。                           |

    ![螢幕擷取畫面，顯示 Azure Spring Cloud 建立 頁面上的 [網路] 索引標籤。](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. 選取 [檢閱及建立]  。

1. 確認規格，然後選取 [建立]。

    ![顯示驗證規格的螢幕擷取畫面。](./media/spring-cloud-v-net-injection/verify-specifications.png)

部署之後，將會在訂用帳戶中另外建立兩個資源群組，以裝載 Azure Spring Cloud 執行個體的網路資源。 移至 [首頁]，然後從頂端功能表項目選取 [資源群組]，以尋找下列新資源群組。

名為 **ap-svc-rt_{service instance name}_{service instance region}** 的資源群組包含服務執行個體的服務執行階段網路資源。

  ![螢幕擷取畫面，顯示服務執行階段。](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

名為 **ap-app_{service instance name}_{service instance region}** 的資源群組包含服務執行個體的 Spring Boot 微服務應用程式網路資源。

  ![顯示應用程式資源群組的螢幕擷取畫面。](./media/spring-cloud-v-net-injection/apps-resource-group.png)

這些網路資源會連線到您在先前映像中建立的虛擬網路。

  ![顯示具有已連線裝置之虛擬網路的螢幕擷取畫面。](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > 資源群組會由 Azure Spring Cloud 完全受控。 「請勿」手動刪除或修改其中的任何資源。

## <a name="limitations"></a>限制

小型的子網路範圍會儲存 IP 位址，但會限制 Azure Spring Cloud 執行個體可以保存的應用程式執行個體數目上限。

| 應用程式子網路 CIDR | IP 總數 | 可用的 IP | 應用程式執行個體上限                                        |
| --------------- | --------- | ------------- | ------------------------------------------------------------ |
| /28             | 16        | 8             | <p> 具有 1 個核心的應用程式：96 <br/> 具有 2 個核心的應用程式：48<br/>  具有 3 個核心的應用程式：32 <br/> 具有 4 個核心的應用程式：24 </p> |
| /27             | 32        | 24            | <p> 具有 1 個核心的應用程式：228<br/> 具有 2 個核心的應用程式：144<br/>  具有 3 個核心的應用程式：96 <br/>  具有 4 個核心的應用程式：72</p> |
| /26             | 64        | 56            | <p> 具有 1 個核心的應用程式：500<br/> 具有 2 個核心的應用程式：336<br/>  具有 3 個核心的應用程式：224<br/>  具有 4 個核心的應用程式：168</p> |
| /25             | 128       | 120           | <p> 具有 1 個核心的應用程式：500<br> 具有 2 個核心的應用程式：500<br>  具有 3 個核心的應用程式：480<br>  具有 4 個核心的應用程式：360</p> |
| /24             | 256       | 248           | <p> 具有 1 個核心的應用程式：500<br/> 具有 2 個核心的應用程式：500<br/>  具有 3 個核心的應用程式：500<br/>  具有 4 個核心的應用程式：500</p> |

針對子網路，Azure 會保留五個 IP 位址，而 Azure Spring Could 至少需要四個位址。 至少需要九個 IP 位址，因此 /29 和 /30 無法使用。

服務執行階段子網路的最低大小為 /28。 此大小與應用程式執行個體的數目無關。

## <a name="next-steps"></a>後續步驟

[在 VNet 中將應用程式部署至 Azure Spring Cloud](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>另請參閱

- [針對 VNET 中的 Azure Spring Cloud 進行疑難排解](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [客戶在 VNET 中執行 Azure Spring Cloud 的責任](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)
