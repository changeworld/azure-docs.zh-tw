---
title: 在 Azure IoT Central 應用程式中建立及執行工作 | Microsoft Docs
description: Azure IoT Central 作業允許大量裝置管理功能，例如更新屬性或執行命令。
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 06/08/2020
ms.topic: how-to
ms.openlocfilehash: 406881a9131aae35b91dcab248745bb426cecf0e
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797831"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>在 Azure IoT Central 應用程式中建立和執行作業

您可以使用 Microsoft Azure IoT Central 透過作業大規模管理連線的裝置。 作業可讓您對裝置屬性進行大量更新，以及執行命令。 本文說明如何開始在您自己的應用程式中使用工作。

## <a name="create-and-run-a-job"></a>建立及執行工作

本節說明如何建立和執行工作，其格式為羅吉斯閘道裝置群組的燈光閾值設定。

1. 在左窗格中，選取 [ **作業**]。

2. 選取 [+ 新增]。

   ![顯示用於建立作業之選項的螢幕擷取畫面。](./media/howto-run-a-job/create-new-job.png)

3. 輸入名稱和描述，以識別您正在建立的工作。

4. 選取您要套用作業的目標裝置群組。 您可以在 [ **摘要** ] 區段中看到您的作業設定適用的裝置數目。

5. 選擇 [ **雲端屬性**]、[ **屬性**] 或 [ **命令** ] 作為要設定的作業類型。 

   若要設定 **屬性** 作業設定，請選取屬性，並設定其新值。 若要設定 **命令** 作業設定，請選擇要執行的命令。 屬性作業可以設定多個屬性。

   ![顯示建立屬性作業之選項的螢幕擷取畫面，稱為「設定光源閾值」。](./media/howto-run-a-job/configure-job.png)

6. 選取 [ **執行** ] 或 [ **儲存**]。 作業現在會出現在您的主要 [ **作業** ] 頁面上。 在此頁面上，您可以看到目前正在執行的作業，以及任何先前執行或儲存之作業的歷程記錄。 您可以隨時重新開啟已儲存的作業，以繼續進行編輯或執行。

   ![顯示所建立作業的名稱、狀態和描述的螢幕擷取畫面。](./media/howto-run-a-job/view-job.png)

   > [!NOTE]
   > 您可以為先前執行的作業查看30天的歷程記錄。

7. 選取已儲存的工作，然後選取 [ **執行** ] 按鈕來執行它。 

   [ **執行您的作業？** ] 對話方塊隨即出現。 選取 [ **執行作業** ] 按鈕以確認。 

   ![確認您要執行作業之對話方塊的螢幕擷取畫面。](./media/howto-run-a-job/run-job.png)

8. 作業會經歷暫止、執行中和已完成的階段。 作業執行詳細資料包含結果計量、持續時間詳細資料和裝置清單方格。 

   從這個總覽中，您也可以選取 [ **結果記錄** 檔] 來下載作業詳細資料的 CSV 檔案，包括裝置及其狀態值。 此資訊有助於進行疑難排解。

   ![顯示裝置狀態的螢幕擷取畫面。](./media/howto-run-a-job/download-details.png)

## <a name="manage-jobs"></a>管理工作

若要停止執行中的作業，請開啟它並選取 [ **停止**]。 作業狀態會變更，以反映作業已停止。 [ **摘要** ] 區段會顯示哪些裝置已完成、失敗或仍處於擱置狀態。

![顯示執行中作業的螢幕擷取畫面，以及用來停止作業的按鈕。](./media/howto-run-a-job/manage-job.png)

當工作處於 [已停止] 狀態之後，您可以選取 [繼續] 繼續 **執行** 作業。 作業狀態會變更，以反映作業現在正在執行。 **摘要**區段會繼續以最新的進度更新。

![顯示已停止工作的螢幕擷取畫面，以及繼續作業的按鈕。](./media/howto-run-a-job/stopped-job.png)

## <a name="copy-a-job"></a>複製作業

若要複製其中一個現有的作業，請在 [ **工作** ] 頁面上選取該作業，然後選取 [ **工作詳細資料**]。 [ **作業詳細資料** ] 頁面隨即出現。 

![顯示工作詳細資料頁面的螢幕擷取畫面。](./media/howto-run-a-job/job-details.png)

選取 [複製]****。

![顯示 [複製] 按鈕的螢幕擷取畫面。](./media/howto-run-a-job/job-details-copy.png)

作業設定的複本會開啟供您編輯，並 **將複製** 附加至作業名稱。 您可以儲存或執行新的作業。

![顯示作業設定複本的螢幕擷取畫面。](./media/howto-run-a-job/copy-job.png)

## <a name="view-job-status"></a>檢視作業狀態

建立作業之後，會使用作業的最新狀態訊息更新 [ **狀態** ] 資料行。 下表列出可能的作業狀態值：

| 狀態訊息       | 狀態意義                                          |
| -------------------- | ------------------------------------------------------- |
| 已完成            | 此工作會在所有裝置上執行。              |
| 失敗               | 此作業失敗，且未在裝置上完全執行。  |
| Pending              | 此作業尚未在裝置上開始執行。         |
| 執行中              | 這項作業目前正在裝置上執行。             |
| 已停止              | 使用者已手動停止此作業。           |

狀態訊息後面會接著工作中的裝置總覽。 下表列出可能的裝置狀態值：

| 狀態訊息       | 狀態意義                                                     |
| -------------------- | ------------------------------------------------------------------ |
| 成功            | 作業執行成功的裝置數目。       |
| 失敗               | 作業執行失敗的裝置數目。       |

若要查看作業的狀態和所有受影響的裝置，請開啟作業。 在每個裝置名稱旁邊，您會看到下列其中一個狀態訊息：

| 狀態訊息       | 狀態意義                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| 已完成            | 此工作已在此裝置上執行。                                     |
| 失敗               | 作業無法在此裝置上執行。 錯誤訊息會顯示詳細資訊。  |
| Pending              | 作業尚未在此裝置上執行。                                   |

若要下載包含作業詳細資料的 CSV 檔案，以及裝置的清單及其狀態值，請選取 [ **下載**]。

## <a name="filter-the-device-list"></a>篩選裝置清單

您可以藉由選取篩選圖示來篩選 [ **作業詳細資料** ] 頁面上的裝置清單。 您可以篩選 [ **裝置識別碼** ] 或 [ **狀態** ] 欄位。

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="顯示篩選裝置清單之選項的螢幕擷取畫面。":::

## <a name="customize-columns-in-the-device-list"></a>自訂裝置清單中的資料行

您可以選取 [資料行選項] 圖示，選擇要在裝置清單中顯示的其他資料行。

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="顯示資料行選項圖示的螢幕擷取畫面。":::

對話方塊可讓您選擇要在裝置清單中顯示的資料行。 選取您要顯示的資料行，選取向右箭號，然後選取 **[確定]**。 若要選取所有可用的資料行，請選取 [全 **選**]。

:::image type="content" source="media/howto-run-a-job/column-picker-popup.png" alt-text="顯示對話方塊的螢幕擷取畫面，可選擇要顯示的資料行。":::

選取的資料行會出現在裝置清單中。

:::image type="content" source="media/howto-run-a-job/column-picker-column-selected.png" alt-text="顯示裝置清單中選取之資料行的螢幕擷取畫面。":::

選取的資料行會保存在使用者會話中，或跨具有應用程式存取權的使用者會話。

## <a name="rerun-jobs"></a>重新執行作業

您可以重新執行具有失敗裝置的工作。 選取 [ **在失敗時重新**執行]。

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="顯示在失敗的裝置上重新執行作業之按鈕的螢幕擷取畫面。":::

輸入作業名稱和描述，然後選取 [ **重新執行作業**]。 提交新的工作，以在失敗的裝置上重試動作。

:::image type="content" source="media/howto-run-a-job/rerun-failed.png" alt-text="顯示重新執行失敗裝置之對話方塊的螢幕擷取畫面。":::

> [!NOTE]
> 您無法從 Azure IoT Central 應用程式同時執行五個以上的作業。
>
> 當作業完成，而且您刪除了工作裝置清單中的裝置時，裝置專案會在裝置名稱中顯示為已刪除。 [詳細資料] 連結不適用於已刪除的裝置。

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何在 Azure IoT Central 應用程式中建立工作，以下是一些後續步驟：

- [管理您的裝置](howto-manage-devices.md)
- [建立裝置範本版本](howto-version-device-template.md)
