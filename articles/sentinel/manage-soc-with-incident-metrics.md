---
title: 使用 Azure Sentinel 中的事件計量，更妥善管理您的 SOCMicrosoft Docs
description: 使用 Azure Sentinel 事件計量畫面和活頁簿中的資訊，協助您管理安全性作業中心 (SOC) 。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2020
ms.author: yelevin
ms.openlocfilehash: 408913fed864ee5f966b96c81afbfee4b2dc8678
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660724"
---
# <a name="manage-your-soc-better-with-incident-metrics"></a>透過事件計量更好的管理 SOC

> [!IMPORTANT]
> 事件計量功能目前處於公開預覽狀態。
> 這些功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

作為安全性作業中心 (SOC) manager，您需要有整體效率的計量和量值，才能測量小組的效能。 您會想要依許多不同的條件（例如嚴重性、MITRE 策略、平均時間分級、平均解決時間等），查看一段時間的事件作業。 Azure Sentinel 現在可讓您使用 Log Analytics 中的新 **SecurityIncident** 資料表和架構，以及隨附的 **安全性操作效率** 活頁簿，提供此資料給您。 您將能夠在一段時間後將您的小組效能視覺化，並使用此深入解析來改善效率。 您也可以針對事件資料表撰寫和使用自己的 KQL 查詢，以建立符合您特定審核需求和 Kpi 的自訂活頁簿。

## <a name="use-the-security-incidents-table"></a>使用安全性事件資料表

**SecurityIncident** 資料表內建于 Azure Sentinel。 您會在 [**記錄** 檔] 下的 **SecurityInsights** 集合中找到其他資料表。 您可以像 Log Analytics 中的任何其他資料表一樣進行查詢。

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incident-table.png" alt-text="安全性事件資料表":::

每次建立或更新事件時，都會在資料表中新增一個新的記錄專案。 這可讓您追蹤對事件所做的變更，並允許更強大的 SOC 計量，但您必須在為此資料表建立查詢時留意這一點，因為您可能需要移除事件的重複專案， (相依于您正在執行) 的實際查詢。 

例如，如果您想要傳回依事件編號排序的所有事件清單，但只想要傳回每個事件的最新記錄，您可以使用 KQL [摘要運算子](/azure/data-explorer/kusto/query/summarizeoperator) 搭配 `arg_max()` [彙總函式](/azure/data-explorer/kusto/query/arg-max-aggfunction)來執行此作業：


```Kusto
SecurityIncident
| summarize arg_max(LastModifiedTime, *) by IncidentNumber
```
### <a name="more-sample-queries"></a>更多範例查詢

平均關閉時間：
```Kusto
SecurityIncident
| summarize arg_max(TimeGenerated,*) by IncidentNumber 
| extend TimeToClosure =  (ClosedTime - CreatedTime)/1h
| summarize 5th_Percentile=percentile(TimeToClosure, 5),50th_Percentile=percentile(TimeToClosure, 50), 
  90th_Percentile=percentile(TimeToClosure, 90),99th_Percentile=percentile(TimeToClosure, 99)
```

平均分級時間：
```Kusto
SecurityIncident
| summarize arg_max(TimeGenerated,*) by IncidentNumber 
| extend TimeToTriage =  (FirstModifiedTime - CreatedTime)/1h
| summarize 5th_Percentile=max_of(percentile(TimeToTriage, 5),0),50th_Percentile=percentile(TimeToTriage, 50), 
  90th_Percentile=percentile(TimeToTriage, 90),99th_Percentile=percentile(TimeToTriage, 99) 
```

## <a name="security-operations-efficiency-workbook"></a>安全性作業效率活頁簿

為了補充 **SecurityIncidents** 資料表，我們提供了現成可用的 **安全性操作效率** 活頁簿範本，讓您用來監視 SOC 作業。 活頁簿包含下列計量： 
- 一段時間內建立的事件 
- 藉由關閉分類、嚴重性、擁有者和狀態所建立的事件 
- 平均分級時間 
- 平均結束時間 
- 經過一段時間的嚴重性、擁有者、狀態、產品和策略所建立的事件 
- 百分位數的時間 
- 關閉百分位數的時間 
- 每位擁有者分級的平均時間 
- 最近的活動 
- 最近的結尾分類  

您可以從 Azure Sentinel 導覽功能表中選擇活頁 **簿** ，然後選取 [ **範本** ] 索引標籤，以找到這個新的活頁簿範本。從資源庫選擇 **安全性作業效率** ，然後按一下其中一個 [ **已儲存** 的活頁簿] 和 [ **查看範本** ] 按鈕。

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incidents-workbooks-gallery.png" alt-text="安全性事件活頁簿圖庫":::

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-operations-workbook-1.png" alt-text="安全性事件活頁簿完成":::

您可以使用範本來建立您專屬的自訂活頁簿，以滿足您的特定需求。

## <a name="securityincidents-schema"></a>SecurityIncidents 架構

[!INCLUDE [SecurityIncidents schema](../../includes/sentinel-schema-security-incident.md)]

## <a name="next-steps"></a>後續步驟

- 若要開始使用 Azure Sentinel，您需要 Microsoft Azure 訂用帳戶。 如果您沒有訂用帳戶，可以註冊[免費試用](https://azure.microsoft.com/free/)。
- 了解如何[將資料上架到 Azure Sentinel](quickstart-onboard.md)，並[掌握您的資料和潛在威脅](quickstart-get-visibility.md)。