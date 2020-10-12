---
author: baanders
description: 包含 Azure 數位 Twins 查詢作業的檔案
ms.service: digital-twins
ms.topic: include
ms.date: 7/28/2020
ms.author: baanders
ms.openlocfilehash: 6dfddd9179569075abdbd94b6c1afa719db9ba1e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87905581"
---
## <a name="query-language-features"></a>查詢語言功能

Azure 數位 Twins 可針對對應項圖形提供廣泛的查詢功能。 查詢會使用類似 SQL 的語法來描述，類似于具有許多可比較功能的 [IoT 中樞查詢語言](../articles/iot-hub/iot-hub-devguide-query-language.md) 。

> [!NOTE]
> 所有 Azure 數位 Twins 查詢作業都會區分大小寫。

以下是 Azure 數位 Twins 查詢語言中的可用作業。

取得數位 twins 的方法 .。。
* 使用運算子) 的模型 (`IS_OF_MODEL`
* 包含 [標記屬性](../articles/digital-twins/how-to-use-tags.md) (屬性) 
* interfaces
* 關聯性
  - 關聯性的屬性

您可以使用下列作業進一步增強您的查詢：
*  (查詢) ，取得多個關聯性類型 `JOIN` 的 twins。 
  - 在預覽期間，最多允許五個層級 `JOIN` 。
* 只選取最上層查詢結果 (`Select TOP` 運算子) 
* 使用純量函數： `IS_BOOL` 、 `IS_DEFINED` 、 `IS_NULL` 、 `IS_NUMBER` 、 `IS_OBJECT` 、、、 `IS_PRIMITIVE` `IS_STRING` `STARTSWITH` 、 `ENDSWITH` 。
* 使用查詢比較運算子： `IN` / `NIN` 、 `=` 、 `!=` 、 `<` 、 `>` 、 `<=` 、 `>=` 。
* 使用任何組合 (`AND` 、 `OR` 、 `NOT` 運算子) 、純量 `IS_OF_MODEL` 函數和比較運算子。
* 使用接續：查詢物件是使用頁面大小來具現化， (最多 100) 。 您可以在後續的 API 呼叫中提供接續權杖，一次取出數位 twins 一頁。