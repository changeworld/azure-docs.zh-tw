---
title: 批次測試 TSV 格式-QnA Maker
titleSuffix: Azure Cognitive Services
description: 瞭解批次測試的 TSV 格式
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 10/24/2019
ms.openlocfilehash: 0ed948b4bf69fc672a59a7825279a12868d10521
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132120"
---
# <a name="batch-testing-tsv-format"></a>批次測試 TSV 格式

您可以從 [原始程式碼](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting) 取得批次測試，也可以 [壓縮為可下載的可執行檔](https://aka.ms/qna_btzip)。 用來執行批次測試的命令格式為：

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|Param|預期的值|
|--|--|
|1|使用[tsv 輸入欄位](#tsv-input-fields)格式化的 tsv 檔名稱|
|2|端點的 URI，並透過 QnA Maker 入口網站的 [發佈] 頁面中的主機。|
|3|在 QnA Maker 入口網站的 [發佈] 頁面上找到的端點金鑰。|
|4|由批次測試針對結果所建立的 tsv 檔案名稱。|

使用下列資訊來瞭解並執行批次測試的 TSV 格式。 

## <a name="tsv-input-fields"></a>TSV 輸入欄位

|TSV 輸入檔欄位|注意|
|--|--|
|KBID|您可在 [發佈] 頁面上找到您的 KB 識別碼。|
|問題|使用者會輸入的問題。|
|中繼資料標記|選用|
|Top 參數|選用| 
|預期答案的識別碼|選用|

![用於批次測試的 TSV 檔案輸入格式。](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>TSV 輸出欄位 

|TSV 輸出檔參數|注意|
|--|--|
|KBID|您可在 [發佈] 頁面上找到您的 KB 識別碼。|
|問題|輸入檔中輸入的問題。|
|回答|您知識庫的最佳解答。|
|解答識別碼|解答識別碼|
|Score|答案的預測分數。 |
|中繼資料標記|與傳回的答案相關聯|
|預期答案的識別碼|選擇性 (只有在指定預期的解答識別碼時) |
|判斷標籤|（選擇性）：只有在指定預期的解答時，值可以是正確或不正確 () |
