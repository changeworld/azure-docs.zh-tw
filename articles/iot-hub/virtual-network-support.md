---
title: Azure IoT 中樞的虛擬網路支援
description: 如何透過 IoT 中樞使用虛擬網路連線模式
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/09/2020
ms.author: jlian
ms.openlocfilehash: fdc106a1a446f51d309ac4317062c8fd20204bae
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94413389"
---
# <a name="iot-hub-support-for-virtual-networks-with-private-link-and-managed-identity"></a>IoT 中樞利用 Private Link 和受控識別支援虛擬網路

根據預設，IoT 中樞的主機名稱會使用可透過網際網路公開路由所傳送 IP 位址來對應至公用端點。 不同客戶會共用此 IoT 中樞公用端點，且廣域網路和內部部署網路中的 IoT 裝置全部都可進行存取。

![IoT 中樞公用端點](./media/virtual-network-support/public-endpoint.png)

IoT 中樞功能 (包括[訊息路由](./iot-hub-devguide-messages-d2c.md)、[檔案上傳](./iot-hub-devguide-file-upload.md)和[大量裝置匯入/匯出](./iot-hub-bulk-identity-mgmt.md)) 也需要從 IoT 中樞透過其公用端點連線到客戶擁有的 Azure 資源。 這些連線路徑共同構成從 IoT 中樞到客戶資源的輸出流量。

您可能想要限制透過所擁有和操作的 VNet 來與 Azure 資源 (包括 IoT 中樞) 連線。 這些原因包括：

* 藉由防止對公用網際網路公開連線，為 IoT 中樞引進網路隔離。

* 從內部部署網路資產啟用私人連線體驗，以確保資料和流量會直接傳輸到 Azure 骨幹網路。

* 防止來自敏感性內部部署網路的外洩攻擊。 

* 遵循使用[私人端點](../private-link/private-endpoint-overview.md)建立的全 Azure 連線模式。

本文描述如何使用 [Azure Private Link](../private-link/private-link-overview.md) 輸入連線到 IoT 中樞，並使用信任的 Microsoft 服務例外從 IoT 中樞輸出連線到其他 Azure 資源來達成這些目標。

## <a name="ingress-connectivity-to-iot-hub-using-azure-private-link"></a>使用 Azure Private Link 輸入連線到 IoT 中樞

私人端點是在客戶所擁有 VNet 內配置的私人 IP 位址，可透過它來連線到 Azure 資源。 透過 Azure Private Link，您可為 IoT 中樞設定私人端點，以允許 VNet 內的服務連線到 IoT 中樞，而不需要將流量傳送至 IoT 中樞的公用端點。 同樣地，內部部署裝置可使用[虛擬私人網路 (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) 或 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 對等互連來取得與 VNet 和 IoT 中樞 (透過其私人端點) 的連線。 因此，您可使用 [IoT 中樞 IP 篩選器](./iot-hub-ip-filtering.md)，並[設定路由不要將任何資料傳送至內建端點](#built-in-event-hub-compatible-endpoint-doesnt-support-access-over-private-endpoint)，以限制或完全封鎖 IoT 中樞公用端點的連線。 此方法可保持使用裝置的私人端點進行中樞的連線。 此設定主要是針對內部部署網路內的裝置。 若是部署在廣域網路中的裝置，則不建議此設定。

![IoT 中樞虛擬網路 engress](./media/virtual-network-support/virtual-network-ingress.png)

請確定符合下列先決條件，再繼續進行：

* 您已[建立 Azure VNet](../virtual-network/quick-create-portal.md)，包含將在其中建立私人端點的子網路。

* 針對在內部部署網路中運作的裝置，請在 Azure VNet 中設定[虛擬私人網路 (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) 或 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 私人對等互連。

### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>設定用於 IoT 中樞輸入的私人端點

私人端點適用于 IoT 中樞裝置 Api (例如裝置到雲端訊息) 和服務 Api (例如) 建立和更新裝置。

1. 在 Azure 入口網站中，選取 [網路]、[私人端點連線]，然後按一下 [+ 私人端點]。

    :::image type="content" source="media/virtual-network-support/private-link.png" alt-text="顯示為 IoT 中樞新增私人端點的位置螢幕擷取畫面":::

1. 提供要在其中建立新私人端點的訂用帳戶、資源群組、名稱和區域。 在理想情況下，私人端點應該在與您中樞相同的區域中建立。

1. 按一下 **[下一步:資源]** ，並提供 IoT 中樞資源的訂用帳戶，然後選取 [Microsoft.Devices/IotHubs] 作為 [資源類型]、 IoT 中樞名稱作為 [資源]，以及 [iotHub] 作為 [目標子資源]。

1. 按一下 **[下一步:設定]** ，並提供要在其中建立私人端點的虛擬網路和子網路。 如有需要，請選取與 Azure 私人 DNS 區域整合的選項。

1. 按一下 **[下一步:標記]** ，並選擇性地為資源提供任何標記。

1. 按一下 [檢閱 + 建立] 以建立 Private Link 資源。

### <a name="built-in-event-hub-compatible-endpoint-doesnt-support-access-over-private-endpoint"></a>內建事件中樞相容端點不支援透過私人端點存取

[內建事件中樞相容端點](iot-hub-devguide-messages-read-builtin.md)不支援透過私人端點存取。 設定時，IoT 中樞的私人端點僅供輸入連線使用。 只能透過公用網際網路從內建事件中樞相容端點取用資料。 

IoT 中樞的 [IP 篩選器](iot-hub-ip-filtering.md) 也不會控制對內建端點進行公用存取。 若要完全封鎖對 IoT 中樞的公用網路存取，您必須： 

1. 設定 IoT 中樞的私人端點存取
1. 關閉[公用網路存取](iot-hub-public-network-access.md)，或使用 ip 篩選器來封鎖所有 IP
1. 藉由[設定路由不要傳送資料給它](iot-hub-devguide-messages-d2c.md)，停止使用內建的事件中樞端點
1. 關閉[後援路由](iot-hub-devguide-messages-d2c.md#fallback-route)
1. 使用[信任的 Microsoft 服務](#egress-connectivity-from-iot-hub-to-other-azure-resources)來設定其他 Azure 資源的輸出

### <a name="pricing-for-private-link"></a>Private Link 的定價

如需定價詳細資料，請參閱 [Azure Private Link 定價](https://azure.microsoft.com/pricing/details/private-link)。

## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>從 IoT 中樞輸出連線到其他 Azure 資源

IoT 中樞可連線到 Azure Blob 儲存體、事件中樞、服務匯流排資源，以透過資源的公用端點進行[訊息路由](./iot-hub-devguide-messages-d2c.md)、[檔案上傳](./iot-hub-devguide-file-upload.md)和[大量裝置匯入/匯出](./iot-hub-bulk-identity-mgmt.md)。 將資源繫結至 VNet 預設會封鎖與資源的連線。 因此，此設定可防止 IoT 中樞將資料傳送至資源。 若要修正此問題，請透過 [信任的 Microsoft 服務] 選項，啟用從 IoT 中樞資源到儲存體帳戶、事件中樞或服務匯流排資源的連線。

### <a name="turn-on-managed-identity-for-iot-hub"></a>開啟 IoT 中樞的受控識別

若要允許其他服務作為信任的 Microsoft 服務來尋找 IoT 中樞，其必須具有系統指派的受控識別。

1. 在 IoT 中樞入口網站中，巡覽至 [身分識別]

1. 在 [狀態] 下，選取 [開啟]，然後按一下 [儲存]。

    :::image type="content" source="media/virtual-network-support/managed-identity.png" alt-text="顯示如何為 IoT 中樞開啟受控識別的螢幕擷取畫面":::

### <a name="assign-managed-identity-to-your-iot-hub-at-creation-time-using-arm-template"></a>使用 ARM 範本在建立時將受控識別指派給您的 IoT 中樞

若要在資源布建時將受控識別指派給您的 IoT 中樞，請使用下列 ARM 範本：

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

以您的資源、和的值取代之後 `name` `location` `SKU.name` `SKU.tier` ，您可以使用 Azure CLI，在現有的資源群組中部署資源：

```azurecli-interactive
az deployment group create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

建立資源之後，您可以使用 Azure CLI，來取得指派給您中樞的受控服務身分識別：

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

### <a name="pricing-for-managed-identity"></a>受控識別的定價

信任的 Microsoft 第一方服務例外功能為免費。 但佈建的儲存體帳戶、事件中樞或服務匯流排資源則會另外收取費用。

### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>輸出連線到儲存體帳戶端點以進行路由

IoT 中樞可將訊息路由傳送至客戶擁有的儲存體帳戶。 若要在有防火牆限制時允許路由功能存取儲存體帳戶， IoT 中樞必須具有[受控識別](#turn-on-managed-identity-for-iot-hub)。 布建受控識別之後，請遵循下列步驟，將 Azure RBAC 許可權授與您中樞的資源身分識別，以存取您的儲存體帳戶。

1. 在 Azure 入口網站中，巡覽至儲存體帳戶的 [存取控制 (IAM)] 索引標籤，然後按一下 [新增角色指派] 區段下的 [新增]。

2. 選取 [儲存體 Blob 資料參與者] ([而「不是」[參與者] 或 [儲存體帳戶參與者]](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)) 作為 [角色]、[Azure AD 使用者、群組或服務主體] 作為 [存取權指派對象為]，然後在下拉式清單中選取 IoT 中樞的資源名稱。 按一下 [儲存]  按鈕。

3. 巡覽至儲存體帳戶中的 [防火牆和虛擬網路] 索引標籤，然後啟用 [允許從選取的網路存取] 選項。 在 [例外] 清單下，核取 [允許信任的 Microsoft 服務存取此儲存體帳戶] 核取方塊。 按一下 [儲存]  按鈕。

4. 在 IoT 中樞的資源頁面上，巡覽至 [訊息路由] 索引標籤。

5. 巡覽至 [自訂端點] 區段，然後按一下 [新增]。 選取 [儲存體] 作為 [端點類型]。

6. 在顯示的頁面上提供端點的名稱，選取想要在 blob 儲存體中使用的容器，並提供編碼和檔案名稱格式。 選取 [以身分 **識別為基礎** ] 作為儲存體端點的 **驗證類型** 。 按一下 [ **建立** ] 按鈕。

現在，自訂儲存體端點已設定為使用中樞系統指派的身分識別，且即使有防火牆限制，也有權存取儲存體資源。 您現在可使用此端點來設定路由規則。

### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>輸出連線到事件中樞端點以進行路由

IoT 中樞可設定為將訊息路由傳送至客戶擁有的事件中樞命名空間。 若要在有防火牆限制時允許路由功能存取事件中樞資源， IoT 中樞必須具有受控識別。 一旦建立受控識別之後，請遵循下列步驟，將 Azure RBAC 許可權授與您中樞的資源身分識別，以存取您的事件中樞。

1. 在 Azure 入口網站中，巡覽至事件中樞的 [存取控制 (IAM)] 索引標籤，然後按一下 [新增角色指派] 區段下的 [新增]。

2. 選取 [事件中樞資料傳送者] 作為 [角色]、[Azure AD 使用者、群組或服務主體] 作為 [存取權指派對象為]，然後在下拉式清單中選取 IoT 中樞的資源名稱。 按一下 [儲存]  按鈕。

3. 巡覽至事件中樞內的 [防火牆和虛擬網路] 索引標籤，然後啟用 [允許從選取的網路存取] 選項。 在 [例外] 清單下，核取 [允許信任的 Microsoft 服務存取此事件中樞] 核取方塊。 按一下 [儲存]  按鈕。

4. 在 IoT 中樞的資源頁面上，巡覽至 [訊息路由] 索引標籤。

5. 巡覽至 [自訂端點] 區段，然後按一下 [新增]。 選取 [事件中樞] 作為 [端點類型]。

6. 在顯示的頁面上，提供您的端點名稱，然後選取您的事件中樞命名空間和實例。 選取 [以身分 **識別為基礎** ] 作為 **驗證類型** ，然後按一下 [ **建立** ] 按鈕。

現在，自訂事件中樞已設定為使用中樞系統指派的身分識別，且即使有防火牆限制，也有權存取事件中樞資源。 您現在可使用此端點來設定路由規則。

### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>輸出連線到服務匯流排端點以進行路由

IoT 中樞可設定為將訊息路由傳送至客戶擁有的服務匯流排命名空間。 若要在有防火牆限制時，允許路由功能存取服務匯流排資源， IoT 中樞必須具有受控識別。 布建受控識別之後，請遵循下列步驟，將 Azure RBAC 許可權授與您中樞的資源身分識別，以存取您的服務匯流排。

1. 在 Azure 入口網站中，巡覽至服務匯流排的 [存取控制 (IAM)] 索引標籤，然後按一下 [新增角色指派] 區段下的 [新增]。

2. 選取 [服務匯流排資料傳送者] 作為 [角色]、[Azure AD 使用者、群組或服務主體] 作為 [存取權指派對象為]，然後在下拉式清單中選取 IoT 中樞的資源名稱。 按一下 [儲存]  按鈕。

3. 巡覽至服務匯流排中的 [防火牆和虛擬網路] 索引標籤，然後啟用 [允許從選取的網路存取] 選項。 在 [例外] 清單下，核取 [允許信任的 Microsoft 服務存取此服務匯流排] 核取方塊。 按一下 [儲存]  按鈕。

4. 在 IoT 中樞的資源頁面上，巡覽至 [訊息路由] 索引標籤。

5. 巡覽至 [自訂端點] 區段，然後按一下 [新增]。 選取 [服務匯流排佇列] 或 [服務匯流排主題] (如果適用的話) 作為 [端點類型]。

6. 在顯示的頁面上，提供端點的名稱，並選取服務匯流排的命名空間以及佇列或主題 (如果適用的話)。 選取 [以身分 **識別為基礎** ] 作為 **驗證類型** ，然後按一下 [ **建立** ] 按鈕。

現在，服務匯流排已設定為使用中樞系統指派的身分識別，且即使有防火牆限制，也有權存取服務匯流排資源。 您現在可使用此端點來設定路由規則。

### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>輸出連線到儲存體帳戶以進行檔案上傳

IoT 中樞的檔案上傳功能可讓裝置將檔案上傳至客戶所擁有儲存體帳戶。 若要讓檔案上傳正常運作，裝置和 IoT 中樞都必須具有與儲存體帳戶的連線。 如果儲存體帳戶上有防火牆限制，裝置就必須使用任何支援的儲存體帳戶機制 (包括[私人端點](../private-link/tutorial-private-endpoint-storage-portal.md)、[服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)或[直接防火牆設定](../storage/common/storage-network-security.md)) 來取得連線。 同樣地，如果儲存體帳戶上有防火牆限制，IoT 中樞就必須設定為透過信任的 Microsoft 服務例外來存取儲存體資源。 基於此目的， IoT 中樞必須具有受控識別。 布建受控識別之後，請遵循下列步驟，將 Azure RBAC 許可權授與您中樞的資源身分識別，以存取您的儲存體帳戶。

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

1. 在 Azure 入口網站中，巡覽至儲存體帳戶的 [存取控制 (IAM)] 索引標籤，然後按一下 [新增角色指派] 區段下的 [新增]。

2. 選取 [儲存體 Blob 資料參與者] ([而「不是」[參與者] 或 [儲存體帳戶參與者]](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)) 作為 [角色]、[Azure AD 使用者、群組或服務主體] 作為 [存取權指派對象為]，然後在下拉式清單中選取 IoT 中樞的資源名稱。 按一下 [儲存]  按鈕。

3. 巡覽至儲存體帳戶中的 [防火牆和虛擬網路] 索引標籤，然後啟用 [允許從選取的網路存取] 選項。 在 [例外] 清單下，核取 [允許信任的 Microsoft 服務存取此儲存體帳戶] 核取方塊。 按一下 [儲存]  按鈕。

4. 在 IoT 中樞的資源頁面上，巡覽至 [檔案上傳] 索引標籤。

5. 在顯示的頁面上，選取想要在 blob 儲存體中使用的容器，然後視需要設定 [檔案通知設定]、[SAS TTL]、[預設 TTL] 和 [最大傳遞計數]。 選取 [以身分 **識別為基礎** ] 作為儲存體端點的 **驗證類型** 。 按一下 [ **建立** ] 按鈕。 如果您在此步驟中收到錯誤，請暫時將您的儲存體帳戶設定為允許來自 **所有網路** 的存取，然後再試一次。 一旦檔案上傳設定完成之後，您就可以在儲存體帳戶上設定防火牆。

現在，您用於檔案上傳的儲存體端點已設定為使用中樞系統所指派身分識別，且即使有防火牆限制，也有權存取儲存體資源。

### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>輸出連線到儲存體帳戶以進行大量裝置匯入/匯出

IoT 中樞支援在客戶提供的儲存體 blob 中大量[匯入/匯出](./iot-hub-bulk-identity-mgmt.md)裝置資訊的功能。 若要讓大量匯入/匯出功能正常運作，裝置和 IoT 中樞都必須具有與儲存體帳戶的連線。

這項功能需要從 IoT 中樞連線到儲存體帳戶。 若要在有防火牆限制時存取服務匯流排資源， IoT 中樞必須具有受控識別。 布建受控識別之後，請遵循下列步驟，將 Azure RBAC 許可權授與您中樞的資源身分識別，以存取您的服務匯流排。

1. 在 Azure 入口網站中，巡覽至儲存體帳戶的 [存取控制 (IAM)] 索引標籤，然後按一下 [新增角色指派] 區段下的 [新增]。

2. 選取 [儲存體 Blob 資料參與者] ([而「不是」[參與者] 或 [儲存體帳戶參與者]](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)) 作為 [角色]、[Azure AD 使用者、群組或服務主體] 作為 [存取權指派對象為]，然後在下拉式清單中選取 IoT 中樞的資源名稱。 按一下 [儲存]  按鈕。

3. 巡覽至儲存體帳戶中的 [防火牆和虛擬網路] 索引標籤，然後啟用 [允許從選取的網路存取] 選項。 在 [例外] 清單下，核取 [允許信任的 Microsoft 服務存取此儲存體帳戶] 核取方塊。 按一下 [儲存]  按鈕。

如需如何使用大量匯入/匯出功能的資訊，則現在可使用 Azure IoT REST API 來[建立匯入/匯出作業](/rest/api/iothub/service/jobs/getimportexportjobs)。 您必須在要求本文中提供 `storageAuthenticationType="identityBased"`，並分別使用 `inputBlobContainerUri="https://..."` 和 `outputBlobContainerUri="https://..."` 作為儲存體帳戶的輸入和輸出 URL。

Azure IoT 中樞 SDK 也會在服務用戶端的登錄管理員中支援這項功能。 下列程式碼片段示範如何使用 C# SDK 來啟動匯入作業或匯出作業。

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

若要使用具有 C#、Java 和 Node.js 虛擬網路支援的這個 Azure IoT SDK 版本：

1. 建立名為 `EnableStorageIdentity` 的環境變數，並將其值設定為 `1`。

2. 下載 SDK：[Java](https://aka.ms/vnetjavasdk) | [C#](https://aka.ms/vnetcsharpsdk) | [Node.js](https://aka.ms/vnetnodesdk)
 
針對 Python，請從 GitHub 下載有限版本。

1. 巡覽至 [GitHub 版本頁面](https://aka.ms/vnetpythonsdk)。

2. 下載下列檔案，您將會在發行頁面底部名為 [資產] 的標題下找到此檔案。
    > *azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl*

3. 開啟終端機，並巡覽至包含所下載檔案的資料夾。

4. 執行下列命令，以安裝具有虛擬網路支援的 Python 服務 SDK：
    > pip install ./azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl


## <a name="next-steps"></a>後續步驟

若要深入了解 IoT 中樞功能，請使用下列連結：

* [訊息路由](./iot-hub-devguide-messages-d2c.md)
* [檔案上傳](./iot-hub-devguide-file-upload.md)
* [大量裝置匯入/匯出](./iot-hub-bulk-identity-mgmt.md)
