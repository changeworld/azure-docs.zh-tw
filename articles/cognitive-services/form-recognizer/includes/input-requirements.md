---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 11783f8fac147e6fb2cf371ee1a588cf318a5e9c
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "75379417"
---
表單辨識器可處理符合下列需求的輸入文件：

* 格式必須為 JPG、PNG、PDF (文字或掃描) 或 TIFF。 內嵌文字的 PDF 更好，因為在擷取和定位字元時完全不可能發生錯誤。
* 如果您的 PDF 有密碼鎖定，則必須先移除鎖定才能提交。
* PDF 和 TIFF 檔必須有 200 頁或更少頁，且訓練資料集的總大小必須為 500 頁或更少頁。
* 針對影像，維度必須介於 600 x 100 像素和 4200 x 4200 像素之間。
* 如果掃描自書面文件，表單應該採用高品質的掃描方式。
* 文字必須使用拉丁字母 (英文字元)。
* 對於不受監督的學習 (不使用標記資料)，資料必須包含索引鍵和值。
* 對於不受監督的學習 (不使用標記資料)，索引鍵必須出現在值的上方或左邊；不能出現在下方或右邊。

表單辨識器目前不支援這些類型的輸入資料：

* 複雜資料表 (巢狀資料表、合併的標題或資料格等等)。
* 核取方塊或選項按鈕。
