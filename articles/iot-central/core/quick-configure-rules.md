---
title: 快速入門 - 在 Azure IoT Central 中設定規則和動作
description: 本快速入門將為建置者說明如何在 Azure IoT Central 應用程式中設定以遙測為基礎的規則和動作。
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 99846a5f2435398d13c436460a2756b1b021a1be
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94990192"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>快速入門：在 Azure IoT Central 中為您的裝置設定規則和動作

*本文適用於操作員、建置員及系統管理員。*

在本快速入門中，您會建立在裝置感應器所回報的濕度超過 55% 時傳送電子郵件的規則。

## <a name="prerequisites"></a>Prerequisites

開始之前，請先完成 [建立 Azure IoT Central 應用程式](./quick-deploy-iot-central.md)和 [將模擬裝置新增至 IoT Central 應用程式](./quick-create-simulated-device.md)這兩個先前的快速入門，以建立要使用的 **感應器控制器** 裝置範本。

## <a name="create-a-telemetry-based-rule"></a>建立以遙測為基礎的規則

1. 若要將以遙測為基礎的新規則新增至應用程式，請在左窗格中選取 [規則]  。

1. 若要建立新的規則，請選取 [+ 新增]。

1. 輸入 **環境濕度** 作為規則名稱。

1. 在 [目標裝置] 區段中，選取 [感應器控制器] 作為裝置範本。 此選項會依據裝置範本類型篩選要套用規則的裝置。 您可以選取 [+ 篩選]  以新增更多篩選準則。

1. 在 [條件]  區段中，您可以定義規則所觸發的動作。 使用下列資訊來定義以溫度遙測為基礎的條件：

    | 欄位        | 值            |
    | ------------ | ---------------- |
    | 測量  | SensorHumid      |
    | 運算子     | 大於  |
    | 值        | 55               |

    若要新增更多條件，請選取 [+ 條件]  。

    :::image type="content" source="media/quick-configure-rules/condition.png" alt-text="顯示規則條件的螢幕擷取畫面":::

1. 若要新增要在規則觸發時所執行的電子郵件動作，請選取 [+ 電子郵件]  。

1. 請使用下表中的資訊來定義您的動作，然後選取 [完成]  ：

    | 設定   | 值                                             |
    | --------- | ------------------------------------------------- |
    | 顯示名稱 | 操作員電子郵件動作                          |
    | 至        | 您的電子郵件地址                                |
    | 注意     | 環境濕已超出閾值。 |

    > [!NOTE]
    > 若要接收電子郵件通知，電子郵件地址必須是[應用程式中的使用者識別碼](howto-administer.md)，且該使用者必須已至少登入應用程式一次。

    :::image type="content" source="media/quick-configure-rules/action.png" alt-text="顯示新增至規則的電子郵件動作螢幕擷取畫面":::

1. 選取 [儲存]  。 您的規則會列在 [規則]  頁面上。

## <a name="test-the-rule"></a>測試規則

規則在您儲存之後，不久即會生效。 符合規則中定義的條件時，應用程式即會傳送電子郵件至您在動作中指定的電子郵件地址。

> [!NOTE]
> 測試完成後，請關閉規則以停止在收件匣中接收警示。

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何：

* 建立以遙測為基礎的規則
* 新增動作

若要深入了解如何監視連線至應用程式的裝置，請繼續進行下列快速入門：

> [!div class="nextstepaction"]
> [使用 Azure IoT Central 監視您的裝置](quick-monitor-devices.md)。
