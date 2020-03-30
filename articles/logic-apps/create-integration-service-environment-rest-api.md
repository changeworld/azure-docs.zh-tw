---
title: 使用邏輯應用 REST API 創建整合服務環境 （ISEs）
description: 通過使用邏輯應用 REST API 創建整合服務環境 （ISE），以便可以從 Azure 邏輯應用訪問 Azure 虛擬網路 （VNET）
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 2c6e35b1e7d160064998004f87c5b14d0eaeac5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127652"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>使用邏輯應用 REST API 創建整合服務環境 （ISE）

本文演示如何通過邏輯應用 REST API 創建[*整合服務環境*（ISE），](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)用於邏輯應用和集成帳戶需要訪問[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)的方案。 ISE 是一個隔離的環境，它使用專用存儲和其他資源，這些資源與"全域"多租戶邏輯應用服務分開。 這種區隔也可減少任何其他 Azure 租用戶可能對您應用程式效能造成的影響。 ISE 還為您提供自己的靜態 IP 位址。 這些 IP 位址與公共多租戶服務中的邏輯應用共用的靜態 IP 位址分開。

要使用 Azure 門戶創建 ISE，請參閱[從 Azure 邏輯應用連接到 Azure 虛擬網路](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)。

> [!IMPORTANT]
> 在 ISE 中運行的邏輯應用、內置觸發器、內置操作和連接器使用不同于基於消耗的定價計畫的定價計畫。 要瞭解 ISEs 的定價和計費的工作原理，請參閱[邏輯應用定價模型](../logic-apps/logic-apps-pricing.md#fixed-pricing)。 有關定價率，請參閱[邏輯應用定價](../logic-apps/logic-apps-pricing.md)。

## <a name="prerequisites"></a>Prerequisites

* [啟用 ISE 訪問](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)[的先決條件](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites)和要求與在 Azure 門戶中創建 ISE 時相同

* 一種工具，可用於使用 HTTPS PUT 請求調用邏輯應用 REST API 來創建 ISE。 例如，您可以使用[Postman](https://www.getpostman.com/downloads/)，也可以生成執行此任務的邏輯應用。

## <a name="send-the-request"></a>傳送要求

要通過調用邏輯應用 REST API 創建 ISE，請發出此 HTTPS PUT 請求：

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> 邏輯應用 REST API 2019-05-01 版本要求您對 ISE 連接器發出自己的 HTTP PUT 請求。

部署通常需要兩個小時才能完成。 有時，部署可能需要長達四個小時。 要檢查部署狀態，請在 Azure 工具列上的[Azure 門戶](https://portal.azure.com)中，選擇通知圖示，該圖示將打開通知窗格。

> [!NOTE]
> 如果部署失敗或您刪除了 ISE，Azure「可能」需要最多一小時的時間來釋出您的子網路。 此延遲意味著您可能需要等待，然後再在另一個 ISE 中重用這些子網。
>
> 如果刪除虛擬網路，Azure 通常會在釋放子網之前最多需要兩個小時，但此操作可能需要更長時間。 
> 刪除虛擬網路時，請確保沒有資源仍然連接。 
> 請參閱[刪除虛擬網路](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)。

## <a name="request-header"></a>要求標頭

在請求標頭中，包括以下屬性：

* `Content-type`：將此屬性值設置為`application/json`。

* `Authorization`：為有權訪問要使用的 Azure 訂閱或資源組的客戶設置此屬性值。

### <a name="request-body-syntax"></a>要求本文語法

下面是請求正文語法，它描述了創建 ISE 時要使用的屬性：

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

### <a name="request-body-example"></a>請求正文示例

此示例請求正文顯示示例值：

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

* [向整合服務環境添加資源](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [管理整合服務環境](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

