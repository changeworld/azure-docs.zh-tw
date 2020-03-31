---
title: 包含檔案
description: 包含檔案
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/17/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 7d81799f7fbdb2b41db421daa1a85ec8cde511eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79538827"
---
### <a name="the-data-model-of-the-schema"></a>架構的資料模型

|欄位|描述|
|----|----|
|**AlertName**|警報顯示名稱|
|**AlertType**|唯一警報識別碼|
|**置信度**|（可選）此警報的置信度（高/低）|
|**信心評分**|（可選）安全警報的數位置信度指示器|
|**描述**|警報的說明文本|
|**DisplayName**|警報的顯示名稱|
|**結束時間**|警報的影響結束時間（導致警報的最後一個事件的時間）|
|**實體**|與警報相關的實體清單。 此清單可以容納不同類型的實體的混合|
|**擴展連結**|（可選）指向與警報相關的所有連結的袋子。 這個袋子可以容納不同類型的連結的混合物|
|**擴充屬性**|與警報相關的一袋其他欄位|
|**正在發生**|確定警報是事件還是常規警報。 事件是一個安全警報，將多個警報聚合到一個安全事件中|
|**ProcessingEndTime**|創建警報的 UTC 時間戳記|
|**產品元件名稱**|（可選）生成警報的產品中元件的名稱。|
|**產品名稱**|常量（"Azure 安全中心"）|
|**ProviderName**|unused|
|**補救步驟**|修復安全威脅的手動操作項|
|**資源 Id**|受影響資源的完整識別碼|
|**嚴重性**|警示嚴重性（高/中/低/資訊）|
|**SourceComputerId**|受影響伺服器的唯一 GUID（如果在伺服器上生成警報）|
|**SourceSystem**|unused|
|**開始時間**|警報的影響開始時間（導致警報的第一個事件的時間）|
|**系統警報 Id**|此安全警報實例的唯一識別碼|
|**租戶 Id**|掃描資源所在的訂閱的父 Azure 活動目錄租戶的識別碼|
|**TimeGenerated**|進行評估的 UTC 時間戳記（安全中心的掃描時間）（與發現時間UTC 相同）|
|**類型**|常數（"安全警報"）|
|**供應商名稱**|提供警報的供應商的名稱（例如"Microsoft"）|
|**供應商原始 ID**|unused|
|**工作區資源組**|如果警報是在報告到工作區的 VM、伺服器、虛擬機器縮放集或應用服務實例上生成的，則包含該工作區資源組名稱|
|**工作區訂閱 Id**|如果警報是在報告到工作區的 VM、伺服器、虛擬機器縮放集或應用服務實例上生成的，則包含該工作區訂閱 Id|
|||
