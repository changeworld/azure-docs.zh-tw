---
title: 在 Azure IoT Central 中設定規則和動作 | Microsoft Docs
description: 本操作操作者文章介紹您作為構建者如何在 Azure IoT 中央應用程式中配置基於遙測的規則和操作。
author: vavilla
ms.author: vavilla
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 509f9557a8128df12353ad02a7c7db02b7b42631
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158439"
---
# <a name="configure-rules"></a>設定規則



*本文適用於操作員、建置員及系統管理員。*

IoT Central 中的規則充當可自訂的回應工具，用於觸發來自連接的設備的活動監視事件。 以下各節介紹如何評估規則。

## <a name="select-target-devices"></a>選擇目標設備

使用目標設備部分選擇將應用此規則的裝置類型。 篩選器允許您進一步優化應包含的設備。 篩選器使用設備範本上的屬性來篩選設備集。 篩選器本身不會觸發操作。 在下面的螢幕截圖中，被攻擊的設備是設備範本類型 **"冰箱**"。 篩檢程式指出，該規則應只包括**冰箱**，其中**製造國家**財產等於**華盛頓**。

![條件](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>使用多種條件

條件是規則引發的原因。 目前，當您向規則添加多個條件時，它們在邏輯上是和在一起。 換句話說，必須滿足所有條件才能將規則評估為 true。  

在下面的螢幕截圖中，當溫度大於 70&deg; F 且濕度小於 10 時，條件將檢查。 當這兩個語句都為 true 時，規則將評估為 true 並觸發操作。

![條件](media/howto-configure-rules/conditions.png)

## <a name="use-aggregate-windowing"></a>使用聚合視窗

規則將聚合時間視窗評估為翻滾視窗。 在下面的螢幕截圖中，時間視窗為五分鐘。 規則每五分鐘評估一次最後五分鐘的資料。 資料僅在它對應的視窗中計算一次。

![輪轉視窗](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>將規則與 IoT 邊緣模組一起使用

限制適用于應用於 IoT 邊緣模組的規則。 不同模組的遙測規則不會計算為有效規則。 以以下示例為例。 規則的第一個條件是模組 A 的溫度遙測。規則的第二個條件是模組 B 上的濕度遙測。由於這兩個條件來自不同的模組，因此這是一組不正確條件。 該規則無效，在嘗試保存規則時將引發錯誤。

## <a name="next-steps"></a>後續步驟

現在，您已經瞭解如何在 Azure IoT 中央應用程式中配置規則，您可以：

> [!div class="nextstepaction"]
> [動態分析資料](howto-create-analytics.md)
