---
title: 遷移總覽-LUIS
description: 瞭解遷移路徑上的內容
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/22/2020
ms.openlocfilehash: d6ecacf9aa1a7e650de74a412ed4f161ed0e0790
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91253797"
---
# <a name="migration-in-luis"></a>LUIS 中的遷移

有幾個專案是在遷移路徑上。 使用下表來瞭解受影響的內容，以及何時需要完成的遷移。

|區域|描述|遷移完成日期|
|--|--|--|
|[預測 Api](luis-migration-api-v3.md)|遷移至 V3 API。|TBD|
|[編寫 API](luis-migration-authoring-entities.md)|遷移至 V3 API。|TBD|
|[撰寫資源](luis-migration-authoring.md)|在 LUIS 入口網站中使用 LUIS 撰寫資源從無撰寫資源遷移至。|2020年11月2日 |
|[必要功能](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)|這項變更是在//Build 會議的2020月進行的變更，僅適用于應用程式使用受限功能的 v3 撰寫 Api。|2020年6月19日|
|[複合實體](migrate-from-composite-entity.md)|將複合實體升級至機器學習服務實體，以建立可獲得更完整預測的實體，以更好的 decomposability 來對實體進行偵測。|TBD|
