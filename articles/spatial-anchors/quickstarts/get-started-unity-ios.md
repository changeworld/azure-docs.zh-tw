---
title: 快速入門：建立 Unity iOS 應用程式
description: 在本快速入門中，您將了解如何使用 Spatial Anchors 建置搭配 Unity 的 iOS 應用程式。
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: b278ac6c824b1583e90cfc9152264f61357dd228
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95971522"
---
# <a name="quickstart-create-a-unity-ios-app-with-azure-spatial-anchors"></a>快速入門：使用 Azure 空間錨點來建立 Unity iOS 應用程式

本快速入門涵蓋如何使用 [Azure 空間錨點](../overview.md)來建立 Unity iOS 應用程式。 Azure Spatial Anchors 是一款跨平台開發人員服務，可讓您使用在一段時間之後仍跨裝置保持其位置的物件，建立混合實境體驗。 當您完成時，您將會有搭配 Unity 建置的 ARKit iOS 應用程式，可用來儲存和回收空間錨點。

您將學習如何：

> [!div class="checklist"]
> * 建立 Spatial Anchors 帳戶
> * 準備 Unity 組建設定
> * 設定 Spatial Anchors 帳戶識別碼和帳戶金鑰
> * 匯出 Xcode 專案
> * 在 iOS 裝置上部署和執行

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

若要完成本快速入門，請確定您具備︰

- 已安裝 <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.4 (LTS)</a>、最新版本 <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> 的 macOS 電腦。
- 透過 HomeBrew 安裝的 Git。 在終端機的單一行輸入以下命令：`/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`。 然後執行 `brew install git` 和 `brew install git-lfs`。
- 由開發人員啟用且<a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">與 ARKit 相容</a>的 iOS 裝置。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>下載 Unity 範例專案並且開啟

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-xcode-project"></a>匯出 Xcode 專案

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

在應用程式中，使用箭頭選取 **BasicDemo**，然後按下 [Go!] 按鈕來執行示範。 依照指示來放置及回收錨點。

![螢幕擷取畫面 1](./media/get-started-unity-ios/screenshot-1.jpg)
![螢幕擷取畫面 2](./media/get-started-unity-ios/screenshot-2.jpg)
![螢幕擷取畫面 3](./media/get-started-unity-ios/screenshot-3.jpg)

完成時，在 Xcode 中按 [停止] 即可停止應用程式。

## <a name="troubleshooting"></a>疑難排解

### <a name="rendering-issues"></a>轉譯問題

在執行應用程式時，如果您看到的背景不是相機 (例如，您看到空白、藍色或其他紋理的背景)，則可能需要在 Unity 中重新匯入資產。 請停止應用程式。 在 Unity 的頂端功能表中，選擇 [資產] -> [全部重新匯入]。 然後，再次執行應用程式。

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [教學課程：跨裝置共用 Spatial Anchors](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [How To:在 Unity 專案中設定 Azure Spatial Anchors](../how-tos/setup-unity-project.md)
