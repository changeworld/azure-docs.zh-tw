---
title: 事件彙總
description: 瞭解用於 IoT 事件聚合的 Azure 安全中心。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d40dac0b-abd0-4ff5-82eb-0f511ee13725
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2019
ms.author: mlottner
ms.openlocfilehash: f72ef8cc5161bd6f885249e7d39344a57fa2368e
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311407"
---
# <a name="azure-security-center-for-iot-event-aggregation"></a>用於 IoT 事件聚合的 Azure 安全中心

IoT 安全代理 Azure 安全中心從本地設備收集數據和系統事件,並將此資料發送到 Azure 雲端處理和分析。 安全代理收集多種類型的設備事件,包括新進程和新的連接事件。 新流程和新連接事件可能都會在一秒內合法地在設備上發生,雖然對於強大和全面的安全性很重要,但安全代理被迫發送的消息數可能快速達到或超過 IoT 中心配額和成本限制。 但是,這些事件包含非常有價值的安全信息,這些資訊對於保護您的設備至關重要。

為了減少額外的配額和成本,同時保持設備受到保護,IoT 代理的 Azure 安全中心聚合這些類型的事件。

預設情況下,事件聚合處於**打開狀態**,儘管不建議這樣做,但可以隨時手動**關閉**。

彙總目前可用於以下類型的事件:

* 流程建立
* 連線建立
* 行程終止(僅限視窗)

## <a name="how-does-event-aggregation-work"></a>事件聚合如何工作?

當事件聚合處於**打開狀態時**,IoT 代理的 Azure 安全中心將事件聚合為間隔期間或時間段。
間隔期結束后,代理會將聚合事件發送到 Azure 雲進行進一步分析。
聚合事件存儲在記憶體中,直到發送到 Azure 雲。

為了減少代理的記憶體佔用空間,每當代理收集與已保存在記憶體中的事件相同的事件時,代理都會增加此特定事件的命中計數。 當聚合時間窗口過去時,代理發送發生的每種特定類型的事件的命中計數。 事件聚合只是每個收集類型的事件的熱門計數的聚合。

僅當滿足以下條件時,事件才被視為相同:

* 行程建立事件 - 當**指令列**,**可執行項目**,**使用者名稱**與**userid**相同時
* 連線建立事件 - 當**指令列**,**使用者 Id**,**方向**,**本地位址**,**遠端位址**、_協定**與目標埠**相同時
* 行程終止事件 ─ 當**執行****與離開狀態**相同時

### <a name="working-with-aggregated-events"></a>使用集合活動事件

在聚合期間,將丟棄未聚合的事件屬性,並顯示在值為 0 的日誌分析中。

* 行程建立事件 -**行程 Id**與**父行程Id**設定為 0
* 連線建立事件 -**行程 Id**與**來源連接埠**設定為 0

## <a name="event-aggregation-based-alerts"></a>基於事件聚合的警報

分析後,IoT Azure 安全中心會為可疑的聚合事件創建安全警報。 從聚合事件創建的警報僅為每個聚合事件顯示一次。

每個事件的聚合開始時間、結束時間和命中計數都記錄在日誌分析中的「額外**詳細資訊**」事件欄位中,以便在調查期間使用。

每個聚合事件表示收集的警報的 24 小時週期。 使用每個事件左上角的事件選項功能表,可以**關閉**每個聚合事件。

## <a name="event-aggregation-twin-configuration"></a>事件聚合孿生配置

更改**Azureiot 安全**模組模組的模組孿生標識的[代理配置物件](how-to-agent-configuration.md)內的 IoT 事件聚合 Azure 安全中心配置。

| 組態名稱 | 可能值 | 詳細資料 | 備註 |
|:-----------|:---------------|:--------|:--------|
| 彙總啟用行程建立 | boolean | 開啟/關閉行程建立事件的事件聚合 |
| 彙總間隔行程建立 | ISO8601 時間跨度字串 | 行程建立事件的集合間隔 |
| 彙總啟用連線建立 | boolean| 開啟/關閉連線建立事件的事件聚合 |
| 聚合間隔連線建立 | ISO8601 時間跨度字串 | 連線建立事件的集合間隔 |
| 彙總啟用行程終止 | boolean | 開啟/關閉行程終止事件的事件集合 | 僅限 Windows|
| 聚合間隔行程終止 | ISO8601 時間跨度字串 | 行程終止事件的集合時間隔 | 僅限 Windows|
|

## <a name="default-configurations-settings"></a>預設設定設定

| 組態名稱 | 預設值 |
|:-----------|:---------------|
| 彙總啟用行程建立 | true |
| 彙總間隔行程建立 | "PT1H"|
| 彙總啟用連線建立 | true |
| 聚合間隔連線建立 | "PT1H"|
| 彙總啟用行程終止 | true |
| 聚合間隔行程終止 | "PT1H"|
|

## <a name="next-steps"></a>後續步驟

在本文中,您瞭解了用於 IoT 安全代理聚合的 Azure 安全中心以及可用的事件配置選項。

要繼續使用用於 IoT 部署的 Azure 安全中心,請使用以下文章:

- 瞭解[安全性代理身份驗證方法](concept-security-agent-authentication-methods.md)
- 選擇並部署[安全代理](how-to-deploy-agent.md)
- 檢視 Azure 安全中心,查看 IoT[服務先決條件](service-prerequisites.md)
- 瞭解如何在[IoT 中心啟用 IoT 服務的 Azure 安全中心](quickstart-onboard-iot-hub.md)
- 從[IoT 常見問題解答的 Azure 安全中心](resources-frequently-asked-questions.md)瞭解有關該服務
