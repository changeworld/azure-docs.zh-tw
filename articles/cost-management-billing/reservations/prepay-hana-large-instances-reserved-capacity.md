---
title: 使用 Azure 保留來節省 SAP HANA 大型執行個體
description: 了解購買 HANA 大型執行個體保留之前需要知道的事項，以及如何進行購買。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: d6d0d0a4c4b3328fa50777b5106bac202c9972ef
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151500"
---
# <a name="save-on-sap-hana-large-instances-with-an-azure-reservation"></a>使用 Azure 保留來節省 SAP HANA 大型執行個體

當您預先購買一或三年期的 Azure 保留時，可以節省 SAP HANA 大型執行個體 (HLI) 的成本。 保留折扣會套用至符合所購買保留執行個體的已佈建 HLI SKU。 本文會協助您了解購買保留之前需要知道的事項，以及如何進行購買。

藉由購買保留，您可以確保一年或三年的 HLI 使用量。 HLI 保留容量購買涵蓋 SKU 隨附的計算和 NFS 儲存體。 保留不包括軟體授權成本，例如作業系統、SAP 或額外的儲存體成本。 保留折扣會自動套用至已佈建的 SAP HLI。 當保留期限結束時，隨用隨付費率會套用至您已佈建的資源。

## <a name="purchase-considerations"></a>購買考量

必須先佈建 HLI SKU，才能進行保留容量購買。 保留的付款方式是預付或每月付款。 下列限制會套用至 HLI 保留容量：

- 保留折扣僅套用至 Enterprise 合約和 Microsoft 客戶合約訂閱。 不支援其他訂閱。
- 針對 HLI 保留容量不支援執行個體大小彈性。 保留僅套用至您購買的 SKU 和區域。
- 不支援自助式取消和交換。
- 保留容量範圍是單一範圍，因此會套用至單一訂用帳戶和資源群組。 無法更新所購買的容量以供另一個訂用帳戶使用。
- 您不能有 HANA 保留容量的共用保留範圍。 您無法分割、合併或更新保留範圍。
- 您可以使用保留容量 API 呼叫，一次購買一個 HLI。 進行額外的 API 呼叫，以購買額外的數量。

您可以在 Azure 入口網站或使用 [REST API](/rest/api/reserved-vm-instances/reservationorder/purchase) 來購買保留容量。

## <a name="buy-a-hana-large-instance-reservation"></a>購買 HANA 大型執行個體保留

使用下列資訊，透過[保留訂單 REST API](/rest/api/reserved-vm-instances/reservationorder/purchase) 購買 HLI 保留。

### <a name="get-the-reservation-order-and-price"></a>取得保留訂單和價格

首先，使用[計算價格](/rest/api/reserved-vm-instances/reservationorder/calculate) API，取得已佈建 HANA 大型執行個體 SKU 的保留訂單和價格。

下列範例會使用 [armclient](https://github.com/projectkudu/ARMClient)，透過 PowerShell 進行 REST API 呼叫。 以下是保留訂單和計算價格 API 要求和要求本文應該類似的內容：

```azurepowershell-interactive
armclient post /providers/Microsoft.Capacity/calculatePrice?api-version=2018-06-01 "{
    'sku': {
        'name': 'SAP_HANA_On_Azure_S224om'
    },
    'location': 'eastus',
    'properties': {
        'reservedResourceType': 'SapHana',
        'billingScopeId': '/subscriptions/11111111-1111-1111-111111111111',
        'term': 'P1Y',
        'quantity': '1',
        'displayName': 'testreservation_S224om',
        'appliedScopes': ['/subscriptions/11111111-1111-1111-111111111111'],
        'appliedScopeType': 'Single',
       'instanceFlexibility': 'NotSupported'
    }
}"
```

如需有關資料欄位及其描述的詳細資訊，請參閱 [HLI 保留欄位](#hli-reservation-fields)。

下列範例回應類似於您獲得的傳回內容。 請注意您針對 `quoteId` 所傳回的值。

```
{
  "properties": {
    "currencyCode": "USD",
    "netTotal": 313219.0,
    "taxTotal": 0.0,
    "isTaxIncluded": false,
    "grandTotal": 313219.0,
    "purchaseRequest": {
      "sku": {
        "name": "SAP_HANA_On_Azure_S224om"
      },
      "location": "eastus",
      "properties": {
        "billingScopeId": "/subscriptions/11111111-1111-1111-111111111111",
        "term": "P1Y",
        "billingPlan": "Upfront",
        "quantity": 1,
        "displayName": "testreservation_S224om",
        "appliedScopes": [
          "/subscriptions/11111111-1111-1111-111111111111"
        ],
        "appliedScopeType": "Single",
        "reservedResourceType": "SapHana",
        "instanceFlexibility": "NotSupported"
      }
    },
    "quoteId": "d0fd3a890795",
    "isBillingPartnerManaged": true,
    "reservationOrderId": "22222222-2222-2222-2222-222222222222",
    "skuTitle": "SAP HANA on Azure Large Instances - S224om - US East",
    "skuDescription": "SAP HANA on Azure Large Instances, S224om",
    "pricingCurrencyTotal": {
      "currencyCode": "USD",
      "amount": 313219.0
    }
  }
}
```

### <a name="make-your-purchase"></a>進行購買

使用傳回的 `quoteId`，以及您從上述[取得保留訂單和價格](#get-the-reservation-order-and-price)一節所得到的 `reservationOrderId`，進行購買。

範例要求如下：

```azurepowershell-interactive
armclient put /providers/Microsoft.Capacity/reservationOrders/22222222-2222-2222-2222-222222222222?api-version=2018-06-01 "{
    'sku': {
        'name': 'SAP_HANA_On_Azure_S224om'
    },
    'location': 'eastus',
    'properties': {
        'reservedResourceType': 'SapHana',
        'billingScopeId': '/subscriptions/11111111-1111-1111-111111111111',
        'term': 'P1Y',
        'quantity': '1',
        'displayName': ' testreservation_S224om',
        'appliedScopes': ['/subscriptions/11111111-1111-1111-111111111111/resourcegroups/123'],
        'appliedScopeType': 'Single',
       'instanceFlexibility': 'NotSupported',
       'renew': true,
       'quoteId': 'd0fd3a890795'
    }
}"
```

範例回應如下。 如果成功下單，`provisioningState` 應該是 `creating`。

```
{
  "id": "/providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222",
  "type": "Microsoft.Capacity/reservationOrders",
  "name": "22222222-2222-2222-2222-222222222222",
  "etag": 1,
  "properties": {
    "displayName": "testreservation_S224om",
    "requestDateTime": "2020-07-14T05:42:34.3528353Z",
    "term": "P1Y",
    "provisioningState": "Creating",
    "reservations": [
      {
        "sku": {
          "name": "SAP_HANA_On_Azure_S224om"
        },
        "id": "/providers/microsoft.capacity/reservationOrders22222222-2222-2222-2222-222222222222/reservations/33333333-3333-3333-3333-3333333333333",
        "type": "Microsoft.Capacity/reservationOrders/reservations",
        "name": "22222222-2222-2222-2222-222222222222/33333333-3333-3333-3333-3333333333333",
        "etag": 1,
        "location": "eastus”
        "properties": {
          "appliedScopes": [
            "/subscriptions/11111111-1111-1111-111111111111/resourcegroups/123"
          ],
          "appliedScopeType": "Single",
          "quantity": 1,
          "provisioningState": "Creating",
          "displayName": " testreservation_S224om",
          "effectiveDateTime": "2020-07-14T05:42:34.3528353Z",
          "lastUpdatedDateTime": "2020-07-14T05:42:34.3528353Z",
          "reservedResourceType": "SapHana",
          "instanceFlexibility": "NotSupported",
          "skuDescription": "SAP HANA on Azure Large Instances – S224om - US East",
          "renew": true
        }
      }
    ],
    "originalQuantity": 1,
    "billingPlan": "Upfront"
  }
}
```

### <a name="verify-purchase-status-success"></a>確認購買狀態成功

執行保留訂單 GET 要求，以查看採購單的狀態。 `provisioningState` 應該是 `Succeeded`。

```azurepowershell-interactive
armclient get /providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222?api-version=2018-06-01
```

回應應該類似下列範例。

```
{
  "id": "/providers/microsoft.capacity/reservationOrders/44444444-4444-4444-4444-444444444444",
  "type": "Microsoft.Capacity/reservationOrders",
  "name": "22222222-2222-2222-2222-222222222222 ",
  "etag": 8,
  "properties": {
    "displayName": "testreservation_S224om",
    "requestDateTime": "2020-07-14T05:42:34.3528353Z",
    "createdDateTime": "2020-07-14T05:44:47.157579Z",
    "expiryDate": "2021-07-14",
    "term": "P1Y",
    "provisioningState": "Succeeded",
    "reservations": [
      {
        "id": "/providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222/reservations/33333333-3333-3333-3333-3333333333333"
      }
    ],
    "originalQuantity": 1,
    "billingPlan": "Upfront"
  }
}
```

## <a name="hli-reservation-fields"></a>HLI 保留欄位

下列資訊說明各種保留欄位的意義。

  **SKU** HLI SKU 名稱。 看起來像 `SAP_HANA_On_Azure_<SKUname>`。

  **位置** 可用的 HLI 區域。 請參閱 [SAP HANA on Azure (大型執行個體) 適用的 SKU](../../virtual-machines/workloads/sap/hana-available-skus.md)，以取得可用的區域。 若要取得位置字串格式，請使用[取得位置 API 呼叫](/rest/api/resources/subscriptions/listlocations#locationlistresult)。

  **保留的資源類型** `SapHana`

  **訂用帳戶** 用來支付保留項目的訂用帳戶。 保留項目的費用會透過訂用帳戶的付款方式收取。 訂用帳戶類型必須是 Enterprise 合約 (供應項目號碼：MS-AZR-0017P 或 MS-AZR-0148P) 或 Microsoft 客戶合約。 費用會從承諾用量金額餘額 (如果有的話) 扣除或作為超額部分收費。

  **範圍** 保留的範圍應該是單一範圍。

  **期限** 一年或三年。 看起來像 `P1Y` 或 `P3Y`。

  **數量** 為保留所購買的執行個體數目。 購買的數量為一次一個 HLI。 針對其他保留，請使用對應的欄位重複 API 呼叫。

## <a name="troubleshoot-errors"></a>針對錯誤進行疑難排解

當您進行保留購買時，可能會收到類似下列範例的錯誤。 可能的原因是 HLI 未佈建以進行購買。 若是如此，請洽詢您的 Microsoft 帳戶小組，在嘗試進行保留購買之前先行佈建 HLI。

```
{
  "error": {
    "code": "BadRequest",
    "message": "Capacity check or quota check failed. Please select a different subscription or 
location. You can also go to https://aka.ms/corequotaincrease to learn about quota increase."
  }
} 
```

## <a name="next-steps"></a>後續步驟

- 深入了解[如何使用 Postman 和 cURL 呼叫 Azure REST API](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)。
- 請參閱 [SAP HANA on Azure (大型執行個體) 適用的 SKU](../../virtual-machines/workloads/sap/hana-available-skus.md)，以取得可用的 SKU 清單和區域。