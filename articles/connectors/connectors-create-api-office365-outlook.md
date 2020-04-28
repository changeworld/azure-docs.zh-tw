---
title: 連線至 Office 365 Outlook
description: 使用 Azure Logic Apps，將管理 Office 365 Outlook 電子郵件、連絡人和行事曆的工作和工作流程自動化
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/08/2020
tags: connectors
ms.openlocfilehash: b0f2b8b9c369fdb42c7e0e7f77fc090424ae3729
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75732659"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>使用 Azure Logic Apps 在 Office 365 Outlook 中管理電子郵件、連絡人和行事曆

有了[Azure Logic Apps](../logic-apps/logic-apps-overview.md)和[Office 365 Outlook connector](/connectors/office365connector/)，您就可以建立邏輯應用程式來建立自動化的工作和工作流程，以管理您的 Office 365 帳戶。 例如，您可以將這些工作自動化：

* 取得、傳送和回復電子郵件。 
* 在行事曆上排程會議。
* 新增和編輯連絡人。 

您可以使用任何觸發程式來啟動您的工作流程，例如，當新的電子郵件送達時、行事曆專案更新時，或在差異服務（例如 Salesforce）中發生事件時。 您可以使用回應觸發程式事件的動作，例如傳送電子郵件或建立新的行事曆事件。 

> [!NOTE]
> 若要自動執行@outlook.com或@hotmail.com帳戶的工作，請使用[Outlook.com 連接器](../connectors/connectors-create-api-outlook.md)。

## <a name="prerequisites"></a>Prerequisites

* [Office 365 帳戶](https://www.office.com/)

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。 

* 您想要在其中存取 Office 365 Outlook 帳戶的邏輯應用程式。 若要使用 Office 365 Outlook 觸發程式啟動您的工作流程，您需要有[空白的邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要將 Office 365 Outlook 動作新增至您的工作流程，您的邏輯應用程式必須已經有觸發程式。

## <a name="add-a-trigger"></a>新增觸發程序

[觸發](../logic-apps/logic-apps-overview.md#logic-app-concepts)程式是在邏輯應用程式中啟動工作流程的事件。 這個範例邏輯應用程式會使用「輪詢」觸發程式，根據指定的間隔和頻率，檢查您的電子郵件帳戶中是否有任何更新的行事曆事件。

1. 在[Azure 入口網站](https://portal.azure.com)中，于邏輯應用程式設計工具中開啟空白邏輯應用程式。

1. 在搜尋方塊中，輸入 `office 365 outlook` 作為篩選條件。 這個範例**會選取即將推出的事件何時即將開始**。
   
   ![選取 [觸發] 以啟動邏輯應用程式](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. 如果系統提示您登入，請提供您的 Office 365 認證，讓您的邏輯應用程式可以連線到您的帳戶。 否則，如果您的連接已經存在，請提供觸發程式屬性的資訊。

   這個範例會選取觸發程式檢查的行事曆，例如：

   ![設定觸發程式的屬性](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. 在觸發程式中，設定**頻率**和**間隔**值。 若要新增其他可用的觸發程式屬性，例如**時區**，請從 [**加入新的參數**] 清單中選取這些屬性。

   例如，如果您希望觸發程式每隔15分鐘檢查一次行事曆，請將**Frequency**設定為**Minute**，並`15`將**Interval**設定為。 

   ![設定觸發程式的頻率和間隔](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. 在設計工具的工具列上，選取 [儲存]****。

現在，新增在觸發程式引發之後執行的動作。 例如，您可以新增 Twilio**傳送訊息**動作，這會在行事曆事件于15分鐘內開始時傳送文字。

## <a name="add-an-action"></a>新增動作

[動作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是由邏輯應用程式中的工作流程所執行的作業。 這個範例邏輯應用程式會在 Office 365 Outlook 中建立新的連絡人。 您可以使用另一個觸發程式或動作的輸出來建立連絡人。 例如，假設您的邏輯應用程式在建立**記錄時**使用 Dynamics 365 觸發程式。 您可以新增 Office 365 Outlook [**建立連絡人**] 動作，並使用 SalesForce 觸發程式的輸出來建立新的連絡人。

1. 在[Azure 入口網站](https://portal.azure.com)中，于邏輯應用程式設計工具中開啟邏輯應用程式。

1. 若要新增動作做為工作流程中的最後一個步驟，請選取 [**新增步驟**]。 

   若要在步驟之間新增動作，請將指標移到這些步驟之間的箭號上。 選取顯示的加號（**+**），然後選取 [**新增動作**]。

1. 在搜尋方塊中，輸入 `office 365 outlook` 作為篩選條件。 這個範例會選取 [**建立連絡人**]。

   ![選取要在邏輯應用程式中執行的動作](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. 如果系統提示您登入，請提供您的 Office 365 認證，讓您的邏輯應用程式可以連線到您的帳戶。 否則，如果您的連線已存在，請提供動作屬性的資訊。

   這個範例會選取動作建立新連絡人的 [連絡人] 資料夾，例如：

   ![設定動作的屬性](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   若要新增其他可用的動作內容，請從 [**加入新的參數**] 清單中選取這些屬性。

1. 在設計工具的工具列上，選取 [儲存]****。

## <a name="connector-specific-details"></a>連接器特定的詳細資料

如需有關觸發程式、動作和限制的技術詳細資料（如連接器的 Swagger 檔案中所述），請參閱[連接器的參考頁面](/connectors/office365connector/)。 

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
