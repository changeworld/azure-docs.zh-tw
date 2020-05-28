---
title: 什麼是平行處理的文件？ - 自訂翻譯工具
titleSuffix: Azure Cognitive Services
description: 平行文件是配對文件，其中的一份文件是另一份文件的翻譯。 該配對中的一份文件會包含原始語言的句子，而另一分文件會包含已翻譯成目標語言的這些句子。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: d397e481fcd11e534e4bb5cf98f8c402435e890e
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "83997072"
---
# <a name="what-are-parallel-documents"></a>什麼是平行處理的文件？

平行文件是配對文件，其中的一份文件是另一份文件的翻譯。 該配對中的一份文件會包含原始語言的句子，而另一分文件會包含已翻譯成目標語言的這些句子。
哪一種語言標示為「來源」，而哪種語言標示為「目標」，平行處理檔也可以用來以任一方向訓練轉譯系統。

## <a name="requirements"></a>需求

您至少需要10000個唯一對齊的平行句子來訓練系統。 這項限制是安全的網路，可確保您的平行句子包含足夠的唯一詞彙，以成功訓練翻譯模型。 最佳做法是持續新增更多平行內容並重新定型，以改善翻譯系統的品質。 請參閱[句子對齊方式](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment)。

Microsoft 要求上傳至自訂翻譯的檔不違反協力廠商的著作權或智慧財產權。 如需詳細資訊，請參閱[使用規定](https://azure.microsoft.com/support/legal/cognitive-services-terms/)。
使用入口網站上傳文件，並不會改變文件本身的智慧財產權歸屬。

## <a name="use-of-parallel-documents"></a>使用平行處理的文件

系統會使用平行處理的文件來：

1.  了解在兩個語言之間通常會以何種方式對應單字、片語和句子。

2.  了解如何根據前後的片語處理適當的內容。 一個字在另一種語言中不一定都會翻譯為同一個字。

最佳做法是，確定文件的來源和目標語言版本之間有 1 對 1 的句子對應性。

如果您的專案屬於特定領域 (類別)，您的文件即應與在該類別內的詞彙一致。 最終的翻譯系統品質，取決於文件集中的句子數目和句子的品質。 您的文件中有越多範例提供某個字在您類別中的特定用法，系統在翻譯期間的作業成效就越理想。

上傳的文件可供您在個別的工作區中私用，且您可以依需求將其用於數量不限的專案或定型中。 從您的文件中擷取的句子會以 Unicode 純文字檔的格式個別儲存在存放庫中，且可由您刪除。 請勿使用自訂翻譯工具作為文件存放庫，您已上傳的文件，將無法以其先前上傳的格式下載。



## <a name="next-steps"></a>後續步驟

- 了解如何在自訂翻譯工具中使用[字典](what-is-dictionary.md)。
