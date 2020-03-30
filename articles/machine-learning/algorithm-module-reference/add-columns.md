---
title: 添加列：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 機器學習中使用"添加列"模組串聯兩個資料集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: f2e067f76d6ed7d89a38e9b8920c407f161969a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456773"
---
# <a name="add-columns-module"></a>添加列模組

本文介紹 Azure 機器學習設計器（預覽）中的模組。

使用此模組可以串聯兩個資料集。 合併指定為輸入的兩個資料集中的所有列以創建單個資料集。 如果需要串聯兩個以上資料集，請使用 **"添加列**"的多個實例。



## <a name="how-to-configure-add-columns"></a>如何配置"添加列"
1. 將 **"添加列"** 模組添加到管道中。

2. 連接要串聯的兩個資料集。 如果要合併兩個以上資料集，可以將 **"添加列**"的多個組合連結在一起。

    - 可以合併兩個行數不同的列。 輸出資料集將填充較小的源列中每行缺少值。

    - 您不能選擇要添加的單個列。 使用 **"添加列**"時，將串聯每個資料集中的所有列。 因此，如果只想添加列的子集，請使用"資料集中選擇列"使用所需的列創建資料集。

3. 提交管道。

### <a name="results"></a>結果
管道運行後：

- 要查看新資料集的第一行，請按右鍵 **"添加列"** 模組並選擇"視覺化"。 或者選擇模組並切換到右側面板中的 **"輸出"** 選項卡，按一下 **"埠輸出**"中的長條圖圖示以視覺化結果。

新資料集的資料行數目等於兩個輸入資料集的資料行總和。

如果輸入資料集中有兩個具有相同名稱的列，則將數位尾碼添加到列的名稱中。 例如，如果一個名為 Target結果的列有兩個實例，則左列將重命名TargetOutcome_1，右列將重命名TargetOutcome_2。

## <a name="next-steps"></a>後續步驟

請參閱 Azure 機器學習[可用的模組集](module-reference.md)。 