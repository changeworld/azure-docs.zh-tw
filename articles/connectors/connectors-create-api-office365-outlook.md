---
title: 連線至 Office 365 Outlook
description: 使用 Azure 邏輯應用自動執行管理 Office 365 Outlook 中的電子郵件、連絡人和日曆的任務和工作流
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/08/2020
tags: connectors
ms.openlocfilehash: b0f2b8b9c369fdb42c7e0e7f77fc090424ae3729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75732659"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>使用 Azure Logic Apps 在 Office 365 Outlook 中管理電子郵件、連絡人和行事曆

使用[Azure 邏輯應用](../logic-apps/logic-apps-overview.md)和[Office 365 Outlook 連接器](/connectors/office365connector/)，可以通過構建邏輯應用創建管理 Office 365 帳戶的自動化任務和工作流。 例如，您可以自動執行以下任務：

* 獲取、發送和回復電子郵件。 
* 在日曆上排程會議。
* 添加和編輯連絡人。 

您可以使用任何觸發器啟動工作流，例如，當新電子郵件到達時、日曆項更新時或在差異服務（如 Salesforce）中發生事件時。 您可以使用回應觸發器事件的操作，例如發送電子郵件或創建新的日曆事件。 

> [!NOTE]
> 要自動執行 或@outlook.com@hotmail.com帳戶的任務，請使用[Outlook.com連接器](../connectors/connectors-create-api-outlook.md)。

## <a name="prerequisites"></a>Prerequisites

* [辦公室 365 帳戶](https://www.office.com/)

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。 

* 要訪問 Office 365 Outlook 帳戶的邏輯應用。 要使用 Office 365 Outlook 觸發器啟動工作流，需要有一個[空白的邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 要向工作流添加 Office 365 Outlook 操作，邏輯應用需要已具有觸發器。

## <a name="add-a-trigger"></a>新增觸發程序

[觸發器](../logic-apps/logic-apps-overview.md#logic-app-concepts)是在邏輯應用中啟動工作流的事件。 此示例邏輯應用使用"輪詢"觸發器，該觸發器根據指定的間隔和頻率檢查電子郵件帳戶中的任何更新日曆事件。

1. 在[Azure 門戶](https://portal.azure.com)中，在邏輯應用設計器中打開空白邏輯應用。

1. 在搜尋方塊中，輸入 `office 365 outlook` 作為篩選條件。 此示例選擇**即將啟動的事件。**
   
   ![選擇觸發器以啟動邏輯應用](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. 如果系統提示您登錄，請提供 Office 365 憑據，以便邏輯應用可以連接到您的帳戶。 否則，如果連接已存在，請提供觸發器屬性的資訊。

   此示例選擇觸發器檢查的日曆，例如：

   ![配置觸發器的屬性](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. 在觸發器中，設置**頻率**和**間隔**值。 要添加其他可用的觸發器屬性（如**時區**），請從 **"添加新參數**"清單中選擇這些屬性。

   例如，如果希望觸發器每 15 分鐘檢查一次日曆，請將 **"頻率**"設置為 **"分鐘**"，`15`並將**間隔**設置為 。 

   ![設置觸發器的頻率和間隔](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. 在設計工具的工具列上，選取 [儲存]****。

現在添加在觸發觸發器後運行的操作。 例如，您可以添加 Twilio**發送消息**操作，該操作在日曆事件在 15 分鐘後啟動時發送文本。

## <a name="add-an-action"></a>新增動作

[操作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是由邏輯應用中的工作流運行的操作。 此示例邏輯應用在 Office 365 Outlook 中創建新連絡人。 您可以使用來自另一個觸發器或操作的輸出來創建連絡人。 例如，假設您的邏輯應用使用 Dynamics 365 觸發器，**創建記錄時**。 您可以添加 Office 365 Outlook**創建連絡人**操作，並使用 SalesForce 觸發器的輸出創建新連絡人。

1. 在[Azure 門戶](https://portal.azure.com)中，在邏輯應用設計器中打開邏輯應用。

1. 要將操作添加為工作流中的最後一步，請選擇 **"新建步驟**"。 

   要在步驟之間添加操作，請將指標移到這些步驟之間的箭頭上。 選擇顯示的加號**+**（），然後選擇 **"添加操作**"。

1. 在搜尋方塊中，輸入 `office 365 outlook` 作為篩選條件。 此示例選擇 **"創建連絡人**"。

   ![選擇要在邏輯應用中運行的操作](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. 如果系統提示您登錄，請提供 Office 365 憑據，以便邏輯應用可以連接到您的帳戶。 否則，如果連接已存在，請提供操作屬性的資訊。

   本示例選擇操作創建新連絡人的連絡人資料夾，例如：

   ![配置操作的屬性](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   要添加其他可用操作屬性，請從 **"添加新參數**"清單中選擇這些屬性。

1. 在設計工具的工具列上，選取 [儲存]****。

## <a name="connector-specific-details"></a>連接器特定的詳細資料

有關連接器 Swagger 檔中所述的觸發器、操作和限制的技術詳細資訊，請參閱[連接器的參考頁](/connectors/office365connector/)。 

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
