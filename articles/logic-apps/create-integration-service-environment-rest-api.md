---
title: 使用邏輯應用 REST API 建立整合服務環境 (ISEs)
description: 使用邏輯應用程式 REST API 建立整合服務環境 (ISE),以便可以從 Azure 邏輯應用存取 Azure 虛擬網路 (VNET)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 0670331d2338b4b6419ffbff1452b5fbac91029f
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478827"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>使用邏輯應用 REST API 建立整合服務環境 (ISE)

本文演示如何通過邏輯應用 REST API 創建[*整合服務環境*(ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)用於邏輯應用和整合帳戶需要存取[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)的方案。 ISE 是一個隔離的環境,它使用專用存儲和其他資源,這些資源與"全域"多租戶邏輯應用服務分開。 這種區隔也可減少任何其他 Azure 租用戶可能對您應用程式效能造成的影響。 ISE 還為您提供自己的靜態 IP 位址。 這些 IP 位址與公共多租戶服務中的邏輯應用共用的靜態 IP 位址分開。

還可以透過使用[範例 Azure 資源管理器快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment)或使用[Azure 門戶](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)創建 ISE。

> [!IMPORTANT]
> 在 ISE 中運行的邏輯應用、內建觸發器、內建操作和連接器使用不同於基於消耗的定價計劃的定價計劃。 要瞭解 ISEs 的定價和計費的工作原理,請參閱[邏輯應用定價模型](../logic-apps/logic-apps-pricing.md#fixed-pricing)。 有關定價率,請參閱[邏輯應用定價](../logic-apps/logic-apps-pricing.md)。

## <a name="prerequisites"></a>Prerequisites

* [開啟 ISE 存取](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)[的先決條件](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites)和要求與在 Azure 門戶中建立 ISE 時相同

* 一種工具,可用於使用 HTTPS PUT 請求呼叫邏輯應用 REST API 來建立 ISE。 例如,您可以使用[Postman](https://www.getpostman.com/downloads/),也可以生成執行此任務的邏輯應用。

## <a name="send-the-request"></a>傳送要求

要透過呼叫邏輯應用程式 REST API 建立 ISE,請發出此 HTTPS PUT 請求:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> 邏輯應用 REST API 2019-05-01 版本要求您對 ISE 連接器發出自己的 HTTP PUT 請求。

部署通常需要兩個小時才能完成。 有時,部署可能需要長達四個小時。 要檢查部署狀態,請在 Azure 工具列上的[Azure 門戶](https://portal.azure.com)中,選擇通知圖示,該圖示將打開通知窗格。

> [!NOTE]
> 如果部署失敗或您刪除了 ISE，Azure「可能」需要最多一小時的時間來釋出您的子網路。 此延遲意味著您可能需要等待,然後再在另一個 ISE 中重用這些子網。
>
> 如果刪除虛擬網路,Azure 通常會在釋放子網之前最多需要兩個小時,但此操作可能需要更長時間。 
> 刪除虛擬網路時,請確保沒有資源仍然連接。 
> 請參考[虛擬網路](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)。

## <a name="request-header"></a>要求標頭

在請求標頭中,包括以下屬性:

* `Content-type`:將此屬性值設定為`application/json`。

* `Authorization`:為有權訪問要使用的 Azure 訂閱或資源組的客戶設置此屬性值。

### <a name="request-body-syntax"></a>要求本文語法

下面是請求正文語法,它描述了創建 ISE 時要使用的屬性:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      }
   }
}
```

### <a name="request-body-example"></a>要求正文示例

此範例要求正文顯示範例值:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      }
   }
}
```

## <a name="next-steps"></a>後續步驟

* [新增資源服務環境](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [管理整合服務環境](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

