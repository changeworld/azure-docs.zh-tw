---
title: 教學課程：建立新的 HoloLens Unity 應用程式
description: 在本教學課程中，您將了解如何使用 Azure Spatial Anchors 建立新的 HoloLens Unity 應用程式。
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 06/22/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: ee8b8c2931d006dbb3d472b545030d3aff79c56a
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2020
ms.locfileid: "85297982"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>教學課程：使用 Azure Spatial Anchors 新建 HoloLens Unity 應用程式的逐步指示

本教學課程將說明如何使用 Azure Spatial Anchors 建立新的 HoloLens Unity 應用程式。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，請確定您具有下列項目︰

1. 已安裝 <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017+</a> 的 Windows 機器，並且具有**通用 Windows 平台開發**工作負載和 **Windows 10 SDK (10.0.18362.0 或更新版本)** 元件，以及 <a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a>。
2. 從 [Visual Studio Marketplace](https://marketplace.visualstudio.com/) 安裝 Visual Studio 的 [C++/WinRT Visual Studio 延伸模組 (VSIX)](https://aka.ms/cppwinrt/vsix)。
3. 已啟用[開發人員模式](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio)的 HoloLens 裝置。 本文需要具有 [Windows 10 2018 年 10 月更新](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (也稱為 RS5) 的 HoloLens 裝置。 若要在 HoloLens 上更新至最新版本，請開啟**設定**應用程式，移至 [更新與安全性]，然後選取 [檢查更新] 按鈕。

## <a name="getting-started"></a>開始使用

首先我們將設定專案和 Unity 場景：
1. 啟動 Unity。
2. 選取 [ **新增**]。
4. 確定已選取 **3D**。
5. 為您的專案命名，然後輸入儲存**位置**。
6. 按一下 [建立專案]。
7. 使用下列途徑，將空的預設場景儲存至新檔案：[檔案] > [另存新檔]。
8. 將新場景命名為**主要**，然後按 [儲存] 按鈕。

**設定專案設定**

現在我們將設定一些 Unity 專案設定，以利將 Windows 全像攝影版 SDK 設為開發目標。

首先，我們將設定應用程式的品質設定。
1. 選取 [編輯] > [專案設定] > [品質]
2. 在 **Windows 市集**標誌下方的資料行中，按一下 [預設] 資料列上的箭號，然後選取 [非常低]。 當 [Windows 市集] 資料行和 [非常低] 資料列中的方塊呈現為綠色時，即表示已正確套用設定。

我們必須讓 Unity 知道我們嘗試匯出的應用程式應建立沉浸式檢視，而不是 2D 檢視。 我們可在以 Windows 10 SDK 為目標的 Unity 上啟用虛擬實境支援，以建立沉浸式檢視。

1. 移至 [編輯] > [專案設定] > [播放器]。
2. 在 [播放程式設定] 的 [偵測器面板] 中，選取 **Windows 市集**圖示。
3. 展開 [XR 設定] 群組。
4. 在 [轉譯] 區段中核取 [支援虛擬實境] 核取方塊，以新增 [虛擬實境 SDK] 清單。
5. 確認 **Windows Mixed Reality** 出現在清單中。 如果沒有，請選取清單底部的 **+** 按鈕，然後選擇 [Windows Mixed Reality]。

> [!NOTE]
> 若未看到 Windows 市集圖示，請仔細檢查以確定您在安裝之前已選取 Windows 市集 .NET 指令碼後端。 如果沒有，您可能需要以正確的 Windows 安裝重新安裝 Unity。

**確認指令碼後端組態**
1. 移至 [編輯] > [專案設定] > [播放器] (先前步驟中的**播放器**可能仍開啟)。
2. 在 [播放程式設定] 的 [偵測器面板] 中，選取 **Windows 市集**圖示。
3. 在 [其他設定] 組態區段中，確定 [指令碼後端] 設為 [IL2CPP]。

**設定功能**
1. 移至 [編輯] > [專案設定] > [播放器] (先前步驟中的**播放器**可能仍開啟)。
2. 在 [播放程式設定] 的 [偵測器面板] 中，選取 **Windows 市集**圖示。
3. 在 [發行設定] 組態區段中，核取 **InternetClientServer** 和 **SpatialPerception**。

**設定主要虛擬相機**
1. 在 [階層面板] 中，選取 [主要相機]。
2. 在 [偵測器] 中，將其轉換位置設為 **0,0,0**。
3. 找出 [清除旗標] 屬性，並將下拉式清單從 [Skybox] 變更為 [純色]。
4. 按一下 [背景] 欄位，以開啟色彩選擇器。
5. 將 **R、G、B 和 A** 設為 **0**。
6. 選取 [新增元件]，然後搜尋 [空間對應 Collider] 並加以新增。

**建立我們的指令碼**
1. 在 [專案] 窗格中，於 **Assets** 資料夾下方建立新的資料夾 **Scripts**。
2. 以滑鼠右鍵按一下該資料夾，然後選取 [建立] > [C# 指令碼]。 將其標題指定為 **AzureSpatialAnchorsScript**。
3. 移至 [GameObject] -> [建立空的]。
4. 加以選取，然後在 [偵測器] 中將其從 **GameObject** 重新命名為 **MixedRealityCloud**。 選取 [新增元件]，然後搜尋並新增 **AzureSpatialAnchorsScript**。

**建立圓球預製項目**
1. 移至 [GameObject] -> [3D 物件] -> [圓球]。
2. 在 [偵測器] 中，將其標尺設為 **0.25、0.25、0.25**。
3. 在 [階層] 窗格中找出**圓球**物件。 按住該物件並拖曳到 [專案] 窗格的 [資產] 資料夾中。
4. 按一下滑鼠右鍵，並**刪除**您在 [階層] 窗格中建立的原始圓球。

您的 [專案] 窗格中現在應該會有圓球預製項目。

## <a name="trying-it-out"></a>立即試用
若要測試是否一切都正常運作，請在 **Unity** 中建置您的應用程式，並從 **Visual Studio** 加以部署。 請依照 [**MR Basics 100：開始使用 Unity** 課程](https://docs.microsoft.com/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio)第 6 章的指示執行此作業。 您應該會看到 Unity 的開始畫面，和隨後的全新顯示畫面。

## <a name="place-an-object-in-the-real-world"></a>將物件放在實際環境中
我們將使用您的應用程式建立及放置物件。 開啟我們在[部署應用程式](#trying-it-out)時建立的 Visual Studio 解決方案。

首先，請在您的 `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs` 中新增下列匯入項目：

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

然後，在您的 `AzureSpatialAnchorsScript` 類別中新增下列成員變數：

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-47,53-57,65-84)]

在繼續之前，我們必須先在 spherePrefab 成員變數上設定我們已建立的圓球預製項目。 請返回 **Unity**。
1. 在 **Unity** 中，選取 [階層] 窗格中的 **MixedRealityCloud** 物件。
2. 按一下您在 [專案] 窗格中儲存的**圓球**預製項目。 將您按住的**圓球**拖曳到 [偵測器] 窗格中位於 [Azure Spatial Anchors 指令碼 (指令碼)] 下方的 [圓球預製項目] 區域上。

您現在應已將**圓球**設為指令碼上的預製項目。 從 **Unity** 進行建置，然後重新開啟產生的 **Visual Studio** 解決方案，如您[立即試用](#trying-it-out)中所做的。

在 **Visual Studio** 中，重新開啟 `AzureSpatialAnchorsScript.cs`。 在 `Start()` 方法中新增下列程式碼。 此程式碼將會連結 `GestureRecognizer`，而在出現空中點選及呼叫 `HandleTap` 時加以偵測。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=86-95,98&highlight=4-10)]

現在，我們必須在 `Update()` 下方新增下列 `HandleTap()` 方法。 它會執行光線投射，並取得要放置圓球的命中點。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=273-283,305-306,310-318)]

現在，我們必須建立圓球。 圓球一開始會是白色，但此值將在稍後調整。 請新增下列 `CreateAndSaveSphere()` 方法：

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-331,396)]

從 **Visual Studio** 執行您的應用程式，再次加以驗證。 這次請點選螢幕以建立您的白色圓球，並將其置於您選擇的介面上。

## <a name="set-up-the-dispatcher-pattern"></a>設定發送器模式

在使用 Unity 時，所有的 Unity API (例如您用來執行 UI 更新的 API) 都必須在主執行緒上執行。 但在我們所將撰寫的程式碼中，我們會在其他執行緒上取得回呼。 我們想要在這些回呼中更新 UI，因此需要透過某種方式從端執行緒切換到主執行緒。 若要從端執行緒執行主執行緒上的程式碼，我們會使用發送器模式。

我們將新增成員變數 dispatchQueue，這是一個動作佇列。 我們會將動作推送到佇列上，然後清除佇列，並在主執行緒上執行動作。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=43-56&highlight=6-9)]

接著，我們會新增將動作新增至佇列的方式。 請在 `Update()` 後面緊接著新增 `QueueOnUpdate()`：

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=112-122)]

現在，我們將使用 update() 迴圈來檢查動作是否已排入佇列。 如果是，我們將清除佇列中的動作，然後加以執行。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=100-110&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>取得 Azure Spatial Anchors SDK

## <a name="via-unity-package"></a>[透過 Unity 套件](#tab/UnityPackage)

現在，我們將下載 Azure Spatial Anchors SDK。 移至 [Azure Spatial Anchors GitHub 發行頁面](https://github.com/Azure/azure-spatial-anchors-samples/releases)。 在資產下方，下載 **AzureSpatialAnchors.unitypackage**。 在 Unity 中移至 [資產]，然後按一下 [匯入套件] > [自訂套件...]。瀏覽至套件並選取 [開啟]。

在彈出的新 [匯入 Unity 套件] 視窗中，取消選取 [外掛程式]，然後按一下右下角的 [匯入]。

現在，我們必須還原 Nuget 套件以取得 Azure Spatial Anchors SDK。 從 **Unity** 進行建置，然後開啟並重新建置產生的 **Visual Studio** 解決方案，如[立即試用](#trying-it-out)中所詳述。

## <a name="via-nugetforunity"></a>[透過 NuGetForUnity](#tab/NuGetForUnity)

首先，我們需要安裝 NuGetForUnity。 移至 [NuGetForUnity GitHub 發行頁面](https://github.com/GlitchEnzo/NuGetForUnity/releases)。 在 [資產] 下，下載最新的 **NuGetForUnity.unitypackage**。 在 Unity 中移至 [資產]，然後按一下 [匯入套件] > [自訂套件...]。瀏覽至套件並選取 [開啟]。 Unity 現在會安裝 NugetForUnity。 如果您在 Unity 中未看到新的 [NuGet] 下拉式清單，則可能需要在 [專案] > [資產] 底下按一下滑鼠右鍵。 然後選取 [全部重新匯入]。

安裝好 NuGetForUnity 之後，請選取 [NuGet] > [管理 NuGet 套件]。 然後搜尋 Microsoft.Azure.SpatialAnchors.Unity，並選取 [安裝]。

我們現在需要建置以取得實際的 Azure Spatial Anchors SDK，原因是我們剛剛下載的 NuGet 套件只包含協助程式指令碼。 從 **Unity** 進行建置，然後開啟並重新建置產生的 **Visual Studio** 解決方案，如[立即試用](#trying-it-out)中所詳述。

---

在您的 **Visual Studio** 解決方案中，將下列匯入新增至您的 `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs`：

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=18-21&highlight=1)]

然後，在您的 `AzureSpatialAnchorsScript` 類別中新增下列成員變數：

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=53-68&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>將本機 Azure Spatial Anchor 連結至本機錨點

我們將設定 Azure Spatial Anchor 的 CloudSpatialAnchorSession。 首先，我們會在 `AzureSpatialAnchorsScript` 類別內新增下列 `InitializeSession()` 方法。 經呼叫後，它將確保在您的應用程式啟動期間會建立 Azure Spatial Anchors 工作階段，並正確地初始化。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=179-208,211-215)]

現在，我們需要撰寫用來處理委派呼叫的程式碼。 我們隨後將新增更多項目。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=217-232)]

現在，我們要將您的 `initializeSession()` 方法連接到 `Start()` 方法中。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=86-98&highlight=12)]

最後，在 `CreateAndSaveSphere()` 方法中新增下列程式碼。 它會將本機 Azure Spatial Anchor 連結至我們放在實際環境中的圓球。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-344,396&highlight=14-25)]

在進一步操作之前，您必須先建立 Azure Spatial Anchors 帳戶以取得帳戶識別碼、金鑰和網域 (如果您還沒有這些項目的話)。 請依照下一節的指示加以取得。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>將本機錨點上傳至雲端

取得 Azure Spatial Anchors 帳戶識別碼、金鑰和網域之後，請將 `Account Id` 貼到 `SpatialAnchorsAccountId` 中、將 `Account Key` 貼到 `SpatialAnchorsAccountKey` 中，並將 `Account Domain` 貼到 `SpatialAnchorsAccountDomain` 中。

最後，我們要將所有項目連結在一起。 在您的 `SpawnNewAnchoredObject()` 方法中新增下列程式碼。 它會在您的圓球建立時隨即叫用 `CreateAnchorAsync()` 方法。 此方法傳回後，下列程式碼會對您的圓球執行最後的更新，將其色彩變更為藍色。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-397&highlight=26-77)]

再次從 **Visual Studio** 執行您的應用程式。 動一動您的頭部，然後藉由空中點選放置圓球。 一旦有足夠的畫面格時，圓球就會變成黃色，並開始進行雲端上傳。 上傳完成後，圓球就會變成藍色。 (選擇性) 您也可以使用 **Visual Studio** 內的 [輸出] 視窗來監視應用程式傳送的記錄訊息。 您將可查看建立進度的建議資訊，和雲端在上傳完成時可能傳回的錨點識別碼。

> [!NOTE]
> 如果您收到「DllNotFoundException：無法載入 DLL 'AzureSpatialAnchors'：找不到指定的模組。」，您應**清除**解決方案並重新加以**建置**。

## <a name="locate-your-cloud-spatial-anchor"></a>找出您的雲端空間錨點

在錨點上傳至雲端後，我們即可嘗試再次將其找出。 我們將在 `HandleTap()` 方法中新增下列程式碼。 此程式碼將會：

* 呼叫 `ResetSession()`，這將會停止 `CloudSpatialAnchorSession`，並從螢幕中移除現有的藍色圓球。
* 重新初始化 `CloudSpatialAnchorSession`。 這麼做可確保我們要找出的錨點會來自雲端，而不是我們所建立的本機錨點。
* 建立**監看員**以尋找我們上傳至 Azure Spatial Anchors 的錨點。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=273-311&highlight=13-31,35-36)]

現在，我們將新增 `ResetSession()` 和 `CleanupObjects()` 方法。 您可以將其置於 `QueueOnUpdate()` 下方

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=124-177)]

現在，我們必須連結在找到所要查詢的錨點時所將叫用的程式碼。 在 `InitializeSession()` 內，新增下列回呼：

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=206-212&highlight=4-5)]


現在，我們將新增會在找到 CloudSpatialAnchor 時建立並放置綠色圓球的程式碼。 此外也會再次啟用螢幕點選，讓您能夠再次重複整個案例：建立另一個本機錨點、加以上傳，並再次將其找出。

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=234-271)]

就這麼簡單！ 最後一次從 **Visual Studio** 執行您的應用程式，並且端對端地嘗試進行整個案例。 移動您的裝置，並放置白色圓球。 然後，繼續移動您的頭部以擷取環境資料，直到圓球變成黃色。 您的本機錨點將會上傳，且圓球會變成藍色。 最後，再次點選螢幕，以移除本機錨點，接著我們將查詢其雲端對應項目。 繼續移動您的裝置，直到找到雲端空間錨點為止。 綠色圓球應會出現在正確的位置，而您可以清除並再次重複整個案例。

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]