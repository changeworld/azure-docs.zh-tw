---
title: 快速入門 - 將模擬裝置新增至 Azure IoT Central
description: 本快速入門說明如何建立裝置範本，並將模擬裝置新增至 IoT Central 應用程式。
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: f8d366554634444db16eb3292f100540f3808e8a
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992836"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application"></a>快速入門：將模擬裝置新增至 IoT Central 應用程式

*本文適用於操作員、建置員及系統管理員。*

裝置範本會為連線至 IoT Central 應用程式的裝置定義功能。 這些功能包括裝置傳送的遙測資料、裝置屬性，以及裝置所回應的命令。 建置者或操作員可從裝置範本將實際和模擬的裝置新增至應用程式。 模擬裝置可讓您在連線實際裝置之前先測試應用程式的行為。

在本快速入門中，您會新增 ESP32-Azure IoT 套件開發面板的裝置範本，並建立模擬裝置。 若要完成本快速入門，您不需要實際的裝置，而是使用模擬裝置。 ESP32 裝置：

* 傳送遙測資料，例如溫度。
* 報告裝置特定的屬性，例如裝置重新開機後的最高溫度。
* 回應命令，例如重新開機。
* 報告一般裝置屬性，例如裝置的韌體版本和序號。

## <a name="prerequisites"></a>Prerequisites

完成[建立 Azure IoT Central 應用程式](./quick-deploy-iot-central.md)快速入門，以使用 [自訂應用程式] > [自訂應用程式]  範本建立 IoT Central 應用程式。

## <a name="create-a-device-template"></a>建立裝置範本

身為建置者，您可以在 IoT Central 應用程式中建立和編輯裝置範本。 發佈裝置範本之後，您可以產生模擬裝置，或從裝置範本連線實際的裝置。 模擬裝置可讓您在連線實際裝置之前先測試您應用程式的行為。

若要將新的裝置範本新增至應用程式，請選取左側窗格上的 [裝置範本]  索引標籤。

:::image type="content" source="media/quick-create-simulated-device/device-definitions.png" alt-text="顯示裝置範本空白清單的螢幕擷取畫面":::

包含裝置模型的裝置範本會定義：

* 裝置所傳送的遙測。
* 裝置屬性。
* 裝置會回應的命令。

### <a name="add-a-device-template"></a>新增裝置範本

有數個選項可用來在 IoT Central 應用程式中新增裝置模型。 您可以從頭開始建立模型、從檔案匯入模型，或從裝置目錄選取裝置。 IoT Central 也支援「裝置優先」方法，應用程式會在實際裝置第一次連線時自動從存放庫匯入模型。

在本快速入門中，您會從裝置目錄中選擇裝置，以建立裝置範本。

下列步驟示範如何使用裝置目錄來匯入 **ESP32** 裝置的模型。 這些裝置會將遙測資料 (例如溫度) 傳送至您的應用程式：

1. 若要新增裝置範本，請選取 [裝置範本] 頁面上的 [+ 新增]。

1. 在 **選取類型** 頁面的 **使用預先設定的裝置範本** 區段中向下捲動，尋找 **ESP32-Azure IoT 套件** 圖格。

1. 選取 **ESP32-Azure IoT 套件** 圖格，然後選取 下一步: **:** 。

1. 在 [檢閱]  頁面上，選取 [建立]  。

1. 幾秒鐘後，您可以看到新的裝置範本：

    :::image type="content" source="media/quick-create-simulated-device/devkit-template.png" alt-text="顯示 ESP32 裝置之裝置範本的螢幕擷取畫面":::

    範本的名稱是 **感應器控制器**。 此模型包含 **感應器控制器**、**SensorTemp** 和 **裝置資訊介面** 等元件。 元件會定義 ESP32 裝置的功能。 功能包括遙測、屬性和命令。

### <a name="add-cloud-properties"></a>新增雲端屬性

裝置範本可包含雲端屬性。 雲端屬性僅存在於 IoT Central 應用程式中，且一律不會傳送至或接收自裝置。 若要新增兩個雲端屬性：

1. 依序選取 [雲端屬性]  和 [+ 新增雲端屬性]  。 請使用下表中的資訊，將兩個雲端屬性新增至您的裝置範本：

    | 顯示名稱      | 語意類型 | 結構描述 |
    | ----------------- | ------------- | ------ |
    | 上次維修日期 | None          | Date   |
    | 客戶名稱     | None          | String |

1. 選取 [儲存]  以儲存變更：

    :::image type="content" source="media/quick-create-simulated-device/cloud-properties.png" alt-text="顯示兩個雲端屬性的螢幕擷取畫面":::

## <a name="views"></a>檢視

身為建置者，您可以自訂應用程式，以對操作員顯示裝置相關資訊。 您的自訂將使操作員能夠管理已連線至應用程式的裝置。 您可以建立兩種類型的檢視，讓操作員用來與裝置互動：

* 用於檢視及編輯裝置和雲端屬性的表單。
* 用來視覺化裝置的儀表板，包括其所傳送的遙測。

### <a name="default-views"></a>預設檢視

產生檢視是開始將重要裝置資訊視覺化的快速方式。 您最多可以為裝置範本產生三個預設檢視：

* [命令]  檢視可讓您的操作員將命令分派至裝置。
* [概觀]  檢視會使用圖表和計量來顯示裝置遙測資料。
* [關於]  檢視會顯示裝置屬性。

在裝置範本中選取 [檢視]  節點。 您可以看到，當您新增範本時，IoT Central 會為您產生 [概觀]  和 [檢視]  。

若要新增 [管理裝置]  表單，讓操作員可用來管理裝置：

1. 選取 [檢視]  節點，然後選取 [編輯裝置和雲端資料]  圖格來新增檢視。

1. 將表單名稱變更為 [管理裝置]  。

1. 選取 [客戶名稱] 和 [上次服務日期] 雲端屬性，以及 [目標溫度] 屬性。 然後選取 [新增區段]  ：

    :::image type="content" source="media/quick-create-simulated-device/new-form.png" alt-text="顯示新增至裝置範本之新表單的螢幕擷取畫面":::

1. 選取 [儲存]  以儲存您的新表單。

## <a name="publish-device-template"></a>發佈裝置範本

您必須先發佈裝置範本，才能建立模擬裝置，或連線實際的裝置。 雖然 IoT Central 在您第一次建立範本時便發佈範本，但您必須發佈更新的版本。

若要發佈裝置範本：

1. 從 **裝置範本** 頁面瀏覽至您的 **感應器控制器** 裝置範本。

1. 選取 [發佈]  ：

    :::image type="content" source="media/quick-create-simulated-device/published-model.png" alt-text="顯示發佈圖示位置的螢幕擷取畫面":::

1. 在 [將此裝置範本發佈至應用程式]  對話方塊上，選取 [發佈]  。

在您發佈裝置範本之後，其會顯示在 [裝置]  頁面上。 在已發佈的裝置範本中，您無法直接編輯裝置模型而不建立新版本。 不過，您可以直接在已發佈的裝置範本中修改雲端屬性、自訂項目和檢視，而無須進行版本控制。 進行任何變更之後，請選取 [發佈]  ，將這些變更推送給您的操作員。

## <a name="add-a-simulated-device"></a>新增模擬裝置

若要將模擬裝置新增至應用程式，您可使用建立的 **ESP32** 裝置範本。

1. 若要以操作員的身分新增裝置，請選擇左窗格中的 [裝置]  。 **裝置** 索引標籤會顯示 ESP32 裝置的 **所有裝置** 和 **感應器控制器** 裝置範本。 選取 [感應器控制器]。

1. 若要新增模擬的 DevKit 裝置，請選取 [+新增]。 使用建議的 **裝置識別碼** 或輸入您自己的識別碼。 裝置識別碼可以包含字母、數字和 `-` 字元。 您也可以輸入新裝置的名稱。 請確定 **模擬此裝置** 設定為 **是**，然後選取 [建立]。

    :::image type="content" source="media/quick-create-simulated-device/simulated-device.png" alt-text="顯示模擬感應器控制器裝置的螢幕擷取畫面":::

現在，您可以與建置者使用模擬資料為裝置範本建立的檢視進行互動：

1. 在 [裝置] 頁面上選取您的模擬裝置

    * [概觀]  檢視會顯示模擬遙測的繪圖：

        :::image type="content" source="media/quick-create-simulated-device/simulated-telemetry.png" alt-text="顯示模擬裝置之概觀頁面螢幕擷取畫面":::

    * **關於** 檢視會顯示屬性值。

    * **命令** 檢視可讓您執行命令，例如在裝置上 **重新開機**。

    * [管理裝置]  檢視是您為操作員所建立的表單，可用來管理裝置。

    * [未經處理資料] 檢視可讓您檢視裝置所傳送的原始遙測和屬性值。 此檢視有助於偵錯裝置。

## <a name="use-a-simulated-device-to-improve-views"></a>使用模擬裝置來改善檢視

在您建立新的模擬裝置之後，建置者可以使用此裝置繼續改善及建置裝置範本的檢視。

1. 在左側窗格選擇 [裝置範本]，然後選取 [感應器控制器] 範本。

1. 選取您要編輯的任何檢視，例如 **概觀**，或建立新的檢視。 選取 [設定預覽裝置]  ，然後按 [從執行中的裝置選取]  。 在此，您可選擇沒有預覽裝置、針對測試所設定的實際裝置，或您已新增至 IoT Central 的現有裝置。

1. 在清單中選擇您的模擬裝置。 然後，選取 [套用]  。 現在，您可以在裝置範本檢視建置體驗中看到相同的模擬裝置。 此檢視適用於圖表和其他視覺效果。

    :::image type="content" source="media/quick-create-simulated-device/configure-preview.png" alt-text="顯示已設定預覽裝置的螢幕擷取畫面":::

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何建立 ESP32 裝置的 **感應器控制器** 裝置範本，並將模擬裝置新增至應用程式。

若要深入了解如何監視連線至應用程式的裝置，請繼續進行下列快速入門：

> [!div class="nextstepaction"]
> [設定規則和動作](./quick-configure-rules.md)
