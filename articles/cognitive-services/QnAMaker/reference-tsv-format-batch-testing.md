---
title: 批次測試 TSV 格式 - QnA 製造商
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "73501032"
---
# <a name="batch-testing-tsv-format"></a>批次處理測試 TSV 格式

批次處理測試可從[原始程式碼](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting)或可[下載的可執行壓縮 。](https://aka.ms/qna_btzip) 運行批次處理測試的命令的格式為：

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|Param|預期的值|
|--|--|
|1|使用[TSV 輸入欄位](#tsv-input-fields)格式化的 tsv 檔的名稱|
|2|用於終結點的 URI，使用 QnA Maker 門戶的發佈頁面中的"您的-HOST"。|
|3|ENDPOINT-KEY，位於 QnA 製造商門戶的發佈頁面上。|
|4|由批次處理測試創建的結果的 tsv 檔的名稱。|

使用以下資訊瞭解和實現用於批次處理測試的 TSV 格式。 

## <a name="tsv-input-fields"></a>TSV 輸入欄位

|TSV 輸入檔欄位|注意|
|--|--|
|KBID|在"發佈"頁上找到的 KB ID。|
|問題|使用者將輸入的問題。|
|中繼資料標記|選用|
|Top 參數|選用| 
|預期答案的識別碼|選用|

![用於批次處理測試的 TSV 檔的輸入格式。](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>TSV 輸出欄位 

|TSV 輸出檔案參數|注意|
|--|--|
|KBID|在"發佈"頁上找到的 KB ID。|
|問題|從輸入檔輸入的問題。|
|Answer|知識庫中的首要答案。|
|答案 ID|答案 ID|
|Score|答案的預測分數。 |
|中繼資料標記|與返回的答案關聯|
|預期答案的識別碼|可選（僅在給出預期答案 ID 時）|
|判斷標籤|可選，值可以是：正確或不正確（僅在給出預期答案時）|
