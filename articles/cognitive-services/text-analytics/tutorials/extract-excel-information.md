---
title: 使用文字分析和電源自動化在 Excel 中解壓縮資訊
titleSuffix: Azure Cognitive Services
description: 瞭解如何在不需要撰寫程式碼的情況下，使用文字分析和電源自動化來解壓縮 Excel 文字。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/27/2019
ms.author: aahi
ms.openlocfilehash: fd70fe14d3765fb7c21b92f62b4d73564176baa2
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201451"
---
# <a name="extract-information-in-excel-using-text-analytics-and-power-automate"></a>使用文字分析和電源自動化在 Excel 中解壓縮資訊 

在本教學課程中，您將建立一項電源自動化流程，將 Excel 試算表中的文字解壓縮，而不需要撰寫程式碼。 

此流程將會收到一份試算表，其中會報告有關單元複雜的問題，並將其分類為兩個類別： 配量 和 其他 它也會將傳送給他們的租使用者名稱和電話號碼解壓縮。 最後，流程會將此資訊附加至 Excel 工作表。 

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 使用電源自動建立流程
> * 從商務用 OneDrive 上傳 Excel 資料
> * 從 Excel 解壓縮文字，並將它傳送至文字分析 API 
> * 使用 API 中的資訊來更新 Excel 工作表。

## <a name="prerequisites"></a>必要條件

- Microsoft Azure 帳戶。 [開始免費試用](https://azure.microsoft.com/free/)或[登入](https://portal.azure.com/)。
- 文字分析資源。 如果您沒有帳戶，您可以[在 Azure 入口網站中建立一個](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)，並使用免費層來完成本教學課程。
- 註冊時為您產生的[金鑰和端點](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)。
- 包含租使用者問題的試算表。 GitHub 上提供範例資料
- Office 365，使用商務用 OneDrive。

## <a name="add-the-excel-file-to-onedrive-for-business"></a>將 Excel 檔案新增至商務用 OneDrive

從[GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx)下載範例 Excel 檔案。 此檔案必須儲存在您的商務用 OneDrive 帳戶中。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Excel 檔案中的範例。":::

這些問題會以原始文字來回報。 我們將使用文字分析 API 的命名實體辨識來將人員名稱和電話號碼解壓縮。 然後流程會在 [描述] 中尋找「配管」一詞來分類問題。 

## <a name="create-a-new-power-automate-workflow"></a>建立新的電源自動化工作流程

移至 [[電源自動化] 網站](https://preview.flow.microsoft.com/)，然後登入。 然後按一下 [**建立**和**排程流程**]。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="流程建立畫面。":::


在 [**建立排程的流程**] 頁面上，使用下欄欄位初始化您的流程：

|欄位 |值  |
|---------|---------|
|**流程名稱**     | 已**排程的評論**或其他名稱。         |
|**啟動中**     |  輸入目前的日期和時間。       |
|**重複間隔**     | **1 小時**        |

## <a name="add-variables-to-the-flow"></a>將變數新增至流程

> [!NOTE]
> 如果您想要查看已完成流程的映射，您可以從[GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/TextAnalytics/flow-diagrams)下載。 

建立變數，代表將加入 Excel 檔案的資訊。 按一下 [**新增步驟**]，然後搜尋 [**初始化變數**]。 執行這四次，以建立四個變數。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="初始化變數。":::

將下列資訊新增至您所建立的變數。 它們代表 Excel 檔案的資料行。 如果有任何變數折迭，您可以按一下它們來展開。

| 動作 |名稱   | 類型 | 值 |
|---------|---------|---|---|
| 將變數初始化 | var_person | String | 人員 |
| 初始化變數2 | var_phone | String | Phone_Number |
| 初始化變數3 | var_plumbing | String | 管道 |
| 初始化變數4 | var_other | String | 其他 | 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="流程變數中包含的資訊":::

## <a name="read-the-excel-file"></a>讀取 excel 檔案

按一下 [**新增步驟**] 並輸入**Excel**，然後從動作清單**中選取出現在資料表中**的資料列。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="加入 excel 資料列。":::

藉由填入此動作中的欄位，將 Excel 檔案新增至流程。 本教學課程需要檔案已上傳至商務用 OneDrive。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="加入 excel 資料列。":::

按一下 [**新增步驟**]，並將 [套用] 新增**至每個**動作。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="新增 apply 命令。":::

按一下 [**選取上一個步驟的輸出**]。 在出現的 [動態內容] 方塊中，選取 [**值**]。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="從 excel 檔案中選取 [輸出]。":::

## <a name="send-a-request-to-the-text-analytics-api"></a>將要求傳送至文字分析 API

如果您還沒有這麼做，您必須在 Azure 入口網站中建立[文字分析資源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)。

### <a name="create-a-text-analytics-connection"></a>建立文字分析連線

在 [套用**至每一個**] 中，按一下 [**新增動作**]。 移至 Azure 入口網站中文字分析資源的 [**金鑰和端點**] 頁面，並取得文字分析資源的金鑰和端點。

在您的流程中，輸入下列資訊以建立新的文字分析連接。

> [!NOTE]
> 如果您已經建立文字分析連線，而且想要變更連線詳細資料，請按一下右上角的省略號，然後按一下 [ **+ 新增連接**]。

| 欄位           | 值                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| 連線名稱 | 連接至您的文字分析資源的名稱。 例如： `TAforPowerAutomate` 。 |
| 帳戶金鑰     | 文字分析資源的索引鍵。                                                                                   |
| 網站 URL        | 文字分析資源的端點。                                                       |

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="將文字分析認證新增至您的流程。":::

## <a name="extract-the-excel-content"></a>將 excel 內容解壓縮 

建立連線之後，搜尋**文字分析**並選取 [**實體**]。 這會從問題的 [描述] 資料行中解壓縮資訊。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="將文字分析認證新增至您的流程。":::

在**文字**欄位中按一下，然後從顯示的動態內容視窗中選取 [**描述**]。 輸入語言 `en`。 （如果您沒有看到語言，請按一下 [顯示] [高級選項]）

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="將文字分析認證新增至您的流程。":::


## <a name="extract-the-person-name"></a>將人員名稱解壓縮

接下來，我們會在文字分析輸出中找到 person 實體類型。 在 [套用**至每一個**] 中，按一下 [**新增動作**]，然後針對**每個**動作建立另一個 [套用]。 在文字方塊內按一下，然後在出現的動態內容視窗中選取 [**實體**]。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="將文字分析認證新增至您的流程。":::

在新建立的 [套用**到每個 2** ] 動作中，按一下 [**新增動作**]，然後新增 [**條件**] 控制項。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="將文字分析認證新增至您的流程。":::

在 [條件] 視窗中，按一下第一個文字方塊。 在 [動態內容] 視窗中，搜尋 [**實體類型**] 並加以選取。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="將文字分析認證新增至您的流程。":::

請確定第二個方塊設定為 [**等於**]。 然後選取第三個方塊，並在動態內容視窗中搜尋 `var_person`。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="將文字分析認證新增至您的流程。":::

在 [**如果是]** 條件中，輸入 Excel，然後選取 [**更新資料列**]。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="將文字分析認證新增至您的流程。":::

輸入 Excel 資訊，並更新 [索引**鍵資料行**]、[索引**鍵值**] 和 [ **PersonName** ] 欄位。 這會將 API 所偵測到的名稱附加至 Excel 工作表。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="將文字分析認證新增至您的流程。":::

## <a name="get-the-phone-number"></a>取得電話號碼

按一下名稱，將 [套用**到每個 2** ] 動作降至最低。 然後再將另一個套用**至每個**動作，就像之前一樣。 它會命名為 [套用**到每3個**]。 選取 [] 文字方塊，並加入**實體**做為此動作的輸出。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-3.png" alt-text="將文字分析認證新增至您的流程。":::

在 [套用**至每3個**] 中，新增**條件**控制項。 它會命名為**Condition 2**。 在第一個文字方塊中，搜尋並新增 [動態內容] 視窗中的 [**實體類型**]。 請確定 [中央] 方塊已設定為 [**等於**]。 然後，在右邊的文字方塊中輸入 `var_phone`。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="將文字分析認證新增至您的流程。":::

在 [**如果是]** 條件中，新增 [**更新資料列**] 動作。 然後，針對 Excel 工作表的 [電話號碼] 資料行，輸入上述的資訊。 這會將 API 偵測到的電話號碼附加至 Excel 工作表。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="將文字分析認證新增至您的流程。":::


## <a name="get-the-plumbing-issues"></a>取得管道問題

按一下名稱，**將套用到每 3**的最小化。 然後在父動作中，針對**每個**建立另一個 [套用]。 選取 [] 文字方塊，然後從 [動態內容] 視窗中，將**實體**新增為此動作的輸出。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-4.png" alt-text="將文字分析認證新增至您的流程。":::


接下來，流程將會檢查來自 Excel 資料表資料列的問題描述是否包含「配管」一詞。 如果是，它會在 IssueType 資料行中新增「配管」。 如果不是，我們會輸入「其他」。

在 [套用**至每4個**] 動作內，新增 [**條件**] 控制項。 它會命名為**條件 3**。 在第一個文字方塊中，使用動態內容視窗，搜尋並加入 Excel 檔案中的**描述**。 請確定中間方塊指出 [**包含**]。 然後，在右邊的文字方塊中，尋找並選取 [`var_plumbing`]。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="將文字分析認證新增至您的流程。":::


在 [**如果是]** 條件中，按一下 [**新增動作**]，然後選取 [**更新資料列**]。 然後輸入如 [之前] 的資訊。 在 [IssueType] 資料行中，選取 [`var_plumbing`]。 這會將「配管」標籤套用到資料列。

在 [**如果沒有**條件] 中，按一下 [**新增動作**]，然後選取 [**更新資料列**]。 然後輸入如 [之前] 的資訊。 在 [IssueType] 資料行中，選取 [`var_other`]。 這會將 [其他] 標籤套用至資料列。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="將文字分析認證新增至您的流程。":::

## <a name="test-the-workflow"></a>測試工作流程

在畫面右上角，依序按一下 [**儲存**] 和 [**測試**]。 選取 **[我將執行觸發程式動作**]。 依序按一下 **儲存 & 測試**、**執行流程** 及 **完成**。

Excel 檔案會在您的 OneDrive 帳戶中更新。 它看起來會像下面這樣。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="已更新的 excel 試算表。":::

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [探索更多解決方案](../text-analytics-user-scenarios.md)
