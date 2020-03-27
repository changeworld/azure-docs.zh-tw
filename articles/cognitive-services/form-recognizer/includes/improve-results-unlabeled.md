---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: f0761847c3677b324ef16c5987eb9a1561dbcbe0
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "75379281"
---
檢查 `"confidence"` 節點下每個索引鍵/值結果的 `"pageResults"` 值。 您也應該查看 `"readResults"` 節點中的信賴分數，其對應至文字讀取作業。 讀取結果的信賴度並不會影響索引鍵/值擷取結果的信賴度，因此您應該同時檢查這兩者。
* 如果讀取作業的信賴分數很低，請嘗試改善輸入文件的品質 (請參閱[輸入需求](../overview.md#input-requirements))。
* 如果索引鍵/值擷取作業的信賴分數很低，請確定所分析的文件與訓練集中使用的文件類型相同。 如果訓練集中的文件有外觀變化，請考慮將其劃分成不同的資料夾，並針對每個變化訓練一個模型。

您的目標信賴分數將取決於您的使用案例，但通常最好是以80% 或以上的分數為目標。 對於更敏感的案例，例如讀取醫療記錄或對帳單，建議使用 100% 的分數。