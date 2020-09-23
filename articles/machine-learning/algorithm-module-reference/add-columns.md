---
title: 新增資料行：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的 [新增資料行] 模組串連兩個資料集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 17882581c4548996c9a8b2a8670b5f720e64da1e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90898988"
---
# <a name="add-columns-module"></a>新增資料行模組

本文描述 Azure Machine Learning 設計工具中的模組。

使用此模組來串連兩個資料集。 您可以結合您指定為輸入的兩個資料集的所有資料行，以建立單一資料集。 如果您需要串連兩個以上的資料集，請使用多個 [ **加入資料行**] 實例。



## <a name="how-to-configure-add-columns"></a>如何設定新增資料行
1. 將 [ **新增資料行** ] 模組新增至您的管線。

2. 連接您想要串連的兩個資料集。 如果您想要結合兩個以上的資料集，您可以將多個 **加入資料行**的組合結合在一起。

    - 您可以結合兩個具有不同資料列數目的資料行。 輸出資料集會針對較小來源資料行中的每個資料列，以遺漏的值填補。

    - 您無法選擇要加入的個別資料行。 當您使用 [ **新增資料行**] 時，會串連每個資料集的所有資料行。 因此，如果您只想要加入資料行的子集，請使用 [資料集] 中的 [選取資料行]，以您想要的資料行建立資料集。

3. 提交管線。

### <a name="results"></a>結果
在管線執行之後：

- 若要查看新資料集的第一個資料列，請以滑鼠右鍵按一下 [ **加入** 資料行] 模組，然後選取 [視覺化]。 或者，選取模組並切換至右面板中的 [ **輸出** ] 索引標籤，按一下 **埠輸出** 中的長條圖圖示以視覺化結果。

新資料集的資料行數目等於兩個輸入資料集的資料行總和。

如果輸入資料集有兩個具有相同名稱的資料行，則會在資料行名稱中加入數值尾碼。 例如，如果有兩個名為 TargetOutcome 的資料行實例，左側資料行會重新命名 TargetOutcome_1，且右邊的資料行會重新命名 TargetOutcome_2。

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 