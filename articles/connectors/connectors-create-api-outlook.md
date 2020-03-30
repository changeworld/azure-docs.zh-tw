---
title: 連接到 Outlook.com
description: 使用 Azure 邏輯應用自動執行管理Outlook.com中的電子郵件、日曆和連絡人的任務和工作流
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 8d3b180b6f1e9dc4ec4b09dd81786cc81e8588da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75707181"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-by-using-azure-logic-apps"></a>使用 Azure 邏輯應用管理Outlook.com中的電子郵件、日曆和連絡人

使用[Azure 邏輯應用](../logic-apps/logic-apps-overview.md)和[Outlook.com連接器](/connectors/outlook/)，您可以創建通過構建邏輯應用來管理您@outlook.com或@hotmail.com帳戶的自動任務和工作流。 例如，您可以自動執行以下任務：

* 獲取、發送和回復電子郵件。
* 在日曆上排程會議。
* 添加和編輯連絡人。

您可以使用任何觸發器啟動工作流，例如，當新電子郵件到達時、日曆項更新時或在差異服務中發生事件時。 您可以使用回應觸發器事件的操作，例如發送電子郵件或創建新的日曆事件。

> [!NOTE]
> 要自動執行 Microsoft 工作帳戶的任務，@fabrikam.onmicrosoft.com例如 ，請使用[Office 365 Outlook 連接器](../connectors/connectors-create-api-office365-outlook.md)。

## <a name="prerequisites"></a>Prerequisites

* [Outlook.com帳戶](https://outlook.live.com/owa/)

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。 

* 您要在其中存取 Outlook.com 帳戶的邏輯應用程式。 要使用Outlook.com觸發器啟動工作流，需要有一個[空白的邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 要向工作流添加Outlook.com操作，邏輯應用需要已具有觸發器。

## <a name="add-a-trigger"></a>新增觸發程序

[觸發器](../logic-apps/logic-apps-overview.md#logic-app-concepts)是在邏輯應用中啟動工作流的事件。 此示例邏輯應用使用"輪詢"觸發器，該觸發器根據指定的間隔和頻率檢查電子郵件帳戶中的任何新電子郵件。

1. 在[Azure 門戶](https://portal.azure.com)中，在邏輯應用設計器中打開空白邏輯應用。

1. 在搜尋方塊中，輸入「outlook.com 」作為篩選條件。 在此示例中，選擇**新電子郵件到達時**。

1. 如果系統提示您登錄，請提供Outlook.com憑據，以便邏輯應用可以連接到您的帳戶。 否則，如果連接已存在，請提供觸發器屬性的資訊：

1. 在觸發器中，設置**頻率**和**間隔**值。

   例如，如果希望觸發器每 15 分鐘輪詢一次，則將 **"頻率**"設置為 **"分鐘**"，並將**間隔**設置為**15**。

1. 在設計器工具列上，選擇**保存**，以保存邏輯應用。

要回應觸發器，添加另一個操作。 例如，您可以添加 Twilio**發送郵件**操作，該操作在電子郵件到達時發送文本。

## <a name="add-an-action"></a>新增動作

[操作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是由邏輯應用中的工作流運行的操作。 此示例邏輯應用從Outlook.com帳戶發送電子郵件。 您可以使用另一個觸發器的輸出來填充操作。 例如，假設您的邏輯應用在**創建物件觸發器時**使用 SalesForce。 您可以添加Outlook.com**發送電子郵件**操作，並在電子郵件中使用 SalesForce 觸發器的輸出。

1. 在[Azure 門戶](https://portal.azure.com)中，在邏輯應用設計器中打開邏輯應用。

1. 要將操作添加為工作流中的最後一步，請選擇 **"新建步驟**"。 

   要在步驟之間添加操作，請將指標移到這些步驟之間的箭頭上。 選擇顯示的加號**+**（），然後選擇 **"添加操作**"。

1. 在搜索框中，輸入"outlook.com"作為篩選器。 在此示例中，選擇 **"發送電子郵件**"。 

1. 如果系統提示您登錄，請提供Outlook.com憑據，以便邏輯應用可以連接到您的帳戶。 否則，如果連接已存在，請提供操作屬性的資訊。

1. 在設計器工具列上，選擇**保存**，以保存邏輯應用。

## <a name="connector-reference"></a>連接器參考

如需連接器的 Swagger 檔案所敘述的技術詳細資料 (例如，觸發程序、動作和限制)，請參閱[連接器的參考頁面](/connectors/outlook/)。 

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
