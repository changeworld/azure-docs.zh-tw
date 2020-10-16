---
title: 快速入門：建立和修改 ExpressRoute 線路：Azure CLI
description: 本快速入門說明如何使用 Azure CLI 建立、佈建、驗證、更新、刪除和取消佈建 ExpressRoute 線路。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: a1d50c3f8f94fbfd7dbcb9b25e051b7f2951c518
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91969053"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit-using-azure-cli"></a>快速入門：使用 Azure CLI 建立和修改 ExpressRoute 線路

本快速入門說明如何使用命令列介面 (CLI) 來建立 Azure ExpressRoute 線路。 本文也會示範如何檢查狀態、更新或刪除和取消佈建線路。

## <a name="prerequisites"></a>必要條件

* 開始設定之前，請檢閱[必要條件](expressroute-prerequisites.md)和[工作流程](expressroute-workflows.md)。
* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 安裝 CLI 命令的最新版本 (2.0 版或更新版本)。 如需關於安裝 CLI 命令的資訊，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli) 和[開始使用 Azure CLI](/cli/azure/get-started-with-azure-cli)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>建立和佈建 ExpressRoute 線路

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>登入您的 Azure 帳戶並且選取您的訂用帳戶

若要開始您的組態，請登入您的 Azure 帳戶。 如果您使用 Cloud Shell 的「試用」功能，將可自動登入。 使用下列範例來協助您連接：

```azurecli-interactive
az login
```

檢查帳戶的訂用帳戶。

```azurecli-interactive
az account list
```

選取您想要建立 ExpressRoute 線路的訂用帳戶。

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

### <a name="get-the-list-of-supported-providers-locations-and-bandwidths"></a>取得支援的提供者、位置和頻寬清單

建立 ExpressRoute 線路之前，您需要有支援的連線提供者、位置和頻寬選項的清單。 CLI 命令 `az network express-route list-service-providers` 會傳回此資訊，在稍後的步驟中將會用到：

```azurecli-interactive
az network express-route list-service-providers
```

回應如下列範例所示：

```output
[
  {
    "bandwidthsOffered": [
      {
        "offerName": "50Mbps",
        "valueInMbps": 50
      },
      {
        "offerName": "100Mbps",
        "valueInMbps": 100
      },
      {
        "offerName": "200Mbps",
        "valueInMbps": 200
      },
      {
        "offerName": "500Mbps",
        "valueInMbps": 500
      },
      {
        "offerName": "1Gbps",
        "valueInMbps": 1000
      },
      {
        "offerName": "2Gbps",
        "valueInMbps": 2000
      },
      {
        "offerName": "5Gbps",
        "valueInMbps": 5000
      },
      {
        "offerName": "10Gbps",
        "valueInMbps": 10000
      }
    ],
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/expressRouteServiceProviders/",
    "location": null,
    "name": "AARNet",
    "peeringLocations": [
      "Melbourne",
      "Sydney"
    ],
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "tags": null,
    "type": "Microsoft.Network/expressRouteServiceProviders"
  },
```

請檢查回應以查看是否列出您的連線提供者。 記下下列資訊，當您建立線路時將會用到：

* Name
* PeeringLocations
* BandwidthsOffered

您現在已經準備好建立 ExpressRoute 線路。

### <a name="create-an-expressroute-circuit"></a>建立 ExpressRoute 線路

> [!IMPORTANT]
> ExpressRoute 線路會從發出服務金鑰時開始收費。 在連線提供者準備好佈建線路之後，再執行這項作業。
>
>

若您還沒有資源群組，您必須在建立 ExpressRoute 線路之前建立一個。 您可以藉由使用下列命令來建立資源群組：

```azurecli-interactive
az group create -n ExpressRouteResourceGroup -l "West US"
```

以下示範如何透過矽谷的 Equinix 建立 200-Mbps ExpressRoute 線路。 如果您使用不同的提供者和不同的設定，請在您提出要求時取代成該資訊。

請確定您指定正確的 SKU 層和 SKU 系列：

* SKU 層會決定 ExpressRoute 線路是[本機](expressroute-faqs.md#expressroute-local)、標準或[進階](expressroute-faqs.md#expressroute-premium)。 您可以指定「本機」、「*標準」或「進階」。 您無法將 SKU 從「標準/進階」變更為「本機」。
* SKU 系列決定計費類型。 您可以指定 [Metereddata] 以採用計量付費數據傳輸方案，選取 [Unlimiteddata] 以採用無限行動數據方案。 您可以將計費類型從 [Metereddata] 變更為 [Unlimiteddata]，但無法將類型從 [Unlimiteddata] 變更為 [Metereddata]。 「本機」線路僅限 [UnlimitedData]。


ExpressRoute 線路會從發出服務金鑰時開始收費。 下列是新服務金鑰的要求範例：

```azurecli-interactive
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

回應包含服務金鑰。

### <a name="list-all-expressroute-circuits"></a>列出所有 ExpressRoute 循環

若要取得您已建立的所有 ExpressRoute 線路清單，請執行 `az network express-route list` 命令。 您隨時可以使用此命令來擷取此資訊。 若要列出所有線路，在不使用任何參數的情況下進行呼叫。

```azurecli-interactive
az network express-route list
```

回應的 [服務金鑰]** 欄位會列出您的服務金鑰。

```output
"allowClassicOperations": false,
"authorizations": [],
"circuitProvisioningState": "Enabled",
"etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
"gatewayManagerEtag": null,
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
"location": "westus",
"name": "MyCircuit",
"peerings": [],
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup",
"serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
"serviceProviderNotes": null,
"serviceProviderProperties": {
  "bandwidthInMbps": 200,
  "peeringLocation": "Silicon Valley",
  "serviceProviderName": "Equinix"
},
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

您可以藉由使用 '-h' 來執行命令，以取得所有參數的詳細描述。

```azurecli-interactive
az network express-route list -h
```

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>將服務金鑰傳送給連線提供者以進行佈建

'ServiceProviderProvisioningState' 提供服務提供者端目前的佈建狀態相關資訊。 狀態提供 Microsoft 端的狀態。 如需詳細資訊，請參閱[工作流程文章](expressroute-workflows.md#expressroute-circuit-provisioning-states)。

當您建立新的 ExpressRoute 線路時，線路會是下列狀態：

```output
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

當連線提供者目前為您啟用線路時，此線路會變更為下列狀態：

```output
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

若要使用 ExpressRoute 線路，它必須處於下列狀態：

```output
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>定期檢查線路金鑰的情況和狀態

檢查服務金鑰的情況和狀態將能讓您得知提供者是否已佈建您的線路。 設定線路之後，[ServiceProviderProvisioningState]** 會顯示為 [Provisioned]**，如下列範例所示：

```azurecli-interactive
az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
```

回應如下列範例所示：

```output
"allowClassicOperations": false,
"authorizations": [],
"circuitProvisioningState": "Enabled",
"etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
"gatewayManagerEtag": null,
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
"location": "westus",
"name": "MyCircuit",
"peerings": [],
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup",
"serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
"serviceProviderNotes": null,
"serviceProviderProperties": {
  "bandwidthInMbps": 200,
  "peeringLocation": "Silicon Valley",
  "serviceProviderName": "Equinix"
},
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

### <a name="create-your-routing-configuration"></a>建立路由組態

如需逐步指示，請參閱 [ExpressRoute 線路路由組態](howto-routing-cli.md) 一文以建立和修改線路對等。

> [!IMPORTANT]
> 這些指示只適用於由提供第 2 層連線服務的服務提供者所建立的線路。 如果您使用的服務提供者是提供受控第 3 層服務 (通常是 IP VPN，如 MPLS)，您的連線提供者會為您設定和管理路由。
>
>

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>將虛擬網路連結到 ExpressRoute 線路

接下來，將虛擬網路連結到 ExpressRoute 線路。 使用[將虛擬網路連結至 ExpressRoute 線路](howto-linkvnet-cli.md)一文。

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>修改 ExpressRoute 線路

您可以修改 ExpressRoute 線路的某些屬性，而不會影響連線。 您可以進行下列變更，而不會有停機時間：

* 您可以啟用或停用 ExpressRoute 線路的 ExpressRoute 進階附加元件。 不支援將 SKU 從「標準/進階」變更為「本機」。
* 只要連接埠有可用的容量，您就可以增加 ExpressRoute 線路的頻寬。 但是，不支援將線路的頻寬降級。
* 您可以將計量方案從 [已計量資料] 變更為 [無限制資料]。 但是，不支援將計量方案從 [無限制資料] 變更為 [已計量資料]。
* 您可以啟用和停用 [允許傳統作業] **。

如需限制的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。

### <a name="to-enable-the-expressroute-premium-add-on"></a>啟用 ExpressRoute 進階附加元件

您可以使用下列命令，為現有的線路啟用 ExpressRoute 進階附加元件：

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

線路現在會啟用 ExpressRoute 進階附加功能。 順利執行命令之後，我們就會立即開始針對進階附加元件功能計費。

### <a name="to-disable-the-expressroute-premium-add-on"></a>停用 ExpressRoute 進階附加元件

> [!IMPORTANT]
> 如果您使用的資源超出標準線路所允許的數量，這項作業可能會失敗。
>
>

停用 ExpressRoute 進階附加元件之前，請了解下列準則：

* 從進階降級為標準之前，您必須確定連結至線路的虛擬網路數目小於 10。 如果您不這樣做，更新要求就會失敗，且我們會以進階費率計費。
* 其他地理政治區域中的所有虛擬網路都必須先取消連結。 如果您未移除連結，更新要求就會失敗，且我們會以進階費率計費。
* 就私用對等設定而言，路由表必須少於 4000 個路由。 如果您的路由資料表大小大於 4000 個路由，BGP 工作階段會卸除。 直到已公告的前置詞數目低於 4000 之前，不會重新啟用 BGP 工作階段。

您可以使用下列範例，為現有的線路停用 ExpressRoute 進階附加元件：

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>更新 ExpressRoute 線路頻寬

請查閱 [ExpressRoute 常見問題集](expressroute-faqs.md)，以取得提供者支援的頻寬選項。 您可以挑選任何比現有線路規模還大的大小。

> [!IMPORTANT]
> 如果現有的連接埠上沒有足夠的容量，您可能必須重新建立 ExpressRoute 線路。 如果該位置已無額外的容量，您無法升級線路。
>
> 降低 ExpressRoute 線路的頻寬時必須中斷運作。 頻寬降級需要取消佈建 ExpressRoute 線路，然後重新佈建新的 ExpressRoute 線路。
>

一旦決定需要的大小後，可以使用下列命令來重新調整線路大小：

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

您的線路會在 Microsoft 端升級。 接下來，您必須連絡連線提供者，將他們的設定更新為符合這項變更。 通知之後，我們會開始以更新的頻寬選項計費。

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>將 SKU 從計量移動為無限

您可以使用下列範例來變更 ExpressRoute 線路的 SKU：

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>控制對傳統和 Resource Manager 環境的存取

請檢閱 [將 ExpressRoute 線路從傳統部署模型移至 Resource Manager 部署模型](expressroute-howto-move-arm.md)中的指示。

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>取消佈建 ExpressRoute 線路

若要取消佈建並刪除 ExpressRoute 線路，請確定您了解下列準則：

* 所有虛擬網路都必須從 ExpressRoute 線路取消連結。 如果此作業失敗，請確認是否有任何虛擬網路連結至循環。
* 如果 ExpressRoute 線路服務提供者佈建狀態為 **Provisioning** 或 **Provisioned**，您就必須與服務提供者一起合作，取消佈建他們那邊的線路。 我們會繼續保留資源並向您收取費用，直到線路服務提供者完成取消佈建並通知我們。
* 若服務提供者已取消佈建線路，表示服務提供者佈建狀態設定為 [未佈建]，則您可以刪除線路。 線路的計費將會停止。

## <a name="clean-up-resources"></a>清除資源

您可以執行下列命令來刪除 ExpressRoute 線路：

```azurecli-interactive
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>後續步驟

當您建立線路並與您的提供者進行佈建之後，請繼續進行下一個步驟來設定對等互連：

> [!div class="nextstepaction"]
> [建立和修改 ExpressRoute 線路的路由](howto-routing-cli.md)
