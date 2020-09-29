---
title: 分割映像目錄
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure Machine Learning 設計工具 (preview) 中使用分割影像目錄模組，將映射目錄的影像分割成兩個不同的集合。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: fe5ba25904298fe1a394a4b01d6bdacc72d599c9
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448674"
---
# <a name="split-image-directory"></a>分割映像目錄

本主題說明如何使用 Azure Machine Learning 設計工具中的 [分割映射目錄] 模組，將映射目錄的影像分割成兩個不同的集合。

當您需要將影像資料分成定型集和測試集時，此模組特別有用。 

## <a name="how-to-configure-split-image-directory"></a>如何設定分割影像目錄

1. 將 **分割映射目錄** 模組新增至您的管線。 您可以在 [電腦視覺/影像資料轉換] 類別下找到此模組。

2. 將它連接到輸出為影像目錄的模組。

3. **第一個輸出中的影像**輸入比例，以指定要放入左方分割中的資料百分比，預設為0.9。 如果小數結果不是整數，模組會使用較小的接近整數。


## <a name="technical-notes"></a>技術說明

### <a name="expected-inputs"></a>預期的輸入

| 名稱                  | 類型           | 描述              |
| --------------------- | -------------- | ------------------------ |
| 輸入影像目錄 | ImageDirectory | 要分割的映射目錄 |

### <a name="module-parameters"></a>模組參數

| 名稱                                   | 類型  | 範圍 | 選用 | 描述                            | 預設 |
| -------------------------------------- | ----- | ----- | -------- | -------------------------------------- | ------- |
| 第一個輸出中的影像分數 | Float | 0-1   | 必要 | 第一個輸出中的影像分數 | 0.9     |

### <a name="outputs"></a>輸出

| 名稱                    | 類型           | 描述                              |
| ----------------------- | -------------- | ---------------------------------------- |
| 輸出影像 directory1 | ImageDirectory | 包含所選映射的映射目錄 |
| 輸出影像 directory2 | ImageDirectory | 包含所有其他映射的映射目錄 |

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 

