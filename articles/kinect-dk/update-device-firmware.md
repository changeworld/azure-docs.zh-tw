---
title: 更新 Azure Kinect DK 固件
description: 瞭解如何使用 Azure Kinect 固件工具更新 Azure Kinect DK 裝置固件。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect、固件、更新、修復
ms.openlocfilehash: eb60003a4233110cb33208bcb8e9784737bb2a8b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276517"
---
# <a name="update-azure-kinect-dk-firmware"></a>更新 Azure Kinect DK 固件

本檔提供如何在 Azure Kinect DK 上更新裝置固件的指導方針。

Azure Kinect DK 不會自動更新固件。 您可以使用[Azure Kinect 固件工具](azure-kinect-firmware-tool.md)，以手動方式將固件更新為最新的可用版本。

## <a name="prepare-for-firmware-update"></a>準備固件更新

1. [下載 SDK](sensor-sdk-download.md)。
2. 安裝 SDK。
3. 在 SDK 安裝位置的 [（SDK 安裝位置）] \tools\ 底下，您應該會發現：

    - AzureKinectFirmwareTool.exe
    - [固件] 資料夾中的 [固件] 檔案，例如*AzureKinectDK_Fw_1. 5.926614. bin*。

4. 將您的裝置連接到主機電腦，並同時開啟它的電源。

> [!IMPORTANT]
> 在固件更新期間，讓 USB 和電源供應器保持連接。 在更新期間移除任一連線，可能會讓固件進入損毀狀態。

## <a name="update-device-firmware"></a>更新裝置的軔體

1. 在 [（SDK 安裝位置）] \tools\ 資料夾中開啟命令提示字元。
2. 使用 Azure Kinect 固件工具更新固件

    `AzureKinectFirmwareTool.exe -u <device_firmware_file.bin>`

    範例：

    `AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin`

3. 等到固件更新完成。 視影像大小而定，可能需要幾分鐘的時間。

### <a name="verify-device-firmware-version"></a>驗證裝置固件版本

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

4. 在固件更新之後，您可以執行[Azure Kinect viewer](azure-kinect-viewer.md)來確認所有感應器是否如預期般運作。

## <a name="troubleshooting"></a>疑難排解

可能有幾個原因導致固件更新失敗。 當固件更新失敗時，請嘗試下列緩和措施步驟：

1. 第二次嘗試執行 [固件更新] 命令。

2. 藉由查詢固件版本來確認裝置是否仍在連線中。        AzureKinectFirmareTool.exe

3. 如果其他所有動作都失敗，請遵循[復原步驟來](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk)還原為原廠固件，然後再試一次。

如有任何其他問題，請參閱[Microsoft 支援頁面](https://aka.ms/kinectsupport)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
>[Azure Kinect 固件工具](azure-kinect-firmware-tool.md)
