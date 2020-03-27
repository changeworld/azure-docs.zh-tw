---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 07e7cc991f127bf4bb4f466c0108962786e45bce
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "68423554"
---
請注意 [效能]  索引標籤的左窗格上出現的 [機率閾值]  滑桿。這是預測必須具備才能視為正確的信賴等級 (用於計算精確度和召回率的目的)。 

當您以高機率閾值來解讀預測呼叫時，它們傾向於傳回具有高精確度的結果，但其代價是召回率&mdash;偵測到的分類正確，但仍有許多分類偵測不到。 低機率閾值的情況相反&mdash;偵測到大部分的實際分類，但該集合中有更多誤報。 記住這一點，您應該根據專案的特定需求設定機率閾值。 然後，當您在用戶端接收預測結果時，您應該使用與此處所用的相同機率閾值。