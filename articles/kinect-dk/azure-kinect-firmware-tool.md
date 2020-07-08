---
title: Azure Kinect 韌體工具
description: 瞭解如何使用 Azure Kinect 固件工具來查詢和更新裝置固件。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect、固件、更新
ms.openlocfilehash: f8b89751d27fb5a4b18d635f45f63f4f36bd05f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276498"
---
# <a name="azure-kinect-dk-firmware-tool"></a>Azure Kinect DK 固件工具

Azure Kinect 固件工具可以用來查詢和更新 Azure Kinect DK 的裝置固件。

## <a name="list-connected-devices"></a>列出已連線的裝置

您可以使用-l 選項來取得已連線裝置的清單。  `AzureKinectFirmwareTool.exe -l`

```console
 == Azure Kinect DK Firmware Tool ==
Found 2 connected devices:
0: Device "000036590812"
1: Device "000274185112"
```

## <a name="check-device-firmware-version"></a>檢查裝置固件版本

您可以使用-q 選項來檢查第一個連接裝置的目前固件版本，例如 `AzureKinectFirmwareTool.exe -q` 。

```console
 == Azure Kinect DK Firmware Tool ==
Device Serial Number: 000036590812
Current Firmware Versions:
    RGB camera firmware:      1.5.92
    Depth camera firmware:    1.5.66
    Depth config file:        6109.7
    Audio firmware:           1.5.14
    Build Config:             Production
    Certificate Type:         Microsoft
```

如果已連接多個裝置，您可以在命令中新增完整的序號，以指定您想要查詢的裝置，例如：

`AzureKinectFirmwareTool.exe -q 000036590812`

## <a name="update-device-firmware"></a>更新裝置的軔體

這項工具最常見的用法是更新裝置固件。 使用選項呼叫工具來進行更新 `-u` 。 固件更新可能需要幾分鐘的時間，視必須更新的固件檔案而定。

如需逐步執行固件更新指示，請參閱[Azure Kinect 固件更新](update-device-firmware.md)。  

`AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin`

如果已連接多個裝置，您可以在命令中新增完整的序號，以指定您想要查詢的裝置。

`AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin 000036590812`

## <a name="reset-device"></a>重設裝置

如果您必須將裝置設為已知狀態，可以使用-r 選項來重設附加的 Azure Kinect DK。

如果已連接多個裝置，您可以在命令中新增完整的序號，以指定您想要查詢的裝置。

`AzureKinectFirmwareTool.exe -r 000036590812`

## <a name="inspect-firmware"></a>檢查固件

檢查「固件」可讓您從「固件」 bin 檔案取得版本資訊，然後才更新實際裝置。

`AzureKinectFirmwareTool.exe -i firmware\AzureKinectDK_Fw_1.5.926614.bin`

```console
 == Azure Kinect DK Firmware Tool ==
Loading firmware package ..\tools\updater\firmware\AzureKinectDK_Fw_1.5.926614.bin.
File size: 1228844 bytes
This package contains:
  RGB camera firmware:      1.5.92
  Depth camera firmware:    1.5.66
  Depth config files: 6109.7 5006.27
  Audio firmware:           1.5.14
  Build Config:             Production
  Certificate Type:         Microsoft
  Signature Type:           Microsoft
```

## <a name="firmware-update-tool-options"></a>[固件更新工具] 選項

```console
 == Azure Kinect DK Firmware Tool ==
* Usage Info *
    AzureKinectFirmwareTool.exe <Command> <Arguments>

Commands:
    List Devices: -List, -l
    Query Device: -Query, -q
        Arguments: [Serial Number]
    Update Device: -Update, -u
        Arguments: <Firmware Package Path and FileName> [Serial Number]
    Reset Device: -Reset, -r
        Arguments: [Serial Number]
    Inspect Firmware: -Inspect, -i
        Arguments: <Firmware Package Path and FileName>

    If no Serial Number is provided, the tool will just connect to the first device.

Examples:
    AzureKinectFirmwareTool.exe -List
    AzureKinectFirmwareTool.exe -Update c:\data\firmware.bin 0123456
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
>[更新裝置固件的逐步指示](update-device-firmware.md)
