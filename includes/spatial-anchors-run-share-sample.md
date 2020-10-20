---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 8/27/2020
ms.author: rgarcia
ms.openlocfilehash: 50e4799f09322eab05b4f8ddf7004c2e0078fdab
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971288"
---
## <a name="android"></a>[Android](#tab/Android)

Java Android 範例支援跨裝置共用。

在 Android Studio 中，開啟範例資料夾中的 *SharedActivity.java* 檔案。 

輸入您在先前的步驟中複製的 URL (來自您的 ASP.NET Web 應用程式 Azure 部署)，作為 *SharedActivity.java* 檔案中的 `SharingAnchorsServiceUrl` 值。 

將 URL 中的 `index.html` 取代為 `api/anchors`。 它看起來應該像這樣：`https://<app_name>.azurewebsites.net/api/anchors`。

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

Objective-C iOS 範例支援跨裝置共用。

開啟範例資料夾中的 *SharedDemoViewController.m* 檔案。 

輸入您在先前的步驟中取得的 URL (來自您的 ASP.NET Web 應用程式 Azure 部署)，作為 *SharedDemoViewController.m* 檔案中的 `SharingAnchorsServiceUrl` 值。 

將 URL 中的 `index.html` 取代為 `api/anchors`。 它看起來應該像這樣：`https://<app_name>.azurewebsites.net/api/anchors`。

將應用程式部署到您的裝置。 

在應用程式啟動後，選取 [點選以啟動共用示範] 選項，然後依照應用程式中的指示操作。 您可以選取 [點選以依其錨點編號尋找錨點]，或 [點選以建立錨點並將其儲存至服務]。

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

Xamarin Android 和 iOS 範例都支援跨裝置共用。

開啟範例資料夾中的 *AccountDetails.cs* 檔案。 

輸入您在上一個步驟中取得的 URL (來自您的 ASP.NET Web 應用程式 Azure 部署)，作為 *AccountDetails.cs* 檔案中的 `AnchorSharingServiceUrl` 值。 

將 URL 中的 `index.html` 取代為 `api/anchors`。 它看起來應該像這樣：`https://<app_name>.azurewebsites.net/api/anchors`。

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>設定 Android 裝置

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>設定 iOS 裝置

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

在 [專案] 窗格上，移至 `Assets\AzureSpatialAnchors.Examples\Resources`。 

選取 **SpatialAnchorSamplesConfig**。 然後，在 [偵測器] 窗格中，輸入 `Sharing Anchors Service` URL (來自您的 ASP.NET Web 應用程式 Azure 部署) 作為 `Base Sharing Url` 的值。 將 `index.html` 取代為 `api/anchors`。 它看起來應該像這樣：`https://<app_name>.azurewebsites.net/api/anchors`。

選取 [檔案] > [儲存] 以儲存場景。

## <a name="deploy-to-your-device"></a>部署至裝置

### <a name="deploy-to-an-android-device"></a>部署至 Android 裝置

登入 Android 裝置，並使用 USB 纜線將其連接到電腦。

藉由選取 [檔案]   > [組建設定]  來開啟 [組建設定]  。

在 [組建中的場景] 底下，確定每個場景旁邊都有勾號。

確定 [匯出專案] 沒有核取記號。 選取 [建置並執行]****。 系統將會提示您儲存 *.apk* 檔案。 您可以將檔案命名為任何名稱。

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>部署到 iOS 裝置

藉由選取 [檔案]   > [組建設定]  來開啟 [組建設定]  。

在 [組建中的場景] 底下，確定每個場景旁邊都有勾號。

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

在 Xcode 中，選取 [停止]**** 來停止應用程式。
