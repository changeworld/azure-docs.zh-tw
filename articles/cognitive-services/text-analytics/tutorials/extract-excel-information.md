---
title: 使用文本分析和電源自動功能在 Excel 中提取資訊
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用文本分析和電源自動功能提取 Excel 文本而無需編寫代碼。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/27/2019
ms.author: aahi
ms.openlocfilehash: fd70fe14d3765fb7c21b92f62b4d73564176baa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78201451"
---
# <a name="extract-information-in-excel-using-text-analytics-and-power-automate"></a>使用文本分析和電源自動功能在 Excel 中提取資訊 

在本教程中，您將創建一個 Power 自動流，以提取 Excel 試算表中的文本，而無需編寫代碼。 

此流程將獲取有關公寓建築群報告的問題的試算表，並將它們分為兩類：管道和其他。 它還將提取發送租戶的姓名和電話號碼。 最後，流將此資訊追加到 Excel 工作表。 

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 使用電源自動創建流
> * 從 OneDrive 上傳 Excel 資料
> * 從 Excel 中提取文本，並將其發送到文本分析 API 
> * 使用 API 中的資訊更新 Excel 工作表。

## <a name="prerequisites"></a>Prerequisites

- Microsoft Azure 帳戶。 [開始免費試用](https://azure.microsoft.com/free/)或[登入](https://portal.azure.com/)。
- 文本分析資源。 如果沒有，可以在[Azure 門戶中創建一個](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)，並使用免費層完成本教程。
- 在註冊期間為您生成的[鍵和終結點](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)。
- 包含租戶問題的試算表。 示例資料在 GitHub 上提供
- 辦公室 365，帶 OneDrive 業務。

## <a name="add-the-excel-file-to-onedrive-for-business"></a>將 Excel 檔添加到企業 OneDrive

從[GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx)下載示例 Excel 檔。 此檔必須存儲在您的 OneDrive 業務帳戶中。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Excel 檔中的示例。":::

這些問題以原始文本報告。 我們將使用文本分析 API 的命名實體識別來提取人員姓名和電話號碼。 然後，流將在描述中查找單詞"plumbs"來對問題進行分類。 

## <a name="create-a-new-power-automate-workflow"></a>創建新的電源自動化工作流

轉到[電源自動網站](https://preview.flow.microsoft.com/)，並登錄。 然後按一下"**創建**"和 **"計畫流**"。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="流創建螢幕。":::


在 **"生成計畫流**"頁上，使用以下欄位初始化流：

|欄位 |值  |
|---------|---------|
|**流程名稱**     | **計畫審核**或其他名稱。         |
|**開始**     |  輸入當前日期和時間。       |
|**重複每個**     | **1 小時**        |

## <a name="add-variables-to-the-flow"></a>向流添加變數

> [!NOTE]
> 如果要查看已完成流的圖像，可以從[GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/TextAnalytics/flow-diagrams)下載它。 

創建表示將添加到 Excel 檔的資訊的變數。 按一下 **"新步驟"** 並搜索**初始化變數**。 執行此操作四次，以創建四個變數。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="初始化變數。":::

將以下資訊添加到您創建的變數。 它們表示 Excel 檔的列。 如果任何變數折疊，您可以按一下它們來展開它們。

| 動作 |名稱   | 類型 | 值 |
|---------|---------|---|---|
| 將變數初始化 | var_person | String | 個人 |
| 初始化變數 2 | var_phone | String | Phone_Number |
| 初始化變數 3 | var_plumbing | String | 管道 |
| 初始化變數 4 | var_other | String | 其他 | 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="流變數中包含的資訊":::

## <a name="read-the-excel-file"></a>讀取 Excel 檔

按一下 **"新建步驟"** 並鍵入**Excel**，然後從動作清單中選擇**表中存在的行清單**。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="添加 Excel 行。":::

通過填寫此操作中的欄位，將 Excel 檔添加到流中。 本教程要求檔已上載到 OneDrive 業務。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="添加 Excel 行。":::

按一下 **"新建步驟**"，並為每個操作添加 **"應用**"。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="添加應用命令。":::

按一下"**從上一步中選擇輸出**"。 在顯示的"動態內容"框中，選擇**值**。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="從 Excel 檔中選擇輸出。":::

## <a name="send-a-request-to-the-text-analytics-api"></a>向文本分析 API 發送請求

如果尚未創建，則需要在 Azure 門戶中創建[文本分析資源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)。

### <a name="create-a-text-analytics-connection"></a>創建文本分析連接

在"**向每個應用**"中，按一下"**添加操作**"。 轉到 Azure 門戶中的文本分析資源**鍵和終結點**頁面，並獲取文本分析資源的鍵和終結點。

在流中，輸入以下資訊以創建新的文本分析連接。

> [!NOTE]
> 如果您已經創建了文本分析連接，並且想要更改連接詳細資訊，請按一下右上角的省略號，然後按一下 **"添加新連接**"。

| 欄位           | 值                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| 連線名稱 | 與文本分析資源連接的名稱。 例如： `TAforPowerAutomate` 。 |
| 帳戶金鑰     | 文本分析資源的關鍵。                                                                                   |
| 網站 URL        | 文本分析資源的終結點。                                                       |

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="將文本分析憑據添加到流中。":::

## <a name="extract-the-excel-content"></a>提取 Excel 內容 

創建連接後，搜索**文本分析**並選擇**實體**。 這將從問題的描述列中提取資訊。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="將文本分析憑據添加到流中。":::

按一下 **"文本"** 欄位，並從顯示的動態內容視窗中選擇 **"描述**"。 輸入`en`語言。 （如果看不到語言，請按一下"顯示高級選項"

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="將文本分析憑據添加到流中。":::


## <a name="extract-the-person-name"></a>提取人員姓名

接下來，我們將在文本分析輸出中找到人員實體類型。 在"**應用於每個**"中，按一下"**添加操作**"，然後創建另一個 **"應用於每個**操作"。 按一下文字方塊內，並在顯示的動態內容視窗中選擇 **"實體**"。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="將文本分析憑據添加到流中。":::

在新創建的 **"應用於每 2 個操作"** 中，按一下"**添加操作**"並添加 **"條件**"控制項。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="將文本分析憑據添加到流中。":::

在"條件"視窗中，按一下第一個文字方塊。 在"動態內容"視窗中，搜索**實體類型**並選擇它。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="將文本分析憑據添加到流中。":::

確保第二個框設置為**等於**。 然後選擇第三個框，然後在動態`var_person`內容視窗中搜索。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="將文本分析憑據添加到流中。":::

在 **"如果是"** 條件中，在 Excel 中鍵入，然後選擇 **"更新行**"。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="將文本分析憑據添加到流中。":::

輸入 Excel 資訊，並更新 **"鍵列**、**鍵值**和**人員姓名"** 欄位。 這將將 API 檢測到的名稱追加到 Excel 工作表中。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="將文本分析憑據添加到流中。":::

## <a name="get-the-phone-number"></a>獲取電話號碼

按一下名稱，將 **"應用於應用於每 2 個**操作"最小化。 然後，像之前一樣 **，向每個**操作添加另一個"應用"。 它將被命名為 **"應用於每個 3**"。 選擇文字方塊，並將**實體**添加為此操作的輸出。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-3.png" alt-text="將文本分析憑據添加到流中。":::

在 **"應用於每個 3"** 中，添加**條件**控制項。 它將被命名為**條件2。** 在第一個文字方塊中，從動態內容視窗中搜索和添加**實體類型**。 確保中心框設置為**等於**。 然後，在右側文字方塊中輸入`var_phone`。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="將文本分析憑據添加到流中。":::

在 **"如果是"** 條件中，添加**更新行**操作。 然後輸入上述資訊，輸入 Excel 工作表的電話號碼列。 這將將 API 檢測到的電話號碼追加到 Excel 工作表中。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="將文本分析憑據添加到流中。":::


## <a name="get-the-plumbing-issues"></a>獲取管道問題

通過按一下名稱**最小化應用於每個 3。** 然後，在父操作中創建另一個 **"應用於每個**應用"。 選擇文字方塊，然後從"動態內容"視窗中添加**實體**作為此操作的輸出。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-4.png" alt-text="將文本分析憑據添加到流中。":::


接下來，流將檢查 Excel 表行中的問題描述是否包含單詞"plumbs"。 如果是，它將在"問題類型"列中添加"管道"。 如果沒有，我們將輸入"其他"。

在 **"應用於每個 4**個操作"中，添加**條件**控制。 它將被命名為**條件3。** 在第一個文字方塊中，使用動態內容視窗搜索並從 Excel 檔中添加 **"說明**"。 確保中心框顯示**包含**。 然後，在正確的文字方塊中，查找並選擇`var_plumbing`。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="將文本分析憑據添加到流中。":::


在 **"如果是"** 條件中，按一下"**添加操作**"並選擇 **"更新行**"。 然後輸入資訊，如前。 在"問題類型"列中`var_plumbing`，選擇 。 這將對行應用"管道"標籤。

在"**如果沒有條件"** 中，按一下"**添加操作**"並選擇 **"更新行**"。 然後輸入資訊，如前。 在"問題類型"列中`var_other`，選擇 。 這將對行應用"其他"標籤。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="將文本分析憑據添加到流中。":::

## <a name="test-the-workflow"></a>測試工作流程

在螢幕的右上角，按一下"**保存**"，然後按一下 **"測試**"。 選擇 **"我將執行觸發器操作**"。 按一下 **"保存&測試**，**運行流**，然後**完成**。

Excel 檔將在 OneDrive 帳戶中更新。 它看起來像下面。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="更新的 Excel 試算表。":::

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [探索更多解決方案](../text-analytics-user-scenarios.md)
