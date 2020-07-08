---
title: 查詢語言
titleSuffix: Azure Digital Twins
description: 瞭解 Azure 數位 Twins 查詢存放區語言的基本概念。
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 3196004015046b4d3d2789745c80d323bacdced9
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85985236"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>關於 Azure 數位 Twins 的查詢語言

回想一下，Azure 數位 Twins 的中心是對應項[**圖形**](concepts-twins-graph.md)，由**數位 Twins**和**關聯**性所構成。 您可以查詢此圖形，以取得其所包含之數位 twins 和關聯性的相關資訊。 這些查詢是以類似 SQL 的自訂查詢語言撰寫，稱為**Azure 數位 Twins 查詢存放區語言**。

若要從用戶端應用程式將查詢提交至服務，您將使用 Azure 數位 Twins**查詢 API**。 這可讓開發人員撰寫查詢，並套用篩選來尋找對應項圖形中的數位 twins 集合，以及其他有關 Azure 數位 Twins 案例的資訊。

## <a name="query-language-features"></a>查詢語言功能

Azure 數位 Twins 會針對對應項圖形提供廣泛的查詢功能。 查詢是以類似 SQL 的語法來描述，這種查詢語言與具有許多可比較功能的[IoT 中樞查詢語言](../iot-hub/iot-hub-devguide-query-language.md)類似。

以下是 Azure 數位 Twins 查詢存放區語言提供的作業：
* 以數位 twins 的屬性取得 twins。
* 取得 twins by 數位 twins 的介面。
* 依關聯性屬性取得 twins。
* 取得多個關聯性類型（ `JOIN` 查詢）的 twins。 允許的數目有所限制 `JOIN` （一層公開預覽）。
* 使用自訂函式 `IS_OF_MODEL(twinCollection, twinTypeName)` ，允許根據對應項的[模型](concepts-models.md)進行篩選。 它支援繼承。
* 使用上述的任何組合（ `AND` 、 `OR` 、 `NOT` 運算子）。
* 使用純量函數： `IS_BOOL` 、 `IS_DEFINED` 、 `IS_NULL` 、 `IS_NUMBER` 、 `IS_OBJECT` `IS_PRIMITIVE` `IS_STRING` `STARTS_WITH` `ENDS_WITH` 、、、、。
* 使用查詢比較運算子： `AND` / `OR` / `NOT` 、 `IN` / `NOT IN` 、 `STARTSWITH` / `ENDSWITH` 、 `=` 、 `!=` 、、、 `<` `>` `<=` 、 `>=` 。
* 使用接續：查詢物件會具現化頁面大小（最多100）。 您可以藉由重複呼叫方法，一次取出數位 twins 一頁 `nextAsTwin` 。

## <a name="next-steps"></a>後續步驟

瞭解如何撰寫查詢，並參閱 how [to：查詢](how-to-query-graph.md)對應項圖形中的用戶端程式代碼範例。