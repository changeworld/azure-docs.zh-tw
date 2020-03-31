---
title: Azure 函數高級計畫
description: Azure 函數高級計畫的詳細資訊和配置選項（VNet、無冷啟動、無限制執行持續時間）。
author: jeffhollan
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: jehollan
ms.openlocfilehash: dd7f6d0760f2b848435e7c77657e261517d29dd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276903"
---
# <a name="azure-functions-premium-plan"></a>Azure 函數高級計畫

Azure 函數高級計畫（有時稱為彈性高級計畫）是函數應用的託管選項。 高級計畫提供 VNet 連接、無冷啟動和高級硬體等功能。  可以將多個功能應用部署到同一高級計畫，並且該計畫允許您配置計算實例大小、基本計畫大小和最大計畫大小。  有關高級計畫和其他計畫和託管類型的比較，請參閱[功能規模和託管選項](functions-scale.md)。

## <a name="create-a-premium-plan"></a>建立進階方案

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

您還可以使用在 Azure CLI 中[創建的 az 函數應用計畫](/cli/azure/functionapp/plan#az-functionapp-plan-create)創建高級計畫。 以下示例創建_彈性高級高級 1_層計畫：

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

在此示例中，替換為資源組`<RESOURCE_GROUP>`，並`<PLAN_NAME>`替換為資源組中唯一的計畫的名稱。 指定[受支援的`<REGION>`](https://azure.microsoft.com/global-infrastructure/services/?products=functions)。 要創建支援 Linux 的高級計畫，請包括`--is-linux`該選項。

創建計畫後，可以使用[創建 az 函數應用](/cli/azure/functionapp#az-functionapp-create)來創建函數應用。 在門戶中，將同時創建計畫和應用。 有關完整 Azure CLI 腳本的示例，請參閱[在高級計畫中創建函數應用](scripts/functions-cli-create-premium-plan.md)。

## <a name="features"></a>特性

以下功能可用於部署到高級計畫的應用。

### <a name="pre-warmed-instances"></a>預熱實例

如果"消耗"計畫中今天未發生事件和執行，則應用可能會擴展到零實例。 當新事件進入時，需要專門使用運行其應用的新實例。  根據應用的不同，專業化新實例可能需要一些時間。  第一次調用時的額外延遲通常稱為應用冷啟動。

在高級計畫中，您可以讓應用在指定數量的實例上預熱，最多達到最低計畫大小。  預熱的實例還允許您在高負載之前預縮放應用。 當應用縮小時，它首先擴展到預熱的實例。 其他實例繼續緩衝並立即加熱，為下一次規模操作做準備。 通過具有預預熱實例的緩衝區，您可以有效地避免冷啟動延遲。  預熱實例是高級計畫的一項功能，您需要保持至少一個實例的運行狀態，並且在計畫處於活動狀態時隨時可用。

您可以通過選擇**功能應用**（進入 **"平臺功能"** 選項卡以及選擇 **"橫向擴展"** 選項）來配置 Azure 門戶中預熱實例的數量。 在函數應用編輯視窗中，預熱的實例特定于該應用，但最小和最大實例適用于整個計畫。

![彈性比例設置](./media/functions-premium-plan/scale-out.png)

還可以使用 Azure CLI 為應用配置預熱的實例。

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>專用網路連接

部署到高級計畫的 Azure 函數利用了[Web 應用的新 VNet 集成](../app-service/web-sites-integrate-with-vnet.md)。  配置後，你的應用可以與 VNet 中的資源通信或通過服務終結點進行保護。  應用程式上也提供 IP 限制，以限制傳入流量。

在高級計畫中將子網分配給函數應用時，需要每個潛在實例具有足夠的 IP 位址的子網。 我們需要一個至少具有 100 個可用位址的 IP 塊。

有關詳細資訊，請參閱[將函數應用與 VNet 集成](functions-create-vnet.md)。

### <a name="rapid-elastic-scale"></a>快速彈性刻度

使用與消耗計畫相同的快速縮放邏輯，為您的應用自動添加其他計算實例。  要瞭解有關縮放的工作原理，請參閱[函數縮放和託管](./functions-scale.md#how-the-consumption-and-premium-plans-work)。

### <a name="longer-run-duration"></a>更長的運行持續時間

使用計畫中的 Azure 函數在單個執行時限制為 10 分鐘。  在高級計畫中，運行持續時間預設為 30 分鐘，以防止失控執行。 但是，您可以[修改 host.json 配置](./functions-host-json.md#functiontimeout)，使其對高級計畫應用無限制（保證 60 分鐘）。

## <a name="plan-and-sku-settings"></a>計畫和 SKU 設置

創建計畫時，配置兩個設置：最小實例數（或計畫大小）和最大突發限制。  保留最小實例並始終運行。

> [!IMPORTANT]
> 無論函數是否正在執行，都會為在最小實例計數中分配的每個實例收費。

如果應用需要超出計畫大小的實例，則可以繼續橫向擴展，直到實例數達到最大突發限制。  只有在超出計畫大小的實例運行並租給您時，才會向您收費。  我們將盡最大努力將你的應用擴展到其定義的最大限制，同時保證你的應用的最小計畫實例。

您可以通過在計畫中選擇 **"橫向擴展"** 選項或部署到該計畫的函數應用（在**平臺功能**下）來配置 Azure 門戶中的計畫大小和最大值。

您還可以從 Azure CLI 增加最大突發限制：

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>可用實例 SKU

創建或縮放計畫時，可以在三個實例大小之間進行選擇。  您將按每秒消耗的內核和記憶體總數計費。  您的應用可以根據需要自動擴展到多個實例。  

|SKU|核心|記憶體|存放裝置|
|--|--|--|--|
|EP1|1|3.5GB|250GB|
|EP2|2|7 GB|250GB|
|EP3|4|14 GB|250GB|

### <a name="memory-utilization-considerations"></a>記憶體利用率注意事項
在記憶體較多的電腦上運行並不總是意味著函數應用將使用所有可用記憶體。

例如，JavaScript 函數應用受 Node.js 中的預設記憶體限制的約束。 要增加此固定記憶體限制，添加值 為`languageWorkers:node:arguments`的應用`--max-old-space-size=<max memory in MB>`設置。

## <a name="region-max-scale-out"></a>區域最大橫向擴展

以下是當前支援的每個區域和作業系統配置中單個計畫的最大橫向擴展值。 要要求增加，請打開一張支援票。

在此處查看功能的完整區域可用性[：Azure.com](https://azure.microsoft.com/global-infrastructure/services/?products=functions)

|區域| Windows | Linux |
|--| -- | -- |
|澳大利亞中部| 20 | 無法使用 |
|澳大利亞中部 2| 20 | 無法使用 |
|澳大利亞東部| 100 | 20 |
|澳大利亞東南部 | 100 | 20 |
|巴西南部| 60 | 20 |
|加拿大中部| 100 | 20 |
|美國中部| 100 | 20 |
|東亞| 100 | 20 |
|美國東部 | 100 | 20 |
|美國東部 2| 100 | 20 |
|法國中部| 100 | 20 |
|德國中西部| 100 | 無法使用 |
|日本東部| 100 | 20 |
|日本西部| 100 | 20 |
|南韓中部| 100 | 20 |
|美國中北部| 100 | 20 |
|北歐| 100 | 20 |
|挪威東部| 20 | 20 |
|美國中南部| 100 | 20 |
|印度南部 | 100 | 無法使用 |
|東南亞| 100 | 20 |
|英國南部| 100 | 20 |
|英國西部| 100 | 20 |
|西歐| 100 | 20 |
|印度西部| 100 | 20 |
|美國中西部| 20 | 20 |
|美國西部| 100 | 20 |
|美國西部 2| 100 | 20 |

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瞭解 Azure 函數縮放和託管選項](functions-scale.md)
