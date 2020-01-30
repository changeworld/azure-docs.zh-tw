---
title: 監視主題和事件訂閱-Azure Event Grid IoT Edge |Microsoft Docs
description: 監視主題和事件訂閱
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 79b223de7a0a0cfdaf799b1f80e585a2a55f7e82
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849730"
---
# <a name="monitor-topics-and-event-subscriptions"></a>監視主題和事件訂閱

邊緣的事件方格會以[Prometheus 展示格式](https://prometheus.io/docs/instrumenting/exposition_formats/)公開一些主題和事件訂閱的計量。 本文說明可用的計量，以及如何加以啟用。

## <a name="enable-metrics"></a>啟用度量

藉由將 `metrics__reporterType` 環境變數設定為容器建立選項中 `prometheus`，來設定要發出計量的模組：

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

計量會在適用于 HTTP 的模組 `5888/metrics`，以及適用于 HTTPs 的 `4438/metrics`。 例如，`http://<modulename>:4438/metrics?api-version=2019-01-01-preview` HTTP。 此時，計量模組可以輪詢端點以收集此[範例架構](https://github.com/veyalla/ehm)中的計量。

## <a name="available-metrics"></a>可用的計量

主題和事件訂閱都會發出計量，讓您深入瞭解事件傳遞和模組效能。

### <a name="topic-metrics"></a>主題計量

| 計量 | 說明 |
| ------ | ----------- |
| EventsReceived | 發佈至主題的事件數目
| UnmatchedEvents | 發行至主題的事件數目不符合事件訂用帳戶且已卸載
| SuccessRequests | 主題收到的輸入發行要求數
| SystemErrorRequests | 因內部系統錯誤而導致的輸入發佈要求數失敗
| UserErrorRequests | 由於使用者錯誤（例如 JSON 格式錯誤），輸入發佈要求的數目失敗
| SuccessRequestLatencyMs | 發佈要求回應延遲（以毫秒為單位）


### <a name="event-subscription-metrics"></a>事件訂閱計量

| 計量 | 說明 |
| ------ | ----------- |
| deliverySuccessCounts | 成功傳遞至已設定端點的事件數目
| deliveryFailureCounts | 無法對設定的端點進行事件傳遞嘗試次數
| deliverySuccessLatencyMs | 成功傳遞事件的延遲（以毫秒為單位）
| deliveryFailureLatencyMs | 事件傳遞失敗的延遲（以毫秒為單位）
| systemDelayForFirstAttemptMs | 第一次嘗試傳遞之前事件的系統延遲（以毫秒為單位）
| deliveryAttemptsCount | 事件傳遞嘗試次數-成功和失敗
| expiredCounts | 無法傳遞的事件數目 