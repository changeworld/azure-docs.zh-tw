---
title: 在 Azure IoT Central 中設定規則和動作 | Microsoft Docs
description: 本操作說明文章將為建置者說明如何在 Azure IoT Central 應用程式中設定以遙測為基礎的規則和動作。
author: vavilla
ms.author: vavilla
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: c4d0639831d2f6f60a719637c5158fba5caf6f43
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659346"
---
# <a name="configure-rules"></a>設定規則

*本文適用於操作員、建置員及系統管理員。*

IoT Central 中的規則是可自訂的回應工具，會觸發來自已連線裝置的主動監視事件。 下列各節說明如何評估規則。

## <a name="select-target-devices"></a>選取目標裝置

使用目標裝置區段，即可選取要套用此規則的裝置種類。 篩選可讓您進一步精簡應包含的裝置。 篩選會使用裝置範本上的屬性來篩選一組裝置。 篩選本身不會觸發動作。 在下列螢幕擷取畫面中，目標裝置的裝置範本類型是**冰箱**。 此篩選會指出規則應該只包含**冰箱**，其中**製造州/省**屬性等於**華盛頓州**。

![條件](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>使用多重條件

條件就是規則要在其中觸發的項目。 目前，當您將多個條件新增至規則時，條件會以邏輯方式 AND 在一起。 換句話說，必須符合所有條件，規則才會評估為 True。  

在下列螢幕擷取畫面中，條件會檢查溫度大於 70&deg; F，且濕度小於 10。 當這兩個陳述都是 True 時，規則會評估為 True 並觸發動作。

![條件](media/howto-configure-rules/conditions.png)

### <a name="use-a-cloud-property-in-a-value-field"></a>在值欄位中使用雲端屬性

您可以在條件的 [值] 欄位中，從裝置範本參考雲端屬性。 雲端屬性和遙測值必須具有類似的類型。 例如，如果**溫度**為雙精度浮點數，則只有雙精度浮點數類型的雲端屬性會顯示為 [值] 下拉式清單中的選項。

如果您選擇事件類型遙測值，[值] 下拉式清單會包含 [任何] 選項。 [任何] 選項表示當您的應用程式收到該類型的事件時，就會引發規則，無論承載為何。

## <a name="use-aggregate-windowing"></a>使用彙總時間範圍

規則會將彙總時間範圍評估為輪轉視窗。 在以下螢幕擷取畫面中，時間範圍為 5 分鐘。 規則會每隔五分鐘評估一次最後五分鐘的資料。 資料只會在其對應的時間範圍中評估一次。

![輪轉視窗](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>搭配 IoT Edge 模組使用規則

限制適用於套用至 IoT Edge 模組的規則。 來自不同模組的遙測規則不會評估為有效規則。 以下列範例為例。 規則的第一個條件是來自模組 A 的溫度遙測。規則的第二個條件是模組 B 上的濕度遙測。由於這兩個條件來自不同的模組，這是一組無效的條件。 規則無效，並且會在嘗試儲存規則時擲回錯誤。

## <a name="next-steps"></a>後續步驟

既然您已了解如何在 Azure IoT Central 應用程式中設定規則，您可以了解如何使用 Power Automate 或 Azure Logic Apps 來[設定進階規則](howto-configure-rules-advanced.md)。
