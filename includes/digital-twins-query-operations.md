---
author: baanders
description: 包含 Azure 數位 Twins 查詢作業的檔案
ms.service: digital-twins
ms.topic: include
ms.date: 7/28/2020
ms.author: baanders
ms.openlocfilehash: 70ff1847548c1328a709cf17c02bba3dd25ba213
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87486643"
---
## <a name="query-language-features"></a>查詢語言功能

Azure 數位 Twins 會針對對應項圖形提供廣泛的查詢功能。 查詢是以類似 SQL 的語法來描述，這種查詢語言與具有許多可比較功能的[IoT 中樞查詢語言](../articles/iot-hub/iot-hub-devguide-query-language.md)類似。

> [!NOTE]
> 所有 Azure 數位 Twins 查詢作業都區分大小寫。

以下是 Azure 數位 Twins 查詢存放區語言提供的作業。

以自己的方式取得數位 twins .。。
* model （使用 `IS_OF_MODEL` 運算子）
* 屬性（包括[標記屬性](../articles/digital-twins/how-to-use-tags.md)）
* interfaces
* 關聯性
  - 關聯性的屬性

您可以使用下列作業進一步增強您的查詢：
* 取得多個關聯性類型（ `JOIN` 查詢）的 twins。 
  - 允許的數目有所限制 `JOIN` （一層公開預覽）。
* 只選取最上方的查詢結果（ `Select TOP` 運算子）
* 使用純量函數： `IS_BOOL` 、 `IS_DEFINED` 、 `IS_NULL` 、 `IS_NUMBER` 、 `IS_OBJECT` `IS_PRIMITIVE` `IS_STRING` `STARTSWITH` `ENDSWITH` 、、、、。
* 使用查詢比較運算子： `IN` / `NIN` 、 `=` 、 `!=` 、 `<` 、 `>` 、 `<=` 、 `>=` 。
* 使用 `AND` `OR` 、純 `NOT` 量 `IS_OF_MODEL` 函數和比較運算子的任何組合（、、運算子）。
* 使用接續：查詢物件會具現化頁面大小（最多100）。 您可以在後續的 API 呼叫中提供接續 token，一次取出數位 twins 一頁。