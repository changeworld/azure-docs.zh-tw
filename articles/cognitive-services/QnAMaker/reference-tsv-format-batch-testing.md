---
title: Batch 測試 TSV 格式-QnA Maker
titleSuffix: Azure Cognitive Services
description: 瞭解批次測試的 TSV 格式
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: dccf034b6497651db70b6cc09fff3f1b1d645a1b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "73501032"
---
# <a name="batch-testing-tsv-format"></a>Batch 測試 TSV 格式

批次測試可從[原始程式碼](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting)取得，或作為[可下載的可執行檔案壓縮](https://aka.ms/qna_btzip)。 執行批次測試的命令格式如下：

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|Param|預期的值|
|--|--|
|1|以[tsv 輸入欄位](#tsv-input-fields)格式化的 tsv 檔案名稱|
|2|端點的 URI，並從 QnA Maker 入口網站的 [發佈] 頁面使用您的主機。|
|3|端點-金鑰，在 QnA Maker 入口網站的 [發佈] 頁面上找到。|
|4|結果的批次測試所建立的 tsv 檔案名稱。|

使用下列資訊來瞭解及執行批次測試的 TSV 格式。 

## <a name="tsv-input-fields"></a>TSV 輸入欄位

|TSV 輸入檔欄位|注意|
|--|--|
|KBID|您在 [發佈] 頁面上找到您的知識庫識別碼。|
|問題|使用者會輸入的問題。|
|中繼資料標記|選用|
|Top 參數|選用| 
|預期答案的識別碼|選用|

![用於批次測試的 TSV 檔案輸入格式。](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>TSV 輸出欄位 

|TSV 輸出檔案參數|備忘錄|
|--|--|
|KBID|您在 [發佈] 頁面上找到您的知識庫識別碼。|
|問題|從輸入檔輸入的問題。|
|Answer|您的知識庫中的最佳解答。|
|解答識別碼|解答識別碼|
|分數|答案的預測分數。 |
|中繼資料標記|與傳回的答案相關聯|
|預期答案的識別碼|選擇性（僅在指定預期的解答識別碼時）|
|判斷標籤|選擇性，值可能是：正確或不正確（僅限指定了預期的答案）|
