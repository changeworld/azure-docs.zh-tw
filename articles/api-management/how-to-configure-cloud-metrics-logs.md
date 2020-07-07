---
title: 設定 Azure API 管理自我裝載閘道的雲端計量和記錄 |Microsoft Docs
description: 瞭解如何設定 Azure API 管理自我裝載閘道的雲端計量和記錄
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/30/2020
ms.author: apimpm
ms.openlocfilehash: d0fbdcb877e91a703306f15fdc7507fd19d534f4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82205127"
---
# <a name="configure-cloud-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>設定 Azure API 管理自我裝載閘道的雲端計量和記錄

本文提供為[自我裝載閘道](./self-hosted-gateway-overview.md)設定雲端計量和記錄的詳細資料。

自我裝載閘道必須與 API 管理服務相關聯，而且在埠443上需要連到 Azure 的輸出 TCP/IP 連線能力。 閘道會利用輸出連線，將遙測資料傳送至 Azure （若已設定）。 

## <a name="metrics"></a>計量
根據預設，自我裝載閘道會透過[Azure 監視器](https://azure.microsoft.com/services/monitor/)發出許多計量，與[雲端中](api-management-howto-use-azure-monitor.md)的受控閘道相同。 

在 `telemetry.metrics.cloud` 閘道部署的 ConfigMap 中，您可以使用金鑰來啟用或停用此功能。 以下是可用設定的明細：

| 欄位  | 預設 | 描述 |
| ------------- | ------------- | ------------- |
| 遙測資料。雲端  | `true` | 透過 Azure 監視器啟用記錄。 值可以是 `true` 、 `false` 。 |


以下是範例設定：

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<contoso-gateway-management-endpoint>"
        telemetry.metrics.cloud: "true"
```

自我裝載閘道目前會透過 Azure 監視器發出下列計量：

| Metric  | 描述 |
| ------------- | ------------- |
| Requests  | 期間內的 API 要求數目 |
| 閘道要求的持續時間 | 從閘道收到要求直到傳入完整回應時的毫秒數 |
| 後端要求的持續時間 | 整體後端 IO (連線、傳送及接收位元組) 所耗費的毫秒數  |

## <a name="logs"></a>記錄

自我裝載閘道目前不會將[診斷記錄](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs)傳送至雲端。 不過，您可以在部署自我裝載閘道的[本機上設定並保存記錄](how-to-configure-local-metrics-logs.md)。 

如果閘道部署在[Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/)中，您可以啟用[Azure 監視器讓容器](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview)從您的容器收集記錄，並在 Log Analytics 中加以查看。 


## <a name="next-steps"></a>後續步驟

* 若要深入瞭解自我裝載閘道，請參閱[AZURE API 管理自我裝載閘道總覽](self-hosted-gateway-overview.md)
* 瞭解如何在[本機設定和保存記錄](how-to-configure-local-metrics-logs.md)


