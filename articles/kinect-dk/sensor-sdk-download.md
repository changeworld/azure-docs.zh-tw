---
title: Azure Kinect Sensor SDK 下載
description: 了解如何在 Windows 和 Linux 上下載並安裝 Azure Kinect Sensor SDK。
author: Brent-A
ms.author: brenta
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: azure, kinect,sdk, 下載更新, 最新, 可用, 安裝
ms.openlocfilehash: 48a3df3962796cf561057e8ec85b754c974200f4
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358500"
---
# <a name="azure-kinect-sensor-sdk-download"></a>Azure Kinect Sensor SDK 下載

此頁面包含每個 Azure Kinect Sensor SDK 版本的下載連結。 安裝程式會提供針對 Azure Kinect 進行開發所需的所有檔案。

## <a name="azure-kinect-sensor-sdk-contents"></a>Azure Kinect Sensor SDK 內容

- 用來使用 Azure Kinect DK 建置應用程式的標頭和程式庫。
- 使用 Azure Kinect DK 的應用程式所需的可轉散發 DLL。
- [Azure Kinect Viewer](azure-kinect-viewer.md)。
- [Azure Kinect Recorder](azure-kinect-recorder.md)。
- [Azure Kinect Firmware Tool](azure-kinect-firmware-tool.md)。

## <a name="windows-download-link"></a>Windows 下載連結

[Microsoft Installer](https://download.microsoft.com/download/3/d/6/3d6d9e99-a251-4cf3-8c6a-8e108e960b4b/Azure%20Kinect%20SDK%201.4.1.exe) | [GitHub 原始程式碼](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1093)

> [!NOTE]
> 安裝 SDK 時，請記住您安裝的路徑。 例如，"C:\Program Files\Azure Kinect SDK 1.2"。 您會在此路徑中找到文章所參考的工具。

您可以在 [GitHub](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md) 上找到先前版本的 Azure Kinect Sensor SDK 和 Firmware。

## <a name="linux-installation-instructions"></a>Linux 安裝指示

目前，唯一支援的發行版本為 Ubuntu 18.04。 若要要求支援其他發行版本，請參閱[此頁面](https://aka.ms/azurekinectfeedback)。

首先，您必須遵循[這裡](/windows-server/administration/linux-package-repository-for-microsoft-software)的指示，設定 [Microsoft 的套件存放庫](https://packages.microsoft.com/)。

現在您可以安裝需要的套件。 `k4a-tools` 套件包含 [Azure Kinect Viewer](azure-kinect-viewer.md)、[Azure Kinect Recorder](record-sensor-streams-file.md) 和 [Azure Kinect Firmware Tool](azure-kinect-firmware-tool.md)。 若要安裝它，請執行

 `sudo apt install k4a-tools`

 `libk4a<major>.<minor>-dev` 套件包含要針對 `libk4a` 建置的標頭和 CMake 檔案。
`libk4a<major>.<minor>` 套件包含執行依存於 `libk4a` 的可執行檔所需的共用物件。

 基本教學課程需要 `libk4a<major>.<minor>-dev` 套件。 若要安裝它，請執行

 `sudo apt install libk4a1.1-dev`

如果命令成功，SDK 就可供使用。

## <a name="change-log-and-older-versions"></a>變更記錄和較舊的版本

您可以在[這裡](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/CHANGELOG.md)找到 Azure Kinect Sensor SDK 的變更記錄。

如果您需要較舊版本的 Azure Kinect Sensor SDK，您可以在[這裡](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md)找到。

## <a name="next-steps"></a>後續步驟

[設定 Azure Kinect DK](set-up-azure-kinect-dk.md)