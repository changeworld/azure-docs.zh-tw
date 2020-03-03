---
title: 快速入門：建立 Unity Android 應用程式
description: 在本快速入門中，您將了解如何使用 Spatial Anchors 建置搭配 Unity 的 Android 應用程式。
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 7acff7f0249cdedcebd367fc315be92cafb9ab78
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77615449"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>快速入門：使用 Azure Spatial Anchors 來建立 Unity Android 應用程式

本快速入門涵蓋如何使用 [Azure Spatial Anchors](../overview.md) 來建立 Unity Android 應用程式。 Azure Spatial Anchors 是一款跨平台開發人員服務，可讓您使用在一段時間之後仍跨裝置保持其位置的物件，建立混合實境體驗。 當您完成時，您將會有搭配 Unity 建置的 ARCore Android 應用程式，可用來儲存和回收空間錨點。

您將學習如何：

> [!div class="checklist"]
> * 建立 Spatial Anchors 帳戶
> * 準備 Unity 組建設定
> * 設定 Spatial Anchors 帳戶識別碼和帳戶金鑰
> * 匯出 Android Studio 專案
> * 在 Android 裝置上部署和執行

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

若要完成本快速入門，請確定您具備︰

- 具有 <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1 或 2019.2</a> 的 Windows 或 macOS 機器，包括 Android Build 支援和 Android SDK & NDK 工具模組。
  - 如果在 Windows 上執行，您也需要 <a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a> 和 <a href="https://git-lfs.github.com/">Git LFS</a>。
  - 如果在 macOS 上執行，請透過 HomeBrew 安裝 Git。 在終端機的單一行輸入以下命令：`/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`。 然後執行 `brew install git` 和 `brew install git-lfs`。
- <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">由開發人員啟用</a>且<a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">具備 ARCore 功能</a>的 Android 裝置。
  - 您的電腦可能需要其他設備磁碟機，才能與您的 Android 裝置通訊。 如需詳細資訊和指示，請參閱[這裡](https://developer.android.com/studio/run/device.html)。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>下載 Unity 範例專案並且開啟

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>設定帳戶識別碼和金鑰

在 [專案]  窗格中瀏覽至 `Assets/AzureSpatialAnchors.Examples/Scenes`，然後開啟 `AzureSpatialAnchorsBasicDemo.unity` 場景檔案。

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

選取 [檔案]   -> [儲存]  以儲存場景。

## <a name="export-the-android-studio-project"></a>匯出 Android Studio 專案

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

在 [執行裝置]  中選取您的裝置，然後按一下 [建立並執行]  。 系統會要求您儲存 `.apk` 檔案，您可以為其選擇任何名稱。

依照應用程式中的指示放置及回收錨點。

## <a name="troubleshooting"></a>疑難排解

### <a name="rendering-issues"></a>轉譯問題

在執行應用程式時，如果您看到的背景不是相機 (例如，您看到空白、藍色或其他紋理的背景)，則可能需要在 Unity 中重新匯入資產。 請停止應用程式。 在 Unity 的頂端功能表中，選擇 [資產] -> [全部重新匯入]  。 然後，再次執行應用程式。

### <a name="unity-20193"></a>Unity 2019.3

由於有重大變更，目前不支援 Unity 2019.3。 請使用 Unity 2019.1 或 2019.2。

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [教學課程：跨裝置共用 Spatial Anchors](../tutorials/tutorial-share-anchors-across-devices.md)
