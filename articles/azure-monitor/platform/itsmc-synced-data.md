---
title: 從您的 ITSM 產品同步至 LA 工作區的資料
description: 本文概要說明從您的 ITSM 產品同步至 LA 工作區的資料。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: fd570950190ceabac413aca2d68368e5e722a3da
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/29/2020
ms.locfileid: "97811978"
---
# <a name="data-synced-from-your-itsm-product"></a>從您的 ITSM 產品同步的資料

事件和變更要求會根據連線的設定 (使用 [同步資料] 欄位) ，從您的 ITSM 工具同步處理到您的 Log Analytics 工作區：
* [ServiceNow](./itsmc-connections-servicenow.md)
* [System Center Service Manager](./itsmc-connections-scsm.md)
* [Cherwell](./itsmc-connections-cherwell.md)
* [Provance](./itsmc-connections-provance.md)

## <a name="synced-data"></a>同步資料

本節說明 ITSMC 所收集資料的一些範例。

**ServiceDesk_CL** 中的欄位會根據您匯入至 Log Analytics 的工作專案類型而有所不同。 以下是兩個工作專案類型的欄位清單：

**工作項目：** **事件**  
ServiceDeskWorkItemType_s="Incident"

**欄位**

- ServiceDeskConnectionName
- 服務台識別碼
- State
- 急迫性
- 影響
- 優先順序
- 升級
- 建立者
- 解決者
- 關閉者
- 來源
- 指派對象
- 類別
- Title
- 描述
- 建立日期
- 關閉日期
- 解決日期
- 上次修改日期
- 電腦

**工作項目：** **變更要求**

ServiceDeskWorkItemType_s="ChangeRequest"

**欄位**
- ServiceDeskConnectionName
- 服務台識別碼
- 建立者
- 關閉者
- 來源
- 指派對象
- Title
- 類型
- 類別
- State
- 升級
- 衝突狀態
- 急迫性
- 優先順序
- 風險
- 影響
- 指派對象
- 建立日期
- 關閉日期
- 上次修改日期
- 要求日期
- 計劃開始日期
- 計劃結束日期
- 工作開始日期
- 工作結束日期
- 描述
- 電腦

## <a name="servicenow-example"></a>ServiceNow 範例 
### <a name="output-data-for-a-servicenow-incident"></a>ServiceNow 事件的輸出資料

| Log Analytics 欄位 | ServiceNow 欄位 |
|:--- |:--- |
| ServiceDeskId_s| Number |
| IncidentState_s | State |
| Urgency_s |急迫性 |
| Impact_s |影響|
| Priority_s | 優先順序 |
| CreatedBy_s | 開啟者 |
| ResolvedBy_s | 解決者|
| ClosedBy_s  | 關閉者 |
| Source_s| 連絡人型別 |
| AssignedTo_s | 指派對象  |
| Category_s | 類別 |
| Title_s|  簡短描述 |
| Description_s|  注意 |
| CreatedDate_t|  已開啟 |
| ClosedDate_t| 關閉|
| ResolvedDate_t|已解決|
| 電腦  | 設定項目 |

### <a name="output-data-for-a-servicenow-change-request"></a>ServiceNow 變更要求的輸出資料

| Log Analytics | ServiceNow 欄位 |
|:--- |:--- |
| ServiceDeskId_s| Number |
| CreatedBy_s | 要求者 |
| ClosedBy_s | 關閉者 |
| AssignedTo_s | 指派對象  |
| Title_s|  簡短描述 |
| Type_s|  類型 |
| Category_s|  類別 |
| CRState_s|  State|
| Urgency_s|  急迫性 |
| Priority_s| 優先順序|
| Risk_s| 風險|
| Impact_s| 影響|
| RequestedDate_t  | 要求的日期 |
| ClosedDate_t | 關閉日期 |
| PlannedStartDate_t  | 計劃開始日期 |
| PlannedEndDate_t  | 計劃結束日期 |
| WorkStartDate_t  | 實際開始日期 |
| WorkEndDate_t | 實際結束日期|
| Description_s | 描述 |
| 電腦  | 設定項目 |

## <a name="next-steps"></a>後續步驟

* [針對 ITSM 連接器中的問題進行疑難排解](./itsmc-resync-servicenow.md)
