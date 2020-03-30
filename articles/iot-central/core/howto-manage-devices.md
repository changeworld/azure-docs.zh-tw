---
title: 在 Azure IoT Central 應用程式中管理裝置 | Microsoft Docs
description: 了解如何以操作員的身分在 Azure IoT Central 應用程式中管理裝置。
author: sarahhubbard
ms.author: sahubbar
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 8725a822c575ce80b9810d56bfd072241ded4c86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157937"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>在 Azure IoT Central 應用程式中管理裝置



本文說明如何以操作員的身分在 Azure IoT Central 應用程式中管理裝置。 身為操作員，您可以：

- 使用 **"設備"** 頁查看、添加和刪除連接到 Azure IoT 中心應用程式的設備。
- 維護裝置的最新清查。
- 通過更改視圖中存儲在裝置屬性中的值，使設備中繼資料保持最新。
- 通過從視圖中更新特定設備上的設置來控制設備的行為。

## <a name="view-your-devices"></a>檢視您的裝置

檢視個別裝置：

1. 選擇左側窗格中的**設備**。 在這裡，您可以看到所有設備和設備範本的清單。

1. 選擇設備範本。

1. 在 **"設備"** 頁的右側窗格中，您將看到從該設備範本創建的設備清單。 選擇個別的裝置以查看該裝置的 [裝置詳細資料] 頁面：

    ![[裝置詳細資料] 頁面](./media/howto-manage-devices/devicelist.png)


## <a name="add-a-device"></a>新增裝置

將裝置新增至 Azure IoT Central 應用程式：

1. 選擇左側窗格中的**設備**。

1. 選擇要用來建立裝置的裝置範本。

1. 選擇 [+ 新增]****。

1. 將**類比****切換打開或****關閉**。 真實裝置是您連線至 Azure IoT Central 應用程式的實體裝置。 模擬裝置具有由 Azure IoT Central 為您產生的範例資料。

1. 按一下 **[建立]**。

1. 此設備現在將顯示在此範本的設備清單中。 選擇設備以查看包含設備所有視圖的設備詳細資訊頁面。

## <a name="import-devices"></a>匯入裝置

若要將大量裝置連接至您的應用程式，您可以從 CSV 檔案中大量匯入裝置。 CSV 檔案應該具有下列資料行及標頭：

* **IOTC_DeviceID**：裝置識別碼應該是全部小寫。
* **IOTC_DeviceName**：此資料行是選擇性的。

在應用程式中大量註冊裝置：

1. 選擇左側窗格中的**設備**。

1. 在左側窗格上，選擇您要用來大量建立裝置的裝置範本。

    > [!NOTE]
    > 如果您還沒有設備範本，則可以在 **"所有設備**"下匯入裝置，並在沒有範本的情況下註冊設備。 匯入裝置後，您可以將它們遷移到範本。

1. 選取 [匯入]****。

    ![匯入動作](./media/howto-manage-devices/bulkimport1a.png)


1. 選取具有所要匯入裝置識別碼清單的 CSV 檔案。

1. 在檔案上傳之後，裝置匯入便會開始。 您可以在"設備操作"面板中跟蹤導入狀態。 此面板在導入開始後自動顯示，或者您可以通過右上角的鐘形圖示訪問它。

1. 導入完成後，"設備操作"面板中將顯示一條成功消息。

    ![匯入成功](./media/howto-manage-devices/bulkimport3a.png)


如果設備導入操作失敗，則會在"設備操作"面板上看到一條錯誤訊息。 隨即會產生擷取所有錯誤的記錄檔，以供您下載。

**將設備遷移到範本**

如果在 **"所有設備**"下啟動導入來註冊設備，則創建設備時沒有任何設備範本關聯。 裝置必須與範本產生關聯，才能探索有關裝置的資料和其他詳細資料。 請遵循下列步驟讓裝置與範本產生關聯：

1. 選擇左側窗格中的**設備**。

1. 在左側面板上，選擇 **"所有設備**" ：

    ![未關聯的裝置](./media/howto-manage-devices/unassociateddevices1a.png)


1. 使用網格上的篩選器確定 **"設備範本"** 列中的值是否對任何設備"取消關聯"。

1. 選取要與範本產生關聯的裝置：

1. 選擇**遷移**：

    ![建立裝置的關聯](./media/howto-manage-devices/unassociateddevices2a.png)


1. 從可用範本清單中選擇範本，然後選擇 **"遷移**"。

1. 選取的裝置會與您選擇的裝置範本產生關聯。


## <a name="export-devices"></a>匯出裝置

若要將真實裝置連接至 IoT Central，您需要它的連接字串。 您可以批量匯出裝置詳細資訊，以獲得創建設備連接字串所需的資訊。 匯出過程會為所有選定設備創建具有設備標識、設備名稱和金鑰的 CSV 檔。

若要從您的應用程式大量匯出裝置：

1. 選擇左側窗格中的**設備**。

1. 在左側窗格中，選擇要從中匯出裝置的設備範本。

1. 選擇要匯出的設備，然後選擇 **"匯出**"操作。

    ![匯出](./media/howto-manage-devices/export1a.png)


1. 匯出程序隨即啟動。 您可以使用"設備操作"面板跟蹤狀態。

1. 匯出完成之後，即會顯示一則成功訊息，以及下載所產生檔案的連結。

1. 選擇 **"下載檔案"** 連結以將檔下載到磁片上的本地資料夾。

    ![匯出成功](./media/howto-manage-devices/export2a.png)


1. 匯出的 CSV 檔案會包含下列資料行：裝置識別碼、裝置名稱、裝置金鑰及 X509 憑證指紋：

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

有關連接字串和將實際設備連接到 IoT 中央應用程式的詳細資訊，請參閱[Azure IoT 中央中的設備連接](concepts-get-connected.md)。

## <a name="delete-a-device"></a>刪除裝置

從 Azure IoT Central 應用程式刪除真實或模擬裝置：

1. 選擇左側窗格中的**設備**。

1. 選取所要刪除裝置的裝置範本。

1. 使用篩選器工具篩選和搜索您的設備。 選中要刪除的設備旁邊的核取方塊。

1. 選擇 **"刪除**"。 您可以在設備操作面板中跟蹤此刪除狀態。

## <a name="change-a-property"></a>變更屬性

雲屬性是與設備關聯的設備中繼資料，如城市和序號。 可寫入屬性控制設備的行為。 也就是說，它們能讓您為裝置提供輸入。  裝置屬性由設備設置，並在 IoT 中心內唯讀。 您可以在**設備的"設備詳細資訊"** 視圖中查看和更新屬性。

1. 選擇左側窗格中的**設備**。

1. 選擇要更改其屬性的設備的設備範本並選擇目標設備。

1. 選擇包含裝置屬性的視圖，此視圖使您能夠輸入值並選擇頁面頂部的 **"保存**"。 在這裡，您可以看到您的設備的屬性及其當前值。 雲屬性和可寫入屬性具有可編輯欄位，而裝置屬性是唯讀的。 對於可寫入屬性，您可以在欄位底部看到其同步狀態。 

1. 修改屬性到所需的值。 可以一次修改多個屬性，並同時更新所有屬性。

1. 選擇 [儲存]****。 如果保存了可寫入屬性，則這些值將發送到您的設備。 當設備確認可寫入屬性的更改時，狀態將返回到**同步**。 如果保存了雲屬性，則該值將更新。


## <a name="next-steps"></a>後續步驟

您現在已了解如何在 Azure IoT Central 應用程式中管理裝置，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [如何使用設備組](tutorial-use-device-groups.md)

<!-- Next how-tos in the sequence -->
