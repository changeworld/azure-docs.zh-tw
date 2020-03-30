---
title: 監視主題和事件訂閱 - Azure 事件網格 IoT 邊緣 |微軟文檔
description: 監視主題和事件訂閱
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ce7c92f121fb458d528d63d0af0aad025b377386
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086670"
---
# <a name="monitor-topics-and-event-subscriptions"></a>監視主題和事件訂閱

邊緣上的事件網格以[Prometheus 展示格式](https://prometheus.io/docs/instrumenting/exposition_formats/)公開主題和事件訂閱的一些指標。 本文介紹了可用的指標以及如何啟用它們。

## <a name="enable-metrics"></a>啟用度量

通過將`metrics__reporterType`環境變數設置為容器創建選項`prometheus`，將模組配置為發出指標：

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

指標將在模組中`5888/metrics`提供 HTTP 和`4438/metrics`HTTPs。 例如，`http://<modulename>:5888/metrics?api-version=2019-01-01-preview`對於 HTTP。 此時，指標模組可以輪詢終結點以收集指標，如本[示例體系結構](https://github.com/veyalla/ehm)中所示。

## <a name="available-metrics"></a>可用的計量

主題和事件訂閱都會發佈指標，讓您深入瞭解事件交付和模組性能。

### <a name="topic-metrics"></a>主題指標

| 計量 | 描述 |
| ------ | ----------- |
| 收到的事件 | 發佈到主題的事件數
| 不匹配的事件 | 發佈到主題的事件數與事件訂閱不匹配並被刪除
| 成功請求 | 主題收到的入站發佈請求數
| 系統錯誤請求 | 由於內部系統錯誤而失敗的入站發佈請求數
| 使用者錯誤請求 | 由於使用者錯誤（如格式錯誤的 JSON），入站發佈請求上的編號失敗
| 成功請求延遲M | 以毫秒為單位發佈請求回應延遲


### <a name="event-subscription-metrics"></a>事件訂閱指標

| 計量 | 描述 |
| ------ | ----------- |
| 交付成功計數 | 成功傳遞到配置的終結點的事件數
| 交付失敗計數 | 未能傳遞到配置的終結點的事件數
| 交付成功延遲M | 以毫秒為單位成功交付的事件延遲
| 交付失敗延遲M | 事件傳遞失敗的延遲（以毫秒為單位）
| 系統延遲首次嘗試 | 在第一次傳遞嘗試之前的事件的系統延遲（以毫秒為單位）
| 交付嘗試計數 | 事件傳遞嘗試次數 - 成功和失敗
| 過期計數 | 過期且未傳遞到配置的終結點的事件數