---
title: Azure Functions 網路功能選項
description: Azure 函數中提供的所有網路選項的概述。
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: d8c3357325eadefec7bb97faba5d600e9c6793a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276708"
---
# <a name="azure-functions-networking-options"></a>Azure Functions 網路功能選項

本文介紹了 Azure 函數託管選項中可用的網路功能。 以下所有網路選項都使您能夠訪問資源，而無需使用可路由位址，或限制對功能應用的 Internet 訪問。

託管模型具有不同級別的網路隔離可用。 選擇正確的網路隔離要求將説明您滿足網路隔離要求。

您可以通過多種方式託管函數應用：

* 在多租戶基礎結構上運行一組計畫選項，具有不同級別的虛擬網路連接和縮放選項：
    * [消耗計畫](functions-scale.md#consumption-plan)，它根據負載動態擴展，並提供最少的網路隔離選項。
    * [高級計畫](functions-scale.md#premium-plan)，也動態擴展，同時提供更全面的網路隔離。
    * Azure[應用服務方案](functions-scale.md#app-service-plan)，以固定規模運行，並提供與高級計畫類似的網路隔離。
* 您可以在[應用服務環境中](../app-service/environment/intro.md)運行函數。 此方法將您的功能部署到虛擬網路中，並提供完整的網路控制和隔離。

## <a name="matrix-of-networking-features"></a>網路功能矩陣

|                |[消費計畫](functions-scale.md#consumption-plan)|[保費計畫](functions-scale.md#premium-plan)|[應用服務方案](functions-scale.md#app-service-plan)|[應用服務環境](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[入站 IP 限制&專用網站訪問](#inbound-ip-restrictions)|✅是的|✅是的|✅是的|✅是的|
|[虛擬網路集成](#virtual-network-integration)|❌不|✅是（區域）|✅是（區域和閘道）|✅是的|
|[虛擬網路觸發器（非 HTTP）](#virtual-network-triggers-non-http)|❌不| ✅是的 |✅是的|✅是的|
|[混合連接](#hybrid-connections)（僅限 Windows）|❌不|✅是的|✅是的|✅是的|
|[出站 IP 限制](#outbound-ip-restrictions)|❌不| ✅是的|✅是的|✅是的|

## <a name="inbound-ip-restrictions"></a>入站 IP 限制

您可以使用 IP 限制來定義允許或拒絕訪問應用的 IP 位址的優先順序排序清單。 該清單可以包括 IPv4 和 IPv6 位址。 當存在一個或多個條目時，清單末尾存在隱式"拒絕所有"。 IP 限制適用于所有功能託管選項。

> [!NOTE]
> 實施網路限制後，只能從虛擬網路內使用門戶編輯器，或者將用於訪問 Azure 門戶的電腦的 IP 位址放在"安全收件者"清單中。 但是，您仍然可以從任何電腦訪問 **"平臺功能**"選項卡上的任何功能。

要瞭解更多資訊，請參閱[Azure 應用服務靜態訪問限制](../app-service/app-service-ip-restrictions.md)。

## <a name="private-site-access"></a>私人網站存取

專用網站訪問是指僅從私人網路絡（如 Azure 虛擬網路）訪問應用。

* 佈建服務終結點時，在[高級](./functions-premium-plan.md)、[消費](functions-scale.md#consumption-plan)[和應用服務](functions-scale.md#app-service-plan)計畫中提供專用網站訪問。
    * 服務終結點可以根據**平臺功能** > **"網路** > **配置訪問限制** > **添加規則**"按應用配置。 虛擬網路現在可以選擇為規則類型。
    * 有關詳細資訊，請參閱[虛擬網路服務終結點](../virtual-network/virtual-network-service-endpoints-overview.md)。
    * 請記住，對於服務終結點，您的功能仍具有對 Internet 的完全出站訪問，即使配置了虛擬網路集成也是如此。
* 專用網站訪問也可在配置內部負載等化器 （ILB） 的應用服務環境中使用。 有關詳細資訊，請參閱[創建和使用具有應用服務環境的內部負載等化器](../app-service/environment/create-ilb-ase.md)。

要瞭解如何設置私有網站訪問，請參閱[建立 Azure 函數私人網路站訪問](functions-create-private-site-access.md)。

## <a name="virtual-network-integration"></a>虛擬網路整合

虛擬網路集成允許函數應用訪問虛擬網路中的資源。 Azure 函數支援兩種類型的虛擬網路集成：

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Azure 函數中的虛擬網路集成使用與應用服務 Web 應用共用基礎結構。 要瞭解有關兩種類型的虛擬網路集成的更多資訊，請參閱：

* [區域虛擬網路集成](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [閘道需要虛擬網路集成](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

要瞭解如何設置虛擬網路集成，請參閱[將函數應用與 Azure 虛擬網路集成](functions-create-vnet.md)。

## <a name="regional-virtual-network-integration"></a>區域虛擬網路集成

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connecting-to-service-endpoint-secured-resources"></a>連接到服務終結點安全資源

要提供更高級別的安全性，可以使用服務終結點將許多 Azure 服務限制為虛擬網路。 然後，您必須將函數應用與該虛擬網路集成以訪問資源。 支援虛擬網路集成的所有計劃都支援此配置。

[瞭解有關虛擬網路服務終結點的更多資訊。](../virtual-network/virtual-network-service-endpoints-overview.md)

## <a name="restricting-your-storage-account-to-a-virtual-network"></a>將存儲帳戶限制為虛擬網路

創建函數應用時，必須創建或連結到支援 Blob、佇列和表存儲的通用 Azure 存儲帳戶。 您當前不能在此帳戶上使用任何虛擬網路限制。 如果在用於功能應用的存儲帳戶上配置虛擬網路服務終結點，這將中斷你的應用。

[詳細瞭解存儲帳戶要求。](./functions-create-function-app-portal.md#storage-account-requirements)

## <a name="using-key-vault-references"></a>使用金鑰保存庫引用 

金鑰保存庫引用允許您在 Azure 函數應用程式中使用 Azure 金鑰保存庫中的秘密，而無需進行任何代碼更改。 Azure Key Vault 是提供集中式祕密管理的服務，可完整控制存取原則和稽核歷程記錄。

當前，如果金鑰保存庫使用服務終結點保護，[則金鑰保存庫引用](../app-service/app-service-key-vault-references.md)將不起作用。 要使用虛擬網路集成連接到金鑰保存庫，您需要在應用程式代碼中調用金鑰保存庫。

## <a name="virtual-network-triggers-non-http"></a>虛擬網路觸發器（非 HTTP）

目前，您可以通過兩種方式之一在虛擬網路內使用非 HTTP 觸發器函數： 
+ 在高級計畫中運行函數應用，並啟用虛擬網路觸發器支援。
+ 在應用服務方案或應用服務環境中運行函數應用。

### <a name="premium-plan-with-virtual-network-triggers"></a>具有虛擬網路觸發器的高級計畫

在高級計畫中運行時，可以將非 HTTP 觸發器功能連接到虛擬網路中運行的服務。 為此，必須為函數應用啟用虛擬網路觸發器支援。 **虛擬網路觸發器支援**設置位於[Azure 門戶](https://portal.azure.com)中，位於**功能應用設置**下。

![VNETToggle](media/functions-networking-options/virtual-network-trigger-toggle.png)

您還可以使用以下 Azure CLI 命令啟用虛擬網路觸發器：

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

函數運行時的版本 2.x 及以上都支援虛擬網路觸發器。 支援以下非 HTTP 觸發器類型。

| 分機 | 最低版本 |
|-----------|---------| 
|[微軟.Azure.Web作業.擴展.存儲](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 或以上 |
|[微軟.Azure.Web作業.擴展.事件中心](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 或以上|
|[微軟.Azure.Web作業.擴展.服務匯流排](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 或以上|
|[微軟.Azure.Web作業.擴展.宇宙DB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 或以上|
|[微軟.Azure.Web作業.擴展.持久任務](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 或以上|

> [!IMPORTANT]
> 啟用虛擬網路觸發器支援時，只有上述觸發器類型會隨應用程式動態擴展。 您仍可以使用上面未列出的觸發器，但它們不會超出其預熱的實例計數。 有關觸發器的完整清單，請參閱[觸發器和綁定](./functions-triggers-bindings.md#supported-bindings)。

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>具有虛擬網路觸發器的應用服務方案和應用服務環境

當函數應用在應用服務方案或應用服務環境中運行時，可以使用非 HTTP 觸發器功能。 要正確觸發函數，必須連接到具有對觸發器連接中定義的資源的存取權限的虛擬網路。 

例如，假設您要配置 Azure Cosmos DB 以僅接受來自虛擬網路的流量。 在這種情況下，必須在提供與該虛擬網路的虛擬網路集成的應用服務方案中部署函數應用。 這使該函數可以由該 Azure Cosmos 資料庫資源觸發。 

## <a name="hybrid-connections"></a>混合式連線

[混合連接](../service-bus-relay/relay-hybrid-connections-protocol.md)是 Azure 中繼的一項功能，可用於訪問其他網路中的應用程式資源。 它可讓您從應用程式存取應用程式端點。 不能使用它來訪問應用程式。 混合連接可用於在 Windows 上運行的函數，除了消耗計畫外。

如 Azure 函數中使用，每個混合連接都與單個 TCP 主機和埠組合相關聯。 這意味著，只要您訪問 TCP 偵聽埠，混合連接的終結點就可以位於任何作業系統和任何應用程式上。 混合連接功能不知道或不關心應用程式協定是什麼或您正在訪問的內容。 它只是提供網路訪問。

要瞭解更多資訊，請參閱[混合連接的應用服務文件](../app-service/app-service-hybrid-connections.md)。 這些相同的配置步驟支援 Azure 功能。

>[!IMPORTANT]
> 混合連接僅在 Windows 計畫中受支援。 不支援 Linux

## <a name="outbound-ip-restrictions"></a>出站 IP 限制

出站 IP 限制在高級計畫、應用服務方案或應用服務環境中可用。 您可以為部署應用服務環境的虛擬網路配置出站限制。

當您將功能應用集成到高級計畫或應用服務方案中與虛擬網路時，預設情況下，該應用仍可以向 Internet 進行出站呼叫。 通過添加應用程式設定`WEBSITE_VNET_ROUTE_ALL=1`，可以強制將所有出站流量發送到虛擬網路，其中網路安全性群組規則可用於限制流量。

## <a name="troubleshooting"></a>疑難排解 

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>後續步驟

要瞭解有關網路和 Azure 功能的更多資訊，：

* [按照有關開始虛擬網路集成的教程進行操作](./functions-create-vnet.md)
* [閱讀功能網路常見問題解答](./functions-networking-faq.md)
* [瞭解有關虛擬網路集成應用服務/功能的更多資訊](../app-service/web-sites-integrate-with-vnet.md)
* [瞭解有關 Azure 中虛擬網路詳細資訊](../virtual-network/virtual-networks-overview.md)
* [使用應用服務環境啟用更多網路功能和控制](../app-service/environment/intro.md)
* [使用混合連接連接到單個本地資源，無需防火牆更改](../app-service/app-service-hybrid-connections.md)
