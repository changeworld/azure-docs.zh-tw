---
title: 常見問題集 - 電腦視覺
titleSuffix: Azure Cognitive Services
description: 取得有關 Azure 認知服務中電腦視覺 API 常見問題集的解答。
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: e8ab205a43e5cb1e8e2b96dbd9600e1fceb29403
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "68564591"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>電腦視覺 API 常見問題集

> [!TIP]
> 如果您在此常見問題集中找不到問題的解答，請嘗試在 [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) 上詢問電腦視覺 API 社群，或連絡 [UserVoice 協助與支援](https://cognitive.uservoice.com/)。

---

**問題**：*我可以將電腦視覺 API 定型以使用自訂標籤嗎？ 例如，我想要在貓 breeds 的圖片中饋送到「訓練」 AI，然後在 AI 要求上獲得知名價值。*

**解答**：這項功能目前無法使用。 不過，我們的工程師正努力將這項功能放入電腦視覺中。

---

**問題**：*在沒有網際網路連線的情況下，可以在本機使用電腦視覺嗎？*

**解答**：我們目前不提供內部部署或本機解決方案。

---

**問題**：*電腦視覺可用來讀取車牌嗎？*

**解答**：視覺 API 提供採用 OCR 的良好文字偵測功能，但目前並未針對車牌進行最佳化。 我們持續不斷地嘗試改善我們的服務，並已將適用於自動車牌辨識的 OCR 新增至我們的功能要求清單。

---

**問題**：*手寫辨識支援哪些類型的撰寫表面？*

**解答**：此技術可搭配不同種類的介面運作，包括白板、白紙和黃色自黏便箋。

---

**問題**：*手寫辨識作業需花費多少時間？*

**解答**：所花費的時間量取決於文字的長度。 若為較長的文字，可能需要數秒鐘的時間。 因此，在「辨識手寫文字」作業完成後，您可能需要先行等待，才能使用「取得手寫文字作業結果」作業擷取結果。

---

**問題**：*手寫辨識技術如何處理一行中使用插入號插入的文字？*

**解答**：手寫辨識作業會以個別行方式傳回這類文字。

---

**問題**：*手寫辨識技術如何處理已刪去的字或行？*

**解答**：如果以多條線刪去字組來呈現它們無法辨識，則手寫辨識作業不會挑選這些字組。 不過，如果使用單條線刪去字組，則會將該刪除線視為雜訊，而手寫辨識作業仍會挑選這些字組。

---

**問題**：*手寫辨識技術支援哪些文字方向？*

**解答**：手寫辨識作業可能會挑選角度最多約 30 度到 40 度的文字。

---
