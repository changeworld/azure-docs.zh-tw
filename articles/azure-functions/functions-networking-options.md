---
title: Azure Functions 網路功能選項
description: Azure 函數中提供的所有網路選項的概述。
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 6637627d48df8f9b6126debc215aac9bceb76f6b
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419569"
---
# <a name="azure-functions-networking-options"></a>Azure Functions 網路功能選項

本文介紹了 Azure 函數託管選項中可用的網路功能。 以下所有網路選項都使您能夠訪問資源,而無需使用可路由位址或限制對功能應用的 Internet 訪問。

託管模型具有不同級別的網路隔離可用。 選擇正確的網路隔離要求可説明您滿足網路隔離要求。

您可以透過多種方法託管函式應用:

* 您可以從在多租戶基礎結構上執行的計畫選項中進行選擇,這些選項具有不同等級的虛擬網路連接和縮放選項:
    * [消耗計畫](functions-scale.md#consumption-plan)可動態擴展以回應負載,並提供最少的網路隔離選項。
    * [高級計劃](functions-scale.md#premium-plan)還動態擴展,並提供更全面的網路隔離。
    * Azure[應用服務計劃](functions-scale.md#app-service-plan)以固定規模運行,並提供類似於高級計劃的網路隔離。
* 您可以在[應用服務環境中](../app-service/environment/intro.md)運行函數。 此方法將您的功能部署到虛擬網路中,並提供完整的網路控制和隔離。

## <a name="matrix-of-networking-features"></a>網路功能矩陣

|                |[消費計劃](functions-scale.md#consumption-plan)|[保費計劃](functions-scale.md#premium-plan)|[套用服務計劃](functions-scale.md#app-service-plan)|[套用服務環境](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[將 IP 限制與專用網站存取](#inbound-ip-restrictions)|✅是|✅是|✅是|✅是|
|[虛擬網路整合](#virtual-network-integration)|❌否|✅是(區域)|✅是(區域和閘道)|✅是|
|[虛擬網路觸發器 (非 HTTP)](#virtual-network-triggers-non-http)|❌否| ✅是 |✅是|✅是|
|[混合連線](#hybrid-connections)(僅限 Windows)|❌否|✅是|✅是|✅是|
|[出站 IP 限制](#outbound-ip-restrictions)|❌否| ✅是|✅是|✅是|

## <a name="inbound-ip-restrictions"></a>輸入 IP 限制

您可以使用 IP 限制來定義允許或拒絕存取應用的 IP 位址的優先順序排序列表。 該清單可以包括 IPv4 和 IPv6 位址。 當存在一個或多個條目時,清單末尾存在隱式"拒絕所有"。 IP 限制適用於所有功能託管選項。

> [!NOTE]
> 實施網路限制後,只能從虛擬網路內使用門戶編輯器,或者將用於訪問 Azure 門戶的電腦的 IP 位址放在「安全收件者」清單中。 但是,您仍然可以從任何電腦訪問 **「平臺功能**」選項卡上的任何功能。

要瞭解更多資訊,請參閱[Azure 應用服務靜態存取限制](../app-service/app-service-ip-restrictions.md)。

## <a name="private-site-access"></a>私人網站存取

專用網站訪問是指僅從專用網路(如 Azure 虛擬網路)訪問應用。

* 配置服務終結點時,在[高級](./functions-premium-plan.md)、[消費](functions-scale.md#consumption-plan)[和應用服務](functions-scale.md#app-service-plan)計畫中提供專用網站訪問。
    * 服務終結點可以根據**平臺功能** > **「網路** > **設定存取限制** > **添加規則**」按應用配置。 虛擬網路現在可以選擇為規則類型。
    * 如需詳細資訊，請參閱[虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)。
    * 請記住,對於服務終結點,您的功能仍具有對 Internet 的完全出站訪問,即使配置了虛擬網路集成也是如此。
* 專用網站訪問也可在配置內部負載均衡器 (ILB) 的應用服務環境中使用。 有關詳細資訊,請參閱[建立和使用具有應用服務環境的內部負載均衡器](../app-service/environment/create-ilb-ase.md)。

要瞭解如何設定私有網站訪問,請參閱[建立 Azure 函數專用網站存取](functions-create-private-site-access.md)。

## <a name="virtual-network-integration"></a>虛擬網路整合

虛擬網路整合式允許函數應用存取虛擬網路中的資源。
Azure 函數支援兩種類型的虛擬網路整合:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Azure 函數中的虛擬網路整合使用與應用服務 Web 應用共用基礎結構。 要瞭解有關兩種類型的虛擬網路集成的更多資訊,請參閱:

* [區域虛擬網路整合](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [閘道所需的虛擬網路整合](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

要瞭解如何設定虛擬網路整合,請參閱[將函數應用與 Azure 虛擬網路整合](functions-create-vnet.md)。

## <a name="regional-virtual-network-integration"></a>區域虛擬網路整合

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connect-to-service-endpoint-secured-resources"></a>連線到服務終結點安全資源

要提供更高級別的安全性,可以使用服務終結點將許多 Azure 服務限制為虛擬網路。 然後,您必須將函數應用與該虛擬網路整合以存取資源。 支援虛擬網路整合的計畫都支援此配置。

要瞭解更多資訊,請參閱[虛擬網路服務終結點](../virtual-network/virtual-network-service-endpoints-overview.md)。

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>將儲存帳戶限制為虛擬網路

建立函數應用時,必須創建或連結到支援 Blob、佇列和表存儲的通用 Azure 儲存帳戶。 您目前不能在此帳戶上使用任何虛擬網路限制。 如果在用於功能應用的存儲帳戶上配置虛擬網路服務終結點,則該配置將中斷應用。

要瞭解更多資訊,請參閱[儲存帳戶要求](./functions-create-function-app-portal.md#storage-account-requirements)。

## <a name="use-key-vault-references"></a>使用金鑰保存

可以使用 Azure 密鑰保管庫引用在 Azure 函數應用程式中使用 Azure 密鑰保管庫中的秘密,而無需進行任何代碼更改。 Azure Key Vault 是提供集中式祕密管理的服務，可完整控制存取原則和稽核歷程記錄。

目前,如果金鑰保管庫使用服務終結點保護,[密鑰保管庫引用](../app-service/app-service-key-vault-references.md)將不起作用。 要使用虛擬網路整合連接到密鑰保管庫,需要在應用程式代碼中調用密鑰保管庫。

## <a name="virtual-network-triggers-non-http"></a>虛擬網路觸發器 (非 HTTP)

目前,您可以通過兩種方式之一在虛擬網路內使用非 HTTP 觸發器函數:

+ 在先進計畫中執行函數應用,並啟用虛擬網路觸發器支援。
+ 在應用服務計劃或應用服務環境中運行函數應用。

### <a name="premium-plan-with-virtual-network-triggers"></a>具有虛擬網路觸發器的進階計劃

運行進階計畫時,可以將非 HTTP 觸發器功能連接到在虛擬網路內運行的服務。 為此,必須為函數應用啟用虛擬網路觸發器支援。 **虛擬網路觸發器支援**設置位於[Azure 門戶](https://portal.azure.com)中,位於**功能應用設置**下。

![虛擬網頁切換](media/functions-networking-options/virtual-network-trigger-toggle.png)

還可以使用以下 Azure CLI 命令啟用虛擬網路觸發器:

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

函數運行時的版本 2.x 及以上都支援虛擬網路觸發器。 支援以下非 HTTP 觸發器類型。

| 分機 | 最小版本 |
|-----------|---------| 
|[微軟.Azure.Web作業.擴展.存儲](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 或以上 |
|[微軟.Azure.Web作業.擴展.事件中心](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 或以上|
|[微軟.Azure.Web作業.擴展.服務總線](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 或以上|
|[微軟.Azure.Web作業.擴展.宇宙DB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 或以上|
|[微軟.Azure.Web作業.擴展.持久任務](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 或以上|

> [!IMPORTANT]
> 啟用虛擬網路觸發器支援時,只有上一表中顯示的觸發器類型會隨應用程式動態擴展。 您仍可以使用不在表中的觸發器,但它們不會超出其預熱的實例計數。 有關觸發器的完整清單,請參閱[觸發器和繫結](./functions-triggers-bindings.md#supported-bindings)。

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>具有虛擬網路觸發器的應用服務計畫和應用服務環境

當函數應用在應用服務計劃或應用服務環境中運行時,可以使用非 HTTP 觸發器功能。 要正確觸發函數,必須連接到具有對觸發器連接中定義的資源的訪問許可權的虛擬網路。

例如,假設您要配置 Azure Cosmos DB 以僅接受來自虛擬網路的流量。 在這種情況下,必須在提供與該虛擬網路的虛擬網路整合的應用服務計畫中部署函數應用。 集成使該 Azure Cosmos 資料庫資源可以觸發函數。

## <a name="hybrid-connections"></a>混合式連線

[混合連接](../service-bus-relay/relay-hybrid-connections-protocol.md)是 Azure 中繼的一項功能,可用於訪問其他網路中的應用程序資源。 它可讓您從應用程式存取應用程式端點。 不能使用它來訪問應用程式。 混合連接可用於在 Windows 上運行的函數,但使用計畫外的所有函數都可用。

如 Azure 函數中使用,每個混合連接都與單個 TCP 主機和埠組合相關聯。 這意味著,只要您訪問 TCP 偵聽埠,混合連接的終結點就可以位於任何作業系統和任何應用程式上。 混合連接功能不知道或不關心應用程式協定是什麼或您正在訪問的內容。 它只是提供網路訪問。

要瞭解更多資訊,請參閱[混合連線的應用程式服務文件](../app-service/app-service-hybrid-connections.md)。 這些相同的配置步驟支援 Azure 功能。

>[!IMPORTANT]
> 混合連接僅在 Windows 計畫中受支援。 不支援 Linux。

## <a name="outbound-ip-restrictions"></a>出站 IP 限制

出站 IP 限制在進階計畫、應用服務計畫或應用服務環境中可用。 您可以為部署應用服務環境的虛擬網路配置出站限制。

當您將功能應用整合到進階計畫或應用服務計畫中與虛擬網路時,預設情況下,該應用仍可以向 Internet 進行出站呼叫。 通過添加應用程式設定`WEBSITE_VNET_ROUTE_ALL=1`,可以強制將所有出站流量發送到虛擬網路,其中網路安全組規則可用於限制流量。

## <a name="troubleshooting"></a>疑難排解

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>後續步驟

要瞭解有關網路和 Azure 功能的更多資訊,:

* [按照有關開始虛擬網路整合的教學進行操作](./functions-create-vnet.md)
* [閱讀功能網路常見問題解答](./functions-networking-faq.md)
* [瞭解有關虛擬網路整合應用服務/功能的更多資訊](../app-service/web-sites-integrate-with-vnet.md)
* [瞭解有關 Azure 中虛擬網路詳細資訊](../virtual-network/virtual-networks-overview.md)
* [使用應用服務環境啟用更多網路功能和控制](../app-service/environment/intro.md)
* [使用混合連線連接到單一一資源資源,無需防火牆變更](../app-service/app-service-hybrid-connections.md)
