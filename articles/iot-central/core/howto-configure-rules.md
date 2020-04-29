---
title: 在 Azure IoT Central 中設定規則和動作 | Microsoft Docs
description: 本操作說明文章會向您示範如何在 Azure IoT Central 應用程式中設定遙測型規則和動作。
author: vavilla
ms.author: vavilla
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 509f9557a8128df12353ad02a7c7db02b7b42631
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80158439"
---
# <a name="configure-rules"></a>設定規則



*本文適用於操作員、建置員及系統管理員。*

IoT Central 中的規則是可自訂的回應工具，它會觸發來自已連線裝置的主動監視事件。 下列各節說明如何評估規則。

## <a name="select-target-devices"></a>選取目標裝置

使用 [目標裝置] 區段，即可選取要套用此規則的裝置種類。 篩選器可讓您進一步精簡應包含的裝置。 篩選器會使用裝置範本上的屬性來篩選一組裝置。 篩選器本身不會觸發動作。 在下列螢幕擷取畫面中，目標裝置的裝置範本類型為 [**冰箱**]。 此篩選準則會指出此規則應該只包含 [**製造的狀態**] 屬性等於 [**華盛頓**] 的**位冰箱**。

![條件](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>使用多個條件

條件就是要在其中觸發的規則。 目前，當您將多個條件新增至規則時，它們會以邏輯方式在一起。 換句話說，必須符合所有條件，規則才會評估為 true。  

在下列螢幕擷取畫面中，當溫度大於 70&deg; F，且濕度小於10時，條件會進行檢查。 當這兩個語句都是 true 時，規則會評估為 true 並觸發動作。

![條件](media/howto-configure-rules/conditions.png)

## <a name="use-aggregate-windowing"></a>使用匯總視窗

規則會將 [匯總時間] 視窗評估為 [輪轉] 視窗。 在下面的螢幕擷取畫面中，時間範圍為5分鐘。 規則會每隔五分鐘評估一次最後五分鐘的資料。 資料只會在其對應的視窗中評估一次。

![輪轉視窗](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>使用規則搭配 IoT Edge 模組

限制適用于套用至 IoT Edge 模組的規則。 來自不同模組之遙測的規則不會評估為有效規則。 請以下列範例為例。 此規則的第一個條件是來自模組 A 的溫度遙測。此規則的第二個條件是在模組 B 上的濕度遙測。由於這兩個條件來自不同的模組，這是一組不正確條件。 此規則無效，並會在嘗試儲存規則時擲回錯誤。

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何在 Azure IoT Central 應用程式中設定規則，您可以：

> [!div class="nextstepaction"]
> [即時分析您的資料](howto-create-analytics.md)
