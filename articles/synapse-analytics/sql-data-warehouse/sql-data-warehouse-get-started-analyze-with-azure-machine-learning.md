---
title: 使用 Azure Machine Learning 分析資料
description: 使用 Azure Machine Learning，根據 Azure Synapse 中儲存的資料建置預測性機器學習模型。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 07/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: 9cf65b2fdeb7faa03b950593db86dd32a4ef91a7
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86495703"
---
# <a name="analyze-data-with-azure-machine-learning"></a>使用 Azure Machine Learning 分析資料

本教學課程使用[Azure Machine Learning 設計](https://docs.microsoft.com/azure/machine-learning/concept-designer)工具來建立預測性機器學習模型。 此模型是以 Azure Synapse 中儲存的資料為基礎。 本教學課程的案例是預測客戶是否可能購買自行車，而不是使用「艾德公司」，自行車店面可以建立目標行銷活動。

## <a name="prerequisites"></a>Prerequisites

若要逐步執行本教學課程，您需要：

* 已預先載入 AdventureWorksDW 範例資料的 SQL 集區。 若要布建此 SQL 集區，請參閱[建立 sql 集](create-data-warehouse-portal.md)區並選擇載入範例資料。 如果您已經有資料倉儲，但沒有範例資料，您可以[手動載入範例資料](load-data-from-azure-blob-storage-using-polybase.md)。
* Azure Machine learning 工作區。 請遵循[此教學](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)課程來建立新的。

## <a name="get-the-data"></a>取得資料

使用的資料位於 AdventureWorksDW 中的 vTargetMail 視圖。 若要使用本教學課程中的資料存放區，資料會先匯出至 Azure Data Lake Storage 帳戶，因為 Azure Synapse 目前不支援資料集。 Azure Data Factory 可用來使用[複製活動](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)，將資料從資料倉儲匯出至 Azure Data Lake Storage。 使用下列查詢進行匯入：

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

一旦 Azure Data Lake Storage 中提供資料之後，Azure Machine Learning 中的資料存放區就會用來連線[到 Azure 儲存體服務](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)。 請遵循下列步驟來建立資料存放區和對應的資料集：

1. 從 Azure 入口網站啟動 Azure Machine learning studio，或在[Azure Machine Learning studio](https://ml.azure.com/)登入。

1. 按一下 [**管理**] 區段中左窗格上的 [**資料存放區**]，然後按一下 [**新增資料**存放區]。

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/datastores-tab.png" alt-text="Azure Machine Learning 介面左窗格的螢幕擷取畫面":::

1. 提供資料存放區的名稱，選取 [類型] 做為 [Azure Blob 儲存體]，並提供位置和認證。 接著按一下 [ **建立**]。

1. 接下來，在 [**資產**] 區段的左窗格中按一下 [**資料集**]。 **從 [資料**存放區] 選取 [**建立資料集**] 選項。

1. 指定資料集的名稱，然後選取要**表格式**的類型。 然後，按 **[下一步]** 繼續進行。

1. 在 [**選取或建立資料存放區] 區段**中，選取**先前建立的資料**存放區選項。 選取稍早建立的資料存放區。 按 [下一步]，並指定路徑和檔案設定。 如果檔案包含欄標題，請務必指定資料行標頭。

1. 最後，按一下 [**建立**] 以建立資料集。

## <a name="configure-designer-experiment"></a>設定設計工具實驗

接下來，依照下列步驟進行設計工具設定：

1. 在 [**作者**] 區段中，按一下左窗格上的 [**設計**工具] 索引標籤。

1. 選取**便於使用的預建模組**來建立新的管線。

1. 在右側的 [設定] 窗格中，指定管線的名稱。

1. 此外，在 [設定] 按鈕中針對 [整個實驗] 選取目標計算叢集至先前布建的叢集。 關閉 [設定] 窗格。

## <a name="import-the-data"></a>匯入資料

1. 在 [搜尋] 方塊下方的左窗格中選取 [**資料集**] 子對話方塊。

1. 將您稍早建立的資料集拖曳至畫布。

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/import-dataset.png" alt-text="畫布上資料集模組的螢幕擷取畫面。":::

## <a name="clean-the-data"></a>清除資料

若要清除資料，請卸載與模型無關的資料行。 遵循下列步驟：

1. 在左窗格中選取 [**模組**] 子標籤。

1. 將 [資料轉換操作] 底下的 [在資料集中選取資料行] 拖曳至畫布中。 將此模組連接到**資料集**模組。

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-in.png" alt-text="畫布上資料行選取模組的螢幕擷取畫面。" lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-out.png":::

1. 按一下模組以開啟 [屬性] 窗格。 按一下 [編輯資料行] 以指定您想要卸載的資料行。

1. 排除兩個資料行：CustomerAlternateKey 和 GeographyKey。 按一下 [儲存] 

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/drop-columns.png" alt-text="顯示已卸載之資料行的螢幕擷取畫面。":::

## <a name="build-the-model"></a>建立模型

資料會分割 80-20: 80% 來定型機器學習模型，並將20% 用來測試模型。 這個二元分類問題中使用了「雙類別」演算法。

1. 將 [**分割資料**] 模組拖曳至畫布。

1. 在 [屬性] 窗格中，針對**第一個輸出資料集內的資料列分數**輸入0.8。

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/split-data.png" alt-text="顯示分割比例0.8 的螢幕擷取畫面。":::

1. 將 [二元促進式決策樹]  模組拖曳至畫布。

1. 將 [**定型模型**] 模組拖曳至畫布。 指定輸入，方法是將它連接到二元促進**式決策樹**（ML 演算法）並**分割資料**（用來定型演算法的資料）模組。

1. 針對 [定型模型模型]，在 [屬性] 窗格的 [**標籤資料行**] 選項中選取 [編輯資料行] 選取**BikeBuyer**資料行做為要預測的資料行，然後選取 [**儲存**]。

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/label-column.png" alt-text="顯示已選取標籤資料行 BikeBuyer 的螢幕擷取畫面。":::

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/train-model.png" alt-text="螢幕擷取畫面：顯示連接到雙類別促進式決策樹和分割資料模組的定型模型模組。":::

## <a name="score-the-model"></a>評分模型

現在，測試模型對測試資料的執行方式。 將會比較兩個不同的演算法，以查看哪一個演算法的執行效能較佳。 遵循下列步驟：

1. 將 [計分模型] 模組拖曳至畫布中，並將其連接到 [訓練模型] 和 [分割資料] 模組。

1. 將**兩個類別的貝氏機率分類平均認知**拖曳至實驗畫布。 相較于二元促進式決策樹，您將比較此演算法的執行方式。

1. 複製並貼上畫布中的模組**訓練模型**和**評分模型**。

1. 將 [評估模型]  模組拖曳至畫布以比較兩個演算法。

1. 按一下 [**提交**] 以設定管線執行。

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-in.png" alt-text="畫布上所有其餘模組的螢幕擷取畫面。" lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-out.png":::

1. 執行完成後，以滑鼠右鍵按一下 [**評估模型**] 模組，然後按一下 [將**評估結果視覺化**]。

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/result-visualize-zoomed-out.png" alt-text="結果的螢幕擷取畫面。":::

提供的計量資訊包括 ROC 曲線、正確性-召回圖表和升力曲線。 查看這些計量，以查看第一個模型的執行效果優於第二個。 若要查看第一個模型的預測，請以滑鼠右鍵按一下 [評分模型] 模組，然後按一下 [視覺化計分資料集] 以查看預測的結果。

您會看到兩個額外的資料行加入至您的測試資料集。

* 評分機率：客戶成為自行車購買者的可能性。
* 評分標籤：由模型完成的分類 – 是自行車購買者 (1) 或不是 (0)。 標籤的機率臨界值設定為 50%，而且可以調整。

將資料行 BikeBuyer （實際）與評分標籤（預測）進行比較，以查看模型的執行效果。 接下來，您可以使用此模型為新客戶進行預測。 您可以[將此模型發佈為 web 服務](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy)，或將結果寫回 Azure Synapse。

## <a name="next-steps"></a>後續步驟

若要深入瞭解 Azure Machine Learning，請參閱[Azure 上的 Machine Learning 簡介](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml)。

在[這裡](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest)瞭解資料倉儲內建的評分。