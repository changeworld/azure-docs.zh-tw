---
title: 匯入和匯出資料參考-QnA Maker
description: 您可以使用此匯入和匯出參考，取得知識庫備份、儲存和取代的最佳結果。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: d73fd6c7f49c10c8aca1060e91fc69b0e581738b
ms.sourcegitcommit: 17e9cb8d05edaac9addcd6e0f2c230f71573422c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2020
ms.locfileid: "97707498"
---
# <a name="import-and-export-data-reference"></a>匯入和匯出資料參考

請參閱此匯入和匯出參考，以取得知識庫備份、儲存和取代的最佳結果。

## <a name="import-and-export-knowledge-base"></a>匯入和匯出知識庫

從匯出的知識庫中， **TSV 和 XLS** 檔案只能透過從 QnA Maker 入口網站中的 [**設定**] 頁面匯入檔案來使用。 在知識庫建立期間，或從 [**設定**] 頁面上的 [ **+ 新增** 檔案] 或 [ **+ 新增 URL** ] 功能，都不能用來做為資料來源。 

當您透過這些 **TSV 和 XLS** 檔案匯入知識庫時，會將 QnA 組加入至編輯來源，而不是從匯出的知識庫中解壓縮 qna 的來源。 
