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
ms.openlocfilehash: 0381418f20e126f94060b9f495fe1fe8b54e713b
ms.sourcegitcommit: bf8c447dada2b4c8af017ba7ca8bfd80f943d508
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2020
ms.locfileid: "85378409"
---
## <a name="create-a-device-template"></a>建立裝置範本

在本機電腦上建立名為 `environmental-sensor` 的資料夾。

下載[環境感應器功能模型](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/9004219bff1e958b7cd6ff2a52209f4b7ae19396/samples/EnvironmentalSensorInline.capabilitymodel.json) JSON 檔案，並儲存在 `environmental-sensor` 資料夾中。

請在文字編輯器中，將 `{YOUR_COMPANY_NAME_HERE}` 的兩個執行個體以您下載的 `EnvironmentalSensorInline.capabilitymodel.json` 檔案中的公司名稱取代。 您只能使用字元 a-z、A-Z、0-9 和底線。

在您的 Azure IoT Central 應用程式中，匯入 `EnvironmentalSensorInline.capabilitymodel.json` 裝置功能模型檔案，來建立稱為*環境感應器*的裝置範本：

![具有匯入裝置功能模型的裝置範本](./media/iot-central-add-environmental-sensor/device-template.png)

裝置功能模型包含兩個介面：標準**裝置資訊**介面和自訂**環境感應器**介面。 **環境感應器**介面會定義下列功能：

| 類型 | 顯示名稱 | 描述 |
| ---- | ------------ | ----------- |
| 屬性 | 裝置狀態     | 裝置的狀態。 有線上/離線兩個狀態可用。 |
| 屬性 (可寫入) | 客戶名稱    | 目前正在操作裝置的客戶名稱。 |
| 屬性 (可寫入) | 亮度等級 | 裝置上的光線亮度等級。 可以指定為1 (高)、2 (中)、3 (低)。 |
| 遙測 | 溫度 | 裝置目前偵測到的溫度。 |
| 遙測 | 溼度    | 裝置目前偵測到的濕度。 |
| Command | blink          | 開始在指定的時間間隔內使裝置上的 LED 閃爍。 |
| Command | turnon         | 開啟裝置上的 LED。 |
| Command | turnoff        | 關閉裝置上的 LED。 |
| Command | rundiagnostics | 此非同步命令會啟動在裝置上執行的診斷。 |

若要自訂如何在 IoT Central 應用程式中顯示**裝置狀態** 屬性，請選取裝置範本中的 [自訂]。 展開 [裝置狀態] 項目，輸入「Online」作為 **True 名稱**，並輸入「Offline」作為 **False 名稱**。 接著儲存變更：

![自訂裝置範本](./media/iot-central-add-environmental-sensor/customize-template.png)

## <a name="create-views"></a>建立檢視

檢視可讓您與連線到 IoT Central 應用程式的裝置互動。 例如，您可以擁有顯示遙測資料的檢視、顯示內容的檢視，以及可讓您編輯可寫入和雲端屬性的檢視。 檢視是裝置範本的一部分。

若要將一些預設檢視新增至 **環境感應器**裝置範本，請瀏覽至您的裝置範本，選取 [檢視]，然後選取 [產生預設檢視]圖格。 請確定 [概觀] 和 [關於] 的狀態為 [開啟]，然後選取 [產生預設的儀表板檢視]。 您現在已在範本中定義了兩個預設檢視。

**環境感應器** 介面包含兩個可寫入的屬性 - **客戶名稱**和**亮度等級**。 若要建立檢視，您可以使用來編輯這些屬性：

1. 選取 [檢視]，再選取 [編輯裝置和雲端資料] 圖格。

1. 輸入「屬性」作為表單名稱。

1. 選取 [亮度層級] 和 [客戶名稱] 屬性。 然後選取 [新增區段]。

1. 儲存您的變更。

![新增檢視以啟用屬性編輯](./media/iot-central-add-environmental-sensor/properties-view.png)

## <a name="publish-the-template"></a>發佈範本

您必須先發佈範本，才能新增使用**環境感應器**裝置範本的裝置。

在裝置範本中，選取 [發佈]。 在 [將此裝置範本發佈至應用程式] 面板上，選取 [發佈]。

若要檢查範本是否已可供使用，請瀏覽至 IoT Central 應用程式中的 [裝置] 頁面。 [裝置] 區段會顯示應用程式中已發佈的裝置清單：

![裝置頁面上的已發佈範本](./media/iot-central-add-environmental-sensor/published-templates.png)

## <a name="add-a-real-device"></a>新增真實裝置

在您的 Azure IoT Central 應用程式中，將實際裝置新增至您在上一節中建立的裝置範本：

1. 在 [裝置] 頁面中，選取 [環境感應器] 裝置範本。

    > [!TIP]
    > 請務必先選取要使用的範本，再選取 [+ 新增]，否則您將建立沒有關聯的裝置。

1. 選取 [+ 新增]。

1. 請確定 **模擬**狀態為**關閉**。 然後選取 [建立]。

按一下裝置名稱，然後選取 [連線]。 記下 [裝置連線] 頁面上的裝置連線資訊，包括 [識別碼範圍]、[裝置識別碼] 和 [主要金鑰]。 建立裝置程式碼時，您會需要這些值：

![裝置連線資訊](./media/iot-central-add-environmental-sensor/device-connection.png)
