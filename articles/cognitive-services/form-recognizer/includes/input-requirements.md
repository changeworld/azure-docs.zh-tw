---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 5de121a1a905a58f8b5eaf8e60c1f8da71ee8cd6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91276932"
---
表單辨識器可處理符合下列需求的輸入文件：

* 格式必須為 JPG、PNG、PDF (文字或掃描) 或 TIFF。 內嵌文字的 PDF 更好，因為在擷取和定位字元時完全不可能發生錯誤。
* 檔案大小必須小於 20 MB。
* 影像維度必須介於 50 x 50 像素和 10000 x 10000 像素之間。
* PDF 維度最多必須是 17 x 17 英寸，對應 Legal 或 A3 紙張大小 (或更小尺寸)。
* 針對 PDF 和 TIFF，只有前 200 個頁面會進行處理 (若使用免費層的訂用帳戶，只會處理前兩個頁面)。
* 訓練資料集的大小總計必須是 500 個頁面或更少。
* 如果您的 PDF 有密碼鎖定，則必須先移除鎖定才能提交。
* 如果掃描自書面文件，表單應該採用高品質的掃描方式。
* 文字必須使用拉丁字母 (英文字元)。
* 對於不受監督的學習 (不使用標記資料)，資料必須包含索引鍵和值。
* 對於不受監督的學習 (不使用標記資料)，索引鍵必須出現在值的上方或左邊；不能出現在下方或右邊。

表單辨識器目前不支援這些類型的輸入資料：

* 複雜資料表 (巢狀資料表、合併的標題或資料格等等)。
* 核取方塊或選項按鈕。
