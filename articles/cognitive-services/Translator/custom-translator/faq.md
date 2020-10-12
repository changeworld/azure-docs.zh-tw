---
title: 常見問題-自訂翻譯
titleSuffix: Azure Cognitive Services
description: 本文包含有關 Azure 認知服務自訂翻譯常見問題的解答。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: reference
ms.openlocfilehash: 06caafe75682a2375a8023787f9905ca1e8117c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88507224"
---
# <a name="custom-translator-frequently-asked-questions"></a>自訂翻譯常見問題

本文包含[自訂翻譯工具](https://portal.customtranslator.azure.ai)相關常見問題的解答。

## <a name="what-are-the-current-restrictions-in-custom-translator"></a>自訂翻譯工具目前有哪些限制？

目前在檔案大小、模型定型和部署模型方面有一些限制。 在設定您的訓練以建置自訂翻譯工具中的模型時，請留意這些限制。

- 提交的檔案必須小於 100 MB。
- 不支援單一語言資料。

## <a name="when-should-i-request-deployment-for-a-translation-system-that-has-been-trained"></a>應於何時要求部署已定型的翻譯系統？

可能需經過數次定型後，才能為您的專案建立最佳的翻譯系統。 如果 BLEU 分數和/或測試結果不理想，您可以嘗試使用更多定型資料或更精確篩選的資料。 您應精確並謹慎地設計調整集和測試集，使其充分代表您要翻譯的內容所使用的術語和樣式。 您在撰寫定型資料時可較為寬鬆，並嘗試使用不同的選項。 當您對系統測試結果中的翻譯感到滿意，而不需新增更多定型資料來改善定型的系統，且想要透過 API 來存取已定型的模型時，即可要求進行系統部署。

## <a name="how-many-trained-systems-can-be-deployed-in-a-project"></a>在一個專案中可部署多少個定型的系統？

每個專案只能部署一個定型的系統。 可能需要經過數次定型才能為您的專案建立適當的翻譯系統，建議您要求部署讓您獲得最佳結果的定型。 您可以利用 BLEU 分數 (愈高愈好)，並在判定翻譯品質適合進行部署之前先諮詢檢閱者，來決定定型的品質。

## <a name="when-can-i-expect-my-trainings-to-be-deployed"></a>定型應會在何時完成部署？

部署通常可在一小時內完成。

## <a name="how-do-you-access-a-deployed-system"></a>如何存取已部署的系統？

已部署的系統可透過 Microsoft Translator Text API V3 (藉由指定 CategoryID) 來存取。 如需翻譯工具文字 API 的詳細資訊，請參閱 [API 參考](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)網頁。

## <a name="how-do-i-skip-alignment-and-sentence-breaking-if-my-data-is-already-sentence-aligned"></a>如果我的資料已將句子對齊，如何略過對齊和斷句？

對於 TMX 檔案和具有 `.align` 副檔名的文字檔，自訂翻譯工具會略過句子對齊和斷句。 `.align` 檔案可讓使用者選擇對已適當對齊、且不需要進一步處理的檔案，略過自訂翻譯工具的斷句和句子對齊程序。 我們建議，`.align` 副檔名僅應使用於已適當對齊的檔案。

如果已擷取的句數不符合具有相同基底名稱的兩個檔案，自訂翻譯工具仍會對 `.align` 檔案執行句子對齊工具。

## <a name="i-tried-uploading-my-tmx-but-it-says-document-processing-failed"></a>我試過上傳我的 TMX，但它顯示「檔處理失敗」


請確定 TMX 符合 <https://www.gala-global.org/tmx-14b> 上的 TMX 1.4b 規格。
