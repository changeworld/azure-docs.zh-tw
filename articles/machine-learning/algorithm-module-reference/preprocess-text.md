---
title: 前置處理文字：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 設計工具中的前置處理文字模塊來清除和簡化文字。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/16/2020
ms.openlocfilehash: 366b30df677a5b74bc7d70e1aea60e05b4df0152
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659274"
---
# <a name="preprocess-text"></a>前置處理文字

本文描述 Azure Machine Learning 設計工具中的模組。

使用前置處理 **文字** 模組來清除和簡化文字。 它支援下列常見的文字處理作業：

* 移除停用字詞
* 使用正則運算式來搜尋和取代特定目標字串
* 詞形歸併還原，將多個相關的單字轉換成單一標準格式
* 案例正規化
* 移除某些類別的字元，例如數位、特殊字元和重複字元的序列，例如 "aaaa"
* 識別和移除電子郵件和 Url

前置處理 **文字** 模組目前僅支援英文。

## <a name="configure-text-preprocessing"></a>設定文字前置處理  

1.  在 Azure Machine Learning 中，將前置處理 **文字** 模組新增至您的管線。 您可以在 **文字分析** 下找到此模組。

1. 連接至少包含一個包含文字之資料行的資料集。

1. 從 [ **語言** ] 下拉式清單中選取語言。

1. **要清除的文字資料行**：選取您想要前置處理的資料行。

1. **移除停用字詞**：如果您想要將預先定義的停用字詞清單套用至文字資料行，請選取此選項。 

    停用字詞清單是與語言相關且可自訂的。

1. **詞形歸併還原**：如果您想要以標準格式表示單字，請選取此選項。 此選項適用于減少其他類似文字標記的唯一出現次數。

    詞形歸併還原進程與語言相依。

1. 偵測 **句子**：如果您想要模組在執行分析時插入句子界限標記，請選取此選項。

    此課程模組使用一連串三個管道字元 `|||` 來代表句子結束字元。

1. 使用正則運算式執行選擇性的尋找和取代作業。 正則運算式會先在所有其他內建選項之前處理。

    * **自訂正則運算式**：定義您要搜尋的文字。
    * **自訂取代字串**：定義單一取代值。

1. 將 **大小寫標準化為小寫**：如果您想要將 ASCII 大寫字元轉換成小寫格式，請選取此選項。

    如果字元未正規化，則大寫和小寫字母中的相同字組會被視為兩個不同的字組。

1. 您也可以從處理的輸出文字中移除下列類型的字元或字元序列：

    * **移除數位**：選取此選項可移除指定語言的所有數位字元。 識別碼與網域相依和語言相依。 如果數位字元是已知單字不可或缺的一部分，可能不會移除該數位。 深入瞭解 [技術](#technical-notes)提示。
    
    * **移除特殊字元**：使用此選項可移除任何非英數位元的特殊字元。
    
    * **移除重複的字元**：選取此選項以移除任何重複超過兩次之序列中的額外字元。 例如，"aaaaa" 之類的序列會縮減為 "aa"。
    
    * **移除電子郵件地址**：選取此選項以移除任何格式的順序 `<string>@<string>` 。  
    * **移除 url**：選取此選項以移除任何包含下列 URL 前置詞的序列： `http` 、 `https` 、 `ftp` 、 `www`
    
1. **展開動詞縮寫**：此選項只適用于使用動詞縮寫的語言;目前僅限英文。 

    例如，藉由選取此選項，您可以取代「*不會* 保留在那裡」*的片語。*

1. 將 **反斜線正規化為斜線**：選取此選項可將的所有實例對應至 `\\` `/` 。

1. 以 **特殊字元分割標記**：如果您想要在、等字元上中斷單字，請選取此選項 `&` `-` 。 這個選項也可以減少重複兩次以上的特殊字元。 

    例如，此字串會分成 `MS---WORD` 三個標記：、 `MS` `-` 和 `WORD` 。

1. 提交管線。

## <a name="technical-notes"></a>技術說明

Studio 中的前置處理 **文字** 模組 (傳統) 和設計工具使用不同的語言模型。 設計工具會使用來自 [spaCy](https://spacy.io/models/en)的多工 CNN 定型模型。 不同的模型會提供不同的 tokenizer 和詞性標記，這會導致不同的結果。

下列是一些範例：

| 設定 | 輸出結果 |
| --- | --- |
|已選取所有選項 </br> 解釋： </br> 針對 ' WC-3 3test 4test ' 中的 ' 3test ' 之類的情況，設計工具會移除整個字組 ' 3test '，因為在此內容中，聲控標籤會將此標記 ' 3test ' 指定為數字，而根據語音的部分，模組會將它移除。| :::image type="content" source="./media/module/preprocess-text-all-options-selected.png" alt-text="已選取所有選項" border="True"::: |
|只有 `Removing number` 選取的 </br> 解釋： </br> 在「3test」、「4-EC」之類的案例中，設計工具 tokenizer 的劑量不會分割這些案例，並將它們視為整個標記。 因此它不會移除這些字組中的數位。| :::image type="content" source="./media/module/preprocess-text-removing-numbers-selected.png" alt-text="只選取 [移除數位]" border="True"::: |

您也可以使用正則運算式來輸出自訂結果：

| 設定 | 輸出結果 |
| --- | --- |
|已選取所有選項 </br> 自訂正則運算式： `(\s+)*(-|\d+)(\s+)*` </br> 自訂取代字串： `\1 \2 \3`| :::image type="content" source="./media/module/preprocess-text-regular-expression-all-options-selected.png" alt-text="已選取所有選項和正則運算式" border="True"::: |
|只有 `Removing number` 選取的 </br> 自訂正則運算式： `(\s+)*(-|\d+)(\s+)*` </br> 自訂取代字串： `\1 \2 \3`| :::image type="content" source="./media/module/preprocess-text-regular-expression-removing-numbers-selected.png" alt-text="移除選取的數位和正則運算式" border="True"::: |


## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 