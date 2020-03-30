---
title: 使用 Azure Machine Learning 分析資料
description: 使用 Azure 機器學習基於 Azure 突觸中存儲的資料構建預測機器學習模型。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: 07570fd456d7f5a75a6b5a3ee635605a5d40072a
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350578"
---
# <a name="analyze-data-with-azure-machine-learning"></a>使用 Azure Machine Learning 分析資料
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure 機器學習](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

本教程使用 Azure 機器學習構建基於 Azure 突觸中存儲的資料的預測機器學習模型。 具體來說，這會為 Adventure Work 建置鎖定目標的行銷活動，預測客戶是否可能購買自行車。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>Prerequisites
若要逐步執行本教學課程，您需要：

* 預載入了 AdventureWorksDW 示例資料的 SQL 池。 要預配此內容，請參閱[創建 SQL 池](create-data-warehouse-portal.md)並選擇載入示例資料。 如果您已經有資料倉儲但沒有範例資料，您可以[手動載入範例資料](load-data-from-azure-blob-storage-using-polybase.md)。

## <a name="1-get-the-data"></a>1. 獲取資料
此資料位於 AdventureWorksDW 資料庫的 dbo.vTargetMail 檢視中。 如何讀取此資料：

1. 登入 [Azure Machine Learning Studio](https://studio.azureml.net/) 並按一下我的實驗。
2. 按一下螢幕左下角的 **+NEW，** 然後選擇 **"空白實驗**"。
3. 輸入您的實驗名稱：目標行銷。
4. 將"資料輸入"下的 **"導入資料模組"** 從模組窗格**中拖**出到畫布中。
5. 在"屬性"窗格中指定 SQL 池的詳細資訊。
6. 指定資料庫 **查詢** 以利讀取感興趣的資料。

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

按一下實驗畫布下方的 [執行] **** ，以執行實驗。

![執行實驗](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1-reader-new.png)

實驗成功執行完畢之後，按一下讀取器模組底部的輸出連接埠，然後選取 [視覺化] **** 以查看匯入的資料。

![檢視匯入的資料](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3-readerdata-new.png)

## <a name="2-clean-the-data"></a>2. 清潔資料
若要清除資料，請卸除與模型無關的某些資料行。 作法：

1. 將"資料**轉換<操作**下的**資料集模組中的"選擇列**"拖動到畫布中。 將此模組連接到**導入資料**模組。
2. 按一下 [屬性] 窗格中的 [啟動資料行選取器] **** 來指定您想要卸除的資料行。

   ![投射資料行](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4-projectcolumns-new.png)
3. 排除兩個資料行：CustomerAlternateKey 和 GeographyKey。

   ![移除不必要的資料行](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5-columnselector-new.png)

## <a name="3-build-the-model"></a>3. 構建模型
我們將會以 80-20 的比例分割資料：80% 訓練機器學習模型以及 20% 測試模型。 我們將利用"雙類"演算法來解決這種二進位分類問題。

1. 將 [分割] **** 模組拖曳至畫布。
2. 在屬性窗格中，為第一個輸出資料集中的行分數輸入 0.8。

   ![將資料分成訓練集和測試集](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6-split-new.png)
3. 將 [二元促進式決策樹] **** 模組拖曳至畫布。
4. 將**訓練模型**模組拖動到畫布中，並通過將其連接到**雙類提升決策樹**（ML 演算法） 和**拆分**（資料以訓練演算法）模組來指定輸入。 

     ![連接培訓模型模組](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7-train-new.png)
5. 然後，按一下 [屬性] 窗格中的 [啟動資料行選取器] **** 。 選取 **BikeBuyer** 資料行做為要預測的資料行。

   ![選取要預測的資料行](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8-traincolumnselector-new.png)

## <a name="4-score-the-model"></a>4. 對模型進行評分
現在，我們將測試模型如何在測試資料上執行。 我們將會比較我們選擇的演算法和不同的演算法，以查看何者的執行效果比較好。

1. 將**分數模型**模組拖動到畫布中，並將其連接到**訓練模型**和**拆分資料**模組。

   ![評分模型](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9-score-new.png)
2. 將 [二元貝氏點機器] **** 拖曳至實驗畫布。 我們將會比較這個演算法和二元促進式決策樹的執行效果。
3. 複製定型模型和分數模型等模組並貼在畫布上。
4. 將 [評估模型] **** 模組拖曳至畫布以比較兩個演算法。
5. **執行** 實驗。

   ![執行實驗](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10-evaluate-new.png)
6. 按一下 [評估模型] 模組底部的輸出連接埠，然後按一下 [視覺化]。

   ![將評估結果視覺化](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11-evalresults-new.png)

提供的指標包括 ROC 曲線、精度召回圖和提升曲線。 查看這些度量，我們可以看到第一個模型的執行效果優於第二個。 要查看第一個模型的預測，請按一下分數模型的輸出埠，然後按一下"視覺化"。

![將評分結果視覺化](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12-scoreresults-new.png)

您會看到另外兩個資料行加入至您的測試資料集。

* 評分機率：客戶成為自行車購買者的可能性。
* 評分標籤：由模型完成的分類 – 是自行車購買者 (1) 或不是 (0)。 標籤的機率臨界值設定為 50%，而且可以調整。

比較 BikeBuyer (實際) 資料行和評分標籤 (預測)，您可以看到模型的執行效果。 接下來，可以使用此模型為新客戶進行預測，並將此模型發佈為 Web 服務，或將結果寫回 Azure Synapse。

## <a name="next-steps"></a>後續步驟
若要深入了解如何建置預測性機器學習模型，請參閱 [Azure 上的機器學習服務簡介](https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/)。