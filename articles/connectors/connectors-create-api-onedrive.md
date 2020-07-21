---
title: 存取和管理 Microsoft OneDrive 中的檔案
description: 在 Azure Logic Apps 中建立自動化工作流程，以上傳和管理 OneDrive 中的檔案
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/18/2016
tags: connectors
ms.openlocfilehash: 8dd54fec963b8f4775a8ade6277b071d62ca3850
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524196"
---
# <a name="access-and-manage-files-in-onedrive-connector-by-using-azure-logic-apps"></a>使用 Azure Logic Apps 存取和管理 OneDrive 連接器中的檔案

藉由使用[Azure Logic Apps](../logic-apps/logic-apps-overview.md)和[OneDrive 連接器](/connectors/onedriveconnector/)，您可以建立自動化的工作和工作流程來管理您的檔案，包括上傳、取得、刪除檔案等等。 透過 OneDrive，您可以執行下列工作：

* 將檔案儲存在 OneDrive 中或更新 OneDrive 中的現有檔案以建置您的工作流程。 
* 使用觸發程序，在 OneDrive 內有檔案建立或更新時，啟動工作流程。
* 使用動作來建立檔案、刪除檔案等等。 例如，當收到有附件的新 Office 365 電子郵件時 (觸發程序)，在 OneDrive 建立新的檔案 (動作)。

本文說明如何在邏輯應用程式中使用 OneDrive 連接器，並且也列出觸發程序和動作。

若要深入瞭解 Logic Apps，請參閱[什麼是邏輯應用程式](../logic-apps/logic-apps-overview.md)以及[建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="connect-to-onedrive"></a>連線到 OneDrive

您必須先建立與服務的「連線」**，才能透過邏輯應用程式存取任何服務。 連線可讓邏輯應用程式與另一個服務連線。 例如，若要連線到 OneDrive，您必須先有 OneDrive「連線」**。 若要建立連線，請輸入平常用來存取所要連線之服務的認證。 因此，在 OneDrive 中，請在 OneDrive 帳戶輸入認證以建立連線。

### <a name="create-the-connection"></a>建立連線

[!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]

## <a name="use-a-trigger"></a>使用觸發程序

觸發程序是可用來啟動邏輯應用程式中所定義之工作流程的事件。 觸發程序會以您想要的間隔和頻率「輪詢」服務。 [深入了解觸發程序](../logic-apps/logic-apps-overview.md#logic-app-concepts)。

1. 在邏輯應用程式設計工具中，輸入 `onedrive` 以取得觸發程式的清單：  

   ![標題為「顯示 Microsoft 受控 Api」的對話方塊有一個包含「onedrive」的方塊。 以下是四個觸發程式的清單。 其中的第一個是「OneDrive-當檔案建立時」。 第二個「OneDrive-修改檔案時」已被選取。](./media/connectors-create-api-onedrive/onedrive-1.png)

2. 選取 [當檔案遭到修改時]****。 如果連線已存在，則選取 [顯示選擇器] 按鈕以選取資料夾。

   ![標題為「檔案已修改時」的對話方塊有一個標題為「資料夾」的方塊，其中有相關聯的 [流覽] 按鈕。](./media/connectors-create-api-onedrive/sample-folder.png)

   如果系統提示您登入，則輸入登入詳細資料來建立連線。 本文中的[建立連線](connectors-create-api-onedrive.md#create-the-connection)一節會列出步驟。

   在此範例中，當所選資料夾中的檔案更新時，邏輯應用程式便會執行。 若要查看此觸發程序的結果，請新增另一個動作，以傳送電子郵件給您。 例如，新增 Office 365 Outlook「傳送電子郵件」** 動作，以在檔案更新時傳送電子郵件給您。

3. 選取 [編輯]**** 按鈕，然後設定 [頻率]**** 和 [間隔]**** 值。 例如，如果您希望觸發程序每隔 15 分鐘輪詢一次，則將 [頻率]**** 設定為 [分鐘]**** 並將 [間隔]**** 設定為 [15]****。 

   ![標題為「當檔案修改時」的對話方塊會顯示五個方塊，分別標示為：「資料夾」、「頻率」、「間隔」、「時區」和「開始時間」。 [頻率] 和 [時區] 欄位有下拉式清單。](./media/connectors-create-api-onedrive/trigger-properties.png)

4. **儲存**您的變更 (工具列的左上角)。 邏輯應用程式將會儲存，而且可能會自動啟用。

## <a name="use-an-action"></a>使用動作

動作是由邏輯應用程式中定義的工作流程所執行的作業。 [深入瞭解動作](../logic-apps/logic-apps-overview.md#logic-app-concepts)。

1. 選取加號。 您會看到幾個選擇︰[新增動作]****、[新增條件]**** 或其中一個 [其他]**** 選項。

   ![螢幕擷取畫面顯示四個按鈕： [+ 新增步驟]、[新增動作]、[新增條件] 和 [...]更多 "。](./media/connectors-create-api-onedrive/add-action.png)

2. 選擇 [新增動作] ****。

3. 在 [搜尋] 方塊中，輸入 `onedrive` 以取得所有可用動作的清單。

   ![標題為「顯示 Microsoft 受控 Api」的對話方塊有一個包含「onedrive」的方塊。 以下是八個動作的清單。 第一個是「OneDrive-建立檔案」，並已選取。](./media/connectors-create-api-onedrive/onedrive-actions.png) 

4. 在我們的範例中，選擇 [OneDrive - 建立檔案]****。 如果連線已存在，則選取 [資料夾路徑]**** 以供放置檔案、輸入 [檔案名稱]****，然後選擇想要的 [檔案內容]****︰  

   ![標題為「建立檔案」的對話方塊會顯示三個方塊，分別標示為「資料夾路徑」、「檔案名」和「資料夾內容」。 [資料夾路徑] 方塊旁有一個目錄 [流覽] 按鈕。](./media/connectors-create-api-onedrive/sample-action.png)

   如果系統提示您輸入連線資訊，請依照本主題中的說明，輸入[建立連接](#create-the-connection)的詳細資料。

   在此範例中，您會在 OneDrive 資料夾中建立新的檔案。 您可以使用另一個觸發程序的輸出以建立 OneDrive 檔案。 例如，新增 Office 365 Outlook「新的電子郵件送達時」** 觸發程序。 然後新增 OneDrive「建立檔案」** 動作，使用 ForEach 內的 [附件] 和 [內容類型] 欄位在 OneDrive 中建立新檔案。

   ![標題為「For each」的對話方塊有一個標示為「選取先前步驟的輸出」的方塊，其中包含「附件」。 有一個 [Create file （建立檔案）] 對話方塊，其中涵蓋 [For each] 方塊的其餘部分，加上標籤為 [資料夾路徑]、[檔案名] 和 [檔案內容]。 ](./media/connectors-create-api-onedrive/foreach-action.png)

5. **儲存**您的變更 (工具列的左上角)。 邏輯應用程式將會儲存，而且可能會自動啟用。

## <a name="connector-specific-details"></a>連接器特定的詳細資料

檢視 Swagger 中定義的任何觸發程序和動作，另請參閱[連接器詳細資料](/connectors/onedriveconnector/)的所有限制。

## <a name="next-steps"></a>後續步驟

* [適用於 Azure Logic Apps 的連接器](apis-list.md)