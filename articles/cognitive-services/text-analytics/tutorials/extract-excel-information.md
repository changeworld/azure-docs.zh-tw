---
title: 使用文字分析和 Power Automate 在 Excel 中擷取資訊
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用文字分析和 Power Automate 來解壓縮 Excel 文字，而不需要撰寫程式碼。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/27/2019
ms.author: aahi
ms.openlocfilehash: 197d28b2ac3d94b6639a6611b2919bdeb2b182e2
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359896"
---
# <a name="extract-information-in-excel-using-text-analytics-and-power-automate"></a>使用文字分析和 Power Automate 在 Excel 中擷取資訊 

在本教學課程中，您將建立 Power Automate 流程，以在 Excel 試算表中解壓縮文字，而不需要撰寫程式碼。 

此流程將會取得有關單元複雜問題的試算表，並將其分類為兩個類別：「配管」和「其他」。 它也會解壓縮傳送這些租使用者的租使用者名稱和電話號碼。 最後，流程會將這份資訊附加到 Excel 工作表。 

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 使用 Power Automate 建立流程
> * 從商務用 OneDrive 上傳 Excel 資料
> * 從 Excel 解壓縮文字，並將它傳送至文字分析 API 
> * 使用 API 中的資訊來更新 Excel 工作表。

## <a name="prerequisites"></a>先決條件

- Microsoft Azure 帳戶。 [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)或[登入](https://portal.azure.com/)。
- 文字分析資源。 如果您沒有帳戶，可以 [在 Azure 入口網站中建立一個](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) ，並使用免費層來完成本教學課程。
- 註冊期間為您產生的 [金鑰和端點](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) 。
- 包含租使用者問題的試算表。 GitHub 上提供的範例資料
- Microsoft 365，使用商務用 OneDrive。

## <a name="add-the-excel-file-to-onedrive-for-business"></a>將 Excel 檔案新增至商務用 OneDrive

從 [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx)下載範例 Excel 檔案。 此檔案必須儲存在您的商務用 OneDrive 帳戶中。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Excel 檔案中的範例。":::

這些問題會以原始文字回報。 我們將使用文字分析 API 的命名實體辨識來解壓縮人員名稱和電話號碼。 然後，流程會尋找描述中的「配管」一字，以分類問題。 

## <a name="create-a-new-power-automate-workflow"></a>建立新的 Power Automate 工作流程

移至 [Power Automate 網站](https://preview.flow.microsoft.com/)，然後登入。 然後按一下 [ **建立** 和 **排程流程** ]。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="流程建立畫面。":::


在 [ **建立排程的流程** ] 頁面上，使用下欄欄位初始化您的流程：

|欄位 |值  |
|---------|---------|
|**流程名稱**     | **排程審核** 或其他名稱。         |
|**啟動中**     |  輸入目前的日期和時間。       |
|**重複間隔**     | **1 小時**        |

## <a name="add-variables-to-the-flow"></a>將變數新增至流程

> [!NOTE]
> 如果您想要查看已完成流程的影像，可以從 [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/TextAnalytics/flow-diagrams)下載。 

建立變數，代表將加入 Excel 檔案中的資訊。 按一下 [ **新增步驟** ]，然後搜尋 [ **初始化變數** ]。 請進行這四次，以建立四個變數。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="初始化變數。":::

將下列資訊新增至您所建立的變數。 它們代表 Excel 檔案的資料行。 如果有任何變數折迭，您可以按一下它們來展開它們。

| 動作 |名稱   | 類型 | 值 |
|---------|---------|---|---|
| 將變數初始化 | var_person | String | 個人 |
| 初始化變數2 | var_phone | String | Phone_Number |
| 初始化變數3 | var_plumbing | String | 管道 |
| 初始化變數4 | var_other | String | 其他 | 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="流程變數中包含的資訊":::

## <a name="read-the-excel-file"></a>讀取 excel 檔案

按一下 [ **新增步驟** ] 並輸入 **Excel** ，然後從動作清單中選取 **出現在資料表中** 的資料列。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="加入 excel 資料列。":::

填入此動作中的欄位，以將 Excel 檔案新增至流程。 本教學課程需要將檔案上傳至商務用 OneDrive。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="填寫 excel 資料列":::

按一下 [ **新增步驟** ]，並將 [套用 **至每個** 動作]。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="新增套用命令。":::

按一下 [ **選取上一個步驟的輸出** ]。 在出現的 [動態內容] 方塊中，選取 [ **值** ]。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="從 excel 檔案中選取 [輸出]。":::

## <a name="send-a-request-to-the-text-analytics-api"></a>將要求傳送至文字分析 API

如果您還沒有這麼做，您必須在 Azure 入口網站中建立 [文字分析資源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) 。

### <a name="create-a-text-analytics-connection"></a>建立文字分析連接

在 [套用 **到每個** ] 中，按一下 [ **新增動作** ]。 移至 Azure 入口網站中您文字分析資源的 **金鑰和端點** 頁面，並取得文字分析資源的金鑰和端點。

在您的流程中輸入下列資訊，以建立新的文字分析連接。

> [!NOTE]
> 如果您已經建立文字分析連接並想要變更連接詳細資料，請按一下右上角的省略號，然後按一下 [ **+ 加入新連接** ]。

| 欄位           | 值                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| 連線名稱 | 您文字分析資源的連接名稱。 例如： `TAforPowerAutomate` 。 |
| 帳戶金鑰     | 文字分析資源的索引鍵。                                                                                   |
| 網站 URL        | 文字分析資源的端點。                                                       |

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="將文字分析認證新增至您的流程。":::

## <a name="extract-the-excel-content"></a>將 excel 內容解壓縮 

建立連線之後，請搜尋 **文字分析** 並選取 [ **實體** ]。 這會從問題的 [描述] 資料行中解壓縮資訊。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="新增文字分析實體。":::

在 [ **文字** ] 欄位中按一下，然後從顯示的動態內容視窗中選取 [ **描述** ]。 輸入 `en` 語言。 如果您沒有看到 Language) ，請 (按一下 [顯示 advanced options]

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="新增文字分析設定。":::


## <a name="extract-the-person-name"></a>將人員名稱解壓縮

接下來，我們會在文字分析輸出中找到 person 實體類型。 在 [套用 **至各項** ] 內，按一下 [ **新增動作** ]，然後建立另一個套用 **至每個** 動作。 在文字方塊內按一下，然後在顯示的動態內容視窗中選取 [ **實體** ]。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="將文字分析認證新增至您的流程。二級":::

在新建立的 [套用 **至每2個** ] 動作中，按一下 [ **新增動作** ]，然後新增 **條件** 控制項。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="將文字分析認證新增至您的流程。3":::

在 [條件] 視窗中，按一下第一個文字方塊。 在 [動態內容] 視窗中，搜尋 **實體類型** ，然後選取它。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="將文字分析認證新增至您的流程。億":::

請確定第二個方塊設定為 [ **等於** ]。 然後選取第三個方塊，然後 `var_person` 在動態內容視窗中搜尋。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="將文字分析認證新增至您的流程。.5":::

在 [ **如果是]** 條件中，輸入 Excel，然後選取 [ **更新資料列** ]。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="將文字分析認證新增至您的流程。6":::

輸入 Excel 資訊，然後更新索引 **鍵資料行** 、索引 **鍵值** 和 **PersonName** 欄位。 這會將 API 所偵測到的名稱附加到 Excel 工作表。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="將文字分析認證新增至您的流程。型":::

## <a name="get-the-phone-number"></a>取得電話號碼

按一下名稱，即可將套用 **至每2個** 動作的最小化。 然後將另一個套用 **到每個** 動作，就像之前一樣。 它會命名為 **Apply to each 3** 。 選取文字方塊，並將 **實體** 新增為此動作的輸出。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-3.png" alt-text="將文字分析認證新增至您的流程。八角":::

在 [套用 **到每個 3** ] 中，新增 **條件** 控制項。 它會命名為 **Condition 2** 。 在第一個文字方塊中，從動態內容視窗搜尋和加入 **實體類型** 。 請確定 [置中] 方塊設定為 [ **等於** ]。 然後，在右邊的文字方塊中，輸入 `var_phone` 。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="將文字分析認證新增至您的流程。形":::

在 [ **如果是]** 條件中，新增 [ **更新資料列** ] 動作。 然後，在 Excel 工作表的 [電話號碼] 欄位中，輸入上述的資訊。 這會將 API 偵測到的電話號碼附加到 Excel 工作表。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="將文字分析認證新增至您的流程。10":::


## <a name="get-the-plumbing-issues"></a>取得管線問題

按一下名稱，即可 **將每3個** 套用的最小化。 然後建立另一個套用至父動作中的 **每個** 。 選取文字方塊，然後從動態內容視窗將 **實體** 新增為此動作的輸出。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-4.png" alt-text="將文字分析認證新增至您的流程。rj-11":::


接下來，流程將會檢查 Excel 資料表資料列中的問題描述是否包含「配管」一字。 如果是，它會在 IssueType 資料行中新增「配管」。 如果沒有，我們會輸入「其他」。

在 [套用 **至每4個** ] 動作內，新增 **條件** 控制項。 它會命名為 **Condition 3** 。 在第一個文字方塊中，使用動態內容視窗，從 Excel 檔案搜尋並加入 **描述** 。 請確定中間方塊顯示 [ **包含** ]。 然後，在右邊的文字方塊中尋找並選取 `var_plumbing` 。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="將文字分析認證新增至您的流程。全年":::


在 [ **如果是]** 條件中，按一下 [ **新增動作** ]，然後選取 [ **更新資料列** ]。 然後輸入像之前的資訊。 在 [IssueType] 資料行中，選取 `var_plumbing` 。 這會將「配管」標籤套用至資料列。

在 [ **如果沒有** 條件] 中，按一下 [ **新增動作** ]，然後選取 [ **更新資料列** ]。 然後輸入像之前的資訊。 在 [IssueType] 資料行中，選取 `var_other` 。 這會將「其他」標籤套用至資料列。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="將文字分析認證新增至您的流程。.13":::

## <a name="test-the-workflow"></a>測試工作流程

在畫面右上角，按一下 [ **儲存** ]，然後按一下 [ **測試** ]。 選取  **[我要執行觸發** 程式] 動作。 按一下 [ **儲存 & 測試** ]、[ **執行流程** ]，然後按一下 [ **完成** ]。

Excel 檔案將在您的 OneDrive 帳戶中更新。 它看起來會如下所示。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="更新的 excel 試算表。":::

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [探索更多解決方案](../text-analytics-user-scenarios.md)
