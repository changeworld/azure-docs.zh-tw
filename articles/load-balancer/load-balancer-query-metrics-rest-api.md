---
title: 使用 REST API 來擷取計量
titleSuffix: Azure Load Balancer
description: 在本文中，開始使用 Azure REST Api 來收集 Azure Load Balancer 的健康情況和使用計量。
services: sql-database
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: how-to
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: eeca560989c174bcb654116eb13da40d6ec79e60
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94700506"
---
# <a name="get-load-balancer-usage-metrics-using-the-rest-api"></a>使用 REST API 取得 Load Balancer 使用計量

使用[Azure REST API](/rest/api/azure/)收集一段時間的[Standard Load Balancer](./load-balancer-overview.md)所處理的位元組數目。

REST API 的完整參考文件和其他範例可於 [Azure 監視器 REST 參考](/rest/api/monitor)中取得。 

## <a name="build-the-request"></a>建立要求

請使用下列 GET 要求以從 Standard Load Balancer 收集 [ByteCount 計量](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics)。 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>要求標頭

以下是必要標頭： 

|要求標頭|描述|  
|--------------------|-----------------|  
|*Content-Type:*|必要。 設定為 `application/json`。|  
|*Authorization:*|必要。 設定為有效的 `Bearer` [存取權杖](/rest/api/azure/#authorization-code-grant-interactive-clients)。 |  

### <a name="uri-parameters"></a>URI 參數

| 名稱 | 描述 |
| :--- | :---------- |
| subscriptionId | 可識別 Azure 訂用帳戶的訂用帳戶識別碼。 如果您有多個訂閱，請參閱使用 [多個訂閱](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)。 |
| resourceGroupName | 包含資源的資源群組名稱。 您可以從 Azure Resource Manager API、CLI 或入口網站取得這個值。 |
| loadBalancerName | Azure Load Balancer 的名稱。 |
| 度量名稱 | 以逗號分隔的有效 [Load Balancer 計量](./load-balancer-standard-diagnostics.md)清單。 |
| api-version | 要用於要求的 API 版本。<br /><br /> 本文件涵蓋 api-version `2018-01-01`，內含於上述 URL 中。  |
| 時間範圍 | 查詢的時間範圍。 它是具有下列格式的字串 `startDateTime_ISO/endDateTime_ISO` 。 此選用參數會設定為傳回此範例中一天份的資料。 |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Request body

這項作業不需要要求本文。

## <a name="handle-the-response"></a>處理回應

當計量值清單成功傳回時，系統會傳回狀態碼 200。 錯誤碼的完整清單可於[參考文件](/rest/api/monitor/metrics/list#errorresponse)中取得。

## <a name="example-response"></a>範例回應 

```json
{
    "cost": 0,
    "timespan": "2018-06-05T03:00:00Z/2018-06-07T03:00:00Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/Microsoft.Insights/metrics/ByteCount",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "ByteCount",
                "localizedValue": "Byte Count"
            },
            "unit": "Count",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-06T17:24:00Z",
                            "total": 1067921034.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:25:00Z",
                            "total": 0.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:26:00Z",
                            "total": 3781344.0
                        },
                    ]
                }
            ]
        }
    ],
    "namespace": "Microsoft.Network/loadBalancers",
    "resourceregion": "eastus"
}
```