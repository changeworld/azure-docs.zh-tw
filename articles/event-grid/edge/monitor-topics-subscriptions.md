---
title: 監視主題和事件訂閱-Azure Event Grid IoT Edge |Microsoft Docs
description: 監視主題和事件訂閱
author: femila
ms.author: femila
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d4fbc5232722bfb08bde9be51d44e8e8d7514570
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84554363"
---
# <a name="monitor-topics-and-event-subscriptions"></a>監視主題和事件訂閱

邊緣的事件方格會以[Prometheus 展示格式](https://prometheus.io/docs/instrumenting/exposition_formats/)公開一些主題和事件訂閱的計量。 本文說明可用的計量，以及如何加以啟用。

## <a name="enable-metrics"></a>啟用度量

`metrics__reporterType` `prometheus` 在容器建立選項中，將環境變數設為，以設定要發出計量的模組：

 ```json
        {
          "Env": [
            "metrics__reporterType=prometheus"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
 ```    

計量會在適用于 `5888/metrics` HTTP 和 HTTPs 的模組中提供 `4438/metrics` 。 例如， `http://<modulename>:5888/metrics?api-version=2019-01-01-preview` 針對 HTTP。 此時，計量模組可以輪詢端點以收集此[範例架構](https://github.com/veyalla/ehm)中的計量。

## <a name="available-metrics"></a>可用的計量

主題和事件訂閱都會發出計量，讓您深入瞭解事件傳遞和模組效能。

### <a name="topic-metrics"></a>主題計量

| 計量 | 描述 |
| ------ | ----------- |
| EventsReceived | 發佈至主題的事件數目
| UnmatchedEvents | 發行至主題的事件數目不符合事件訂用帳戶且已卸載
| SuccessRequests | 主題收到的輸入發行要求數
| SystemErrorRequests | 因內部系統錯誤而導致的輸入發佈要求數失敗
| UserErrorRequests | 由於使用者錯誤（例如 JSON 格式錯誤），輸入發佈要求的數目失敗
| SuccessRequestLatencyMs | 發佈要求回應延遲（以毫秒為單位）


### <a name="event-subscription-metrics"></a>事件訂閱計量

| 計量 | 描述 |
| ------ | ----------- |
| DeliverySuccessCounts | 成功傳遞至已設定端點的事件數目
| DeliveryFailureCounts | 無法傳遞至已設定端點的事件數目
| DeliverySuccessLatencyMs | 成功傳遞事件的延遲（以毫秒為單位）
| DeliveryFailureLatencyMs | 事件傳遞失敗的延遲（以毫秒為單位）
| SystemDelayForFirstAttemptMs | 第一次嘗試傳遞之前事件的系統延遲（以毫秒為單位）
| DeliveryAttemptsCount | 事件傳遞嘗試次數-成功和失敗
| ExpiredCounts | 已過期且未傳遞至已設定端點的事件數目