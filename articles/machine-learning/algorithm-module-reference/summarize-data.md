---
title: 資料總結
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的摘要資料模組，針對資料集中的資料行產生基本的描述性統計資料包表。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 01/27/2020
ms.openlocfilehash: be6fd633f026c98e8f75467dc8661e695e121721
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841262"
---
# <a name="summarize-data"></a>資料總結

本文說明 Azure Machine Learning 設計工具（預覽）的模組。

使用 [摘要資料] 模組來建立一組標準統計量值，以描述輸入資料表中的每個資料行。

當您想要瞭解完整資料集的特性時，摘要統計資料會很有用。 例如，您可能需要知道：

- 每個資料行中有多少個遺漏值？
- 功能資料行中有多少個唯一值？
- 每個資料行的平均值和標準差為何？

此模組會計算每個資料行的重要分數，並針對提供做為輸入的每個變數（資料行），各傳回一個摘要統計資料列。

## <a name="how-to-configure-summarize-data"></a>如何設定摘要資料  

1. 將**摘要資料**模組新增至您的管線。 您可以在設計工具的**統計函數**類別目錄中找到此模組。

1. 連接您想要產生報表的資料集。

    如果您只想要報告一些資料行，請使用 [[選取資料集中的資料行](select-columns-in-dataset.md)] 模組來投影要使用的資料行子集。

1. 不需要任何其他參數。 根據預設，此模組會分析提供做為輸入的所有資料行，並視資料行中的數值型別而定，輸出一組相關的統計資料，如[結果](#results)一節中所述。

1. 執行管道。

## <a name="results"></a>結果

模組中的報表可包含下列統計資料。 

|資料行名稱|說明|
|------|------|  
|**功能**|資料行的名稱|
|**Count**|所有資料列的計數|
|**唯一值計數**|資料行中的唯一值數目|
|**遺漏值計數**|資料行中的唯一值數目|
|**Min**|資料行中的最小值|  
|**Max**|資料行中的最大值|
|**平均數**|所有資料行值的平均值|
|**平均偏差**|資料行值的平均偏差|
|**第1個四位**|第一個分位的值|
|**Median**|中位數資料行值|
|**第3個四位**|第三個分位的值|
|**模式**|資料行值的模式|
|**Range**|代表最大值與最小值之間值數目的整數|
|**樣本變異數**|資料行的變異數;請參閱附注|
|**範例標準差**|資料行的標準差;請參閱附注|
|**樣本偏斜**|資料行的偏斜請參閱附注|
|**範例峰**|資料行的峰度;請參閱附注|
|**P 0。5**|0.5% 百分位數|
|**P1**|1% 百分位數|
|**P5**|5% 百分位數|
|**P95**|95% 百分位數|
|**P 99。5**|99.5% 百分位數 |

## <a name="technical-notes"></a>技術說明

- 若為非數值資料行，則只會計算 [計數]、[唯一值計數] 和 [遺漏值計數] 的值。 若為其他統計資料，則會傳回 null 值。

- 包含布林值的資料行是使用下列規則來處理：

    - 計算 Min 時，會套用邏輯 AND。
    
    - 計算 Max 時，會套用邏輯 OR
    
    - 在計算範圍時，模組會先檢查資料行中的唯一值數目是否等於2。
    
    - 在計算需要浮點計算的任何統計資料時，True 的值會被視為1.0，而 False 的值會被視為0.0。

## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 的[模組集合](module-reference.md)。  
