---
title: 監視主題和事件訂閱-Azure 事件方格 IoT Edge |Microsoft Docs
description: 監視主題和事件訂閱
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: d230be4f74abd61ad7b7f0fdb3facb32ee63da73
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171528"
---
# <a name="monitor-topics-and-event-subscriptions"></a>監視主題和事件訂閱

Edge 上的事件方格會以 [Prometheus 展示格式](https://prometheus.io/docs/instrumenting/exposition_formats/)公開一些主題和事件訂閱的計量。 本文說明可用的計量，以及如何加以啟用。

## <a name="enable-metrics"></a>啟用度量

將 `metrics__reporterType` 環境變數設定為 `prometheus` 容器建立選項中的環境變數，以設定要發出計量的模組：

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

適用于 `5888/metrics` HTTP 和 HTTPs 的模組會提供計量 `4438/metrics` 。 例如， `http://<modulename>:5888/metrics?api-version=2019-01-01-preview` 用於 HTTP。 此時，計量模組可以輪詢端點來收集計量，如下列 [範例架構](https://github.com/veyalla/ehm)所示。

## <a name="available-metrics"></a>可用的計量

主題和事件訂閱都會發出計量，讓您深入瞭解事件傳遞和模組的效能。

### <a name="topic-metrics"></a>主題計量

| 計量 | 描述 |
| ------ | ----------- |
| EventsReceived | 發行至主題的事件數目
| UnmatchedEvents | 發行至主題且不符合事件訂閱且已卸載的事件數目
| SuccessRequests | 主題接收的輸入發佈要求數目
| SystemErrorRequests | 由於內部系統錯誤而失敗的輸入發佈要求數目
| UserErrorRequests | 由於使用者錯誤（例如 JSON 格式錯誤），導致輸入發佈要求的數目失敗
| SuccessRequestLatencyMs | 發佈要求回應延遲（毫秒）


### <a name="event-subscription-metrics"></a>事件訂用帳戶計量

| 計量 | 描述 |
| ------ | ----------- |
| DeliverySuccessCounts | 成功傳遞至已設定端點的事件數目
| DeliveryFailureCounts | 無法傳遞至設定端點的事件數目
| DeliverySuccessLatencyMs | 成功傳遞事件的延遲（以毫秒為單位）
| DeliveryFailureLatencyMs | 事件傳遞失敗的延遲（以毫秒為單位）
| SystemDelayForFirstAttemptMs | 第一次傳遞嘗試之前的事件系統延遲（以毫秒為單位）
| DeliveryAttemptsCount | 事件傳遞嘗試次數-成功和失敗
| ExpiredCounts | 已過期且未傳遞至設定端點的事件數目