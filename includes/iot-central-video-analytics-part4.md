---
title: 包含檔案
description: 包含檔案
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 164f5803b6e9e62447423735e98f6e4c36c73f13
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876630"
---
### <a name="add-relationships"></a>新增關聯性

在 **LVA Edge 閘道**裝置範本的 [模組/LVA 邊緣閘道模組] 底下，選取 [關聯性]。 選取 [+ 新增關聯性] 並新增下列兩個關聯性：

|顯示名稱               |名稱          |目標 |
|-------------------------- |------------- |------ |
|LVA Edge 動作偵測器   |使用預設值   |LVA Edge 動作偵測器裝置 |
|LVA Edge 物件偵測器   |使用預設值   |LVA Edge 物件偵測器裝置 |

然後選取 [儲存]。

:::image type="content" source="media/iot-central-video-analytics-part4/relationships.png" alt-text="新增關聯性":::

### <a name="add-views"></a>新增檢視

**LVA Edge 閘道**裝置範本不包含任何檢視定義。

若要在裝置範本中新增檢視：

1. 在 **LVA Edge 閘道**裝置範本中，瀏覽至 [檢視]，然後選取 [視覺化裝置] 磚。

1. 輸入「LVA Edge 閘道裝置」作為檢視名稱。

1. 將下列這些磚新增至檢視：

    * 具有**裝置資訊**屬性的磚：**裝置型號**、**製造商**、**作業系統**、**處理器架構**、**軟體版本**、**總記憶體**，以及**總儲存體**。
    * 具有**可用記憶體**和**系統活動訊號**遙測值的折線圖磚。
    * 具有下列事件的事件歷程記錄磚：**建立相機**、**刪除相機**、**模組重新啟動**、**模組已開始**、**模組已停止**。
    * 顯示 **IoT Central 用戶端狀態**遙測的上一個已知值磚 (2x1)。
    * 顯示**模組狀態**遙測的上一個已知值磚 (2x1)。
    * 顯示**系統活動訊號**遙測的上一個已知值磚 (1x1)。
    * 顯示**連結相機**遙測的上一個已知值磚 (1x1)。

    :::image type="content" source="media/iot-central-video-analytics-part4/gateway-dashboard.png" alt-text="新增關聯性":::

1. 選取 [儲存]。

### <a name="publish-the-device-template"></a>發佈裝置範本

您必須先發佈裝置範本，才能將裝置新增至應用程式：

1. 在 **LVA Edge 閘道**裝置範本中，選取 [發佈]。

1. 在 [將此裝置範本發佈至應用程式] 頁面上，選取 [發佈]。

**LVA Edge 閘道**現在可作為裝置類型，以在應用程式中的 [裝置] 頁面上使用。

## <a name="add-a-gateway-device"></a>新增閘道裝置

若要將 **LVA Edge 閘道**裝置新增至應用程式：

1. 瀏覽至 [裝置] 頁面，然後選取 [LVA Edge 閘道] 裝置範本。

1. 選取 [+ 新增]。

1. 在 [建立新裝置] 對話方塊中，將裝置名稱變更為 LVA Gateway 001，並將裝置識別碼變更為 lva-gateway-001。

    > [!NOTE]
    > 裝置識別碼在應用程式中必須是唯一的。

1. 選取 [建立]。

裝置狀態為 [已註冊]。

### <a name="get-the-device-credentials"></a>取得裝置認證

您需要可讓裝置連線到您 IoT Central 應用程式的認證。 若要取得裝置認證：

1. 在 [裝置] 頁面上，選取您建立的 **lva-gateway-001** 裝置。

1. 選取 [連接]。

1. 在 [裝置連線] 頁面上，記下 scratchpad.txt 檔案中的 [識別碼範圍]、[裝置識別碼] 和裝置的 [主要金鑰]。 您稍後會用到這些值。

1. 請確定連線方法已設定為 [共用存取簽章]。

1. 選取 [關閉]。

## <a name="next-steps"></a>後續步驟

您現在已使用**影片分析 - 物件和動作偵測**應用程式範本建立了 IoT Central 應用程式、建立了閘道裝置的裝置範本，以及將閘道裝置新增至應用程式。

如果您想要使用執行雲端 VM 且具有模擬影片串流的 IoT Edge 模組，來試用影片分析 - 物件和動作偵測應用程式：

> [!div class="nextstepaction"]
> [建立適用於影片分析的 IoT Edge 執行個體 (Linux VM)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-vm.md)

如果您想要使用執行真實裝置且具有真實 **ONVIF** 相機的 IoT Edge 模組，來試用影片分析 - 物件和動作偵測應用程式：

> [!div class="nextstepaction"]
> [建立適用於影片分析的 IoT Edge 執行個體 (Intel NUC)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-nuc.md)
