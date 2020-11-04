---
title: 識別案例和規劃分析程序 - Team Data Science Process | Azure 機器學習
description: 藉由考量一系列關鍵問題來識別案例和規劃進階分析資料處理。
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
ms.openlocfilehash: ffe1a4b9b32223cc57c6a6399fdc074a9d51d714
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322397"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>如何識別案例和規劃進階分析資料處理

您需要哪些資料才能建立一個可對資料集執行進階分析處理的環境？ 本文提出一系列問題，可幫助識別您的案例相關的工作和資源。

若要瞭解預測性分析的高階步驟順序，請參閱[何謂 Team Data Science Process (TDSP)](overview.md)。 針對您案例的相關工作，每個步驟都會需要特定的資源。

請回答下列範圍的重要問題來識別您的案例：

* 資料邏輯
* 資料特性
* 資料集品質
* 慣用的工具和語言

## <a name="logistic-questions-data-locations-and-movement"></a>邏輯問題：資料位置和移動

邏輯問題涵蓋下列項目：

* 資料來源位置
* Azure 中的目標目的地
* 移動資料的需求，包括排程、數量和涉及的資源

在分析程序期間，您可能需要移動資料數次。 常見的案例是將本機資料移到 Azure 上的某種儲存體，再移至 Machine Learning Studio。

### <a name="what-is-your-data-source"></a>資料來源是什麼？

您的資料是在本機或在雲端中？ 可能的位置包括：

* 可公開使用的 HTTP 位址
* 本機或網路檔案位置
* SQL Server 資料庫
* Azure 儲存體容器

### <a name="what-is-the-azure-destination"></a>Azure 目的地是什麼？

資料必須在何處進行處理或建模？ 

* Azure Blob 儲存體
* SQL Azure 資料庫
* Azure VM 上的 SQL Server
* HDInsight (Azure 上的 Hadoop) 或 Hive 資料表
* Azure Machine Learning
* 可裝載的 Azure 虛擬硬碟

### <a name="how-are-you-going-to-move-the-data"></a>您要如何移動資料？

如需用來將資料內嵌或載入至各種不同儲存體和處理環境的程序和資源，請參閱：

* [將資料載入至儲存體環境以便進行分析](ingest-data.md)
* [從各種資料來源將定型資料匯入 Azure Machine Learning Studio (傳統) ](../classic/import-data.md)

### <a name="does-the-data-need-to-be-moved-on-a-regular-schedule-or-modified-during-migration"></a>資料是否需要定期移動或在移轉期間修改？

需要持續移轉資料時，請考慮使用 Azure Data Factory (ADF)。 ADF 可在以下案例中提供協助：

* 同時涉及內部部署和雲端資源的混合式案例
* 在移轉的過程中由商務邏輯交易、修改或變更資料的案例

如需詳細資訊，請參閱 [使用 Azure Data Factory 將資料從 SQL Server 資料庫移至 SQL Azure](move-sql-azure-adf.md)。

### <a name="how-much-of-the-data-is-to-be-moved-to-azure"></a>有多少資料要移至 Azure？

大型資料集可能會超過某些環境的儲存體容量。 如需範例，請參閱下一節中 Machine Learning Studio (傳統) 的大小限制討論。 在這種情況下，您在分析期間可以使用資料樣本。 如需如何在各種 Azure 環境中縮減取樣資料集的詳細資訊，請參閱 [在 Team Data Science Process 中進行資料取樣](sample-data.md)。

## <a name="data-characteristics-questions-type-format-and-size"></a>資料特性問題：類型、格式和大小

這些問題對於規劃儲存體與處理環境很重要， 能協助您針對您的資料類型選擇適當的案例，並了解任何限制。

### <a name="what-are-the-data-types"></a>資料類型是什麼？

* 數值
* 類別
* 字串
* Binary

### <a name="how-is-your-data-formatted"></a>如何將資料格式化？

* 逗號分隔 (CSV) 或定位鍵分隔 (TSV) 一般檔案
* 已壓縮或未壓縮
* Azure Blob
* Hadoop Hive 資料表
* SQL Server 資料表

### <a name="how-large-is-your-data"></a>您的資料大小為何？

* 小型：小於 2 GB
* 中型：大於 2 GB 且小於 10 GB
* 大型：大於 10 GB

採用 Azure Machine Learning Studio (傳統) 環境，例如：

* 如需 Azure Machine Learning Studio 支援的資料格式和 類型的清單，請參閱 [支援的資料格式和資料類型](../classic/import-data.md#supported-data-formats-and-data-types) 一節。
* 如需分析程序中使用的其他 Azure 服務限制的相關資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與限制](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

## <a name="data-quality-questions-exploration-and-pre-processing"></a>資料品質問題：探索和前置處理

### <a name="what-do-you-know-about-your-data"></a>您對資料的熟悉程度為何？

了解有關您資料的基本特性：

* 展現出什麼模式或趨勢
* 有何極端值
* 有多少個值遺漏

此步驟很重要，可幫助您：

* 判斷需要多少前置處理
* 制定假設以建議最適合的功能或分析類型
* 制定計劃收集其他資料

實用的資料檢查技術，包括描述性統計算與視覺效果繪製。 如需如何在各種 Azure 環境中探索資料集的詳細資訊，請參閱 [在 Team Data Science Process 中探索資料](explore-data.md)。

### <a name="does-the-data-require-preprocessing-or-cleaning"></a>資料是否需要前置處理或清除？

您可能需要前置處理與清除您的資料，才能有效率地將資料用於機器學習。 未經處理的資料通常會有雜訊且不可靠， 還可能會有遺漏值。 使用這類資料進行模型化可能會產生誤導的結果。 如需說明，請參閱 [準備增強機器學習服務的資料的工作](prepare-data.md)。

## <a name="tools-and-languages-questions"></a>工具和語言的問題

有許多語言、開發環境和工具選項。 請了解您的需求和喜好設定。

### <a name="what-languages-do-you-prefer-to-use-for-analysis"></a>您想要使用哪些語言進行分析？

* R
* Python
* SQL

### <a name="what-tools-should-you-use-for-data-analysis"></a>您應該使用哪些工具進行資料分析？

* [Microsoft Azure PowerShell](/powershell/azure/) -用來以指令碼語言管理 Azure 資源的指令碼語言
* [Azure Machine Learning Studio](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
* [Revolution Analytics](https://www.microsoft.com/sql-server/machinelearningserver)
* [RStudio](https://www.rstudio.com)
* [適用於 Visual Studio 的 Python 工具](/visualstudio/python/)
* [Anaconda](https://www.anaconda.com/)
* [Jupyter 筆記本](https://jupyter.org/)
* [Microsoft Power BI](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>識別您的進階分析案例

回答上一區段中的問題之後，您便已準備好決定哪個案例最符合您的情況。 [Azure 機器學習服務中的進階分析案例](plan-sample-scenarios.md)中概述範例案例。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [何謂 Team Data Science Process (TDSP)？](overview.md)