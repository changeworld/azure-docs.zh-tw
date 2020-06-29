---
title: 包含檔案
ms.topic: include
ms.custom: include file
ms.date: 5/17/2020
ms.openlocfilehash: 79a45825be66b44d869ef741917436de87a1f5d0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653181"
---
了解標準化的[概念](../luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation)以及如何使用[版本](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) API 來更新這些設定，或使用 LUIS 入口網站 [設定] 頁面的 [管理] 區段。


|UI 設定|API 設定|資訊|
|--|--|--|
|使用非決定性訓練|`UseAllTrainingData`|訓練會使用一小部分的負面取樣。 如果您想要使用所有資料，而不是少量的負面取樣，請設定為 `true`。 |
|將變音符號標準化|`NormalizeDiacritics`|將變音符號標準化後，會將語句中含有變音符號的字元取代為一般字元。 只有支援變音符號的[語言](../luis-reference-application-settings.md#diacritics-normalization)才可以使用此設定。|
|將標點符號標準化|`NormalizePunctuation`|標點符號標準化的意義在於，在您的模型進行定型之前，以及在您的端點查詢受到預測之前，會從語句中移除標點符號。|
|將文字形式標準化|`NormalizeWordForm`|忽略字根以外的文字形式。|
