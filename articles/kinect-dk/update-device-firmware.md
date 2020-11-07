---
title: 更新 Azure Kinect DK 固件
description: 瞭解如何使用 Azure Kinect 固件工具更新 Azure Kinect DK 裝置固件。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect、固件、更新、復原
ms.openlocfilehash: 71557798a584635356cc47fd7c4a0309df4d018a
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94356216"
---
# <a name="update-azure-kinect-dk-firmware"></a>更新 Azure Kinect DK 固件

本檔提供如何更新 Azure Kinect DK 上的裝置固件的指引。

Azure Kinect DK 不會自動更新固件。 您可以使用 [Azure Kinect 固件工具](azure-kinect-firmware-tool.md) ，以手動方式將固件更新至最新的可用版本。

## <a name="prepare-for-firmware-update"></a>準備進行固件更新

1. [下載 SDK](sensor-sdk-download.md)。
2. 安裝 SDK。
3. 在 SDK 安裝位置的 (SDK 安裝位置) \tools\ 下，您應該會發現：

    - AzureKinectFirmwareTool.exe
    - 在 [固件] 資料夾中的5.926614，例如 *AzureKinectDK_Fw_1.。*

4. 將您的裝置連接到主機電腦，並同時將其開機。

> [!IMPORTANT]
> 在固件更新期間保持 USB 和電源供應器的連線。 在更新期間移除任一個連線可能會讓固件處於損毀狀態。

## <a name="update-device-firmware"></a>更新裝置的軔體

1. 在 (SDK 安裝位置) \tools\ 資料夾中開啟命令提示字元。
2. 使用 Azure Kinect 固件工具更新固件

    `AzureKinectFirmwareTool.exe -u <device_firmware_file.bin>`

    範例：

    `AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin`

3. 等候固件更新完成。 這可能需要幾分鐘的時間，視映射大小而定。

### <a name="verify-device-firmware-version"></a>確認裝置固件版本

1. 確認已更新固件。

    `AzureKinectFirmwareTool.exe -q`

2. 請參閱下列範例。

    ```console
       >AzureKinectFirmwareTool.exe -q
       == Azure Kinect DK Firmware Tool ==
        Device Serial Number: 000805192412
        Current Firmware Versions:
        RGB camera firmware:      1.6.102
        Depth camera firmware:    1.6.75
        Depth config file:        6109.7
        Audio firmware:           1.6.14
        Build Config:             Production
        Certificate Type:         Microsoft
    ```

3. 如果您看到上述輸出，則會更新您的固件。

4. 在固件更新之後，您可以執行 [Azure Kinect 檢視器](azure-kinect-viewer.md) 來確認所有感應器都如預期般運作。

## <a name="troubleshooting"></a>疑難排解

有幾個原因可能會導致固件更新失敗。 當固件更新失敗時，請嘗試下列風險降低步驟：

1. 再試一次執行 [固件更新] 命令。

2. 藉由查詢固件版本來確認裝置仍保持連線。        AzureKinectFirmareTool.exe

3. 如果所有其他失敗，請遵循 [復原步驟來](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk) 還原為出廠預設值，然後再試一次。

如有任何其他問題，請參閱 [Microsoft 支援頁面](./index.yml)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
>[Azure Kinect 固件工具](azure-kinect-firmware-tool.md)