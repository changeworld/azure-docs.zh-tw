---
title: 使用 Logic Apps REST API 建立整合服務環境（Ise）
description: 使用 Logic Apps REST API 建立整合服務環境（ISE），讓您可以從 Azure Logic Apps 存取 Azure 虛擬網路（Vnet）
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: d33207639ebef912307a3c594ec274fd9609bd67
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84656549"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>使用 Logic Apps REST API 建立整合服務環境 (ISE)

本文說明如何在您的邏輯應用程式和整合帳戶需要存取[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)的情況下，透過 Logic Apps REST API 建立[*整合服務環境*（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 。 ISE 是一種專用環境，會使用與「全域」多租用戶 Logic Apps 服務分開的專用儲存體和其他資源。 這種區隔也可減少任何其他 Azure 租用戶可能對您應用程式效能造成的影響。 ISE 也會為您提供專用的靜態 IP 位址。 這些 IP 位址與邏輯應用程式在公用多租用戶服務中所共用的靜態 IP 位址不同。

您也可以使用[範例 Azure Resource Manager 快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment)，或使用[AZURE 入口網站](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)來建立 ISE。

> [!IMPORTANT]
> Logic Apps、內建動作，以及在您 ISE 中執行的連接器，其使用的定價方案不同於耗用量式定價方案。 若要了解適用於 ISE 的定價和計費方式，請參閱 [Logic Apps 定價模型](../logic-apps/logic-apps-pricing.md#fixed-pricing)。 如需定價費率，請參閱 [Logic Apps 定價](../logic-apps/logic-apps-pricing.md)。

## <a name="prerequisites"></a>必要條件

* 相同的[必要條件](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites)和需求，可讓您在 Azure 入口網站中建立 ise 時，[啟用 ise 的存取權。](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)

* 一種工具，可讓您使用 HTTPS PUT 要求呼叫 Logic Apps REST API 來建立 ISE。 例如，您可以使用[Postman](https://www.getpostman.com/downloads/)，也可以建立執行這項工作的邏輯應用程式。

## <a name="send-the-request"></a>傳送要求

若要藉由呼叫 Logic Apps REST API 來建立您的 ISE，請提出此 HTTPS PUT 要求：

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Logic Apps REST API 2019-05-01 版要求您自行建立 ISE 連接器的 HTTP PUT 要求。

部署通常會在兩個小時內完成。 有時候，部署需要的時間可能高達四小時。 若要檢查部署狀態，請在 [ [Azure 入口網站](https://portal.azure.com)的 Azure 工具列上，選取 [通知] 圖示，這會開啟 [通知] 窗格。

> [!NOTE]
> 如果部署失敗或您刪除 ISE，Azure「可能」需要最多一小時的時間來釋出您的子網路。 這個延遲表示，您可能必須稍等，才能在另一個的 ISE 中重複使用這些子網路。
>
> 如果您刪除虛擬網路，Azure 通常最多需要兩小時的時間，才能釋出您的子網路，但此作業可能需要更長的時間。 
> 刪除虛擬網路時，請確定沒有任何資源仍處於連線狀態。 
> 請參閱[刪除虛擬網路](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)。

## <a name="request-header"></a>要求標頭

在要求標頭中，包含下列屬性：

* `Content-type`：將此屬性值設定為 `application/json` 。

* `Authorization`：將此屬性值設定為可存取您想要使用之 Azure 訂用帳戶或資源群組之客戶的持有人權杖。

<a name="request-body"></a>

## <a name="request-body"></a>Request body

以下是要求本文語法，其描述建立 ISE 時要使用的屬性。 若要建立允許使用安裝在位置之自我簽署憑證的 ISE `TrustedRoot` ，請在 `certificates` ISE 定義的區段內包含物件 `properties` 。 針對現有的 ISE，您可以只傳送物件的 PATCH 要求 `certificates` 。 如需有關使用自我簽署憑證的詳細資訊，請參閱[HTTP 連接器-自我簽署憑證](../connectors/connectors-native-http.md#self-signed)。

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
      },
      // Include `certificates` object to enable self-signed certificate support
      "certificates": {
         "testCertificate": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

### <a name="request-body-example"></a>要求本文範例

這個範例要求主體會顯示範例值：

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
      },
      "certificates": {
         "testCertificate": {
            "publicCertificate": "LS0tLS1CRUdJTiBDRV...",
            "kind": "TrustedRoot"
         }
   }
}
```

## <a name="next-steps"></a>後續步驟

* [將資源新增到整合服務環境](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [管理整合服務環境](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

