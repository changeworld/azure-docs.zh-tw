---
title: 在 Azure IoT Central 應用程式中建立及執行工作 | Microsoft Docs
description: Azure IoT Central 作業允許大量裝置管理功能，例如更新屬性或執行命令。
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/08/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 2dd1eddc841cc484957c2124de3419799c4e59b7
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206779"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>在您的 Azure IoT Central 應用程式中建立及執行作業

您可以使用 Microsoft Azure IoT Central 利用工作來大規模管理您的已連線裝置。 作業可讓您對裝置屬性進行大量更新，並執行命令。 本文說明如何在您自己的應用程式中開始使用作業。

## <a name="create-and-run-a-job"></a>建立及執行工作

本節說明如何建立及執行工作。 它會示範如何為一組羅吉斯閘道裝置設定光源閾值。

1. 從左窗格中，流覽至 [**作業**]。

2. 選取 [ **+ 新增**] 以建立新的作業：

    > [!div class="mx-imgBorder"]
    > ![建立新作業](./media/howto-run-a-job/createnewjob.png)

3. 輸入 [名稱] 和 [描述] 來識別您要建立的作業。

4. 選取您想要將作業套用至其中的目標裝置群組。 您可以在 [**摘要**] 區段中查看您的作業設定適用的裝置數目。

5. 接下來，選擇 [**屬性**] 或 [**命令**] 做為要設定的作業類型。 若要設定**屬性**作業設定，請選取屬性，並設定其新值。 若要設定**命令**，或選擇要執行的命令。 屬性作業可以設定多個屬性：

    > [!div class="mx-imgBorder"]
    > ![設定作業](./media/howto-run-a-job/configurejob.png)

6. 選取您的裝置之後，請選擇 [**執行**] 或 [**儲存**]。 作業現在會出現在您的主要 [**作業**] 頁面上。 在此頁面上，您可以看到目前正在執行的作業，以及任何先前執行或儲存之作業的歷程記錄。 您已儲存的作業可以隨時重新開啟，以繼續進行編輯或執行：

    > [!div class="mx-imgBorder"]
    > ![檢視作業](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > 您可以針對先前執行的作業，查看30天的歷程記錄。

7. 若要取得作業的總覽，請從清單中選取要查看的作業。 此總覽包含作業詳細資料、裝置和裝置狀態值。 在此總覽中，您也可以選取 [**下載工作詳細資料**] 來下載作業詳細資料的 CSV 檔案，包括裝置及其狀態值。 這種資訊對疑難排解很有用：

    > [!div class="mx-imgBorder"]
    > ![查看裝置狀態](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>停止執行中的工作

若要停止其中一個執行中的作業，請開啟它，然後選取 [**停止**]。 作業狀態會變更以反映作業已停止。 [**摘要**] 區段會顯示哪些裝置已完成、失敗或仍處於擱置狀態：

    > [!div class="mx-imgBorder"]
    > ![Stop job](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>執行已停止的作業

若要執行目前已停止的作業，請選取它，然後選取 [**執行**]。 作業狀態會變更，以反映作業現在正在執行。 [**摘要**] 區段會繼續以最新的進度更新：

    > [!div class="mx-imgBorder"]
    > ![Resumed job](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>複製作業

若要複製其中一個現有的作業，請開啟它，然後選取 [**複製**]。 [作業設定] 的複本隨即開啟供您編輯，而 [**複製**] 則會附加至工作名稱的結尾。 您可以儲存或執行新的作業：

    > [!div class="mx-imgBorder"]
    > ![Copy job](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>檢視工作狀態

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

### <a name="view-the-device-status"></a>檢視裝置狀態

若要查看作業和所有受影響裝置的狀態，請選取該作業。 若要下載包含作業詳細資料的 CSV 檔案（包括裝置清單及其狀態值），請選取 [**下載作業詳細資料**]。 在每個裝置名稱旁邊，您會看到下列其中一則狀態訊息：

| 狀態訊息       | 狀態意義                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Completed            | 工作已在此裝置上執行。                                     |
| 失敗               | 工作在此裝置上執行失敗。 錯誤訊息會顯示詳細資訊。  |
| Pending              | 作業尚未在此裝置上執行。                                   |

> [!NOTE]
> 如果裝置已被刪除，您就無法選取該裝置。 它會以裝置識別碼顯示為已刪除。

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何在 Azure IoT Central 應用程式中建立作業，以下是一些後續步驟：

- [管理您的裝置](howto-manage-devices.md)
- [建立裝置範本版本](howto-version-device-template.md)
