---
title: 使用 REST API 取得 Azure 虛擬機器使用量資料
description: 使用 Azure REST API 來收集虛擬機器的使用量計量。
author: rloutlaw
ms.service: virtual-machines
ms.subservice: monitoring
ms.custom: REST
ms.topic: how-to
ms.date: 06/13/2018
ms.author: routlaw
ms.openlocfilehash: 925246eb2adae59ca74e363c4d4a6952f11c3744
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87292324"
---
# <a name="get-virtual-machine-usage-metrics-using-the-rest-api"></a>使用 REST API 來取得虛擬機器的使用量計量

此範例說明如何使用 [Azure REST API](/rest/api/azure/) 來擷取 Linux 虛擬機器的 CPU 使用量。

REST API 的完整參考文件和其他範例可於 [Azure 監視器 REST 參考](/rest/api/monitor)中取得。 

## <a name="build-the-request"></a>建立要求

請使用下列 GET 要求以從虛擬機器收集 [CPU 百分比計量](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>要求標頭

以下是必要標頭： 

|要求標頭|描述|  
|--------------------|-----------------|  
|*Content-Type:*|必要。 設定為 `application/json`。|  
|*Authorization:*|必要。 設定為有效的 `Bearer` [存取權杖](/rest/api/azure/#authorization-code-grant-interactive-clients)。 |  

### <a name="uri-parameters"></a>URI 參數

| 名稱 | 說明 |
| :--- | :---------- |
| subscriptionId | 可識別 Azure 訂用帳戶的訂用帳戶識別碼。 如果您有多個訂用帳戶，請參閱使用[多個訂閱](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)。 |
| resourceGroupName | 與資源相關聯的 Azure資源群組名稱。 您可以從 Azure Resource Manager API、CLI 或入口網站取得這個值。 |
| vmname | Azure 虛擬機器的名稱。 |
| metricnames | 以逗號分隔的有效 [Load Balancer 計量](../../load-balancer/load-balancer-standard-diagnostics.md)清單。 |
| api-version | 要用於要求的 API 版本。<br /><br /> 本文件涵蓋 api-version `2018-01-01`，內含於上述 URL 中。  |
| 時間範圍 | 具有 `startDateTime_ISO/endDateTime_ISO` 格式的字串，可定義所傳回計量的時間範圍。 此選用參數會設定為傳回此範例中一天份的資料。 |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Request body

這項作業不需要要求本文。

## <a name="handle-the-response"></a>處理回應

當計量值清單成功傳回時，系統會傳回狀態碼 200。 錯誤碼的完整清單可於[參考文件](/rest/api/monitor/metrics/list#errorresponse)中取得。

## <a name="example-response"></a>範例回應 

```json
{
    "cost": 0,
    "timespan": "2018-06-08T23:48:10Z/2018-06-09T00:48:10Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "Percentage CPU",
                "localizedValue": "Percentage CPU"
            },
            "unit": "Percent",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-08T23:48:00Z",
                            "average": 0.44
                        },
                        {
                            "timeStamp": "2018-06-08T23:49:00Z",
                            "average": 0.31
                        },
                        {
                            "timeStamp": "2018-06-08T23:50:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:51:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:52:00Z",
                            "average": 0.285
                        } ]
                } ]
        } ]
}
```
