---
title: 套用轉換：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的 [套用轉換] 模組，根據先前計算的轉換來修改輸入資料集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/26/2020
ms.openlocfilehash: a5db3935ae445ee7dcf8129eb1d4c75fcb64302f
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92739193"
---
# <a name="apply-transformation-module"></a>套用轉換模組

本文描述 Azure Machine Learning 設計工具中的模組。

您可以使用此模組，根據先前計算的轉換來修改輸入資料集。 如果您需要更新推斷管線中的轉換，則必須要有此模組。

例如，如果您使用「正規化 **資料** 」模組來使用 z 分數來將定型資料正規化，則您也會想要使用在評分階段期間針對定型所計算的 z 分數值。 在 Azure Machine Learning 中，您可以將正規化方法儲存為轉換，然後使用 [套用 **轉換** ] 將 z 分數套用至輸入資料，然後再進行評分。

## <a name="how-to-save-transformations"></a>如何儲存轉換

設計工具可讓您將資料轉換儲存為數據 **集** ，讓您可以在其他管線中使用它們。

1. 選取已成功執行的資料轉換模組。

1. 選取 [ **輸出 + 記錄** ] 索引標籤。

1. 尋找轉換輸出，然後選取 [ **註冊資料集** ]，將它儲存為模組選擇區中 [ **資料集** ] 類別下的模組。

## <a name="how-to-use-apply-transformation"></a>如何使用套用轉換  
  
1. 將 [套用 **轉換** ] 模組新增至您的管線。 您可以在模組選擇區的 **模型評分 & 評估** 區段中找到此模組。 
  
1. 在模組選擇區中的 **資料集** 下，尋找您想要使用的已儲存轉換。

1. 將已儲存轉換的輸出連接到 [套用 **轉換** ] 模組的左側輸入埠。

    資料集應該有完全相同的架構 (資料行數目、資料行名稱、資料類型) 為首次設計轉換的資料集。  
  
1. 將所需模組的資料集輸出連接至套用 **轉換** 模組的右側輸入埠。
  
1. 若要將轉換套用至新的資料集，請提交管線。  

> [!IMPORTANT]
> 若要確定定型管線中的更新轉換也適用于推斷管線，您必須在定型管線中的每次更新轉換時，依照下列步驟執行：
> 1. 在定型管線中，將「 [選取資料行」轉換](select-columns-transform.md) 的輸出註冊為資料集。
> ![註冊模組輸出的資料集](media/module/select-columns-transform-register-dataset.png)
> 1. 在推斷管線中，移除 **TD** 模組，並將它取代為上一個步驟中已註冊的資料集。
> ![取代 TD 模組](media/module/replace-tranformation-directory.png)

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 