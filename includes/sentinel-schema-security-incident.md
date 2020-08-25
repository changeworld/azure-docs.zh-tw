---
title: 包含檔案
description: 包含檔案
services: azure-sentinel
author: yelevin
ms.service: azure-sentinel
ms.topic: include
ms.date: 06/28/2020
ms.author: yelevin
ms.custom: include file
ms.openlocfilehash: 63cb53dc60a718892d4bf86140e7fd51303bd61c
ms.sourcegitcommit: f1b18ade73082f12fa8f62f913255a7d3a7e42d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/24/2020
ms.locfileid: "88761709"
---
### <a name="the-data-model-of-the-schema"></a>架構的資料模型

| 欄位 | 資料類型 | 描述 |
| ---- | ---- | ---- |
| **AdditionalData** | 動態 | 警示計數、書簽計數、批註計數、警示產品名稱和策略 |
| **AlertIds** | 動態 | 建立事件的警示 |
| **BookmarkIds** | 動態 | 書簽的實體 |
| **分類** | 字串 | 事件關閉分類 |
| **ClassificationComment** | 字串 | 事件關閉分類批註 |
| **ClassificationReason** | 字串 | 事件關閉分類原因 |
| **ClosedTime** | Datetime | 上次關閉事件時的時間戳記 (UTC)  |
| **註解** | 動態 | 事件批註 |
| **CreatedTime** | Datetime | 建立事件時的時間戳記 (UTC)  |
| **描述** | 字串 | 事件描述 |
| **FirstActivityTime** | Datetime | 第一個事件時間 |
| **FirstModifiedTime** | Datetime | 第一次修改事件時的時間戳記 (UTC)  |
| **I e** | 字串 | 內部 GUID |
| **IncidentNumber** | int |  |
| **IncidentUrl** | 字串 | 事件的連結 |
| **標籤** | 動態 | Tags |
| **LastActivityTime** | Datetime | 上次事件時間 |
| **LastModifiedTime** | Datetime | 上次修改事件時的時間戳記 (UTC)  <br> (目前記錄所描述的修改)  |
| **ModifiedBy** | 字串 | 修改事件的使用者或系統 |
| **擁有者** | 動態 |  |
| **RelatedAnalyticRuleIds** | 動態 | 觸發事件警示的來源規則 |
| **嚴重性** | 字串 | 事件的嚴重性 (High/Medium/Low/資訊)  |
| **SourceSystem** | 字串 | 常數 ( ' Azure ' )  |
| **狀態** | 字串 |  |
| **TenantId** | 字串 |  |
| **TimeGenerated** | Datetime | 建立目前記錄時的時間戳記 (UTC)  <br>修改事件) 時 ( |
| **標題** | 字串 | 
| **類型** | 字串 | 常數 ( ' SecurityIncident ' )  |
|
