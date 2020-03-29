---
title: 延遲工作流中的下一個操作
description: 使用 Azure 邏輯應用中的延遲或延遲操作，等待在邏輯應用工作流中運行下一個操作
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
tags: connectors
ms.openlocfilehash: 5348ade1ba6eec6cbd360849411b4520cb3c2b19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74787331"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>延遲在 Azure 邏輯應用中運行下一個操作

要讓邏輯應用在運行下一個操作之前等待大量時間，可以在邏輯應用工作流中的操作之前添加內置**延遲 - 計畫**操作。 或者，您可以添加內置**延遲直到 - 計畫**操作以等待特定日期和時間，然後再運行下一個操作。 有關內置計畫操作和觸發器的詳細資訊，請參閱[使用 Azure 邏輯應用計畫並運行定期自動化、任務和工作流](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

* **延遲**：在下一個操作運行之前，等待指定的時間單位數，如秒、分鐘、小時、天、周或月。

* **延遲至**：等待，直到指定的日期和時間，然後下一個操作運行。

以下是使用這些操作的一些示例方法：

* 等到工作日再透過電子郵件傳送狀態更新。

* 在恢復和檢索資料之前，延遲工作流，直到 HTTP 調用完成。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果沒有訂閱，可以[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 關於[邏輯應用](../logic-apps/logic-apps-overview.md)的基本知識。 在使用操作之前，邏輯應用必須首先從觸發器開始。 您可以在添加延遲操作之前使用所需的任何觸發器並添加其他操作。 本主題使用 Office 365 Outlook 觸發器。 如果您是邏輯應用的新功能，請[瞭解如何創建第一個邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>添加延遲操作

1. 在邏輯應用設計器中，在要添加延遲操作的步驟下，選擇 **"新建步驟**"。

   要在步驟之間添加延遲操作，請將指標移到連接步驟的箭頭上。 選擇顯示的加號 （+），然後選擇 **"添加操作**"。

1. 在搜索框中，輸入"延遲"作為篩選器。 從動作清單中選擇此操作：**延遲**

   ![添加"延遲"操作](./media/connectors-native-delay/add-delay-action.png)

1. 指定在下一個操作運行之前等待的時間量。

   ![設置延遲的時間量](./media/connectors-native-delay/delay-time-intervals.png)

   | 屬性 | JSON 名稱 | 必要 | 類型 | 描述 |
   |----------|-----------|----------|------|-------------|
   | Count | count | 是 | 整數  | 要延遲的時間單位數 |
   | 單位 | unit | 是 | String | 時間單位，`Second`例如： `Minute`、 `Hour`、 `Day`、 `Week`、 、 、 、`Month` |
   ||||||

1. 添加要在工作流中運行的任何其他操作。

1. 完成後，儲存邏輯應用程式。

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>添加延遲直到操作

1. 在邏輯應用設計器中，在要添加延遲操作的步驟下，選擇 **"新建步驟**"。

   要在步驟之間添加延遲操作，請將指標移到連接步驟的箭頭上。 選擇顯示的加號 （+），然後選擇 **"添加操作**"。

1. 在搜索框中，輸入"延遲"作為篩選器。 從動作清單中選擇此操作：**延遲至**

   ![新增「延遲直到」動作](./media/connectors-native-delay/add-delay-until-action.png)

1. 提供要恢復工作流的結束日期和時間。

   ![指定何時結束延遲的時間戳記](./media/connectors-native-delay/delay-until-timestamp.png)

   | 屬性 | JSON 名稱 | 必要 | 類型 | 描述 |
   |----------|-----------|----------|------|-------------|
   | 時間戳記 | timestamp | 是 | String | 使用此格式恢復工作流的結束日期和時間： <p>YYYY-MM-DDThh：mm：ssZ <p>例如，如果您希望 2017 年 9 月 18 日下午 2：00，請指定"2017-09-18T14：00：00Z"。 <p>**注：** 此時間格式必須遵循[ISO 8601 日期時間規範](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)[（UTC 日期時間格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)），但沒有[UTC 偏移量](https://en.wikipedia.org/wiki/UTC_offset)。 如果沒有時區，則必須在末尾添加字母"Z"，沒有任何空格。 這個 "Z" 係指對等的[航海時間](https://en.wikipedia.org/wiki/Nautical_time)。 |
   ||||||

1. 添加要在工作流中運行的任何其他操作。

1. 完成後，儲存邏輯應用程式。

## <a name="next-steps"></a>後續步驟

* [使用"定期"觸發器創建、計畫和運行重複的任務和工作流](../connectors/connectors-native-recurrence.md)
* [適用於 Logic Apps 的連接器](../connectors/apis-list.md)