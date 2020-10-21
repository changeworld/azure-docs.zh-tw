---
title: 快速入門：建立 Xamarin iOS 應用程式
description: 在本快速入門中，您將了解如何使用 Spatial Anchors 建置搭配 Xamarin 的 iOS 應用程式。
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 610112f9374f2ca39a8df4b849aeda4abf965e57
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92097417"
---
# <a name="quickstart-create-a-xamarin-ios-app-with-azure-spatial-anchors"></a>快速入門：使用 Azure Spatial Anchors 建立 Xamarin iOS 應用程式

本快速入門說明如何使用 [Azure Spatial Anchors](../overview.md) 建立搭配 Xamarin 的 iOS 應用程式。 Azure Spatial Anchors 是一款跨平台開發人員服務，可讓您使用在一段時間之後仍跨裝置保持其位置的物件，建立混合實境體驗。 完成作業後，您將會有可儲存和回收空間錨點的 iOS 應用程式。

您將學習如何：

> [!div class="checklist"]
> * 建立 Spatial Anchors 帳戶
> * 設定 Spatial Anchors 帳戶識別碼和帳戶金鑰
> * 在 iOS 裝置上部署和執行

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

若要完成本快速入門，請確定您具備︰
- 執行 macOS High Sierra (10.13) 或以上版本、且具有下列項目的 Mac：
  - 從 [App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12) 安裝的最新版 Xcode 和 iOS SDK。
  - 最新版的 <a href="/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Visual Studio for Mac 8.1+</a>。
  - <a href="https://git-scm.com/download/mac" target="_blank">Git for macOS</a>。
  - <a href="https://git-lfs.github.com/">Git LFS</a>。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>開啟範例專案

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

在 Visual Studio 中開啟 `Xamarin/SampleXamarin.sln`。

## <a name="configure-account-identifier-and-key"></a>設定帳戶識別碼和金鑰

下一個步驟是將應用程式設定為使用您的帳戶識別碼和帳戶金鑰。 [設定空間錨點資源](#create-a-spatial-anchors-resource)時，將它們複製到文字編輯器中。

開啟 `Xamarin/SampleXamarin.Common/AccountDetails.cs`。

找出 `SpatialAnchorsAccountKey` 欄位，並將 `Set me` 取代為帳戶金鑰。

找出 `SpatialAnchorsAccountId` 欄位，並將 `Set me` 取代為帳戶識別碼。

找出 `SpatialAnchorsAccountDomain` 欄位，並將 `Set me` 取代為帳戶網域。

## <a name="deploy-the-app-to-your-ios-device"></a>將應用程式部署到您的 iOS 裝置

將 iOS 裝置開啟電源並登入，然後使用 USB 纜線將其連接到電腦。

將起始專案設為 **SampleXamarin.iOS**、將 [解決方案組態] 變更為 [發行]，然後在裝置選取器下拉式清單中選取要部署到的裝置。

![Visual Studio 設定](./media/get-started-xamarin-iOS/visual-studio-macos-configuration.jpg)

選取 [執行] > [啟動但不偵錯] 以部署並啟動您的應用程式。

在應用程式中，選取 [基本] 以執行示範，依照指示放置及回收錨點。

> ![螢幕擷取畫面 1](./media/get-started-xamarin-ios/screenshot-1.jpg)
> ![螢幕擷取畫面 2](./media/get-started-xamarin-ios/screenshot-2.jpg)
> ![螢幕擷取畫面 3](./media/get-started-xamarin-ios/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [教學課程：跨裝置共用 Spatial Anchors](../tutorials/tutorial-share-anchors-across-devices.md)