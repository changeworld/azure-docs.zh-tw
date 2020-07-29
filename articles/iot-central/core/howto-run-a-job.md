---
title: 在 Azure IoT Central 應用程式中建立及執行工作 | Microsoft Docs
description: Azure IoT Central 作業允許大量裝置管理功能，例如更新屬性或執行命令。
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 06/08/2020
ms.topic: how-to
ms.openlocfilehash: 6073f71eb21ba4a6739647964d4888044d6ee59a
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87283594"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>在您的 Azure IoT Central 應用程式中建立及執行作業

您可以使用 Microsoft Azure IoT Central 利用工作來大規模管理您的已連線裝置。 作業可讓您對裝置屬性進行大量更新，並執行命令。 本文說明如何在您自己的應用程式中開始使用作業。

## <a name="create-and-run-a-job"></a>建立及執行工作

本節說明如何建立及執行工作。 它會示範如何為一組羅吉斯閘道裝置設定光源閾值。

1. 從左窗格中，流覽至 [**作業**]。

2. 選取 [ **+ 新增**] 以建立新的作業：

    ![建立新工作](./media/howto-run-a-job/create-new-job.png)

3. 輸入 [名稱] 和 [描述] 來識別您要建立的作業。

4. 選取您想要將作業套用至其中的目標裝置群組。 您可以在 [**摘要**] 區段中查看您的作業設定適用的裝置數目。

5. 接下來，選擇 [**雲端] 屬性**、[**屬性**] 或 [**命令**] 做為要設定的作業類型。 若要設定**屬性**作業設定，請選取屬性，並設定其新值。 若要設定**命令**，請選擇要執行的命令。 屬性作業可以設定多個屬性：

    ![設定工作](./media/howto-run-a-job/configure-job.png)

6. 建立作業之後，請選擇 [**執行**] 或 [**儲存**]。 作業現在會出現在您的主要 [**作業**] 頁面上。 在此頁面上，您可以看到目前正在執行的作業，以及任何先前執行或儲存之作業的歷程記錄。 您已儲存的作業可以隨時重新開啟，以繼續進行編輯或執行：

    ![檢視工作](./media/howto-run-a-job/view-job.png)

    > [!NOTE]
    > 您可以針對先前執行的作業，查看30天的歷程記錄。

7. 按一下已儲存的作業，然後按一下 [執行] 按鈕來執行作業。 [執行作業] 快顯視窗隨即顯示。 按一下 [執行作業] 按鈕來確認。 

    ![執行作業](./media/howto-run-a-job/run-job.png)

8. 作業會經歷不同的擱置、執行中和已完成的階段。 作業執行詳細資料包含 [結果計量]、[持續時間詳細資料] 和 [裝置清單] 方格。 在此總覽中，您也可以選取 [**結果記錄**檔] 來下載作業詳細資料的 CSV 檔案，包括裝置及其狀態值。 這種資訊對疑難排解很有用。

    ![檢視裝置狀態](./media/howto-run-a-job/download-details.png)

## <a name="manage-jobs"></a>管理工作

若要停止其中一個執行中的作業，請開啟它，然後選取 [**停止**]。 作業狀態會變更以反映作業已停止。 [**摘要**] 區段會顯示哪些裝置已完成、失敗或仍處於擱置狀態。

![管理作業](./media/howto-run-a-job/manage-job.png)

當作業處於停止狀態時，您可以按一下 [**繼續**] 繼續執行作業。 作業狀態會變更，以反映作業現在正在執行。 [**摘要**] 區段會繼續以最新的進度更新。

![已停止工作](./media/howto-run-a-job/stopped-job.png)

## <a name="copy-a-job"></a>複製作業

若要複製其中一個現有的作業，請在 [**作業**] 頁面上選取它，然後選取 [**工作詳細資料**]。 將會顯示 [工作詳細資料] 頁面。 

![作業詳細資料](./media/howto-run-a-job/job-details.png)

按一下 [**複製**]

![作業詳細資料](./media/howto-run-a-job/job-details-copy.png)

[作業設定] 的複本隨即開啟供您編輯，而 [**複製**] 則會附加至 [作業名稱]。 您可以儲存或執行新的作業：

![複製作業](./media/howto-run-a-job/copy-job.png)

## <a name="view-job-status"></a>檢視作業狀態

建立作業之後，[**狀態**] 資料行會以作業的最新狀態訊息更新。 下表列出可能的狀態值：

| 狀態訊息       | 狀態意義                                          |
| -------------------- | ------------------------------------------------------- |
| Completed            | 此工作已在所有裝置上執行。              |
| 失敗               | 此工作失敗而未在裝置上完整執行。  |
| Pending              | 此作業尚未開始在裝置上執行。         |
| 執行中              | 此工作目前正在裝置上執行。             |
| 已停止              | 此工作已被使用者手動停止。           |

狀態訊息後面會有作業中的裝置總覽。 下表列出可能的裝置狀態值：

| 狀態訊息       | 狀態意義                                                     |
| -------------------- | ------------------------------------------------------------------ |
| 成功            | 作業執行成功的裝置數目。       |
| 失敗               | 作業執行失敗的裝置數目。       |

### <a name="view-the-device-status-values"></a>查看裝置狀態值

若要查看作業和所有受影響裝置的狀態，請開啟作業。 在每個裝置名稱旁邊，您會看到下列其中一則狀態訊息：

| 狀態訊息       | 狀態意義                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Completed            | 此裝置上執行的作業。                                     |
| 失敗               | 此工作無法在此裝置上執行。 錯誤訊息會顯示詳細資訊。  |
| Pending              | 作業尚未在此裝置上執行。                                   |

若要下載 CSV 檔案，其中包含工作詳細資料和裝置清單及其狀態值，請選取 [**下載**]。

### <a name="filter-the-list-of-devices"></a>篩選裝置清單

您可以藉由選取篩選圖示來篩選 [作業詳細資料] 頁面上的裝置清單。 您可以篩選 [**裝置識別碼**] 或 [**狀態**] 欄位：

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="篩選裝置清單":::

### <a name="customize-columns-in-the-device-list"></a>自訂裝置清單中的資料行

您可以選取 [資料行選項] 圖示，選擇要顯示在裝置清單中的其他資料行：

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="資料行選項":::

您會看到一個對話方塊，讓您選擇要在裝置清單中顯示的資料行。 選取您想要顯示的資料行，選取向右箭號圖示，然後選取 **[確定]**。 若要選取所有可用的資料行，請選取 [**全選**]：

:::image type="content" source="media/howto-run-a-job/column-picker-popup.png" alt-text="資料行選擇器對話方塊":::

選取的資料行會顯示在裝置清單上：

:::image type="content" source="media/howto-run-a-job/column-picker-column-selected.png" alt-text="選取資料行":::

選取的資料行會保存在使用者會話中，或跨可存取應用程式的使用者會話。

## <a name="rerun-jobs"></a>重新執行作業

您可以重新執行具有失敗裝置的作業。 選取 [**重新**執行]：

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="重新執行作業":::

輸入 [作業名稱] 和 [描述]，然後選取 [**重新執行作業**]。 已提交新的工作，以在失敗的裝置上重試此動作：

:::image type="content" source="media/howto-run-a-job/rerun-failed.png" alt-text="重新執行失敗的裝置":::

> [!NOTE]
> 您不能同時從 IoT Central 應用程式執行五個以上的作業。

> [!NOTE]
> 當作業完成，而且您刪除了工作裝置清單中的裝置時，裝置名稱和裝置詳細資料連結中的裝置專案會顯示為已刪除，但無法供已刪除的裝置使用。

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何在 Azure IoT Central 應用程式中建立作業，以下是一些後續步驟：

- [管理您的裝置](howto-manage-devices.md)
- [建立裝置範本版本](howto-version-device-template.md)
