---
title: 遷移總覽-LUIS
description: 瞭解什麼是遷移路徑
ms.topic: how-to
ms.date: 05/22/2020
ms.openlocfilehash: a27945b5ac3dc2625c9520a8dd413b774b5d7adf
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837472"
---
# <a name="migration-in-luis"></a>LUIS 中的遷移

有數個專案是在遷移路徑上。 使用下表來瞭解受影響的內容，以及何時需要完整的遷移。

|區域|描述|遷移完成日期|
|--|--|--|
|[預測 Api](luis-migration-api-v3.md)|遷移至 V3 API。|TBD|
|[編寫 API](luis-migration-authoring-entities.md)|遷移至 V3 API。|TBD|
|[撰寫資源](luis-migration-authoring.md)|在 LUIS 入口網站中使用 LUIS 撰寫資源，從無撰寫資源遷移至。|2020年8月30日 |
|[必要功能](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)|這項變更是在 build 會議的5月2020進行，僅適用于使用受條件約束功能的 v3 撰寫 Api。|2020年6月19日|
|[複合實體](migrate-from-composite-entity.md)|將複合實體升級為機器學習實體，以建立可接收更完整預測的實體，並提供更好的 decomposability 來對實體進行偵測。|TBD|
