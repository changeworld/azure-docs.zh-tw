---
title: 連接到 Outlook.com
description: 使用 Azure Logic Apps 將在 Outlook.com 中管理電子郵件、行事曆和連絡人的工作和工作流程自動化
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 8d3b180b6f1e9dc4ec4b09dd81786cc81e8588da
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "75707181"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-by-using-azure-logic-apps"></a>使用 Azure Logic Apps 管理 Outlook.com 中的電子郵件、行事曆和連絡人

使用 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 和 [Outlook.com 連接器](/connectors/outlook/)，您可以建立自動化的工作和工作流程，以 @outlook.com 藉 @hotmail.com 由建立邏輯應用程式來管理您的或帳戶。 例如，您可以將這些工作自動化：

* 取得、傳送和回復電子郵件。
* 在行事曆上排程會議。
* 新增及編輯連絡人。

您可以使用任何觸發程式來啟動工作流程，例如，當新的電子郵件送達時、行事曆專案更新時，或事件發生在差異服務時。 您可以使用回應觸發程式事件的動作，例如傳送電子郵件或建立新的行事曆事件。

> [!NOTE]
> 若要將 Microsoft 工作帳戶（例如）的工作自動化 @fabrikam.onmicrosoft.com ，請使用 [Office 365 Outlook 連接器](../connectors/connectors-create-api-office365-outlook.md)。

## <a name="prerequisites"></a>必要條件

* [Outlook.com 帳戶](https://outlook.live.com/owa/)

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。 

* 您要在其中存取 Outlook.com 帳戶的邏輯應用程式。 若要使用 Outlook.com 觸發程式來啟動您的工作流程，您需要有 [空白的邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要將 Outlook.com 動作新增至您的工作流程，您的邏輯應用程式必須已經有觸發程式。

## <a name="add-a-trigger"></a>新增觸發程序

[觸發](../logic-apps/logic-apps-overview.md#logic-app-concepts)程式是在邏輯應用程式中啟動工作流程的事件。 此範例邏輯應用程式會使用「輪詢」觸發程式，根據指定的間隔和頻率，在您的電子郵件帳戶中檢查是否有任何新的電子郵件。

1. 在 [Azure 入口網站](https://portal.azure.com)中，于邏輯應用程式設計工具中開啟空白邏輯應用程式。

1. 在搜尋方塊中，輸入「outlook.com 」作為篩選條件。 在此範例中，請選取 **新的電子郵件送達的時間**。

1. 如果系統提示您登入，請提供您的 Outlook.com 認證，讓您的邏輯應用程式能夠連線到您的帳戶。 否則，如果您的連接已經存在，請提供觸發程式屬性的資訊：

1. 在觸發程式中，設定 **頻率** 和 **間隔** 值。

   例如，如果您想要讓觸發程式每隔15分鐘輪詢一次，請將 **頻率** 設定為 **分鐘**，然後將 **間隔** 設定為 **15**。

1. 在設計工具的工具列上，選取 [儲存]，以 **儲存**邏輯應用程式。

若要回應觸發程式，請新增另一個動作。 例如，您可以新增 Twilio **傳送訊息** 動作，這會在電子郵件送達時傳送文字。

## <a name="add-an-action"></a>新增動作

[動作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是由邏輯應用程式中的工作流程所執行的作業。 此範例邏輯應用程式會從您的 Outlook.com 帳戶傳送電子郵件。 您可以使用另一個觸發程式的輸出來填入動作。 例如，假設您的邏輯應用程式 **在建立物件時** 使用 SalesForce。 您可以新增 Outlook.com **傳送電子郵件** 動作，並使用電子郵件中 SalesForce 觸發程式的輸出。

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

1. 若要將動作新增為工作流程中的最後一個步驟，請選取 [ **新增步驟**]。 

   若要在步驟之間新增動作，請將指標移至這些步驟之間的箭頭上。 選擇出現的加號 ( **+** )，然後選取 [新增動作]。

1. 在搜尋方塊中，輸入 "outlook.com" 作為篩選準則。 針對此範例，請選取 [ **傳送電子郵件**]。 

1. 如果系統提示您登入，請提供您的 Outlook.com 認證，讓您的邏輯應用程式能夠連線到您的帳戶。 否則，如果您的連接已經存在，請提供動作屬性的資訊。

1. 在設計工具的工具列上，選取 [儲存]，以 **儲存**邏輯應用程式。

## <a name="connector-reference"></a>連接器參考

如需連接器的 Swagger 檔案所敘述的技術詳細資料 (例如，觸發程序、動作和限制)，請參閱[連接器的參考頁面](/connectors/outlook/)。 

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
