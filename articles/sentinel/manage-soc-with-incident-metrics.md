---
title: 使用 Azure Sentinel 中的事件計量，更適當地管理您的 SOC |Microsoft Docs
description: 使用 [Azure Sentinel 事件計量] 畫面和活頁簿中的資訊，協助您管理安全性作業中心（SOC）。
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
ms.date: 06/29/2020
ms.author: yelevin
ms.openlocfilehash: f14b0050aefc598d26dec7a7781a3378ccaa7570
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294093"
---
# <a name="manage-your-soc-better-with-incident-metrics"></a>透過事件計量更好的管理 SOC

> [!IMPORTANT]
> 事件計量功能目前為公開預覽狀態。
> 這些功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

身為安全性作業中心（SOC）管理員，您必須擁有整體的效率計量和量值，才能測量小組的效能。 您會想要查看一段時間內的事件作業，方法有許多不同的準則，例如嚴重性、MITRE 策略、平均分級時間、平均解決時間等等。 Azure Sentinel 現在會使用 Log Analytics 中的新**SecurityIncident**資料表和架構，以及隨附的**安全性作業效率**活頁簿，將此資料提供給您。 您將能夠視覺化小組在一段時間內的效能，並使用此深入解析來提升效率。 您也可以針對事件資料表撰寫和使用您自己的 KQL 查詢，以建立符合您特定審核需求和 Kpi 的自訂活頁簿。

## <a name="use-the-security-incidents-table"></a>使用安全性事件資料表

**SecurityIncident**資料表內建在 Azure Sentinel 中。 您會在 [**記錄**] 下的 [ **SecurityInsights** ] 集合中找到其他資料表。 您可以像 Log Analytics 中的任何其他資料表一樣進行查詢。

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incident-table.png" alt-text="安全性事件資料表":::

每次建立或更新事件時，新的記錄專案都會加入至資料表。 這可讓您追蹤對事件所做的變更，並允許更強大的 SOC 計量，但當您為此資料表建立查詢時，您必須留意這一點，因為您可能需要移除事件的重複專案（取決於您所執行的確切查詢）。 

例如，如果您想要傳回依據事件編號排序的所有事件清單，但只想要傳回每個事件的最新記錄檔，您可以使用 KQL[摘要運算子](https://docs.microsoft.com/azure/data-explorer/kusto/query/summarizeoperator)搭配 `arg_max()` [彙總函式](https://docs.microsoft.com/azure/data-explorer/kusto/query/arg-max-aggfunction)來執行此動作：

`SecurityIncident` <br>
`| summarize arg_max(LastModifiedTime, *) by IncidentNumber`

## <a name="security-operations-efficiency-workbook"></a>安全性作業效率活頁簿

為了補充**SecurityIncidents**資料表，我們為您提供了現成可用的**安全性作業效率**活頁簿範本，讓您用來監視 SOC 作業。 活頁簿包含下列計量： 
- 一段時間內建立的事件 
- 關閉分類、嚴重性、擁有者和狀態所建立的事件 
- 平均分級時間 
- 平均結束時間 
- 一段時間內由嚴重性、擁有者、狀態、產品和策略所建立的事件 
- 百分位數的時間 
- 關閉百分位數的時間 
- 每個擁有者的平均時間 
- 最近的活動 
- 最近的收盤分類  

您可以從 [Azure Sentinel] 導覽功能表選擇 [活頁**簿**]，然後選取 [**範本**] 索引標籤，以找到這個新的活頁簿範本。從資源庫選擇 [**安全性作業效率**]，然後按一下 [**已儲存**的活頁簿] 和 [**視圖範本**

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incidents-workbooks-gallery.png" alt-text="安全性事件活頁簿資源庫":::

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-operations-workbook-1.png" alt-text="安全性事件活頁簿完成":::

您可以使用範本來建立您自己的自訂活頁簿，並根據您的特定需求量身打造。

## <a name="securityincidents-schema"></a>SecurityIncidents 架構

[!INCLUDE [SecurityIncidents schema](../../includes/sentinel-schema-security-incident.md)]

## <a name="next-steps"></a>後續步驟

- 若要開始使用 Azure Sentinel，您需要 Microsoft Azure 訂用帳戶。 如果您沒有訂用帳戶，可以註冊[免費試用](https://azure.microsoft.com/free/)。
- 了解如何[將資料上架到 Azure Sentinel](quickstart-onboard.md)，並[掌握您的資料和潛在威脅](quickstart-get-visibility.md)。
