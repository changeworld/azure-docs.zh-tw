---
title: 關鍵字命名指南 - 語音服務
titleSuffix: Azure Cognitive Services
description: 創建有效的關鍵字對於確保您的設備能夠持續、準確地回應至關重要。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: 1d363f9f6f9cb5b1ea28e6ae15a9cef857304cab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370668"
---
# <a name="guidelines-for-creating-an-effective-keyword"></a>創建有效關鍵字的指南

創建有效的關鍵字對於確保您的設備能夠持續、準確地回應至關重要。 自訂關鍵字是區分您的設備並增強品牌的有效方法。 在本文中，您將學習創建有效關鍵字的一些指導原則。

## <a name="choose-an-effective-keyword"></a>選擇有效的關鍵字

選擇關鍵字時，請考慮以下準則：

> [!div class="checklist"]
> * 您的關鍵字應為英語單詞或短語。
> * 說出喚醒字所需的時間不應超過兩秒。
> * 由 4 到 7 個音節所構成的單字，是效果最佳的喚醒字。 例如，"嘿，電腦"是一個很好的關鍵字。 只有 "Hey" 則不好。
> * 關鍵字應遵循常見的英語發音規則。
> * 遵循一般英文發音規則的獨特或甚至虛構的單字可減少誤判的情況。 例如，"電腦"可能是一個很好的關鍵字。
> * 請勿選擇一般單字。 例如，"eat" 和 "go" 是一般對話中常說的單字。 它們可能會不小心觸發您的裝置。
> * 避免使用可能具有替代發音的關鍵字。 使用者將必須知道讓裝置回應的「正確」發音。 例如，"509" 可能會讀為 "five zero nine"、"five oh nine" 或 "five hundred and nine"。 "R.E.I." 可能會讀為 "r-e-i" 或 "ray"。 "Live" 可能會讀為"/līv/" 或 "/liv/"。
> * 請勿使用特殊字元、符號或數字。 例如，"Go+"和"20 + 貓"可能是有問題的關鍵字。 不過，"go sharp" 或 "twenty plus cats" 是可用的喚醒字。 您仍然可以使用品牌中的符號，並使用行銷管道和文件來強調適當的發音。

> [!NOTE]
> 如果您選擇商標字作為關鍵字，請確保您擁有該商標，或者您擁有商標擁有者的許可才能使用該字。 對於因您選擇的關鍵字而可能出現的任何法律問題，Microsoft 概不負責。

## <a name="next-steps"></a>後續步驟

瞭解如何[使用語音工作室創建自訂關鍵字](speech-devices-sdk-create-kws.md)。
