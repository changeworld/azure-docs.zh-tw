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
ms.openlocfilehash: 3bbfd167e89ae1b5f9b7de1df5fd1cb72c720cb6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86254520"
---
# <a name="configure-cloud-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>設定 Azure API 管理自我裝載閘道的雲端計量和記錄

本文提供設定 [自我裝載閘道](./self-hosted-gateway-overview.md)之雲端計量和記錄的詳細資料。

自我裝載閘道必須與 API 管理服務相關聯，且需要在埠443上對 Azure 進行輸出 TCP/IP 連線。 如果設定此閘道，閘道會利用輸出連線將遙測傳送至 Azure。 

## <a name="metrics"></a>計量
根據預設，自我裝載的閘道會透過 [Azure 監視器](https://azure.microsoft.com/services/monitor/)發出許多計量，與 [雲端中](api-management-howto-use-azure-monitor.md)的受控閘道相同。 

您可以使用 `telemetry.metrics.cloud` 閘道部署 ConfigMap 中的金鑰來啟用或停用此功能。 以下是可用設定的細目：

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

| 計量  | 描述 |
| ------------- | ------------- |
| Requests  | 期間內的 API 要求數目 |
| 閘道要求的持續時間 | 從閘道收到要求直到傳入完整回應時的毫秒數 |
| 後端要求的持續時間 | 整體後端 IO (連線、傳送及接收位元組) 所耗費的毫秒數  |

## <a name="logs"></a>記錄

自我裝載閘道目前不會將 [診斷記錄](./api-management-howto-use-azure-monitor.md#activity-logs) 傳送至雲端。 不過，您可以在部署自我裝載閘道的 [本機設定和保存記錄](how-to-configure-local-metrics-logs.md) 。 

如果閘道部署在 [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/)中，您可以啟用 [容器的 Azure 監視器](../azure-monitor/insights/container-insights-overview.md) ，以從您的容器收集記錄，並在 Log Analytics 中加以查看。 


## <a name="next-steps"></a>接下來的步驟

* 若要深入瞭解自我裝載的閘道，請參閱 [AZURE API 管理自我裝載閘道總覽](self-hosted-gateway-overview.md)
* 瞭解如何在 [本機設定和保存記錄](how-to-configure-local-metrics-logs.md)
