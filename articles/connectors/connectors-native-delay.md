---
title: 延遲工作流程中的下一個動作
description: 等候在邏輯應用程式工作流程中執行下一個動作，方法是使用 [延遲] 或 [延遲到 Azure Logic Apps 中的動作]。
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
tags: connectors
ms.openlocfilehash: 5348ade1ba6eec6cbd360849411b4520cb3c2b19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74787331"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>在 Azure Logic Apps 中執行下一個動作的延遲

若要讓邏輯應用程式在執行下一個動作之前等候一段時間，您可以在邏輯應用程式的工作流程中的動作之前，新增內建**延遲排程**動作。 或者，您可以加入內建延遲到 **-Schedule**動作，以等到特定日期和時間執行下一個動作。 如需內建排程動作和觸發程式的詳細資訊，請參閱[使用 Azure Logic Apps 排程和執行循環性自動化、工作和工作流程](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

* **延遲**：等候指定的時間單位數，例如秒、分鐘、小時、天、周或月，在下一個動作執行之前。

* **延遲到**：等待下一個動作執行之前的指定日期和時間。

以下是一些使用這些動作的範例方式：

* 等到工作日再透過電子郵件傳送狀態更新。

* 延遲工作流程，直到 HTTP 呼叫完成，再繼續和抓取資料。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有訂用帳戶，您可以[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* [邏輯應用程式](../logic-apps/logic-apps-overview.md)的基本知識。 您的邏輯應用程式必須先從觸發程式開始，才可以使用動作。 您可以使用任何想要的觸發程式，並在新增延遲動作之前新增其他動作。 本主題使用 Office 365 Outlook 觸發程式。 如果您不熟悉邏輯應用程式，請瞭解[如何建立您的第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>新增 Delay 動作

1. 在邏輯應用程式設計工具中，于您要新增 [延遲] 動作的步驟底下，選擇 [**新增步驟**]。

   若要在步驟之間新增延遲動作，請將指標移到連接步驟的箭號上。 選擇顯示的加號（+），然後選取 [**新增動作**]。

1. 在搜尋方塊中，輸入「延遲」作為篩選準則。 從 [動作] 清單中，選取此動作： [**延遲**]

   ![新增「延遲」動作](./media/connectors-native-delay/add-delay-action.png)

1. 指定下一個動作執行之前要等待的時間量。

   ![設定延遲的時間長度](./media/connectors-native-delay/delay-time-intervals.png)

   | 屬性 | JSON 名稱 | 必要 | 類型 | 描述 |
   |----------|-----------|----------|------|-------------|
   | Count | count | 是 | 整數 | 要延遲的時間單位數 |
   | 單位 | unit | 是 | String | 時間`Second`單位，例如：、 `Minute`、 `Hour`、 `Day` `Week`、或。`Month` |
   ||||||

1. 新增您想要在工作流程中執行的任何其他動作。

1. 完成後，儲存邏輯應用程式。

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>新增 [延遲到] 動作

1. 在邏輯應用程式設計工具中，于您要新增 [延遲] 動作的步驟底下，選擇 [**新增步驟**]。

   若要在步驟之間新增延遲動作，請將指標移到連接步驟的箭號上。 選擇顯示的加號（+），然後選取 [**新增動作**]。

1. 在搜尋方塊中，輸入「延遲」作為篩選準則。 從 [動作] 清單中，選取此動作： [**延遲到**]

   ![新增「延遲直到」動作](./media/connectors-native-delay/add-delay-until-action.png)

1. 提供您要繼續工作流程的結束日期和時間。

   ![指定結束延遲的時間戳記](./media/connectors-native-delay/delay-until-timestamp.png)

   | 屬性 | JSON 名稱 | 必要 | 類型 | 描述 |
   |----------|-----------|----------|------|-------------|
   | 時間戳記 | timestamp | 是 | String | 使用此格式繼續工作流程的結束日期和時間： <p>YYYY-MM-DDThh： MM： ssZ <p>例如，如果您想要在2017年9月18日下午2:00，請指定 "2017-09-18T14：00： 00Z"。 <p>**注意：** 此時間格式必須遵循[utc 日期時間格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)的[ISO 8601 日期時間規格](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)，但不含[utc 時差](https://en.wikipedia.org/wiki/UTC_offset)。 如果沒有時區，您必須在結尾加上字母 "Z"，而不含任何空格。 這個 "Z" 係指對等的[航海時間](https://en.wikipedia.org/wiki/Nautical_time)。 |
   ||||||

1. 新增您想要在工作流程中執行的任何其他動作。

1. 完成後，儲存邏輯應用程式。

## <a name="next-steps"></a>後續步驟

* [使用週期觸發程式來建立、排程及執行循環性工作和工作流程](../connectors/connectors-native-recurrence.md)
* [適用於 Logic Apps 的連接器](../connectors/apis-list.md)