---
title: Azure IoT 裝置布建服務 (DPS) 虛擬網路支援
description: '如何搭配使用虛擬網路連線模式與 Azure IoT 裝置布建服務 (DPS) '
services: iot-hub
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: wesmc
ms.openlocfilehash: f1409a931195d236b2729e629e4603c606137593
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94959776"
---
# <a name="azure-iot-hub-device-provisioning-service-dps-support-for-virtual-networks"></a>Azure IoT 中樞裝置布建服務 (DPS) 虛擬網路支援

本文介紹使用 DPS 以 IoT 中樞布建之 IoT 裝置的虛擬網路 (VNET) 連線模式。 此模式會在客戶擁有的 Azure VNET 內，提供裝置、DPS 和 IoT 中樞之間的私人連線能力。 

在使用 VNET 設定 DPS 的大部分情況下，您的 IoT 中樞也會設定在相同的 VNET 中。 如需適用于 IoT 中樞的 VNET 支援和設定的詳細資訊，請參閱 [Iot 中樞虛擬網路支援](../iot-hub/virtual-network-support.md)。



## <a name="introduction"></a>簡介

根據預設，DPS 主機名稱會對應至具有可透過網際網路公開路由傳送 IP 位址的公用端點。 所有客戶都可以看到此公用端點。 IoT 裝置可透過廣域網路以及內部部署網路來嘗試存取公用端點。

基於許多原因，客戶可能會想要限制對 Azure 資源（例如 DPS）的連線能力。 這些原因包括：

* 防止透過公用網際網路的連線暴露。 您可以透過網路層級隔離為 IoT 中樞和 DPS 資源引進額外的安全性層級，來降低暴露程度

* 從內部部署網路資產啟用私人連線體驗，以確保資料和流量會直接傳輸到 Azure 骨幹網路。

* 防止來自敏感性內部部署網路的外洩攻擊。 

* 遵循使用[私人端點](../private-link/private-endpoint-overview.md)建立的全 Azure 連線模式。

限制連線的常見方法包括 [DPS IP 篩選規則](./iot-dps-ip-filtering.md) 和虛擬網路 (VNET) 搭配 [私人端點](../private-link/private-endpoint-overview.md)。 本文的目標是要說明使用私人端點之 DPS 的 VNET 方法。 

在內部部署網路中操作的裝置可以使用 [虛擬私人網路 (VPN) ](../vpn-gateway/vpn-gateway-about-vpngateways.md) 或 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 私用對等互連來連線到 Azure 中的 VNET，並透過私人端點存取 DPS 資源。 

私人端點是在客戶擁有的 VNET 內配置的私人 IP 位址，可供存取 Azure 資源。 藉由為 DPS 資源擁有私用端點，您將能夠允許在 VNET 內操作的裝置要求您的 DPS 資源布建，而不允許公用端點的流量。


## <a name="prerequisites"></a>Prerequisites

請確定符合下列先決條件，再繼續進行：

* 您的 DPS 資源已建立並連結到您的 IoT 中樞。 如需設定新 DPS 資源的指引，請參閱[使用 Azure 入口網站設定 IoT 中樞裝置](./quick-setup-auto-provision.md)布建服務。

* 您已使用將建立私人端點的子網布建 Azure VNET。 如需詳細資訊，請參閱 [使用 Azure CLI 建立虛擬網路](../virtual-network/quick-create-cli.md)。

* 針對在內部部署網路內運作的裝置，請將 [虛擬私人網路 (VPN) ](../vpn-gateway/vpn-gateway-about-vpngateways.md) 或 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 私人對等互連設定至您的 Azure VNET。

## <a name="private-endpoint-limitations"></a>私人端點限制

使用私人端點時，請注意下列 DPS 目前的限制：

* 當 DPS 資源和連結的中樞位於不同的雲端時，私人端點將無法使用 DPS。 例如， [Azure Government 和全域 Azure](../azure-government/documentation-government-welcome.md)。

* 目前，當 Azure 函式鎖定至 VNET 和私人端點時， [具有 DPS Azure Functions 的自訂配置原則](how-to-use-custom-allocation-policies.md) 將無法運作。 

* 目前的 DPS VNET 支援僅適用于在 DPS 中輸入的資料。 資料輸出（也就是從 DPS 到 IoT 中樞的流量）會使用內部服務對服務機制，而不是專用的 VNET。 目前無法使用 DPS 與 IoT 中樞之間的完整 VNET 型輸出鎖定。

* 最低延遲配置原則可用來將裝置指派給具有最低延遲的 IoT 中樞。 虛擬網路環境中的此配置原則不可靠。 

## <a name="set-up-a-private-endpoint"></a>設定私人端點

若要設定私人端點，請遵循下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)中，開啟您的 DPS 資源，然後按一下 [ **網路** ] 索引標籤。按一下 [ **私人端點連接** ] 和 [ **+ 私人端點**]。

    ![加入 DPS 的新私人端點](./media/virtual-network-support/networking-tab-add-private-endpoint.png)

2. 在 [ _建立私人端點_ 基本] 頁面上，輸入下表所述的資訊。

    ![建立私人端點的基本概念](./media/virtual-network-support/create-private-endpoint-basics.png)

    | 欄位 | 值 |
    | :---- | :-----|
    | **訂用帳戶** | 選擇想要包含私人端點的 Azure 訂用帳戶。  |
    | **資源群組** | 選擇或建立包含私人端點的資源群組 |
    | **Name**       | 輸入私人端點的名稱 |
    | **區域**     | 選擇的區域必須與包含 VNET 的區域相同，但它不一定要與 DPS 資源相同。 |

    按一下 **[下一步：資源]** 以設定私人端點將指向的資源。

3. 在 [ _建立私人端點資源_ ] 頁面上，輸入下表所述的資訊。

    ![建立私人端點資源](./media/virtual-network-support/create-private-endpoint-resource.png)

    | 欄位 | 值 |
    | :---- | :-----|
    | **訂用帳戶**        | 選擇包含您私人端點將指向之 DPS 資源的 Azure 訂用帳戶。  |
    | **資源類型**       | 選擇 [ **Microsoft. 裝置/ProvisioningServices**]。 |
    | **Resource**            | 選取私人端點將對應的 DPS 資源。 |
    | **目標子資源** | 選取 [ **iotDps**]。 |

    > [!TIP]
    > 如需有關 **依資源識別碼或別名設定連接到 Azure 資源** 的資訊，請參閱本文的「 [要求私用端點](#request-a-private-endpoint) 」一節。


    按一下 **[下一步：設定]** 以設定私人端點的 VNET。

4. 在 [ _建立私人端點_ 設定] 頁面上，選擇您要在其中建立私人端點的虛擬網路和子網。
 
    按 **[下一步：標記]**，並選擇性地為您的資源提供任何標記。

    ![設定私人端點](./media/virtual-network-support/create-private-endpoint-configuration.png)

6. 按一下 [ **審核] + [建立** ]，然後 **建立** 以建立私人端點資源。


## <a name="request-a-private-endpoint"></a>要求私人端點

您可以依資源識別碼向 DPS 資源要求私用端點。 為了進行此要求，您需要資源擁有者提供資源識別碼給您。 

1. 會在 DPS 資源的 [屬性] 索引標籤上提供資源識別碼，如下所示。

    ![DPS 內容索引標籤](./media/virtual-network-support/dps-properties.png)

    > [!CAUTION]
    > 請注意，資源識別碼包含訂用帳戶識別碼。 

2. 擁有資源識別碼之後，請依照 [_建立私人端點資源_] 頁面上的在步驟 3 [設定私人端點](#set-up-a-private-endpoint)中的步驟執行。 按一下 [ **依資源識別碼或別名連線至 Azure 資源]** ，然後在下表中輸入資訊。 

    | 欄位 | 值 |
    | :---- | :-----|
    | **資源識別碼或別名** | 輸入 DPS 資源的資源識別碼。 |
    | **目標子資源** | 輸入 **iotDps** |
    | **要求訊息** | 輸入 DPS 資源擁有者的要求訊息。<br>例如，套用至物件的 <br>`Please approve this new private endpoint`<br>`for IoT devices in site 23 to access this DPS instance`  |

    按一下 **[下一步：設定]** 以設定私人端點的 VNET。

3. 在 [ _建立私人端點_ 設定] 頁面上，選擇要在其中建立私人端點的虛擬網路和子網。
 
    按 **[下一步：標記]**，並選擇性地為您的資源提供任何標記。

4. 按一下 [ **審核] + [建立** ]，然後 **建立** 以建立私人端點要求。

5. DPS 擁有者會在 DPS [網路] 索引標籤上的 [ **私人端點** 連線] 清單中看到私人端點要求。在該頁面上，擁有者可以 **核准** 或 **拒絕** 私人端點要求，如下所示。

    ![DPS 核准](./media/virtual-network-support/approve-dps-private-endpoint.png)


## <a name="pricing-private-endpoints"></a>定價私人端點

如需定價詳細資料，請參閱 [Azure Private Link 定價](https://azure.microsoft.com/pricing/details/private-link)。



## <a name="next-steps"></a>後續步驟

您可以使用下列連結來深入瞭解 DPS 安全性功能：

* [安全性](./concepts-service.md#attestation-mechanism)
* [TLS 1.2 支援](tls-support.md)