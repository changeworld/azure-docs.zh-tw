---
title: 快速入門 - 設定 Azure Kinect 全身追蹤
description: 在本快速入門中，您將設定適用於 Azure Kinect 的全身追蹤 SDK。
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: kinect, azure, 感應器, 存取, 景深, sdk, 全身, 追蹤, 關節, 設定, cuda, nvidia
ms.openlocfilehash: 2cf4c1097730f88fc4bd66c28e1bdddd7fea8640
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "85277777"
---
# <a name="quickstart-set-up-azure-kinect-body-tracking"></a>快速入門：設定 Azure Kinect 全身追蹤

本快速入門將引導您完成在 Azure Kinect DK 上執行全身追蹤的程序。

## <a name="system-requirements"></a>系統需求

全身追蹤 SDK 需要在主機電腦上安裝 NVIDIA GPU。 [系統需求](system-requirements.md)頁面中會描述建議的全身追蹤主機電腦需求。

## <a name="install-software"></a>安裝軟體

### <a name="install-the-latest-nvidia-driver"></a>[安裝最新的 NVIDIA 驅動程式](https://www.nvidia.com/Download/index.aspx?lang=en-us)

下載並安裝適用於您圖形卡的最新 NVIDIA 驅動程式。 較舊的驅動程式可能無法與使用全身追蹤 SDK 時所轉散發的 CUDA 二進位檔相容。

### <a name="visual-c-redistributable-for-visual-studio-2015"></a>[適用於 Visual Studio 2015 的 Visual C++ 可轉散發套件](https://www.microsoft.com/en-us/download/details.aspx?id=48145)

下載和安裝適用於 Visual Studio 2015 的 Visual C++ 可轉散發套件。 

## <a name="set-up-hardware"></a>設定硬體

### <a name="set-up-azure-kinect-dk"></a>[設定 Azure Kinect DK](set-up-azure-kinect-dk.md)

啟動 [Azure Kinect 檢視器](azure-kinect-viewer.md)，以檢查 Azure Kinect DK 是否已正確設定。

## <a name="download-the-body-tracking-sdk"></a>下載全身追蹤 SDK
 
1. 選取連結以[下載全身追蹤 SDK](body-sdk-download.md)
2. 在您的電腦上安裝全身追蹤 SDK。

## <a name="verify-body-tracking"></a>驗證全身追蹤

啟動 **Azure Kinect 全身追蹤檢視器**，以檢查是否已正確設定全身追蹤 SDK。 此檢視器會隨 SDK msi 安裝程式一起安裝。 您可以在 [開始] 功能表或 `<SDK Installation Path>\tools\k4abt_simple_3d_viewer.exe` 中找到此檢視器。

如果您的 GPU 功能不夠強大，但仍想要測試結果，則可以透過下列命令，在命令列中啟動 **Azure Kinect 全身追蹤檢視器**：`<SDK Installation Path>\tools\k4abt_simple_3d_viewer.exe CPU`

如果一切都已正確設定，則應該會出現一個視窗，內有 3D 點雲和已追蹤的身體。


![全身追蹤 3D 檢視器](./media/quickstarts/samples-simple3dviewer.png)

## <a name="examples"></a>範例

您可以在[這裡](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples)找到如何使用全身追蹤 SDK 的範例。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
>[建立第一個全身追蹤應用程式](build-first-body-app.md)

