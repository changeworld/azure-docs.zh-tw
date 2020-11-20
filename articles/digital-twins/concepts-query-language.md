---
title: 查詢語言
titleSuffix: Azure Digital Twins
description: 瞭解 Azure 數位 Twins 查詢語言的基本概念。
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperfq2
ms.openlocfilehash: 89e95b0c56ce5603096fb1ac9af74cb0ad53ee6b
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955220"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>關於 Azure 數位 Twins 的查詢語言

回想一下，Azure 數位 Twins 的中心是對應于數位 Twins 和關聯性的對應項 [圖形](concepts-twins-graph.md)。 

您可以查詢此圖表，以取得其所包含的數位 twins 和關聯性的相關資訊。 這些查詢是以類似 SQL 的自訂查詢語言撰寫，稱為「 **Azure 數位 Twins 查詢語言**」。 這類似于具有許多可比較功能的 [IoT 中樞查詢語言](../iot-hub/iot-hub-devguide-query-language.md) 。

本文描述查詢語言和其功能的基本概念。 如需查詢語法以及如何執行查詢要求的詳細範例，請參閱 [*如何：查詢對應項圖形*](how-to-query-graph.md)。

## <a name="about-the-queries"></a>關於查詢

您可以使用 Azure 數位 Twins 查詢語言，根據其 .。。
* 包含 [標記屬性](how-to-use-tags.md) (屬性) 
* 模型
* 關聯性
  - 關聯性的屬性

若要從用戶端應用程式將查詢提交至服務，您將使用 Azure 數位 Twins [**查詢 API**](/rest/api/digital-twins/dataplane/query)。 使用 API 的其中一個方法是透過 Azure 數位 Twins 的其中一個 [sdk](how-to-use-apis-sdks.md#overview-data-plane-apis) 。

## <a name="reference-expressions-and-conditions"></a>參考：運算式和條件

本節說明可用來撰寫 Azure 數位 Twins 查詢的運算子和函數。 如需說明如何使用這些功能的範例查詢，請參閱 [*如何：查詢*](how-to-query-graph.md)對應項圖形。

> [!NOTE]
> 所有 Azure 數位 Twins 查詢作業都會區分大小寫，因此請小心使用模型中所定義的確切名稱。 如果屬性名稱的拼寫錯誤或大小寫不正確，則結果集是空的，而且不會傳回任何錯誤。

### <a name="operators"></a>運算子

支援下列運算子：

| 系列 | 運算子 |
| --- | --- |
| 邏輯 |`AND`, `OR`, `NOT` |
| 比較 | `=`, `!=`, `<`, `>`, `<=`, `>=` |
| 包含 | `IN`, `NIN` |

### <a name="functions"></a>函式

支援下列類型檢查和轉換函數：

| 函式 | 描述 |
| -------- | ----------- |
| `IS_DEFINED` | 傳回布林值，表示屬性是否已經指派值。 只有當值為基本型別時，才支援此功能。 基本類型包括字串、布林值、數值或 `null` 。 `DateTime`、物件類型和陣列皆不受支援。 |
| `IS_OF_MODEL` | 傳回布林值，指出指定的對應項是否符合指定的模型類型 |
| `IS_BOOL` | 傳回布林值，表示指定之運算式的類型為布林值。 |
| `IS_NUMBER` | 傳回布林值，表示指定之運算式的類型為數字。 |
| `IS_STRING` | 傳回布林值，表示指定之運算式的類型為字串。 |
| `IS_NULL` | 傳回布林值，表示指定之運算式的類型為 null。 |
| `IS_PRIMITIVE` | 傳回布林值，可指出所指定之運算式的類型是否為基本類型 (字串、布林值、數值或 `null`)。 |
| `IS_OBJECT` | 傳回布林值，表示指定之運算式的類型為 JSON 物件。 |

以下是支援的字串函數：

| 函式 | 描述 |
| -------- | ----------- |
| `STARTSWITH(x, y)` | 傳回布林值，表示第一個字串運算式是否以第二個字串運算式開頭。 |
| `ENDSWITH(x, y)` | 傳回布林值，表示第一個字串運算式是否以第二個字串運算式結尾。 |

## <a name="query-limitations"></a>查詢限制

本節說明查詢語言的限制。

* 時間：在查詢中反映您實例的變更之前，可能會有最多10秒的延遲。 例如，如果您完成了使用 DigitalTwins API 建立或刪除 twins 等作業，則結果可能不會立即反映在查詢 API 要求中。 等候短暫的時間應該足以解決問題。
* 語句內不支援子查詢 `FROM` 。
* `OUTER JOIN` 不支援語義，這表示如果關聯性的等級為零，則會從輸出結果集中刪除整個「資料列」。
* 圖表遍歷深度限制為 `JOIN` 每個查詢五個層級。
* 作業的來源受到 `JOIN` 限制：查詢必須宣告開始查詢的 twins。

## <a name="next-steps"></a>後續步驟

瞭解如何撰寫查詢，並查看 [*如何：查詢*](how-to-query-graph.md)對應項圖形中的用戶端程式代碼範例。