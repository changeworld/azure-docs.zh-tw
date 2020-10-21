---
title: 快速入門：使用 Unity 建立 HoloLens 應用程式
description: 在本快速入門中，您將了解如何使用 Spatial Anchors 建置搭配 Unity 的 HoloLens 應用程式。
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 09/29/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 143bc89518ff1811ad6789f71fcf7a9267e99f73
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92097434"
---
# <a name="quickstart-create-a-unity-hololens-app-that-uses-azure-spatial-anchors"></a>快速入門：建立使用 Azure 空間錨點的 Unity HoloLens 應用程式

在本快速入門中，您將建立使用 [Azure 空間錨點](../overview.md)的 Unity HoloLens 應用程式。 Spatial Anchors 是一款跨平台開發人員服務，可讓您使用在一段時間之後仍跨裝置保持其位置的物件，建立混合式實境體驗。 當您完成時，您將會有搭配 Unity 建置的 HoloLens 應用程式，並可儲存和回收空間錨點。

您將學習如何：

- 建立 Spatial Anchors 帳戶。
- 準備 Unity 組建設定。
- 設定 Spatial Anchors 帳戶識別碼和帳戶金鑰。
- 匯出 HoloLens Visual Studio 專案。
- 在 HoloLens 裝置上部署應用程式並加以執行。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

若要完成本快速入門：

- 您需要一部已安裝 <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.4 (LTS)</a> 以及 <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> 或更新版本的 Windows 電腦。 您的 Visual Studio 安裝必須包含**通用 Windows 平台開發**工作負載和 **Windows 10 SDK (10.0.18362.0 或更新版本)** 元件。 您也必須安裝 <a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a> 和 <a href="https://git-lfs.github.com/">Git LFS</a>。
- 您需要一個已啟用[開發人員模式](/windows/mixed-reality/using-visual-studio)的 HoloLens 裝置。 [Windows 10 2020 年 5 月更新](/windows/mixed-reality/whats-new/release-notes-may-2020)必須安裝在裝置上。 若要在 HoloLens 上更新至最新版本，請開啟**設定**應用程式，移至 [更新與安全性]  ，然後選取 [檢查更新]  。
- 在您的應用程式中，您必須啟用 **SpatialPerception** 功能。 此設定位於 [組建設定]   >  [播放器設定]   >  [發行設定]   >  [功能]  中。
- 在您的應用程式中，您必須以 [Windows Mixed Reality SDK]  啟用 [虛擬實境支援]  。 此設定位於 [組建設定]   >  [播放器設定]   >  [XR設定]  中。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>下載 Unity 範例專案並且開啟

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

藉由選取 [檔案]   > [組建設定]  來開啟 [組建設定]  。

在 [平台]  區段中，選取 [通用 Windows 平台]  。 將 [目標裝置]  變更為 [HoloLens]  。

選取 [切換平台]  ，將平台變更為 [通用 Windows 平台]  。 Unity 可能會提示您安裝 UWP 支援元件 (如果元件遺失的話)。

![Unity 組建設定視窗](./media/get-started-unity-hololens/unity-build-settings.png)

關閉 [組建設定]  視窗。

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-hololens-visual-studio-project"></a>匯出 HoloLens Visual Studio 專案

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

選取 [組建]  。 在對話方塊中，選取要在其中匯出 HoloLens Visual Studio 專案的資料夾。

匯出完成時，會顯示含有匯出的 HoloLens 專案的資料夾。

## <a name="deploy-the-hololens-application"></a>部署 HoloLens 應用程式

在資料夾中，按兩下 [HelloAR U3D.sln]  ，以在 Visual Studio 中開啟專案。

將 [方案組態]  變更為 [發行]  ，並將 [方案平台]  變更為 [x86]  ，然後從部署目標選項中選取 [裝置]  。

如果使用 HoloLens 2，請使用 **ARM64** 作為**解決方案平台**，而非使用 **x86**。

   ![Visual Studio 設定](./media/get-started-unity-hololens/visual-studio-configuration.png)

開啟 HoloLens 裝置並登入，然後使用 USB 纜線將該裝置連接到電腦。

選取 [偵錯]   > [開始偵錯]  來部署您的應用程式並開始偵錯。

在應用程式中，使用箭頭選取 **BasicDemo**，然後按下 [Go!] 按鈕來執行示範。 依照指示來放置及回收錨點。

![螢幕擷取畫面 1](./media/get-started-unity-hololens/screenshot-1.jpg)
![螢幕擷取畫面 2](./media/get-started-unity-hololens/screenshot-2.jpg)
![螢幕擷取畫面 3](./media/get-started-unity-hololens/screenshot-3.jpg)
![螢幕擷取畫面 4](./media/get-started-unity-hololens/screenshot-4.jpg)

在 Visual Studio 中，透過選取 [停止偵錯]  或按下 Shift + F5 來停止應用程式。

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [教學課程：跨裝置共用空間錨點](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [How To:在 Unity 專案中設定 Azure Spatial Anchors](../how-tos/setup-unity-project.md)