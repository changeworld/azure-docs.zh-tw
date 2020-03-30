---
title: 使用 Azure 哨兵時的有用資源*微軟文檔
description: 本文檔為您提供了使用 Azure Sentinel 時的有用資源的清單。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2019
ms.author: yelevin
ms.openlocfilehash: 2110d3319cebf693ef06deec26a29fa655e35035
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585266"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>用於使用 Azure 哨兵的有用資源



本文列出了可説明您獲取有關使用 Azure Sentinel 的詳細資訊的資源。

Azure 邏輯應用連接器：<https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>稽核與報告
Azure Sentinel 的稽核記錄在[Azure 活動日誌](../azure-monitor/platform/platform-logs-overview.md)中維護。

可以審核以下支援的操作。

|作業名稱|    資源類型|
|----|----|
|創建或更新活頁簿  |微軟.見解/活頁簿|
|刪除活頁簿    |微軟.見解/活頁簿|
|設置工作流   |Microsoft.Logic/workflows|
|刪除工作流    |Microsoft.Logic/workflows|
|創建保存的搜索    |微軟.操作見解/工作區/保存的搜索|
|刪除保存的搜索    |微軟.操作見解/工作區/保存的搜索|
|更新警報規則 |微軟.安全見解/警報規則|
|刪除警報規則 |微軟.安全見解/警報規則|
|更新警報規則回應操作 |微軟.安全見解/警報規則/操作|
|刪除警報規則回應操作 |微軟.安全見解/警報規則/操作|
|更新書簽   |微軟.安全見解/書簽|
|刪除書簽   |微軟.安全見解/書簽|
|更新案例   |微軟.安全見解/案例|
|更新案例調查  |微軟.安全見解/案例/調查|
|創建案例注釋   |微軟.安全見解/案例/評論|
|更新資料連線器 |微軟.安全洞察/資料連線器|
|刪除資料連線器 |微軟.安全洞察/資料連線器|
|更新設定    |微軟.安全見解/設置|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>在 Azure 哨兵中查看審核和報告資料

可以通過從 Azure 活動日誌資料流到 Azure Sentinel 來查看此資料，然後可以對這些資料執行研究和分析。

1. 連接[Azure 活動](connect-azure-activity.md)資料來源。 執行此操作後，審核事件將資料流到**日誌**螢幕中稱為 AzureActivity 的新表中。
2. 然後，使用 KQL 查詢資料，就像使用任何其他表一樣。



## <a name="vendor-documentation"></a>供應商文檔

| **供應商**  | **在 Azure 哨兵中使用事件** | **連結**|
|----|----|----|
| GitHub| 用於訪問社區頁面| <https://github.com/Azure/Azure-Sentinel> |
| 帕洛阿爾托| 配置 CEF| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| 複數視覺 | Kusto 查詢語言課程| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>博客和論壇

在 Azure 哨兵[的技術社區空間](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel)上發佈您的問題。

查看[來自 Tech社區和](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/)的 Azure Sentinel 博客文章。


## <a name="next-steps"></a>後續步驟
在本文檔中，您得到了一個在使用 Azure Sentinel 時有用的資源清單。 您可以在[Microsoft Azure 安全和合規性博客上](https://blogs.msdn.com/b/azuresecurity/)找到有關 Azure 安全性和合規性的其他資訊。
