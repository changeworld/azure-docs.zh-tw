---
title: 虛擬網路的 Azure IoT 中樞支援
description: 如何搭配 IoT 中樞使用虛擬網路連線模式
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: jlian
ms.openlocfilehash: c0d01ae6507864373a79282476846d6f96adf83b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231436"
---
# <a name="iot-hub-support-for-virtual-networks"></a>虛擬網路的 IoT 中樞支援

本文介紹 VNET 連線模式，並介紹如何透過客戶擁有的 Azure VNET 來設定 IoT 中樞的私人連線體驗。

> [!NOTE]
> 本文中所述的 IoT 中樞功能目前可供在下欄區域[使用受控服務識別建立](#create-an-iot-hub-with-managed-service-identity)的 IoT 中樞使用：美國東部、美國中南部和美國西部2。


## <a name="introduction"></a>簡介

根據預設，IoT 中樞主機名稱會對應至公用端點，並透過網際網路透過可公開路由傳送的 IP 位址。 如下圖所示，此 IoT 中樞公用端點會在不同客戶所擁有的中樞之間共用，並可透過廣域網路以及內部部署網路的方式來存取。

有數個 IoT 中樞功能包括[訊息路由](./iot-hub-devguide-messages-d2c.md)、檔案[上傳](./iot-hub-devguide-file-upload.md)，以及[大量裝置匯入/匯出](./iot-hub-bulk-identity-mgmt.md)，同樣需要透過其公用端點，從 IoT 中樞連線到客戶擁有的 Azure 資源。 如下所示，這些連接路徑會共同構成從 IoT 中樞到客戶資源的輸出流量。
![IoT 中樞公用端點](./media/virtual-network-support/public-endpoint.png)


基於幾個原因，客戶可能會想要透過自己擁有和操作的 VNET，限制其 Azure 資源（包括 IoT 中樞）的連線能力。 其中包括：

* 藉由防止透過公用網際網路連線到您的中樞，透過網路層級隔離為您的 IoT 中樞引進額外的安全性層。

* 從您的內部部署網路資產啟用私人連線體驗，確保您的資料和流量會直接傳輸到 Azure 骨幹網路。

* 防止機密內部部署網路的外泄攻擊。 

* 遵循使用[私用端點](../private-link/private-endpoint-overview.md)建立的全 Azure 連線模式。


本文說明如何使用[私人端點](../private-link/private-endpoint-overview.md)來達到這些目標，以進行 IoT 中樞的輸入連線，例如使用 Azure 受信任的協力廠商服務例外狀況，從 IoT 中樞到其他 Azure 資源的輸出連線能力。


## <a name="ingress-connectivity-to-iot-hub-using-private-endpoints"></a>使用私人端點的 IoT 中樞輸入連線能力

私用端點是在客戶擁有的 VNET 內配置的私人 IP 位址，可透過它來連線到 Azure 資源。 藉由讓 IoT 中樞擁有私人端點，您將能夠允許在 VNET 內運作的服務觸達 IoT 中樞，而不需要將流量傳送至 IoT 中樞的公用端點。 同樣地，在您的內部部署環境中運作的裝置可以使用[虛擬私人網路（VPN）](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)或[ExpressRoute](https://azure.microsoft.com/services/expressroute/)私人對等互連，來連線到您在 Azure 中的 VNET，然後再連接到您的 IoT 中樞（透過其私人端點）。 因此，想要限制與 IoT 中樞公用端點（或可能完全封鎖）連線的客戶，可以使用[IoT 中樞防火牆規則](./iot-hub-ip-filtering.md)來達到此目標，同時使用私用端點保留其中樞的連線能力。

> [!NOTE]
> 此設定的主要重點在於內部部署網路內的裝置。 對於部署在廣域網路中的裝置，不建議使用此設定。

![IoT 中樞公用端點](./media/virtual-network-support/virtual-network-ingress.png)

在繼續之前，請確定符合下列必要條件：

* 您的 IoT 中樞必須以[受控服務識別](#create-an-iot-hub-with-managed-service-identity)布建。

* 您的 IoT 中樞必須布建在其中一個[支援的區域](#regional-availability-private-endpoints)中。

* 您已布建 Azure VNET，其中包含將在其中建立私人端點的子網。 如需詳細資訊，請參閱[使用 Azure CLI 建立虛擬網路](../virtual-network/quick-create-cli.md)。

* 對於在內部部署網路內運作的裝置，請將[虛擬私人網路（VPN）](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)或[ExpressRoute](https://azure.microsoft.com/services/expressroute/)私人對等互連設定至您的 Azure VNET。


### <a name="regional-availability-private-endpoints"></a>區域可用性（私人端點）

在下欄區域中建立的 IoT 中樞支援私用端點：

* 美國東部

* 美國中南部

* 美國西部 2


### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>設定 IoT 中樞輸入的私用端點

若要設定私用端點，請遵循下列步驟：

1. 執行下列 Azure CLI 命令，以使用您的訂用帳戶重新註冊 Azure IoT 中樞提供者：

    ```azurecli-interactive
    az provider register --namespace Microsoft.Devices --wait --subscription  <subscription-name>
    ```

2. 流覽至 IoT 中樞入口網站中的 [**私人端點**連線] 索引標籤（此索引標籤僅適用于在[支援的區域](#regional-availability-private-endpoints)中的 IoT **+** 中樞），然後按一下符號以加入新的私人端點。

3. 提供訂用帳戶、資源群組、名稱和區域，以在中建立新的私人端點（最好是在您的中樞所在的相同區域中建立私用端點; 如需詳細資訊，請參閱[區域可用性一節](#regional-availability-private-endpoints)）。

4. 按一下 **[下一步：資源]**，並提供您 IoT 中樞資源的訂用帳戶，然後選取 **[Microsoft Devices/IotHubs]** 作為 [資源類型]、[IoT 中樞名稱] 做為 [**資源**]，然後**iotHub**為 [目標子資源]。

5. 按 **[下一步：設定]** ，並提供您的虛擬網路和子網以在中建立私人端點。 如有需要，請選取要與 Azure 私人 DNS 區域整合的選項。

6. 按 **[下一步：** 標籤]，並選擇性地為您的資源提供任何標記。

7. 按一下 [**檢查 + 建立**] 以建立您的私用端點資源。


### <a name="pricing-private-endpoints"></a>定價（私人端點）

如需定價詳細資料，請參閱 [Azure Private Link 定價](https://azure.microsoft.com/pricing/details/private-link)。


## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>從 IoT 中樞到其他 Azure 資源的輸出連線能力

IoT 中樞需要存取您的 Azure blob 儲存體、事件中樞、[訊息路由](./iot-hub-devguide-messages-d2c.md)的服務匯流排資源、檔案[上傳](./iot-hub-devguide-file-upload.md)，以及[大量裝置匯入/匯出](./iot-hub-bulk-identity-mgmt.md)，這通常會在資源的公用端點上進行。 當您將儲存體帳戶、事件中樞或服務匯流排資源系結至 VNET 時，建議的設定預設會封鎖對資源的連線。 因此，這會妨礙需要存取這些資源的 IoT 中樞功能。

若要減輕這種情況，您必須透過**Azure 第一方**的 [信任的服務] 選項，啟用從 IoT 中樞資源到儲存體帳戶、事件中樞或服務匯流排資源的連線能力。

先決條件如下：

* 您的 IoT 中樞必須布建在其中一個[支援的區域](#regional-availability-trusted-microsoft-first-party-services)中。

* 您的 IoT 中樞必須在中樞布建時間指派受控服務識別。 遵循如何[使用受控服務識別建立中樞](#create-an-iot-hub-with-managed-service-identity)的指示。


### <a name="regional-availability-trusted-microsoft-first-party-services"></a>區域可用性（信任的 Microsoft 第一方服務）

Azure 受信任的協力廠商服務例外，略過 Azure 儲存體的防火牆限制、事件中樞和服務匯流排資源僅支援下欄區域中的 IoT 中樞：

* 美國東部

* 美國中南部

* 美國西部 2


### <a name="pricing-trusted-microsoft-first-party-services"></a>定價（信任的 Microsoft 第一方服務）

受信任的 Microsoft 第一方服務例外功能在[支援區域](#regional-availability-trusted-microsoft-first-party-services)的 IoT 中樞中是免費的。 已布建的儲存體帳戶、事件中樞或服務匯流排資源的費用會分別套用。


### <a name="create-an-iot-hub-with-managed-service-identity"></a>使用受控服務識別建立 IoT 中樞

受控服務識別可以在資源布建時間指派給您的中樞（現有的中樞目前不支援這項功能），這需要 IoT 中樞使用 TLS 1.2 做為最低版本。 基於此目的，您必須使用下列 ARM 資源範本：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Devices/IotHubs",
      "apiVersion": "2020-03-01",
      "name": "<provide-a-valid-resource-name>",
      "location": "<any-of-supported-regions>",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "minTlsVersion": "1.2"
      },
      "sku": {
        "name": "<your-hubs-SKU-name>",
        "tier": "<your-hubs-SKU-tier>",
        "capacity": 1
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-02-01",
      "name": "updateIotHubWithKeyEncryptionKey",
      "dependsOn": [
        "<provide-a-valid-resource-name>"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "0.9.0.0",
          "resources": [
            {
              "type": "Microsoft.Devices/IotHubs",
              "apiVersion": "2020-03-01",
              "name": "<provide-a-valid-resource-name>",
              "location": "<any-of-supported-regions>",
              "identity": {
                "type": "SystemAssigned"
              },
              "properties": {
                "minTlsVersion": "1.2"
              },
              "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
              }
            }
          ]
        }
      }
    }
  ]
}
```

取代您的`name`資源、 `location` `SKU.name`和`SKU.tier`的值之後，您可以使用 Azure CLI，在現有的資源群組中部署資源，使用：

```azurecli-interactive
az deployment group create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

建立資源之後，您可以使用 Azure CLI 來抓取指派給中樞的受控服務識別：

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

布建受控服務識別的 IoT 中樞之後，請遵循對應的章節來設定[儲存體帳戶](#egress-connectivity-to-storage-account-endpoints-for-routing)、[事件中樞](#egress-connectivity-to-event-hubs-endpoints-for-routing)和[服務匯流排](#egress-connectivity-to-service-bus-endpoints-for-routing)資源的路由端點，或配置[檔案上傳](#egress-connectivity-to-storage-accounts-for-file-upload)和[大量裝置匯入/匯出](#egress-connectivity-to-storage-accounts-for-bulk-device-importexport)。


### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>對儲存體帳戶端點的輸出連線能力以進行路由

IoT 中樞可以設定為將訊息路由至客戶擁有的儲存體帳戶。 若要在有防火牆限制時允許路由功能存取儲存體帳戶，您的 IoT 中樞必須具有受控服務識別（請參閱如何[使用受控服務識別建立中樞](#create-an-iot-hub-with-managed-service-identity)）。 布建受控服務識別之後，請遵循下列步驟，為您中樞的資源身分識別提供 RBAC 許可權，以存取您的儲存體帳戶。

1. 在 [Azure 入口網站中，流覽至儲存體帳戶的 **[存取控制（IAM）** ] 索引標籤，然後按一下 [**新增角色指派**] 區段底下的 [**新增**]。

2. 選取 [**儲存體 Blob 資料參與者**] 做為 [**角色**]、[ **Azure AD 使用者]、[群組] 或 [服務主體**]，做為**指派存取權**，並在下拉式清單中選取您 IoT 中樞的資源名稱。 按一下 [儲存]  按鈕。

3. 流覽至儲存體帳戶中的 [**防火牆和虛擬網路**] 索引標籤，並啟用 [**允許從選取的網路存取**] 選項。 在 [**例外**狀況] 清單底下，勾選 [**允許信任的 Microsoft 服務存取此儲存體帳戶**] 方塊。 按一下 [儲存]  按鈕。

4. 在 IoT 中樞的資源頁面上，流覽至 [**訊息路由**] 索引標籤。

5. 流覽至 [**自訂端點**] 區段，然後按一下 [**新增**]。 選取 [**儲存體**] 作為端點類型。

6. 在顯示的頁面上，提供端點的名稱，選取您想要在 blob 儲存體中使用的容器、提供編碼，以及檔案名格式。 選取 [**系統指派**] 做為儲存體端點的**驗證類型**。 按一下 [ **建立** ] 按鈕。

現在，您的自訂儲存體端點已設定為使用中樞系統指派的身分識別，而它有權存取您的儲存體資源，但其防火牆限制也一樣。 您現在可以使用此端點來設定路由規則。


### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>路由的事件中樞端點的輸出連線能力

IoT 中樞可以設定為將訊息路由至客戶擁有的事件中樞命名空間。 若要在有防火牆限制時允許路由功能存取事件中樞資源，您的 IoT 中樞必須具有受控服務識別（請參閱如何[使用受控服務識別建立中樞](#create-an-iot-hub-with-managed-service-identity)）。 布建受控服務識別之後，請遵循下列步驟，為您中樞的資源身分識別提供 RBAC 許可權，以存取您的事件中樞。

1. 在 [Azure 入口網站中，流覽至您的 [事件中樞**存取控制（IAM）** ] 索引標籤，然後按一下 [**新增角色指派**] 區段底下的 [**新增**]。

2. 選取 [**事件中樞資料**傳送者] 作為 [**角色**]、[ **Azure AD 使用者]、[群組] 或 [服務主體**]，做為**指派存取權**，並在下拉式清單中選取 IoT 中樞的資源名稱。 按一下 [儲存]  按鈕。

3. 流覽至事件中樞內的 [**防火牆和虛擬網路**] 索引標籤，並啟用 [**允許從選取的網路存取**] 選項。 在 [**例外**狀況] 清單底下，核取 [**允許信任的 Microsoft 服務存取事件中樞**] 方塊。 按一下 [儲存]  按鈕。

4. 在 IoT 中樞的資源頁面上，流覽至 [**訊息路由**] 索引標籤。

5. 流覽至 [**自訂端點**] 區段，然後按一下 [**新增**]。 選取 [**事件中樞**] 做為端點類型。

6. 在顯示的頁面上，提供您的端點名稱、選取您的事件中樞命名空間和實例，然後按一下 [**建立**] 按鈕。

現在，您的自訂事件中樞端點已設定為使用中樞系統指派的身分識別，而且它具有存取事件中樞資源的許可權，但它的防火牆限制卻有。 您現在可以使用此端點來設定路由規則。


### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>路由的服務匯流排端點的輸出連線能力

IoT 中樞可以設定為將訊息路由至客戶擁有的服務匯流排命名空間。 若要在有防火牆限制時允許路由功能存取服務匯流排資源，您的 IoT 中樞必須具有受控服務識別（請參閱如何[使用受控服務識別建立中樞](#create-an-iot-hub-with-managed-service-identity)）。 布建受控服務識別之後，請遵循下列步驟，為您中樞的資源身分識別提供 RBAC 許可權，以存取您的服務匯流排。

1. 在 [Azure 入口網站中，流覽至您的服務匯流排 [**存取控制（IAM）** ] 索引標籤，然後按一下 [**新增角色指派**] 區段底下的 [**新增**]。

2. 選取 [**服務匯流排資料**傳送者] 作為 [**角色**]、[ **Azure AD 使用者]、[群組] 或 [服務主體**] 做為**指派存取權**，然後在下拉式清單中選取您 IoT 中樞的資源名稱。 按一下 [儲存]  按鈕。

3. 流覽至服務匯流排中的 [**防火牆和虛擬網路**] 索引標籤，並啟用 [**允許從選取的網路存取**] 選項。 在 [**例外**狀況] 清單底下，核取 [**允許受信任的 Microsoft 服務存取此服務匯流排**] 的核取方塊。 按一下 [儲存]  按鈕。

4. 在 IoT 中樞的資源頁面上，流覽至 [**訊息路由**] 索引標籤。

5. 流覽至 [**自訂端點**] 區段，然後按一下 [**新增**]。 選取 [**服務匯流排佇列**] 或 [**服務匯流排] 主題**（如適用）作為端點類型。

6. 在顯示的頁面上，提供您的端點名稱、選取您的服務匯流排命名空間和佇列或主題（如適用）。 按一下 [ **建立** ] 按鈕。

現在，您的自訂服務匯流排端點已設定為使用中樞系統指派的身分識別，而且它具有存取服務匯流排資源的許可權，但其防火牆限制是如此。 您現在可以使用此端點來設定路由規則。


### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>對儲存體帳戶進行檔案上傳的輸出連線

IoT 中樞的檔案上傳功能，可讓裝置將檔案上傳至客戶擁有的儲存體帳戶。 若要讓檔案上傳能夠運作，裝置和 IoT 中樞都必須具有儲存體帳戶的連線能力。 如果儲存體帳戶上有防火牆限制，您的裝置就必須使用任何支援的儲存體帳戶機制（包括[私人端點](../private-link/create-private-endpoint-storage-portal.md)、[服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)或[直接防火牆](../storage/common/storage-network-security.md)設定）來取得連線。 同樣地，如果儲存體帳戶上有防火牆限制，IoT 中樞必須設定為透過受信任的 Microsoft 服務例外來存取儲存體資源。 基於此目的，您的 IoT 中樞必須具有受控服務識別（請參閱如何[使用受控服務識別建立中樞](#create-an-iot-hub-with-managed-service-identity)）。 布建受控服務識別之後，請遵循下列步驟，為您中樞的資源身分識別提供 RBAC 許可權，以存取您的儲存體帳戶。

1. 在 [Azure 入口網站中，流覽至儲存體帳戶的 **[存取控制（IAM）** ] 索引標籤，然後按一下 [**新增角色指派**] 區段底下的 [**新增**]。

2. 選取 [**儲存體 Blob 資料參與者**] 做為 [**角色**]、[ **Azure AD 使用者]、[群組] 或 [服務主體**]，做為**指派存取權**，並在下拉式清單中選取您 IoT 中樞的資源名稱。 按一下 [儲存]  按鈕。

3. 流覽至儲存體帳戶中的 [**防火牆和虛擬網路**] 索引標籤，並啟用 [**允許從選取的網路存取**] 選項。 在 [**例外**狀況] 清單底下，勾選 [**允許信任的 Microsoft 服務存取此儲存體帳戶**] 方塊。 按一下 [儲存]  按鈕。

4. 在 IoT 中樞的資源頁面上，流覽至 **[** 檔案上傳] 索引標籤。

5. 在顯示的頁面上，選取您想要在 blob 儲存體中使用的容器，並視需要設定 [檔案**通知設定**]、[ **SAS TTL**]、[**預設 TTL** ] 和 [**最大傳遞計數**]。 選取 [**系統指派**] 做為儲存體端點的**驗證類型**。 按一下 [ **建立** ] 按鈕。

現在，您的「檔案上傳」的儲存體端點已設定為使用中樞系統指派的身分識別，而它有權存取您的儲存體資源（儘管其防火牆限制）。


### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>大量裝置匯入/匯出之儲存體帳戶的輸出連線能力

IoT 中樞支援從客戶提供的儲存體 blob 大量匯[入/匯出](./iot-hub-bulk-identity-mgmt.md)裝置資訊的功能。 若要讓大量匯入/匯出功能能夠運作，裝置和 IoT 中樞都必須具有儲存體帳戶的連線能力。

這種功能需要從 IoT 中樞到儲存體帳戶的連線能力。 若要在有防火牆限制時存取服務匯流排資源，您的 IoT 中樞必須具有受控服務識別（請參閱如何[使用受控服務識別建立中樞](#create-an-iot-hub-with-managed-service-identity)）。 布建受控服務識別之後，請遵循下列步驟，為您中樞的資源身分識別提供 RBAC 許可權，以存取您的服務匯流排。

1. 在 [Azure 入口網站中，流覽至儲存體帳戶的 **[存取控制（IAM）** ] 索引標籤，然後按一下 [**新增角色指派**] 區段底下的 [**新增**]。

2. 選取 [**儲存體 Blob 資料參與者**] 做為 [**角色**]、[ **Azure AD 使用者]、[群組] 或 [服務主體**]，做為**指派存取權**，並在下拉式清單中選取您 IoT 中樞的資源名稱。 按一下 [儲存]  按鈕。

3. 流覽至儲存體帳戶中的 [**防火牆和虛擬網路**] 索引標籤，並啟用 [**允許從選取的網路存取**] 選項。 在 [**例外**狀況] 清單底下，勾選 [**允許信任的 Microsoft 服務存取此儲存體帳戶**] 方塊。 按一下 [儲存]  按鈕。

您現在可以使用 Azure IoT REST API 的來[建立匯入匯出作業](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs)，以取得如何使用大量匯入/匯出功能的相關資訊。 請注意，您必須`storageAuthenticationType="identityBased"`在要求主體中提供，並分別使用`inputBlobContainerUri="https://..."`和`outputBlobContainerUri="https://..."`作為儲存體帳戶的輸入和輸出 URL。


Azure IoT 中樞 SDK 也會在服務用戶端的登錄管理員中支援這項功能。 下列程式碼片段示範如何使用 c # SDK，在中起始匯入作業或匯出作業。

```csharp
// Call an import job on the IoT Hub
JobProperties importJob = 
await registryManager.ImportDevicesAsync(
  JobProperties.CreateForImportJob(inputBlobContainerUri, outputBlobContainerUri, null, StorageAuthenticationType.IdentityBased), 
  cancellationToken);

// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
await registryManager.ExportDevicesAsync(
    JobProperties.CreateForExportJob(outputBlobContainerUri, true, null, StorageAuthenticationType.IdentityBased),
    cancellationToken);
```


針對 c #、JAVA 和 node.js 使用 Azure IoT Sdk 的此區域有限版本搭配虛擬網路支援：

1. 建立名為`EnableStorageIdentity`的環境變數，並將其`1`值設定為。

2. 下載 SDK： [JAVA](https://aka.ms/vnetjavasdk) | [c #](https://aka.ms/vnetcsharpsdk) | [node.js](https://aka.ms/vnetnodesdk)
 
針對 Python，請從 GitHub 下載我們的有限版本。

1. 流覽至[GitHub 版本頁面](https://aka.ms/vnetpythonsdk)。

2. 下載下列檔案，您可以在 [發行] 頁面底部的 [**資產**] 標題底下找到此檔案。
    > *azure_iot_hub-2.2. 0_limited-py2. py3-none-any. .whl*

3. 開啟終端機，並流覽至包含所下載檔案的資料夾。

4. 執行下列命令以安裝具有虛擬網路支援的 Python 服務 SDK：
    > pip install./azure_iot_hub-2.2. 0_limited-py2. py3-none-any. .whl


## <a name="next-steps"></a>後續步驟

若要深入瞭解 IoT 中樞功能，請使用下列連結：

* [訊息路由](./iot-hub-devguide-messages-d2c.md)
* [檔案上傳](./iot-hub-devguide-file-upload.md)
* [大量裝置匯入/匯出](./iot-hub-bulk-identity-mgmt.md) 
