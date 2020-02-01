---
title: 表單辨識器的新功能
titleSuffix: Azure Cognitive Services
description: 瞭解表單辨識器 API 的最新變更。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: pafarley
ms.openlocfilehash: 2109d25d3962063c711dcab491855d9ebf1cf694
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901875"
---
# <a name="whats-new-in-form-recognizer"></a>表單辨識器的新功能

本文將重點放在新版本的表單辨識器 API 中的主要變更。

> [!NOTE]
> 除非另有指定，否則此檔集中的快速入門和指南一律會使用 API 的最新版本。

## <a name="form-recognizer-20-preview"></a>表單辨識器2.0 （預覽）

### <a name="new-features"></a>新功能

* **自訂模型**
  * **使用標籤進行定型**您現在可以使用手動加上標籤的資料來定型自訂模型。 這會造就表現更佳的模型，並可產生可搭配複雜表單或含有值 (但不含索引鍵) 的表單使用的模型。
  * **非同步 API**您可以使用非同步 API 呼叫來定型和分析大型資料集和檔案。
  * **TIFF 檔案支援**您現在可以使用 TIFF 檔來定型和解壓縮資料。
  * **提取精確度改進**

* **預先建立的接收模型**
  * **秘訣數量**您現在可以將秘訣數量和其他手寫值解壓縮。
  * **明細專案解壓縮**您可以從收據中解壓縮行專案值。
  * **信賴值**您可以針對每個已解壓縮的值，查看模型的信賴度。
  * **提取精確度改進**

* **版面配置解壓縮**您現在可以使用版面配置 API，從您的表單中解壓縮文字資料和資料表資料。

### <a name="custom-model-api-changes"></a>自訂模型 API 變更

定型和使用自訂模型的所有 Api 都已重新命名，而某些同步方法現在是非同步。 以下是主要變更：

* 定型模型的程式現在是非同步。 您會透過 **/Custom/models** API 呼叫來起始訓練。 此呼叫會傳回作業識別碼，您可以將其傳遞至**自訂/模型/{modelID}** 以傳回定型結果。
* **/Custom/models/{modelID}/analyze** API 呼叫現在會起始索引鍵/值的提取。 此呼叫會傳回作業識別碼，您可以將其傳遞至**自訂/模型/{modelID}/analyzeResults/{resultID}** ，以返回提取結果。
* 定型作業的作業識別碼現在可以在 HTTP 回應的**位置**標頭中找到，而不是在作業 **-位置**標頭中。

### <a name="receipt-api-changes"></a>接收 API 變更

用於讀取銷售收據的 Api 已重新命名。

* **/Prebuilt/receipt/analyze** API 呼叫現在會起始接收資料提取。 此呼叫會傳回作業識別碼，您可以將其傳遞至 **/prebuilt/receipt/analyzeResults/{resultID}** ，以傳回提取結果。

### <a name="output-format-changes"></a>輸出格式變更

所有 API 呼叫的 JSON 回應都有新的格式。 某些索引鍵和值已加入、移除或重新命名。 如需目前 JSON 格式的範例，請參閱快速入門。

## <a name="next-steps"></a>後續步驟

請完成[快速入門](quickstarts/curl-train-extract.md)來開始使用[表單辨識器 API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)。