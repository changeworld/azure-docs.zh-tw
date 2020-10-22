---
title: 教學課程 - 在虛擬網路中部署 Azure Spring Cloud
description: 在虛擬網路中部署 Azure Spring Cloud (VNet 插入)。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/21/2020
ms.custom: devx-track-java
ms.openlocfilehash: f1a6a99285e54338b0020aad63fef2944ce3469d
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92088664"
---
# <a name="tutorial-deploy-azure-spring-cloud-in-azure-virtual-network-vnet-injection"></a>教學課程：在 Azure 虛擬網路中部署 Azure Spring Cloud (VNet 插入)

**本文適用於：** ✔️ Java ✔️ C#

本教學課程說明如何在虛擬網路中部署 Azure Spring Cloud 服務執行個體。 這有時稱為「VNet 插入」。  

此部署可實現下列目標：

* 讓 Azure Spring Cloud 應用程式和服務執行階段可以與公司網路上的網際網路分隔開來
* 讓 Azure Spring Cloud 可以與內部部署資料中心內的系統和/或其他虛擬網路中的 Azure 服務互動
* 讓客戶得以控制 Azure Spring Cloud 的輸入和輸出網路通訊

## <a name="prerequisites"></a>必要條件
您必須根據[在 Azure 入口網站上註冊資源提供者](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)的指示或執行下列 az CLI 命令，來註冊 Azure Spring Cloud 資源提供者 `Microsoft.AppPlatform`：

```azurecli
az provider register --namespace Microsoft.AppPlatform
```
## <a name="virtual-network-requirements"></a>虛擬網路需求
您作為 Azure Spring Cloud 服務執行個體部署目的地的虛擬網路必須符合下列需求：

* **位置**：虛擬網路必須位於與 Azure Spring Cloud 服務執行個體相同的位置。
* 訂用帳戶：虛擬網路必須位於與 Azure Spring Cloud 服務執行個體相同的訂用帳戶。
* **子網路**：虛擬網路必須包含兩個專用於 Azure Spring Cloud 服務執行個體的子網路： 
    * 一個用於服務執行階段
    * 一個用於 Spring Boot 微服務應用程式。 
    * 這些子網路與 Azure Spring Cloud 服務執行個體之間有一對一的關聯性。 您必須針對所部署的每個服務執行個體使用新的子網路，且每個子網路只能包含單一服務執行個體。
* **位址空間**：一個最高 /28 的 CIDR 區塊用於服務執行階段子網路，另一個最高 /24 的 CIDR 區塊用於 Spring Boot 微服務應用程式子網路。
* **路由表**：子網路不能有相關聯的現有路由表。

下列程序描述如何為包含 Azure Spring Cloud 執行個體的虛擬網路進行設定。

## <a name="create-a-virtual-network"></a>建立虛擬網路
如果您已經有可裝載 Azure Spring Cloud 服務執行個體的虛擬網路，請略過步驟 1、2 和 3。 您可以從步驟 4 開始，以準備虛擬網路的子網路。

1. 從 Azure 入口網站功能表選取 [建立資源]。 從 Azure Marketplace 選取 [網路] > [虛擬網路]。

1. 在 [建立虛擬網路] 對話方塊中，輸入或選取下列資訊：

    |設定          |值                                             |
    |-----------------|--------------------------------------------------|
    |訂用帳戶     |選取您的訂用帳戶。                         |
    |資源群組   |選取您的資源群組，或建立新的資源群組。  |
    |Name             |輸入 *azure-spring-cloud-vnet*                   |
    |Location         |選取 [美國東部]****                                |

1. 按一下 **[下一步IP 位址 >]** 。 
 
1. 針對 [IPv4 位址空間]，輸入 10.1.0.0/16。

1. 選取 [新增子網路]，然後輸入 *service-runtime-subnet* 作為 [子網路名稱]，以及輸入 10.1.0.0/24 作為 [子網路位址範圍]。 然後按一下 [ **新增**]。

1. 再次選取 [新增子網路]，然後輸入 *apps-subnet* 作為 [子網路名稱]，以及輸入 10.1.1.0/24 作為 [子網路位址範圍]。  按一下 [新增] 。

1. 按一下 [檢閱 + 建立]。 讓其餘項目保留預設值，然後按一下 [建立]。

## <a name="grant-service-permission-to-the-virtual-network"></a>將服務權限授與給虛擬網路

選取您先前建立的虛擬網路 [azure-spring-cloud-vnet]。

1. 選取 [存取控制 (IAM)]，然後選取 [新增] > [新增角色指派]。

    ![VNet 的存取控制](./media/spring-cloud-v-net-injection/access-control.png)

2. 在 [新增角色指派] 對話方塊中，輸入或選取這項資訊：

    |設定  |值                                             |
    |---------|--------------------------------------------------|
    |角色     |選取 [擁有者]                                  |
    |選取   |輸入 *Azure Spring Cloud 資源提供者*      |

    接著選取 [Azure Spring Cloud 資源提供者]，然後按一下 [儲存]。

    ![將 Azure Spring Cloud 資源提供者授與給 VNet](./media/spring-cloud-v-net-injection/grant-azure-spring-cloud-resource-provider-to-vnet.png)

若要達到此目的，您也可以執行下列 az CLI 命令

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

## <a name="deploy-azure-spring-cloud-service-instance-in-the-virtual-network"></a>在虛擬網路中部署 Azure Spring Cloud 服務執行個體

1. 使用在 https://ms.portal.azure.com 開啟 Azure 入口網站。

1. 從頂端的搜尋方塊中，搜尋 **Azure Spring Cloud**，然後從結果中選取 [Azure Spring Cloud]。

1. 在 [Azure Spring Cloud] 頁面上，選取 [+ 新增]。

1. 填寫 Azure Spring Cloud [建立] 頁面上的表單。 

1. 選取與虛擬網路相同的資源群組和區域。

1. 針對 [服務詳細資料] 底下的 [名稱]，請選取 [azure-spring-cloud-vnet]。

1. 選取 [網路] 索引標籤，然後選取下列選項：

    |設定                                |值                                             |
    |---------------------------------------|--------------------------------------------------|
    |在您自己的虛擬網路中部署     |選取 [是]                                    |
    |虛擬網路                        |選取 [azure-spring-cloud-vnet]                  |
    |服務執行階段子網路                 |選取 [service-runtime-subnet]                   |
    |Spring Boot 微服務應用程式子網路   |選取 [apps-subnet]                              |

    ![建立網路索引標籤](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. 按一下 [檢閱及建立]。

1. 確認規格，然後按一下 [建立]。

部署之後，將會在訂用帳戶中另外建立兩個資源群組，以裝載 Azure Spring Cloud 服務執行個體的網路資源。  瀏覽至 [首頁]，然後從頂端功能表項目選取 [資源群組]，以尋找下列新資源群組。

名為 *azure-spring-cloud-service-runtime_{service instance name}_{service instance region}* 的資源群組包含服務執行個體的服務執行階段網路資源。

  ![服務執行階段](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

名為 *azure-spring-cloud-service-runtime_{service instance name}_{service instance region}* 的資源群組包含服務執行個體的 Spring Boot 微服務應用程式網路資源。

  ![應用程式資源群組](./media/spring-cloud-v-net-injection/apps-resource-group.png)

這些網路資源會連線到您前面建立的虛擬網路。

  ![具有連線裝置的 V-net](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > 資源群組會完全由 Azure Spring Cloud 服務負責管理。 請勿手動刪除或修改其中的任何資源。

## <a name="next-steps"></a>後續步驟

[在 VNet 中將應用程式部署至 Azure Spring Cloud](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>另請參閱

- [針對 VNET 中的 Azure Spring Cloud 進行疑難排解](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [客戶在 VNET 中執行 Azure Spring Cloud 的責任](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)