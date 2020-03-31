---
title: 瞭解 IoT 事件聚合的 Azure 安全中心*微軟文檔
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
ms.openlocfilehash: ca1d1a5761e62b2838a474dcb83f450987972998
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73928964"
---
# <a name="azure-security-center-for-iot-event-aggregation"></a>用於 IoT 事件聚合的 Azure 安全中心

IoT 安全代理 Azure 安全中心從本地設備收集資料和系統事件，並將此資料發送到 Azure 雲進行處理和分析。 安全代理收集多種類型的設備事件，包括新進程和新的連接事件。 新進程和新的連接事件可能在幾秒鐘內在設備上頻繁發生，雖然對強大和全面的安全性很重要，但安全代理強制發送的消息數可能快速到達或超過 IoT 中心配額和成本限制。 但是，這些事件包含非常有價值的安全資訊，這些資訊對於保護您的設備至關重要。

為了減少額外的配額和成本，同時保持設備受到保護，IoT 代理的 Azure 安全中心聚合這些類型的事件。

預設情況下，事件聚合處於**打開狀態**，儘管不建議這樣做，但可以隨時手動**關閉**。

聚合當前可用於以下類型的事件：
* 流程創建
* 連接創建
* 進程終止（僅限視窗）

## <a name="how-does-event-aggregation-work"></a>事件聚合如何工作？
當事件聚合處於**打開狀態時**，IoT 代理的 Azure 安全中心將事件聚合為間隔期間或時間段。
間隔期結束後，代理會將彙總事件發送到 Azure 雲進行進一步分析。
彙總事件存儲在記憶體中，直到發送到 Azure 雲。

為了減少代理的記憶體佔用空間，每當代理收集與已保存在記憶體中的事件相同的事件時，代理都會增加此特定事件的命中計數。 當聚合時間視窗過去時，代理髮送發生的每種特定類型的事件的命中計數。 事件聚合只是每個收集類型的事件的熱門計數的聚合。

僅當滿足以下條件時，事件才被視為相同： 

* 進程創建事件 - 當**命令列**、**可執行項**、**使用者名**和**userid**相同時
* 連接創建事件 - 當**命令列**、**使用者 Id**、**方向**、**本地位址**、**遠端位址**、_協定**和目標埠**相同時
* 進程終止事件 - 當**可執行**和**退出狀態**相同時

### <a name="working-with-aggregated-events"></a>使用彙總事件

在聚合期間，將丟棄未聚合的事件屬性，並顯示在值為 0 的日誌分析中。 
* 進程創建事件 -**進程 Id**和**父進程Id**設置為 0
* 連接創建事件 -**進程 Id**和**源埠**設置為 0

## <a name="event-aggregation-based-alerts"></a>基於事件聚合的警報 
分析後，IoT Azure 安全中心會為可疑的彙總事件創建安全警報。 從彙總事件創建的警報僅為每個彙總事件顯示一次。

每個事件的聚合開始時間、結束時間和命中計數都記錄在日誌分析中的"額外**詳細資訊**"事件欄位中，以便在調查期間使用。 

每個彙總事件表示收集的警報的 24 小時週期。 使用每個事件左上角的事件選項功能表，可以**關閉**每個彙總事件。    

## <a name="event-aggregation-twin-configuration"></a>事件聚合孿生配置
更改**Azureiot 安全**模組模組的模組孿生標識的[代理設定物件](how-to-agent-configuration.md)內的 IoT 事件聚合 Azure 安全中心配置。

| 組態名稱 | 可能值 | 詳細資料 | 備註 |
|:-----------|:---------------|:--------|:--------|
| 聚合啟用進程創建 | boolean | 啟用/禁用進程創建事件的事件聚合 |
| 聚合間隔進程創建 | ISO8601 時間跨度字串 | 進程創建事件的聚合間隔 |
| 聚合啟用連接創建 | boolean| 啟用/禁用連接創建事件的事件聚合 |
| 聚合間隔連接創建 | ISO8601 時間跨度字串 | 連接創建事件的聚合間隔 |
| 聚合啟用進程終止 | boolean | 啟用/禁用進程終止事件的事件聚合 | 僅限 Windows|
| 聚合間隔進程終止 | ISO8601 時間跨度字串 | 進程終止事件的聚合間隔 | 僅限 Windows|
|

## <a name="default-configurations-settings"></a>預設配置設置

| 組態名稱 | 預設值 |
|:-----------|:---------------|
| 聚合啟用進程創建 | true |
| 聚合間隔進程創建 | "PT1H"|
| 聚合啟用連接創建 | true |
| 聚合間隔連接創建 | "PT1H"|
| 聚合啟用進程終止 | true |
| 聚合間隔進程終止 | "PT1H"|
|

## <a name="next-steps"></a>後續步驟

在本文中，您瞭解了用於 IoT 安全代理聚合的 Azure 安全中心以及可用的事件配置選項。

要繼續使用用於 IoT 部署的 Azure 安全中心，請使用以下文章：

- 瞭解[安全代理身份驗證方法](concept-security-agent-authentication-methods.md)
- 選擇並部署[安全代理](how-to-deploy-agent.md)
- 查看 Azure 安全中心，查看 IoT[服務先決條件](service-prerequisites.md)
- 瞭解如何在[IoT 中心啟用 IoT 服務的 Azure 安全中心](quickstart-onboard-iot-hub.md)
- 從[IoT 常見問題解答的 Azure 安全中心](resources-frequently-asked-questions.md)瞭解有關該服務
