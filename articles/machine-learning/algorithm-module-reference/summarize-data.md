---
title: 資料總結
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的「摘要資料」模組，為資料集中的資料行產生基本描述性統計資料包表。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 01/27/2020
ms.openlocfilehash: 5206565b85d1551e5e551f1dfe75d28c93bc53f0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90898213"
---
# <a name="summarize-data"></a>資料總結

本文描述 Azure Machine Learning 表設計工具的模組。

使用「摘要資料」模組來建立一組標準統計量值，以描述輸入資料表中的每個資料行。

當您想要瞭解完整資料集的特性時，摘要統計資料會很有用。 例如，您可能需要知道：

- 每個資料行中遺漏多少值？
- 特徵資料行中有多少唯一值？
- 每個資料行的平均值和標準差為何？

此模組會計算每個資料行的重要分數，並傳回每個變數 (資料行的摘要統計資料列，) 提供作為輸入。

## <a name="how-to-configure-summarize-data"></a>如何設定摘要資料  

1. 將 **摘要資料** 模組新增至您的管線。 您可以在設計工具的 **統計函數** 類別中找到此模組。

1. 連接您想要產生報表的資料集。

    如果您只想要報告某些資料行，請使用 [ [資料集模組中的選取資料行](select-columns-in-dataset.md) ] 來投影要使用的資料行子集。

1. 不需要任何其他參數。 依預設，此模組會分析提供做為輸入的所有資料行，並根據資料行中的數值型別，輸出一組相關的統計資料，如 [ [結果](#results) ] 區段中所述。

1. 提交管線。

## <a name="results"></a>結果

模組中的報表可以包含下列統計資料。 

|資料行名稱|描述|
|------|------|  
|**功能**|資料行的名稱|
|**Count**|所有資料列的計數|
|**唯一值計數**|資料行中的唯一值數目|
|**遺漏值計數**|資料行中的唯一值數目|
|**Min**|資料行中的最小值|  
|**Max**|資料行中的最大值|
|**平均數**|所有資料行值的平均值|
|**平均偏差**|資料行值的平均偏差|
|**第 1 四分位數**|第一次四個數值|
|**Median**|中位數資料行值|
|**第 3 四分位數**|第三個四個點的值|
|**Mode**|資料行值的模式|
|**Range**|整數，代表最大值與最小值之間的值數目|
|**樣本差異**|資料行的變異數;請參閱附注|
|**範例標準差**|資料行的標準差;請參閱附注|
|**範例偏斜**|資料行的不對稱;請參閱附注|
|**範例峰值**|資料行的峰度;請參閱附注|
|**P 0。5**|0.5% 百分位數|
|**P1**|1% 百分位數|
|**P5**|5% 百分位數|
|**P95**|95% 百分位數|
|**P 99。5**|99.5% 百分位數 |

## <a name="technical-notes"></a>技術說明

- 針對非數值資料行，只會計算計數、唯一值計數和遺漏值計數。 至於其他統計資料，則傳回 null 值。

- 包含布林值的資料行會使用下列規則來處理：

    - 計算 Min 時，套用邏輯 AND。
    
    - 在計算 Max 時，套用邏輯 OR
    
    - 計算「範圍」時，模組會先檢查資料行中的唯一值數目是否等於 2。
    
    - 在計算需要浮點計算的任何統計資料，True 值視為 1.0，False 值視為 0.0。

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。  
