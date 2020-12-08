---
title: 包含檔案
description: 包含檔案
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 03/12/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 28f676892967abbd0da63d7a75ea3d164b87ce97
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017463"
---
身為 Azure IoT Central 應用程式的操作者，您可以：

* 在 [概觀]  頁面上，檢視裝置所傳送的遙測資料：

    :::image type="content" source="media/iot-central-monitor-thermostat/view-telemetry.png" alt-text="檢視裝置遙測":::

* 在 [關於]  頁面上，檢視裝置屬性：

    :::image type="content" source="media/iot-central-monitor-thermostat/about-properties.png" alt-text="檢視裝置屬性":::

## <a name="customize-the-device-template"></a>自訂裝置範本

身為解決方案開發人員，您可以自訂在控溫器裝置連線時自動建立的 IoT Central 裝置範本。

若要新增雲端屬性來儲存與裝置相關聯的客戶名稱：

1. 在 IoT Central 應用程式中，瀏覽至 **裝置範本** 頁面上的 **控溫器**。

1. 在 **控溫器** 裝置範本中，選取 [雲端屬性]。

1. 選取 [新增雲端屬性]。 在 **顯示名稱** 中輸入「客戶名稱」，然後選擇 [字串] 作為 **結構描述**。 接著，選取 [儲存]。

若要自訂如何在 IoT Central 應用程式中顯示 **Get Max-Min report**  命令，請選取裝置範本中的 [自訂]。 取代 **Get Max-Min report。** 改為使用 Get status report。 接著，選取 [儲存]。

**控溫器** 模型包含 **目標溫度** 可寫入屬性，裝置範本包含 **客戶名稱** 雲端屬性。 若要建立操作員可以編輯這些屬性的檢視：

1. 選取 [檢視]，再選取 [編輯裝置和雲端資料] 圖格。

1. 輸入「屬性」作為表單名稱。

1. 選取 [目標溫度] 和 [客戶名稱] 屬性。 然後選取 [新增區段]。

1. 儲存您的變更。

:::image type="content" source="media/iot-central-monitor-thermostat/properties-view.png" alt-text="更新屬性值的檢視":::

## <a name="publish-the-device-template"></a>發佈裝置範本

您必須先發佈裝置範本，操作員才能看到並使用您的自訂項目。

從 **控溫器** 裝置範本中，選取 [發佈]。 在 [將此裝置範本發佈至應用程式] 面板上，選取 [發佈]。

操作員現在可以使用 **屬性** 檢視來更新屬性值，並在裝置命令頁面上呼叫名為 **Get status report** 的命令：

* 在 [屬性]  頁面上更新可寫入的屬性值：

    :::image type="content" source="media/iot-central-monitor-thermostat/update-properties.png" alt-text="更新裝置屬性":::

* 呼叫 [命令]  頁面中的命令：

    :::image type="content" source="media/iot-central-monitor-thermostat/call-command.png" alt-text="呼叫命令":::

    :::image type="content" source="media/iot-central-monitor-thermostat/command-response.png" alt-text="檢視命令回應":::
