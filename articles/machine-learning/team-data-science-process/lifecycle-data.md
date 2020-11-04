---
title: 資料取得與認知的 Team Data Science Process
description: 資料科學專案資料取得與認知階段的目標、工作和交付項目
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f7770e929e51ee51b09060e4247c5f92b27d1035
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93311874"
---
# <a name="data-acquisition-and-understanding-stage-of-the-team-data-science-process"></a>資料取得與認知階段的 Team Data Science Process

本文將概要說明與 Team Data Science Process (TDSP) 資料取得與認知階段相關聯的目標、工作和交付項目。 此流程會提供建議的生命週期，供您建構資料科學專案。 生命週期可描繪出專案一般執行時 (通常會反覆進行) 的主要階段：

   1. **商務理解**
   2. **資料取得和瞭解**
   3. **模型化**
   4. **部署**
   5. **客戶接受度**

以下是 TDSP 生命週期的視覺表示法： 

![TDSP 生命週期](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>目標
* 產生已了解與目標變數之關聯性的乾淨、高品質資料集。 在適當的分析環境中找出資料集，讓您準備好建立模型。
* 開發定期重新整理資料並加以評分之資料管線的方案架構。

## <a name="how-to-do-it"></a>如何執行此動作
此階段會解決三項主要工作︰

   * **內嵌資料** 到目標分析環境。
   * **瀏覽資料** 來判斷資料品質是否適合用來回答問題。 
   * **設定資料管線** 來對新資料或定期重新整理的資料進行評分。

### <a name="ingest-the-data"></a>內嵌資料
設定程序以將資料從來源位置移到目標位置，您可以在其中執行訓練和預測等分析作業。 如需如何使用各種 Azure 資料服務來移動資料的技術詳細資料和選項，請參閱[將資料載入至儲存體環境以便進行分析](ingest-data.md)。 

### <a name="explore-the-data"></a>探索資料
在訓練模型之前，您必須全面了解資料。 真實的資料集通常會有雜訊、遺漏值或具有許多不一致之處。 您可以使用資料摘要和視覺效果來稽核資料的品質，並提供處理資料所需的資訊以便讓資料準備好建立模型。 此程序通常會反覆進行。

TDSP 提供自動化公用程式 [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils)，以協助將資料視覺化及準備資料的摘要報告。 我們建議您先從 IDEAR 來瀏覽資料，以協助您在不必編寫程式碼的情況下，以互動方式來初步了解資料。 然後您可以針對資料探索和視覺效果撰寫自訂程式碼。 如需清理資料的指引，請參閱[準備增強機器學習服務的資料的工作](prepare-data.md)。  

當您滿意已清理資料的品質之後，下一步是深入了解資料中固有的模式。 這種資料分析可協助您為目標選擇和開發適當的預測模型。 尋找資料與目標相關程度的證據。 然後判斷是否有足夠的資料可繼續進行下一個模型建立步驟。 同樣地，此程序通常會反覆進行。 您可能需要尋找具有更精確或更相關資料的新資料來源，以擴大前一個階段中所初步找到的資料集。 

### <a name="set-up-a-data-pipeline"></a>設定資料管線
除了一開始要內嵌和清理資料外，您通常還需要設定用來對新資料評分或定期重新整理資料的程序，以做為持續學習程序的一部分。 您可以使用資料管線或工作流程來完成評分。 [使用 Azure Data Factory 文章將資料從 SQL Server 實例移至 Azure SQL Database](move-sql-azure-adf.md) ，會提供如何使用[Azure Data Factory](https://azure.microsoft.com/services/data-factory/)設定管線的範例。 

在此階段中，您要開發資料管線的方案架構。 您要開發與資料科學專案下一個階段平行的管線。 根據您的商務需求，以及要整合此解決方案之現有系統的條件約束，管線可以是下列其中一個選項： 

   * 批次型
   * 串流或即時 
   * 混合式 

## <a name="artifacts"></a>Artifacts
以下是此階段的交付項目：

   * [資料品質報告](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/DataSummaryReport.md) \(英文\)︰這份報告包含資料摘要、每個屬性與目標之間的關聯性、變數排名等等。 TDSP 中所提供的 [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) 工具，可在任何表格式資料集 (例如 CSV 檔案或關聯式資料表) 上快速產生這份報告。 
   * **方案架構** ︰在您建置模型後，方案架構可以是用來對新資料執行評分或預測之資料管線的圖表或描述。 它也包含可根據新資料重新訓練模型的管線。 當您使用 TDSP 目錄結構範本時，請將文件儲存在 [Project](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) 目錄中。
   * **檢查點決策** ：開始進行全功能工程和模型建立之前，您可以重新評估專案，以判斷預期值是否足以繼續進行。 比方說，您可能已準備好繼續、需要收集更多資料，或是因為不存在可以回答問題的資料而放棄專案。

## <a name="next-steps"></a>後續步驟

以下是 TDSP 生命週期中每個步驟的連結：

   1. [了解商務](lifecycle-business-understanding.md)
   2. [資料取得和瞭解](lifecycle-data.md)
   3. [模型化](lifecycle-modeling.md)
   4. [部署](lifecycle-deployment.md)
   5. [客戶接受度](lifecycle-acceptance.md)

我們會提供完整的逐步解說，示範適用于特定案例的程式中的所有步驟。 [範例逐步解說](walkthroughs.md)一文提供有連結和縮圖描述的案例清單。 這些逐步解說示範如何將雲端、內部部署工具與服務組合成工作流程或管線，以建立智慧型應用程式。 

如需如何在使用 Azure Machine Learning Studio 的 TDSP 中執行步驟的範例，請參閱[配合使用 Team Data Science Process 與 Azure Machine Learning]()。