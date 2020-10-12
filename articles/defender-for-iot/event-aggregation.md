---
title: 事件彙總
description: 瞭解適用于 IoT 的 Defender 事件匯總。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2019
ms.author: mlottner
ms.openlocfilehash: aec750d246ce99fa65431e23ef68e70418db0017
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936874"
---
# <a name="defender-for-iot-event-aggregation"></a>Defender for IoT 事件匯總

Defender for IoT 安全性代理程式會從您的本機裝置收集資料和系統事件，並將此資料傳送至 Azure 雲端以進行處理和分析。 安全性代理程式會收集許多類型的裝置事件，包括新的進程和新的連接事件。 新的程式和新的線上活動可能會在一秒內發生于裝置上，但對於健全且全面的安全性很重要，而強制傳送的訊息數目可能會快速抵達或超過您的 IoT 中樞配額和成本限制。 不過，這些事件包含高價值的安全性資訊，對於保護您的裝置而言很重要。

為了減少額外的配額和成本，同時保護您的裝置，IoT 代理程式的 Defender 會匯總這些類型的事件。

事件匯總預設為**開啟**，雖然不建議這麼做，但可以隨時手動**關閉。**

匯總目前適用于下列類型的事件：

* ProcessCreate
* ConnectionCreate
* 僅限 ProcessTerminate (Windows) 

## <a name="how-does-event-aggregation-work"></a>事件匯總的運作方式為何？

當事件匯總保持 **開啟**時，Defender for IoT 代理程式會匯總間隔期間或時間範圍的事件。
經過一段時間後，代理程式會將匯總的事件傳送至 Azure 雲端，以供進一步分析。
匯總的事件會儲存在記憶體中，直到傳送到 Azure 雲端為止。

若要降低代理程式的記憶體使用量，只要代理程式收集到已保留在記憶體中的事件時，代理程式會增加此特定事件的計數。 當匯總時間範圍通過時，代理程式會傳送所發生之每個特定事件種類的計數。 事件匯總只是每個所收集事件種類的計數匯總。

只有在符合下列條件時，事件才會被視為相同：

* ProcessCreate 事件-當**命令列**、**可執行檔**、**使用者****名稱**和 userid 相同時
* ConnectionCreate 事件-當 **命令列**、 **userId**、 **方向**、 **本機位址**、 **遠端位址**、* * 通訊協定和 **目的地埠** 相同時
* ProcessTerminate 事件- **可執行檔** 和結束 **狀態** 完全相同

### <a name="working-with-aggregated-events"></a>使用匯總事件

在匯總期間，系統會捨棄未匯總的事件屬性，並以0值顯示于 log analytics 中。

* ProcessCreate 事件- **processId**和 **parentProcessId** 設定為0
* ConnectionCreate 事件- **processId**和 **來源埠** 設定為0

## <a name="event-aggregation-based-alerts"></a>以事件匯總為基礎的警示

分析之後，Defender for IoT 會針對可疑的匯總事件建立安全性警示。 從匯總事件建立的警示只會針對每個匯總事件顯示一次。

每個事件的匯總開始時間、結束時間和計數都會記錄在 Log Analytics 的 [事件 **ExtraDetails** ] 欄位中，以供調查之用。

每個匯總事件都代表一段24小時的收集警示。 使用每個事件左上方的 [事件選項] 功能表，您可以 **關閉** 每個個別的匯總事件。

## <a name="event-aggregation-twin-configuration"></a>事件匯總對應項設定

在**azureiotsecurity**模組之模組對應項身分識別的[代理程式設定物件](how-to-agent-configuration.md)內，變更 Defender for IoT 事件匯總的設定。

| 組態名稱 | 可能值 | 詳細資料 | 備註 |
|:-----------|:---------------|:--------|:--------|
| aggregationEnabledProcessCreate | boolean | 啟用/停用進程建立事件的事件匯總 |
| aggregationIntervalProcessCreate | ISO8601 Timespan 字串 | 進程建立事件的匯總間隔 |
| aggregationEnabledConnectionCreate | boolean| 啟用/停用連接建立事件的事件匯總 |
| aggregationIntervalConnectionCreate | ISO8601 Timespan 字串 | 連接建立事件的匯總間隔 |
| aggregationEnabledProcessTerminate | boolean | 啟用/停用進程終止事件的事件匯總 | 僅限 Windows|
| aggregationIntervalProcessTerminate | ISO8601 Timespan 字串 | 進程終止事件的匯總間隔 | 僅限 Windows|
|

## <a name="default-configurations-settings"></a>預設設定

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

在本文中，您已瞭解 Defender for IoT 安全性代理程式匯總，以及可用的事件設定選項。

若要繼續開始使用 Defender 進行 IoT 部署，請使用下列文章：

- 瞭解 [安全性代理程式驗證方法](concept-security-agent-authentication-methods.md)
- 選取和部署 [安全性代理程式](how-to-deploy-agent.md)
- 審核 Defender 的 IoT [服務必要條件](service-prerequisites.md)
- 瞭解如何 [在 Iot 中樞啟用 Defender For iot 服務](quickstart-onboard-iot-hub.md)
- 深入瞭解[Defender For IoT](resources-frequently-asked-questions.md)的服務常見問題
