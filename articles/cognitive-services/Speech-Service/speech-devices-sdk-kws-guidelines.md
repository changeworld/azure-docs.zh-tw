---
title: 關鍵字命名方針-語音服務
titleSuffix: Azure Cognitive Services
description: 建立有效的關鍵字對於確保您的裝置會一致且正確地回應非常重要。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: c00d27e5e7b7a8d614ce94ea4024b6093669757c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399836"
---
# <a name="guidelines-for-creating-an-effective-keyword"></a>建立有效關鍵字的指導方針

建立有效的關鍵字對於確保您的裝置會一致且正確地回應非常重要。 自訂您的關鍵字是區分您的裝置並強化商標的有效方式。 在本文中，您將瞭解一些建立有效關鍵字的指導原則。

## <a name="choose-an-effective-keyword"></a>選擇有效的關鍵字

當您選擇關鍵字時，請考慮下列指導方針：

> [!div class="checklist"]
> * 您的關鍵字應該是英文單字或片語。
> * 說出喚醒字所需的時間不應超過兩秒。
> * 由 4 到 7 個音節所構成的單字，是效果最佳的喚醒字。 例如，「嗨，Computer」是不錯的關鍵字。 只有 "Hey" 則不好。
> * 關鍵字應遵循常用的英文發音規則。
> * 遵循一般英文發音規則的獨特或甚至虛構的單字可減少誤判的情況。 例如，"computerama" 可能是不錯的關鍵字。
> * 請勿選擇一般單字。 例如，"eat" 和 "go" 是一般對話中常說的單字。 它們可能會不小心觸發您的裝置。
> * 請避免使用可能有其他發音的關鍵字。 使用者將必須知道讓裝置回應的「正確」發音。 例如，"509" 可能會讀為 "five zero nine"、"five oh nine" 或 "five hundred and nine"。 "R.E.I." 可能會讀為 "r-e-i" 或 "ray"。 "Live" 可能會讀為"/līv/" 或 "/liv/"。
> * 請勿使用特殊字元、符號或數字。 例如，「Go #」和「20 + 貓」可能是有問題的關鍵字。 不過，"go sharp" 或 "twenty plus cats" 是可用的喚醒字。 您仍然可以使用品牌中的符號，並使用行銷管道和文件來強調適當的發音。

> [!NOTE]
> 如果您選擇商標單字做為關鍵字，請確定您擁有該商標，或您具有商標擁有者的許可權，才能使用此字組。 Microsoft 對於您選擇的關鍵字可能會發生的任何法律問題概不負任何責任。

## <a name="next-steps"></a>後續步驟

瞭解如何[使用語音 Studio 建立自訂關鍵字](speech-devices-sdk-create-kws.md)。
