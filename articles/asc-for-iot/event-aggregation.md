---
title: 事件彙總
description: 深入瞭解 IoT 事件匯總 Azure 資訊安全中心。
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81311407"
---
# <a name="azure-security-center-for-iot-event-aggregation"></a>IoT 事件匯總的 Azure 資訊安全中心

Azure 資訊安全中心 IoT 安全性代理程式會從您的本機裝置收集資料和系統事件，並將此資料傳送至 Azure 雲端進行處理和分析。 安全性代理程式會收集許多類型的裝置事件，包括新的進程和新的連接事件。 新的進程和新的線上活動可能會在一秒內于裝置上合法地發生，而且對於健全且全面的安全性來說，安全性代理程式強制傳送的訊息數目可能會快速達到或超過您的 IoT 中樞配額和成本限制。 不過，這些事件包含非常重要的安全性資訊，對保護您的裝置很重要。

為了減少額外的配額和成本，同時保護您的裝置，Azure 資訊安全中心 IoT 代理程式會匯總這些類型的事件。

事件匯總預設為**開啟**，但不建議，可以**隨時以手動方式關閉。**

匯總目前適用于下列類型的事件：

* ProcessCreate
* ConnectionCreate
* ProcessTerminate （僅限 Windows）

## <a name="how-does-event-aggregation-work"></a>事件匯總如何運作？

當事件匯總**已保留時**，IoT 代理程式 Azure 資訊安全中心會匯總間隔期間或時間範圍的事件。
一旦超過間隔期間，代理程式就會將匯總的事件傳送至 Azure 雲端，以供進一步分析。
匯總的事件會儲存在記憶體中，直到傳送至 Azure 雲端為止。

為了降低代理程式的記憶體使用量，每當代理程式收集到已保留在記憶體中的事件時，代理程式就會增加此特定事件的計數。 當匯總時間範圍通過時，代理程式會傳送每個發生之特定事件種類的計數。 事件匯總只是每個收集之事件種類的計數匯總。

只有在符合下列條件時，才會將事件視為相同：

* ProcessCreate 事件-**命令列**、**可執行檔**、使用者**名稱**和**userid**都相同
* ConnectionCreate 事件-**命令列**、 **userId**、 **direction**、**本機位址**、**遠端位址**、* * 通訊協定和**目的地埠**都相同
* ProcessTerminate 事件-當**可執行檔**和結束**狀態**完全相同時

### <a name="working-with-aggregated-events"></a>使用匯總事件

在匯總期間，系統會捨棄未匯總的事件屬性，並顯示在 log analytics 中，其值為0。

* ProcessCreate 事件- **processId**，而**parentProcessId**設定為0
* ConnectionCreate 事件- **processId**，而**來源埠**設定為0

## <a name="event-aggregation-based-alerts"></a>以事件匯總為基礎的警示

分析之後，適用于 IoT 的 Azure 資訊安全中心會針對可疑的匯總事件建立安全性警示。 從匯總事件建立的警示只會針對每個匯總事件出現一次。

每個事件的匯總開始時間、結束時間和計數都會記錄在 Log Analytics 中的 [事件**ExtraDetails** ] 欄位內，以供調查期間使用。

每個匯總事件都代表一段24小時的收集警示。 使用每個事件左上方的 [事件選項] 功能表，您可以**關閉**每個個別的匯總事件。

## <a name="event-aggregation-twin-configuration"></a>事件匯總對應項設定

在**azureiotsecurity**模組之模組對應項識別的[agent 設定物件](how-to-agent-configuration.md)中，對 IoT 事件匯總的設定進行 Azure 資訊安全中心變更。

| 組態名稱 | 可能值 | 詳細資料 | 備註 |
|:-----------|:---------------|:--------|:--------|
| aggregationEnabledProcessCreate | boolean | 啟用/停用進程建立事件的事件匯總 |
| aggregationIntervalProcessCreate | ISO8601 Timespan 字串 | 進程建立事件的匯總間隔 |
| aggregationEnabledConnectionCreate | boolean| 啟用/停用連接建立事件的事件匯總 |
| aggregationIntervalConnectionCreate | ISO8601 Timespan 字串 | 連接建立事件的匯總間隔 |
| aggregationEnabledProcessTerminate | boolean | 啟用/停用進程終止事件的事件匯總 | 僅限 Windows|
| aggregationIntervalProcessTerminate | ISO8601 Timespan 字串 | 進程終止事件的匯總間隔 | 僅限 Windows|
|

## <a name="default-configurations-settings"></a>預設設定值

| 組態名稱 | 預設值 |
|:-----------|:---------------|
| aggregationEnabledProcessCreate | true |
| aggregationIntervalProcessCreate | PT1H|
| aggregationEnabledConnectionCreate | true |
| aggregationIntervalConnectionCreate | PT1H|
| aggregationEnabledProcessTerminate | true |
| aggregationIntervalProcessTerminate | PT1H|
|

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解 IoT 安全性代理程式匯總的 Azure 資訊安全中心，以及可用的事件設定選項。

若要繼續開始使用 Azure 資訊安全中心進行 IoT 部署，請使用下列文章：

- 瞭解[安全性代理程式驗證方法](concept-security-agent-authentication-methods.md)
- 選取並部署[安全性代理程式](how-to-deploy-agent.md)
- 審查 IoT[服務必要條件](service-prerequisites.md)Azure 資訊安全中心
- 瞭解如何[在您的 IoT 中樞中啟用 IoT 服務的 Azure 資訊安全中心](quickstart-onboard-iot-hub.md)
- 深入瞭解[適用于 IoT 的 Azure 資訊安全中心](resources-frequently-asked-questions.md)的服務常見問題
