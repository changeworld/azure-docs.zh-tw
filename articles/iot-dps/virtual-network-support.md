---
title: 適用于虛擬網路的 Azure IoT 裝置布建服務（DPS）支援
description: 如何搭配使用虛擬網路連線模式與 Azure IoT 裝置布建服務（DPS）
services: iot-hub
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: wesmc
ms.openlocfilehash: 97168c95893ffdea99180c997265f259176ed07f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531213"
---
# <a name="azure-iot-hub-device-provisioning-service-dps-support-for-virtual-networks"></a>虛擬網路的 Azure IoT 中樞裝置布建服務（DPS）支援

本文介紹使用 DPS 搭配 IoT 中樞布建 IoT 裝置的虛擬網路（VNET）連線模式。 此模式會在客戶擁有的 Azure VNET 內，提供裝置、DPS 和 IoT 中樞之間的私人連線能力。 

在使用 VNET 設定 DPS 的大多數情況下，您的 IoT 中樞也會在相同的 VNET 中設定。 如需有關適用于 IoT 中樞的 VNET 支援和設定的詳細資訊，請參閱[IoT 中樞虛擬網路支援](../iot-hub/virtual-network-support.md)。



## <a name="introduction"></a>簡介

根據預設，DPS 主機名稱會對應至公用端點，並在網際網路上透過可公開路由傳送的 IP 位址。 所有客戶都可以看到此公用端點。 IoT 裝置可以透過廣域網路和內部部署網路來嘗試存取公用端點。

基於幾個原因，客戶可能會想要限制對 Azure 資源（例如 DPS）的連線能力。 這些原因包括：

* 防止透過公用網際網路的連線暴露。 透過針對 IoT 中樞和 DPS 資源的網路層級隔離引進額外的安全性層級，可以降低暴露的風險

* 從內部部署網路資產啟用私人連線體驗，以確保資料和流量會直接傳輸到 Azure 骨幹網路。

* 防止來自敏感性內部部署網路的外洩攻擊。 

* 遵循使用[私人端點](../private-link/private-endpoint-overview.md)建立的全 Azure 連線模式。

限制連線能力的常見方法包括[DPS IP 篩選器規則](./iot-dps-ip-filtering.md)，以及具有[私人端點](../private-link/private-endpoint-overview.md)的虛擬網路（VNET）。 本文的目的是要說明使用私用端點之 DPS 的 VNET 方法。 

在內部部署網路中運作的裝置可以使用[虛擬私人網路（VPN）](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)或[ExpressRoute](https://azure.microsoft.com/services/expressroute/)私人對等互連來連線到 Azure 中的 VNET，並透過私人端點存取 DPS 資源。 

私人端點是在客戶擁有的 VNET 內配置的私人 IP 位址，可供存取 Azure 資源。 藉由為您的 DPS 資源擁有私用端點，您將能夠允許在 VNET 內運作的裝置要求您的 DPS 資源布建，而不允許公用端點的流量。


## <a name="prerequisites"></a>先決條件

請確定符合下列先決條件，再繼續進行：

* 已建立您的 DPS 資源，並將其連結至您的 IoT 中樞。 如需設定新 DPS 資源的指引，請參閱[使用 Azure 入口網站設定 IoT 中樞裝置佈建服務](./quick-setup-auto-provision.md)

* 您已布建 Azure VNET，其中包含將在其中建立私人端點的子網。 如需詳細資訊，請參閱[使用 Azure CLI 建立虛擬網路](../virtual-network/quick-create-cli.md)。

* 對於在內部部署網路內運作的裝置，請將[虛擬私人網路（VPN）](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)或[ExpressRoute](https://azure.microsoft.com/services/expressroute/)私人對等互連設定至您的 Azure VNET。

## <a name="private-endpoint-limitations"></a>私用端點限制

使用私用端點時，請注意下列 DPS 目前的限制：

* 當 DPS 資源和連結的中樞位於不同的雲端時，私人端點將無法與 DPS 搭配使用。 例如， [Azure Government 和全域 Azure](../azure-government/documentation-government-welcome.md)。

* 目前，[使用 DPS Azure Functions 的自訂配置原則](how-to-use-custom-allocation-policies.md)不會使用 VNET 和私人端點。 

* 目前的 DPS VNET 支援僅適用于將資料輸入至 DPS。 資料輸出（從 DPS 到 IoT 中樞的流量）會使用內部服務對服務機制，而不是專用的 VNET。 DPS 和 IoT 中樞之間的完整 VNET 型輸出鎖定支援目前無法使用。

* 最低延遲配置原則是用來將裝置指派給具有最低延遲的 IoT 中樞。 此配置原則在虛擬網路環境中並不可靠。 

## <a name="set-up-a-private-endpoint"></a>設定私用端點

若要設定私用端點，請遵循下列步驟：

1. 在[Azure 入口網站](https://portal.azure.com/)中，開啟您的 DPS 資源，然後按一下 [**網路**] 索引標籤。按一下 [**私人端點**連線] 和 [ **+ 私人端點**]。

    ![新增 DPS 的新私用端點](./media/virtual-network-support/networking-tab-add-private-endpoint.png)

2. 在 [_建立私人端點_基本概念] 頁面上，輸入下表所述的資訊。

    ![設定新的私用端點對應的資源](./media/virtual-network-support/create-private-endpoint-basics.png)

    | 欄位 | 值 |
    | :---- | :-----|
    | **訂用帳戶** | 選擇所需的 Azure 訂用帳戶以包含私人端點。  |
    | **資源群組** | 選擇或建立資源群組以包含私人端點 |
    | **名稱**       | 輸入私人端點的名稱 |
    | **區域**     | 選擇的區域必須與包含 VNET 的區域相同，但不一定要與 DPS 資源相同。 |

    按 **[下一步：資源]** ，設定私人端點將指向的資源。

3. 在 [_建立私人端點資源_] 頁面上，輸入下表所述的資訊。

    ![設定新的私用端點對應的資源](./media/virtual-network-support/create-private-endpoint-resource.png)

    | 欄位 | 值 |
    | :---- | :-----|
    | **訂用帳戶**        | 選擇 Azure 訂用帳戶，其中包含您的私用端點將指向的 DPS 資源。  |
    | **資源類型**       | 選擇 [ **Microsoft Devices/ProvisioningServices**]。 |
    | **Resource**            | 選取私人端點將對應的 DPS 資源。 |
    | **目標子資源** | 選取 [ **iotDps**]。 |

    > [!TIP]
    > 在本文的[要求私用端點](#request-a-private-endpoint)一節中會提供 [**依資源識別碼或別名連線到 Azure 資源]** 設定的資訊。


    按 **[下一步：設定]** 以設定私人端點的 VNET。

4. 在 [_建立私人端點_設定] 頁面上，選擇要在其中建立私人端點的虛擬網路和子網。
 
    按 **[下一步：** 標籤]，並選擇性地為您的資源提供任何標記。

    ![設定新的私用端點對應的資源](./media/virtual-network-support/create-private-endpoint-configuration.png)

6. 按一下 [審核] [ **+ 建立**]，然後按一下 [**建立**] 以建立您的私人端點資源。


## <a name="request-a-private-endpoint"></a>要求私用端點

您可以依資源識別碼要求 DPS 資源的私用端點。 若要提出此要求，您需要資源擁有者為您提供資源識別碼。 

1. 資源識別碼會在 DPS 資源的 [屬性] 索引標籤上提供，如下所示。

    ![DPS 屬性索引標籤](./media/virtual-network-support/dps-properties.png)

    > [!CAUTION]
    > 請注意，資源識別碼包含訂用帳戶識別碼。 

2. 當您擁有資源識別碼之後，請遵循在_建立私用端點資源_頁面上，[將私用端點設定](#set-up-a-private-endpoint)為步驟3中的步驟。 按一下 [**依資源識別碼或別名連線至 Azure 資源]** ，然後輸入下表中的資訊。 

    | 欄位 | 值 |
    | :---- | :-----|
    | **資源識別碼或別名** | 輸入 DPS 資源的資源識別碼。 |
    | **目標子資源** | 輸入**iotDps** |
    | **要求訊息** | 輸入 DPS 資源擁有者的要求訊息。<br>例如 <br>`Please approve this new private endpoint`<br>`for IoT devices in site 23 to access this DPS instance`  |

    按 **[下一步：設定]** 以設定私人端點的 VNET。

3. 在 [_建立私人端點_設定] 頁面上，選擇要在其中建立私人端點的虛擬網路和子網。
 
    按 **[下一步：** 標籤]，並選擇性地為您的資源提供任何標記。

4. 按一下 [審核] [ **+ 建立**]，然後按一下 [**建立**] 以建立您的私人端點要求。

5. DPS 擁有者會在 [DPS 網路功能] 索引標籤上的 [**私人端點連接**] 清單中看到私人端點要求。在該頁面上，擁有者可以**核准**或**拒絕**私用端點要求，如下所示。

    ![DPS 屬性索引標籤](./media/virtual-network-support/approve-dps-private-endpoint.png)


## <a name="pricing-private-endpoints"></a>定價私人端點

如需定價詳細資料，請參閱 [Azure Private Link 定價](https://azure.microsoft.com/pricing/details/private-link)。



## <a name="next-steps"></a>後續步驟

若要深入瞭解 DPS 安全性功能，請使用下列連結：

* [安全性](concepts-security.md)
* [TLS 1.2 支援](tls-support.md)
