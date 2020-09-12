---
title: 在 Azure IoT Central 應用程式中建立及執行工作 | Microsoft Docs
description: Azure IoT Central 作業允許大量裝置管理功能，例如更新屬性或執行命令。
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 09/10/2020
ms.topic: how-to
ms.openlocfilehash: 82a370bcda2d762abb36e6e43ab755c45348e4c5
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2020
ms.locfileid: "90019934"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>在 Azure IoT Central 應用程式中建立和執行作業

您可以使用 Azure IoT Central，透過作業大規模管理連線的裝置。 作業可讓您對裝置和雲端屬性進行大量更新，以及執行命令。 本文說明如何開始在您自己的應用程式中使用工作。

## <a name="create-and-run-a-job"></a>建立及執行工作

下列範例示範如何建立及執行作業，以設定羅吉斯閘道裝置群組的光源閾值。 您可以使用工作嚮導來建立和執行作業。 您可以儲存作業以供稍後執行。

1. 在左窗格中，選取 [ **作業**]。

1. 選取 [ **+ 新增作業**]。

1. 在 [ **設定您的作業** ] 頁面上，輸入名稱和描述，以識別您正在建立的作業。

1. 選取您要套用作業的目標裝置群組。 您可以在 **裝置群組** 選取範圍下方，查看您的作業設定適用的裝置數目。

1. 選擇 [ **雲端屬性**]、[ **屬性**] 或 [ **命令** ] 作為 **作業類型**：

    若要設定 **屬性** 作業設定，請選取屬性，並設定其新值。 若要設定 **命令** 作業設定，請選擇要執行的命令。 屬性作業可以設定多個屬性。

    :::image type="content" source="media/howto-run-a-job/configure-job.png" alt-text="顯示建立屬性作業之選項的螢幕擷取畫面，稱為「集合光源閾值」":::

    選取 [ **儲存並** 結束]，將作業加入至 [ **作業** ] 頁面上已儲存的作業清單。 您稍後可以從已儲存的作業清單中返回工作。

    選取 **[下一步]** 以移至 [ **審核** ] 頁面。 [評論] 頁面會顯示作業設定詳細資料。 選取 [ **執行** ] 以提交作業。

    :::image type="content" source="media/howto-run-a-job/job-wizard-review.png" alt-text="工作 wizard 審核頁面的螢幕擷取畫面":::

1. 作業會經歷 *暫*止、 *正在*執行和 *已完成* 的階段。 作業執行詳細資料包含結果計量、持續時間詳細資料和裝置清單方格。

    當作業完成時，您可以選取 [ **結果記錄** 檔] 來下載作業詳細資料的 CSV 檔案，包括裝置及其狀態值。 此資訊有助於進行疑難排解。

    :::image type="content" source="media/howto-run-a-job/download-details.png" alt-text="顯示裝置狀態的螢幕擷取畫面":::

1. 作業現在會出現在 [**工作**] 頁面上的 [**過去30天**] 清單中。 此頁面會顯示目前正在執行的作業，以及任何先前執行或儲存之作業的歷程記錄。

    > [!NOTE]
    > 您可以為先前執行的作業查看30天的歷程記錄。

## <a name="manage-jobs"></a>管理工作

若要停止執行中的作業，請開啟它並選取 [ **停止**]。 作業狀態會變更，以反映作業已停止。 [ **摘要** ] 區段會顯示哪些裝置已完成、失敗或仍處於擱置狀態。

:::image type="content" source="media/howto-run-a-job/manage-job.png" alt-text="顯示執行中作業和停止作業之按鈕的螢幕擷取畫面":::

當工作處於 [已停止] 狀態時，您可以選取 [繼續] 繼續 **執行** 作業。 作業狀態會變更，以反映作業現在正在執行。 **摘要**區段會繼續以最新的進度更新。

:::image type="content" source="media/howto-run-a-job/stopped-job.png" alt-text="顯示已停止工作的螢幕擷取畫面，以及繼續作業的按鈕":::

## <a name="copy-a-job"></a>複製作業

若要複製現有的作業，請選取已執行的作業。 選取 [作業結果] 頁面或 [作業詳細資料] 頁面上的 [ **複製** ]：

:::image type="content" source="media/howto-run-a-job/job-details-copy.png" alt-text="顯示 [複製] 按鈕的螢幕擷取畫面":::

作業設定的複本會開啟供您編輯，並 **將複製** 附加至作業名稱。

## <a name="view-job-status"></a>檢視作業狀態

建立作業之後，[ **狀態** ] 資料行會以最新的作業狀態訊息更新。 下表列出可能的 *作業狀態值* ：

| 狀態訊息       | 狀態意義                                          |
| -------------------- | ------------------------------------------------------- |
| 已完成            | 此工作會在所有裝置上執行。              |
| Failed               | 此工作失敗，且未在裝置上完整執行。  |
| Pending              | 此作業尚未在裝置上開始執行。         |
| 執行中              | 這項作業目前正在裝置上執行。             |
| 已停止              | 使用者已手動停止此作業。           |

狀態訊息後面會接著工作中的裝置總覽。 下表列出可能的 *裝置狀態值* ：

| 狀態訊息       | 狀態意義                                                     |
| -------------------- | ------------------------------------------------------------------ |
| 成功            | 作業執行成功的裝置數目。       |
| Failed               | 作業執行失敗的裝置數目。       |

若要查看作業的狀態和所有受影響的裝置，請開啟作業。 在每個裝置名稱旁邊，您會看到下列其中一個狀態訊息：

| 狀態訊息       | 狀態意義                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| 已完成            | 此工作已在此裝置上執行。                                     |
| Failed               | 作業無法在此裝置上執行。 錯誤訊息會顯示詳細資訊。  |
| Pending              | 作業尚未在此裝置上執行。                                   |

若要下載包含作業詳細資料的 CSV 檔案，以及裝置的清單及其狀態值，請選取 [ **結果記錄**檔]。

## <a name="filter-the-device-list"></a>篩選裝置清單

您可以藉由選取篩選圖示來篩選 [ **作業詳細資料** ] 頁面上的裝置清單。 您可以篩選 [ **裝置識別碼** ] 或 [ **狀態** ] 欄位：

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="顯示篩選裝置清單之選項的螢幕擷取畫面。":::

## <a name="customize-columns-in-the-device-list"></a>自訂裝置清單中的資料行

您可以藉由選取 [資料行選項] 圖示，將資料行新增至裝置清單：

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="顯示資料行選項圖示的螢幕擷取畫面。":::

使用 [資料 **行選項** ] 對話方塊來選擇裝置清單資料行。 選取您要顯示的資料行，選取向右箭號，然後選取 **[確定]**。 若要選取所有可用的資料行，請選擇 [ **全選**]。 選取的資料行會出現在裝置清單中。

選取的資料行會保存在使用者會話中，或跨具有應用程式存取權的使用者會話。

## <a name="rerun-jobs"></a>重新執行作業

您可以重新執行具有失敗裝置的工作。 選取 [ **在失敗時重新**執行]：

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="顯示在失敗的裝置上重新執行作業之按鈕的螢幕擷取畫面。":::

輸入作業名稱和描述，然後選取 [ **重新執行作業**]。 提交新的工作，以在失敗的裝置上重試動作。

> [!NOTE]
> 您無法從 Azure IoT Central 應用程式同時執行五個以上的作業。
>
> 當作業完成，而且您刪除了工作裝置清單中的裝置時，裝置專案會在裝置名稱中顯示為已刪除。 [詳細資料] 連結不適用於已刪除的裝置。

## <a name="next-steps"></a>接下來的步驟

現在您已瞭解如何在 Azure IoT Central 應用程式中建立工作，以下是一些後續步驟：

- [管理您的裝置](howto-manage-devices.md)
- [建立裝置範本版本](howto-version-device-template.md)
