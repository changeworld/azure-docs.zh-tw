---
title: 快速入門：建立 Android 應用程式
description: 在本快速入門中，您將了解如何使用 Spatial Anchors 建置 Android 應用程式。
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3f794d1c70baee07b9ff3ed5d8299cf8ad3bf983
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652494"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>快速入門：使用 Azure Spatial Anchors 建立 Android 應用程式

本快速入門將說明如何透過 Java 或 C++/NDK 使用 [Azure Spatial Anchors](../overview.md) 建立 Android 應用程式。 Azure Spatial Anchors 是一款跨平台開發人員服務，可讓您使用在一段時間之後仍跨裝置保持其位置的物件，建立混合實境體驗。 當您完成時，您將會有可儲存和回收空間錨點的 ARCore Android 應用程式。

您將學習如何：

> [!div class="checklist"]
> * 建立 Spatial Anchors 帳戶
> * 設定 Spatial Anchors 帳戶識別碼和帳戶金鑰
> * 在 Android 裝置上部署和執行

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

若要完成本快速入門，請確定您具備︰

- 具有 <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4+</a> 的 Windows 或 macOS 機器。
  - 如果在 Windows 上執行，您也需要 <a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a> 和 <a href="https://git-lfs.github.com/">Git LFS</a>。
  - 如果在 macOS 上執行，請透過 HomeBrew 安裝 Git。 在終端機的單一行輸入以下命令：`/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`。 然後執行 `brew install git` 和 `brew install git-lfs`。
  - 若要建置 NDK 範例，您也必須在 Android Studio 中安裝 NDK 和 CMake 3.6 或更高的 SDK Tools。
- <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">由開發人員啟用</a>且<a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">具備 ARCore 功能</a>的 Android 裝置。
  - 您的電腦可能需要其他設備磁碟機，才能與您的 Android 裝置通訊。 如需詳細資訊和指示，請參閱[這裡](https://developer.android.com/studio/run/device.html)。
- 您的應用程式必須以 ARCore **1.11.0** 作為目標。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>開啟範例專案

# <a name="java"></a>[Java](#tab/openproject-java)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

從[這裡](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.11.0/libraries/include/arcore_c_api.h) 下載 `arcore_c_api.h`，並將其放在 `Android\NDK\libraries\include`中。

從新複製的存放庫，執行下列命令來初始化子模組：

```console
git submodule update --init --recursive
```

---

開啟 Android Studio。

# <a name="java"></a>[Java](#tab/openproject-java)

選取 [開啟現有的 Android Studio 專案]，並選取位於 `Android/Java/` 上的專案。

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

選取 [開啟現有的 Android Studio 專案]，並選取位於 `Android/NDK/` 上的專案。

---

## <a name="configure-account-identifier-and-key"></a>設定帳戶識別碼和金鑰

下一個步驟是將應用程式設定為使用您的帳戶識別碼和帳戶金鑰。 [設定空間錨點資源](#create-a-spatial-anchors-resource)時，將它們複製到文字編輯器中。

# <a name="java"></a>[Java](#tab/openproject-java)

開啟 `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsManager.java`。

找出 `SpatialAnchorsAccountKey` 欄位，並將 `Set me` 取代為帳戶金鑰。

找出 `SpatialAnchorsAccountId` 欄位，並將 `Set me` 取代為帳戶識別碼。

找出 `public AzureSpatialAnchorsManager(Session arCoreSession)` 並新增下列程式碼行，以替換先前帳戶網域中的項目：`spatialAnchorsSession.getConfiguration().setAccountDomain("MyAccountDomain");`。

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

開啟 `Android/NDK/app/src/main/cpp/AzureSpatialAnchorsApplication.cpp`。

找出 `SpatialAnchorsAccountKey` 欄位，並將 `Set me` 取代為帳戶金鑰。

找出 `SpatialAnchorsAccountId` 欄位，並將 `Set me` 取代為帳戶識別碼。

找出 `AzureSpatialAnchorsApplication::StartCloudSession()` 並新增下列程式碼行，以替換先前帳戶網域中的項目：`m_cloudSession->Configuration()->AccountDomain("MyAccountDomain");`。

---

## <a name="deploy-the-app-to-your-android-device"></a>將應用程式部署到您的 Android 裝置

將 Android 裝置開機並登入，然後使用 USB 纜線將其連接到電腦。

在 Android Studio 工具列中選取 [執行]。

![Android Studio 的部署和執行](./media/get-started-android/android-studio-deploy-run.png)

在 [選取部署目標] 對話方塊中選取 Android 裝置，然後選取 [確定] 以在 Android 裝置上執行應用程式。

依照應用程式中的指示放置及回收錨點。

在 Android Studio 工具列中選取 [停止]，以停止應用程式。

![Android Studio 的停止](./media/get-started-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [教學課程：跨裝置共用 Spatial Anchors](../tutorials/tutorial-share-anchors-across-devices.md)
