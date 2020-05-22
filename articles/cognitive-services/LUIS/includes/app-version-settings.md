---
title: 包含檔案
ms.topic: include
ms.custom: include file
ms.date: 5/04/2020
ms.openlocfilehash: 819dfa3127eb91a2f08687a76ea0586439b650a4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590976"
---
|層級|UI 設定|API 設定|資訊|
|--|--|--|--|
|App|將端點設為公用|`Public`|任何人都可以存取您的公用應用程式 (如果他們有預測金鑰)，並得知您的應用程式識別碼。 |
|版本|使用非決定性訓練|`UseAllTrainingData`|訓練會使用一小部分的負面取樣。 如果您想要使用所有資料，而不是少量的負面取樣，請設定為 `true`。 |
|版本|將變音符號標準化|`NormalizeDiacritics`|將變音符號標準化後，會將語句中含有變音符號的字元取代為一般字元。|
|版本|將標點符號標準化|`NormalizePunctuation`|標點符號標準化的意義在於，在您的模型進行定型之前，以及在您的端點查詢受到預測之前，會從語句中移除標點符號。|
|版本|將文字形式標準化|`NormalizeWordForm`|忽略字根以外的文字形式。|

了解標準化的[概念](../luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation)，以及如何使用[應用程式](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58aeface39e2bb03dcd5909e)和[版本](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) API 來更新這些設定，或在 LUIS 入口網站中使用 [應用程式設定] 頁面的 [管理] 區段。