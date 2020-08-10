---
title: 快速入門：建立 Xamarin Android 應用程式
description: 在本快速入門中，您將了解如何使用 Spatial Anchors 建置搭配 Xamarin 的 Android 應用程式。
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 8a7bb300bb634b342b1141d175b0046c7d2d7e2b
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810304"
---
# <a name="quickstart-create-a-xamarin-android-app-with-azure-spatial-anchors"></a>快速入門：使用 Azure Spatial Anchors 建立 Xamarin Android 應用程式

本快速入門說明如何使用 [Azure Spatial Anchors](../overview.md) 建立搭配 Xamarin 的 Android 應用程式。 Azure Spatial Anchors 是一款跨平台開發人員服務，可讓您使用在一段時間之後仍跨裝置保持其位置的物件，建立混合實境體驗。 完成作業後，您將會有可儲存和回收空間錨點的 Android 應用程式。

您將學習如何：

> [!div class="checklist"]
> * 建立 Spatial Anchors 帳戶
> * 設定 Spatial Anchors 帳戶識別碼和帳戶金鑰
> * 在 Android 裝置上部署和執行

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

若要完成本快速入門，請確定您具備︰
- Windows 或 macOS 電腦：
  - 如果使用 Windows：
    - 最新版的 <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019 16.2+</a>。
    - <a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a>。
    - <a href="https://git-lfs.github.com/">Git LFS</a>。
  - 如果使用 macOS：
    - 最新版的 <a href="https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Visual Studio for Mac 8.1+</a>。
    - <a href="https://git-scm.com/download/mac" target="_blank">Git for macOS</a>。
    - <a href="https://git-lfs.github.com/">Git LFS</a>。
- 已在您選擇的平台上安裝並執行的最新版 Xamarin.Android。 如需安裝 Xamarin.Android 的指南，請參閱 [Xamarin.Android 安裝](https://docs.microsoft.com/xamarin/android/get-started/installation/index)指南。
- <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">由開發人員啟用</a>且<a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">具備 ARCore 功能</a>的 Android 裝置。
  - 您的電腦可能需要其他設備磁碟機，才能與您的 Android 裝置通訊。 如需詳細資訊，請參閱[這裡](https://developer.android.com/studio/run/device.html)。
- 您的應用程式必須以 ARCore **1.8** 作為目標。

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

## <a name="deploy-the-app-to-your-android-device"></a>將應用程式部署到您的 Android 裝置

將 Android 裝置開啟電源並登入，然後使用 USB 纜線將其連接到電腦。

將起始專案設為 **SampleXamarin.Android**、將 [解決方案組態] 變更為 [發行]，然後在裝置選取器下拉式清單中選取要部署到的裝置。

# <a name="windows"></a>[Windows](#tab/deploy-windows)

![Visual Studio 設定](./media/get-started-xamarin-android/visual-studio-windows-configuration.png)

選取 [偵錯] > [開始偵錯] 以部署並啟動您的應用程式。

# <a name="macos"></a>[macOS](#tab/deploy-macos)

![Visual Studio 設定](./media/get-started-xamarin-android/visual-studio-macos-configuration.jpg)

選取 [執行] > [啟動但不偵錯] 以部署並啟動您的應用程式。

---

在應用程式中，選取 [基本] 以執行示範，依照指示放置及回收錨點。

> ![螢幕擷取畫面 1](./media/get-started-xamarin-android/screenshot-1.jpg)
> ![螢幕擷取畫面 2](./media/get-started-xamarin-android/screenshot-2.jpg)
> ![螢幕擷取畫面 3](./media/get-started-xamarin-android/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [教學課程：跨裝置共用 Spatial Anchors](../tutorials/tutorial-share-anchors-across-devices.md)
