---
title: 表單辨識器的新功能
titleSuffix: Azure Cognitive Services
description: 瞭解表單識別器 API 的最新更改。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 5f8c66db491b93278fedf1378d3df86e7ce5fdbf
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531077"
---
# <a name="whats-new-in-form-recognizer"></a>表單辨識器的新功能

表單識別器服務會持續更新。 使用本文可隨時瞭解功能增強功能、修補程序和文檔更新的最新動向。

> [!NOTE]
> 除非指定,否則表單識別器的快速啟動和指南始終使用最新版本的 API。

## <a name="march-2020"></a>2020 年 3 月 

### <a name="new-features"></a>新功能

* **為標籤的值類型**現在,您可以使用表單識別器範例標籤工具指定要標記的值的類型。 目前支援以下值型態與變體:
  * `string`
    * 預設值`no-whitespaces`,`alphanumeric`
  * `number`
    * 預設`currency`
  * `date` 
    * 預設值`dmy` `mdy`, ,`ymd`
  * `time`
  * `integer`

  請參閱[示例標籤工具](./quickstarts/label-tool.md#specify-tag-value-types)指南,瞭解如何使用此功能。


* **表視覺化**範例標籤工具現在顯示文件中識別的表。 這樣,您就可以在標記和分析之前查看從文檔中識別和提取的表。 可以使用圖層選項打開和關閉此功能。

  這是如何識別和提取表的範例:

  > [!div class="mx-imgBorder"]
  > ![使用範例標記工具進行表視覺化](./media/whats-new/formre-table-viz.png)

    提取的表在 下`"pageResults"`的 JSON 輸出中可用。

  > [!IMPORTANT]
  > 不支持標記表。 如果表未自動識別和外推,則只能將它們標記為鍵/值對。 將表標記為鍵/值對時,將每個單元格標記為唯一值。

### <a name="extraction-enhancements"></a>擷取增強功能

此版本包括提取增強和精度改進,特別是在同一行文本中標記和提取多個鍵/值對的功能。 
 
### <a name="sample-labeling-tool-is-now-open-source"></a>範例標籤工具現在是開源的

表單識別器示例標籤工具現在可作為開源專案提供。 您可以將其整合到解決方案中,並針對客戶進行更改,以滿足您的需求。

有關表單識別器範例標籤工具的詳細資訊,請查看[GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)上提供的文檔。

### <a name="tls-12-enforcement"></a>TLS 1.2 強制執行

現在對於此服務的所有 HTTP 要求都會強制執行 TLS 1.2。 如需詳細資訊，請參閱 [Azure 認知服務安全性](../cognitive-services-security.md)。

## <a name="january-2020"></a>2020 年 1 月

此版本引入了表單識別器 2.0(預覽)。 在以下各節中,您將找到有關新功能、增強功能和更改的詳細資訊。 

### <a name="new-features"></a>新功能

* **自訂模型**
  * **帶標籤的列車**現在,您可以使用手動標記的數據訓練自定義模型。 這會造就表現更佳的模型，並可產生可搭配複雜表單或含有值 (但不含索引鍵) 的表單使用的模型。
  * **非同步 API**您可以使用非同步 API 調用來訓練和分析大型資料集和檔。
  * **TIFF 檔案支援**您現在可以使用 TIFF 文件進行訓練和提取數據。
  * **擷取精度提高**

* **預先建置的回條模型**
  * **小費金額**您現在可以提取小費金額和其他手寫值。
  * **列項目擷**取可以從收貨中提取行專案值。
  * **置標度值**您可以查看模型對每個提取值的置信度。
  * **擷取精度提高**

* **配置擷取**現在可以使用佈局 API 從表單中提取文本資料和表資料。

### <a name="custom-model-api-changes"></a>自訂模型 API 變更

所有用於培訓和使用自定義模型的 API 都已重新命名,並且某些同步方法現在是非同步的。 以下是主要變更:

* 訓練模型的過程現在是異步的。 您可以通過 **/自定義/模型**API 呼叫啟動培訓。 此調用返回操作 ID,您可以將其傳遞到**自定義/模型/{模型 ID}** 以返回培訓結果。
* 鍵/值提取現在由 **/自定義/模型/[模型 ID]/分析**API 調用啟動。 此調用返回操作 ID,您可以將其傳遞到**自定義/模型/模型 ID}/分析結果/[結果 ID]** 以返回提取結果。
* 現在,在 HTTP 回應**的位置**標頭中可以找到"訓練" 操作 ID,而不是 **「操作位置**」標頭。

### <a name="receipt-api-changes"></a>收貨 API 變更

用於讀取銷售收據的 API 已重新命名。

* 接收資料提取現在由 **/預建構/接收/分析**API 呼叫啟動。 此調用返回操作 ID,您可以將該操作 ID 傳遞到 **/預建構/接收/分析結果/{結果 ID}** 以返回提取結果。

### <a name="output-format-changes"></a>輸出格式變更

所有 API 調用的 JSON 回應都有新的格式。 已添加、刪除或重命名某些鍵和值。 有關當前 JSON 格式的範例,請參閱快速入門。

## <a name="next-steps"></a>後續步驟

請完成[快速入門](quickstarts/curl-train-extract.md)來開始使用[表單辨識器 API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)。