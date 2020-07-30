---
title: Azure Functions 網路選項
description: Azure Functions 中所有可用的網路選項概觀。
ms.topic: conceptual
ms.date: 4/11/2019
ms.openlocfilehash: 60258ef4aa3bbbbab69acd4f5106c774caa6f46f
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87385937"
---
# <a name="azure-functions-networking-options"></a>Azure Functions 網路選項

本文說明 Azure Functions 的各項裝載選項可用的網路功能。 下列所有網路選項都可讓您在不使用網際網路路由式位址的情況下存取資源，或限制函式應用程式的網際網路存取。

裝載模型有不同層級的網路隔離可用。 選擇正確的層級可協助您符合網路隔離需求。

您可以透過幾種方式來裝載函式應用程式：

* 您可以從在多租用戶基礎結構上執行的方案選項中進行選擇，其具有各種層級的虛擬網路連線能力和調整選項：
    * [取用方案](functions-scale.md#consumption-plan)會動態調整來回應負載，並提供最少的網路隔離選項。
    * [進階方案](functions-scale.md#premium-plan)也會動態調整，並提供更全面的網路隔離。
    * Azure [App Service 方案](functions-scale.md#app-service-plan)會以固定的規模運作，並提供類似進階方案的網路隔離。
* 您可以在 [App Service 環境](../app-service/environment/intro.md)中執行函式。 此方法會將您的函式部署至虛擬網路，並提供完整的網路控制和隔離功能。

## <a name="matrix-of-networking-features"></a>網路功能的矩陣

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="inbound-ip-restrictions"></a>輸入 IP 限制

您可使用 IP 限制，針對允許或拒絕存取應用程式的 IP 位址，定義依優先順序排列的清單。 這份清單可以包含 IPv4 和 IPv6 位址。 有一個或多個項目時，清單結尾會有隱含的「全部拒絕」語句。 IP 限制適用所有函式裝載選項。

> [!NOTE]
> 有了網路限制，您只能從虛擬網路使用入口網站編輯器，或是當您在 [安全收件者] 清單上放入用來存取 Azure 入口網站的電腦 IP 位址時，才可使用入口網站編輯器。 不過，您仍然可以從任何電腦存取 [平台功能] 索引標籤上的任何功能。

若要深入了解，請參閱 [Azure App Service 靜態存取限制](../app-service/app-service-ip-restrictions.md)。

## <a name="private-site-access"></a>私人網站存取

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

## <a name="virtual-network-integration"></a>虛擬網路整合

虛擬網路整合可讓您的函式應用程式存取虛擬網路內的資源。
Azure Functions 支援兩種虛擬網路整合：

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Azure Functions 中的虛擬網路整合會使用共用基礎結構搭配 App Service Web 應用程式。 若要深入了解這兩種類型的虛擬網路整合，請參閱：

* [區域性虛擬網路整合](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [閘道所需的虛擬網路整合](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

若要了解如何設定虛擬網路整合，請參閱[整合函式應用程式與 Azure 虛擬網路](functions-create-vnet.md)。

## <a name="regional-virtual-network-integration"></a>區域性虛擬網路整合

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connect-to-service-endpoint-secured-resources"></a>連線至服務端點安全資源

若要提供較高層級的安全性，您可使用服務端點，將一些 Azure 服務限制於虛擬網路。 接著，您必須將函式應用程式與該虛擬網路整合，才能存取資源。 支援虛擬網路整合的所有方案都支援此設定。

若要深入了解，請參閱[虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)。

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>將儲存體帳戶限定於虛擬網路

建立函式應用程式時，您必須建立或連結至支援 Blob、佇列及資料表儲存體的一般用途 Azure 儲存體帳戶。 您目前無法在此帳戶上使用任何虛擬網路限制。 如果您在用於函式應用程式的儲存體帳戶上設定虛擬網路服務端點，該設定將會中斷您的應用程式。

若要深入了解，請參閱[儲存體帳戶需求](./functions-create-function-app-portal.md#storage-account-requirements)。

## <a name="use-key-vault-references"></a>使用 Key Vault 參考

您可使用 Azure Key Vault 參考在 Azure Functions 應用程式中使用來自 Azure Key Vault 的祕密，而不需進行任何程式碼變更。 Azure Key Vault 是提供集中式祕密管理的服務，可完整控制存取原則和稽核歷程記錄。

目前，如果您的金鑰保存庫受到服務端點的保護，[Key Vault 參考](../app-service/app-service-key-vault-references.md) 將無法運作。 若要使用虛擬網路整合來連線到金鑰保存庫，您必須在應用程式代碼中呼叫 Key Vault。

## <a name="virtual-network-triggers-non-http"></a>虛擬網路觸發程序 (非 HTTP)

目前，您可使用下列其中一種方式，在虛擬網路內使用非 HTTP 觸發程序函式：

+ 在進階方案中執行函式應用程式，並啟用虛擬網路觸發程序支援。
+ 在 App Service 方案或 App Service 環境中執行函式應用程式。

### <a name="premium-plan-with-virtual-network-triggers"></a>具有虛擬網路觸發程序的進階方案

當您執行進階方案時，可以將非 HTTP 觸發程序函式連線到在虛擬網路中執行的服務。 若要這麼做，您必須啟用函式應用程式的虛擬網路觸發程序支援。 [虛擬網路觸發程序支援] 設定位於 [Azure 入口網站](https://portal.azure.com)中的 [設定] > [函式執行階段設定] 之下。

:::image type="content" source="media/functions-networking-options/virtual-network-trigger-toggle.png" alt-text="VNETToggle":::

您也可使用下列 Azure CLI 命令來啟用虛擬網路觸發程序：

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

2\.x 版和更新版本的 Functions 執行階段可支援虛擬網路觸發程序。 支援下列非 HTTP 觸發程序類型。

| 分機 | 最小版本 |
|-----------|---------| 
|[Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 或更新版本 |
|[Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 或更新版本|
|[Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 或更新版本|
|[Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 或更新版本|
|[Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 或更新版本|

> [!IMPORTANT]
> 當您啟用虛擬網路觸發程序支援時，只有上表中顯示的觸發程序類型會隨您的應用程式動態調整。 您仍然可以使用不在表格中的觸發程序，但其不會調整超過其預熱的執行個體計數。 如需完整的觸發程序清單，請參閱[觸發程序和繫結](./functions-triggers-bindings.md#supported-bindings)。

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>具有虛擬網路觸發程序的 App Service 方案和 App Service 環境

當您的函式應用程式在 App Service 方案或 App Service 環境中執行時，您可使用非 HTTP 觸發程序函式。 為了讓函式能正確地觸發，您必須連線到可存取觸發程序連線中所定義資源的虛擬網路。

例如，假設您想要將 Azure Cosmos DB 設定為只接受來自虛擬網路的流量。 在此情況下，您必須在提供與該虛擬網路整合的 App Service 方案中部署函式應用程式。 整合可讓該 Azure Cosmos DB 資源觸發函式。

## <a name="hybrid-connections"></a>混合式連線

[混合式連線](../azure-relay/relay-hybrid-connections-protocol.md)是 Azure 轉送的功能，您可用來存取其他網路中的應用程式資源。 它可讓您從應用程式存取應用程式端點。 您可將其用於存取應用程式。 混合式連線適用於所有在 Windows 上執行的函式 (但取用方案除外)。

如同在 Azure Functions 中使用，每個混合式連線都會與單一 TCP 主機和連接埠組合相互關聯。 這意謂著混合式連線的端點可位於任何作業系統和任何應用程式上，只要您正在存取 TCP 接聽連接埠即可。 混合式連線不知道 (或不在意) 應用程式通訊協定為何，或您要存取什麼資源。 其只會提供網路存取。

若要深入了解，請參閱[混合式連線的 App Service 文件](../app-service/app-service-hybrid-connections.md)。 這些相同的設定步驟支援 Azure Functions。

>[!IMPORTANT]
> 只有在 Windows 方案上支援混合式連線。 不支援 Linux。

## <a name="outbound-ip-restrictions"></a>輸出 IP 限制

輸出 IP 限制適用於高階方案、App Service 方案或 App Service 環境。 您可以為部署 App Service 環境的虛擬網路設定輸出限制。

當您將高階方案或 App Service 方案中的函式應用程式與虛擬網路整合時，應用程式預設仍可對網際網路進行輸出呼叫。 您可藉由新增應用程式設定 `WEBSITE_VNET_ROUTE_ALL=1`，強制將所有輸出流量傳送到您的虛擬網路，在其中可使用網路安全性群組規則來限制流量。

## <a name="automation"></a>自動化
下列 Api 可讓您以程式設計方式管理區域虛擬網路整合：

+ **Azure CLI**：使用 [`az functionapp vnet-integration`](/cli/azure/functionapp/vnet-integration) 命令來新增、列出或移除區域虛擬網路整合。  
+ **ARM 範本**：您可以使用 Azure Resource Manager 範本來啟用區域虛擬網路整合。 如需完整範例，請參閱[此函數快速入門範本](https://azure.microsoft.com/resources/templates/101-function-premium-vnet-integration/)。

## <a name="troubleshooting"></a>疑難排解

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>後續步驟

若要深入了解網路和 Azure Functions：

* [遵循有關開始使用虛擬網路整合的教學課程](./functions-create-vnet.md)
* [閱讀 Functions 網路常見問題集](./functions-networking-faq.md)
* [深入了解與 App Service / Functions 的虛擬網路整合](../app-service/web-sites-integrate-with-vnet.md)
* [深入了解 Azure 中的虛擬網路](../virtual-network/virtual-networks-overview.md)
* [透過 App Service 環境啟用更多網路功能與控制](../app-service/environment/intro.md)
* [使用混合式連線來連線到個別內部部署資源，而不需進行防火牆變更](../app-service/app-service-hybrid-connections.md)
