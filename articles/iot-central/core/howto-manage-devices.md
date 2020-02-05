---
title: 在 Azure IoT Central 應用程式中管理裝置 | Microsoft Docs
description: 了解如何以操作員的身分在 Azure IoT Central 應用程式中管理裝置。
author: sarahhubbard
ms.author: sahubbar
ms.date: 12/06/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: dfbfd65223ddad5e8a20958d62ef86b5aa16af43
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023475"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>在 Azure IoT Central 應用程式中管理裝置



本文說明如何以操作員的身分在 Azure IoT Central 應用程式中管理裝置。 身為操作員，您可以：

- 使用 [**裝置**] 頁面，即可查看、新增及刪除連線至 Azure IoT Central 應用程式的裝置。
- 維護裝置的最新清查。
- 藉由從您的視圖變更裝置屬性中儲存的值，讓您的裝置中繼資料保持最新狀態。
- 藉由從您的視圖更新特定裝置上的設定，來控制裝置的行為。

## <a name="view-your-devices"></a>檢視您的裝置

檢視個別裝置：

1. 選擇左窗格中的 [**裝置**]。 在這裡，您會看到所有裝置和裝置範本的清單。

1. 選擇裝置範本。

1. 在 [**裝置**] 頁面的右窗格中，您會看到從該裝置範本建立的裝置清單。 選擇個別的裝置以查看該裝置的 [裝置詳細資料] 頁面：

    ![[裝置詳細資料] 頁面](./media/howto-manage-devices/devicelist.png)


## <a name="add-a-device"></a>新增裝置

將裝置新增至 Azure IoT Central 應用程式：

1. 選擇左窗格中的 [**裝置**]。

1. 選擇要用來建立裝置的裝置範本。

1. 選擇 [+ 新增]。

1. 將**模擬** **切換為開啟**或**關閉**。 真實裝置是您連線至 Azure IoT Central 應用程式的實體裝置。 模擬裝置具有由 Azure IoT Central 為您產生的範例資料。

1. 按一下頁面底部的 [新增]。

1. 此裝置現在會出現在此範本的裝置清單中。 選取裝置以查看包含裝置所有視圖的 [裝置詳細資料] 頁面。

## <a name="import-devices"></a>匯入裝置

若要將大量裝置連接至您的應用程式，您可以從 CSV 檔案中大量匯入裝置。 CSV 檔案應該具有下列資料行及標頭：

* **IOTC_DeviceID**：裝置識別碼應該是全部小寫。
* **IOTC_DeviceName**：此資料行是選擇性的。

在應用程式中大量註冊裝置：

1. 選擇左窗格中的 [**裝置**]。

1. 在左側窗格上，選擇您要用來大量建立裝置的裝置範本。

    > [!NOTE]
    > 如果您還沒有裝置範本，您可以在 [**所有裝置**] 底下匯入裝置，並在沒有範本的情況下進行註冊。 匯入裝置之後，您就可以將它們遷移至範本。

1. 選取 [匯入]。

    ![匯入動作](./media/howto-manage-devices/bulkimport1a.png)


1. 選取具有所要匯入裝置識別碼清單的 CSV 檔案。

1. 在檔案上傳之後，裝置匯入便會開始。 您可以在 [裝置作業] 面板中追蹤匯入狀態。 此面板會在匯入開始後自動顯示，或您可以透過右上角的鐘圖示來存取。

1. 匯入完成之後，[裝置作業] 面板中會顯示成功訊息。

    ![匯入成功](./media/howto-manage-devices/bulkimport3a.png)


如果裝置匯入作業失敗，您會在 [裝置操作] 面板上看到錯誤訊息。 隨即會產生擷取所有錯誤的記錄檔，以供您下載。

**將裝置遷移至範本**

如果您在 [**所有裝置**] 下啟動 [匯入] 來註冊裝置，則會建立不含任何裝置範本關聯的裝置。 裝置必須與範本產生關聯，才能探索有關裝置的資料和其他詳細資料。 請遵循下列步驟讓裝置與範本產生關聯：

1. 選擇左窗格中的 [**裝置**]。

1. 在左面板中，選擇 [**所有裝置**]：

    ![未關聯的裝置](./media/howto-manage-devices/unassociateddevices1a.png)


1. 使用方格上的篩選準則來判斷裝置**範本**資料行中的值是否為任何裝置的「未關聯」。

1. 選取要與範本產生關聯的裝置：

1. 選取 [**遷移**]：

    ![建立裝置的關聯](./media/howto-manage-devices/unassociateddevices2a.png)


1. 從可用範本的清單中選擇範本，然後選取 [**遷移**]。

1. 選取的裝置會與您選擇的裝置範本產生關聯。


## <a name="export-devices"></a>匯出裝置

若要將真實裝置連接至 IoT Central，您需要它的連接字串。 您可以大量匯出裝置詳細資料，以取得建立裝置連接字串所需的資訊。 匯出程式會針對所有選取的裝置，建立具有裝置身分識別、裝置名稱和金鑰的 CSV 檔案。

若要從您的應用程式大量匯出裝置：

1. 選擇左窗格中的 [**裝置**]。

1. 在左窗格中，選擇您要從中匯出裝置的裝置範本。

1. 選取您要匯出的裝置，然後選取 [**匯出**] 動作。

    ![匯出](./media/howto-manage-devices/export1a.png)


1. 匯出程序隨即啟動。 您可以使用 [裝置作業] 面板來追蹤狀態。

1. 匯出完成之後，即會顯示一則成功訊息，以及下載所產生檔案的連結。

1. 選取 [**下載檔案**] 連結，將檔下載至磁片上的本機資料夾。

    ![匯出成功](./media/howto-manage-devices/export2a.png)


1. 匯出的 CSV 檔案會包含下列資料行：裝置識別碼、裝置名稱、裝置金鑰及 X509 憑證指紋：

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

如需連接字串和將真實裝置連線到 IoT Central 應用程式的詳細資訊，請參閱[Azure IoT Central 中的裝置連線能力](concepts-get-connected.md)。

## <a name="delete-a-device"></a>刪除裝置

從 Azure IoT Central 應用程式刪除真實或模擬裝置：

1. 選擇左窗格中的 [**裝置**]。

1. 選取所要刪除裝置的裝置範本。

1. 使用篩選工具來篩選及搜尋您的裝置。 勾選 [要刪除的裝置] 旁的方塊。

1. 選擇 [刪除]。 您可以在裝置操作面板中追蹤這項刪除作業的狀態。

## <a name="change-a-property"></a>變更屬性

雲端屬性是與裝置相關聯的裝置中繼資料，例如城市和序號。 可寫入的屬性可控制裝置的行為。 也就是說，它們能讓您為裝置提供輸入。  裝置屬性是由裝置所設定，而且在 IoT Central 內是唯讀的。 您可以在裝置的 [**裝置詳細資料**] 視圖上，查看和更新屬性。

1. 選擇左窗格中的 [**裝置**]。

1. 選擇您想要變更其屬性之裝置的裝置範本，然後選取目標裝置。

1. 選擇包含裝置屬性的視圖，此視圖可讓您輸入值，然後選取頁面頂端的 [**儲存**]。 在這裡，您會看到裝置的屬性和其目前的值。 [雲端屬性] 和 [可寫入屬性] 具有編輯欄位，而 [裝置屬性] 是唯讀的。 針對可寫入的屬性，您可以在欄位底部看到其同步狀態。 

1. 將屬性修改為您所需的值。 您可以一次修改多個屬性，並同時更新所有內容。

1. 選擇 [儲存]。 如果您已儲存可寫入的屬性，這些值會傳送至您的裝置。 當裝置確認可寫入屬性的變更時，狀態會回到 [已**同步**處理]。 如果您已儲存雲端屬性，則會更新此值。


## <a name="next-steps"></a>後續步驟

您現在已了解如何在 Azure IoT Central 應用程式中管理裝置，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [如何使用裝置群組](tutorial-use-device-groups.md)

<!-- Next how-tos in the sequence -->
