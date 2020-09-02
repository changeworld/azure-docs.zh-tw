---
title: 快速入門：從麥克風辨識語音，C# (Unity) - 語音服務
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.custom: devx-track-csharp
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.openlocfilehash: 8307cfee90fdf65d119bbc1e55eabb0533a4a6ad
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88926396"
---
> [!NOTE]
> 適用於 Unity 的語音 SDK 支援 Windows Desktop (x86 和 x64) 或通用 Windows 平台 (x86、x64、ARM/ARM64)、Android (x86、ARM32/64) 以及 iOS (x64 模擬器、ARM32 和 ARM64)

## <a name="prerequisites"></a>Prerequisites

開始之前：

> [!div class="checklist"]
> * [建立 Azure 語音資源](../../../../get-started.md)
> * [設定您的開發環境並建立空白專案](../../../../quickstarts/setup-platform.md?tabs=unity&pivots=programming-language-csharp)
> * 確定您可以存取麥克風以擷取音訊

如果您已經這麼做，很棒。 讓我們繼續進行。

## <a name="create-a-unity-project"></a>建立 Unity 專案

1. 開啟 Unity。 如果您首次使用 Unity，便會出現 [Unity 中樞]  *<version number>* 視窗。 (您也可以直接開啟 Unity 中樞來進入此視窗)。

   [![Unity 中樞視窗](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png#lightbox)
1. 選取 [ **新增**]。 隨即會出現 [使用 Unity 建立新專案]  *<version number>* 視窗。

   [![在 Unity 中樞中建立新專案](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. 在 [專案名稱]  中，輸入 **csharp-unity**。
1. 在 [範本]  中，如果尚未選取 [3D]  ，請加以選取。
1. 在 [位置]  中，選取或建立用來儲存專案的資料夾。
1. 選取 [建立]  。

不久後就會出現 [Unity 編輯器] 視窗。



## <a name="add-ui"></a>新增 UI

現在，讓我們在場景中新增最小的 UI。 此 UI 會包含用來觸發語音辨識的按鈕，以及用來顯示結果的文字欄位。 在 [[階層]  視窗](https://docs.unity3d.com/Manual/Hierarchy.html)中，會顯示 Unity 使用新專案所建立的場景範例。

1. 在 [階層]  視窗頂端，選取 [建立]   > [UI]   > [按鈕]  。

   此動作會建立三個可在 [階層]  視窗中檢視的遊戲物件：**按鈕**物件、含有按鈕的**畫布**物件，以及 **EventSystem** 物件。

   [![Unity 編輯器環境](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [瀏覽 [場景]  檢視](https://docs.unity3d.com/Manual/SceneViewNavigation.html)，以清楚檢視 [[場景]  檢視](https://docs.unity3d.com/Manual/UsingTheSceneView.html)中的畫布和按鈕。

1. 在 [[偵測器]  視窗](https://docs.unity3d.com/Manual/UsingTheInspector.html) (依預設位於右側) 中，將 **Pos X** 和 **Pos Y** 屬性設定為 **0**，以便讓按鈕位於畫布中央。

1. 在 [階層]  視窗中，選取 [建立]   > [UI]   > [文字]  以建立**文字**物件。

1. 在 [偵測器]  視窗中，將 **Pos X** 和 **Pos Y** 屬性設定為 **0** 和 **120**，並將 [寬度]  和 [高度]  屬性設定為 **240** 和 **120**。 這些值可確保文字欄位和按鈕不會重疊。

完成之後，[場景]  檢視應該會如下列螢幕擷取畫面所示：

[![Unity 編輯器中的 [場景] 檢視](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>新增範例程式碼

若要新增 Unity 專案的指令碼程式碼範例，請遵循下列步驟：

1. 在 [[專案] 視窗](https://docs.unity3d.com/Manual/ProjectView.html)中，選取 [建立]   > [C# 指令碼]  以新增 C# 指令碼。

   [![Unity 編輯器中的 [專案] 視窗](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. 將指令碼命名為 `HelloWorld`。

1. 按兩下 `HelloWorld` 以編輯新建立的指令碼。

   > [!NOTE]
   > 若要設定供 Unity 用來編輯的程式碼編輯器，請選取[編輯]   > [喜好設定]  ，然後移至 [外部工具]  喜好設定。 如需詳細資訊，請參閱 [Unity 使用者手冊](https://docs.unity3d.com/Manual/Preferences.html)。

1. 將現有指令碼取代為下列程式碼：

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/from-microphone/Assets/Scripts/HelloWorld.cs#code)]

1. 尋找字串 `YourSubscriptionKey` 並將其取代為您的語音服務訂用帳戶金鑰。

1. 尋找 `YourServiceRegion` 字串，並將其取代為訂用帳戶所關聯[區域](https://aka.ms/speech/sdkregion)中的 [區域識別碼]  。

1. 儲存指令碼的變更。

現在，返回 Unity 編輯器，然後將指令碼新增為其中一個遊戲物件的元件：

1. 在 [階層]  視窗中，選取 [畫布]  物件。

1. 在 [偵測器]  視窗中，選取 [新增元件]  按鈕。

   [![Unity 編輯器中的 [偵測器] 視窗](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. 在下拉式清單中，搜尋我們前面建立的 `HelloWorld` 指令碼並加以新增。 [偵測器]  視窗中便會出現 [Hello World (指令碼)]  區段，並列出兩個未初始化的屬性，分別是 [輸出文字]  和 [開始辨識按鈕]  。 這些 Unity 元件屬性會符合 `HelloWorld` 類別的公用屬性。

1. 選取 [開始辨識按鈕]  屬性的物件選擇器 (屬性右邊的小圓圈圖示)，然後選擇您稍早建立的 [按鈕]  物件。

1. 選取 [輸出文字]  屬性的物件選擇器，然後選擇您稍早建立的 [文字]  物件。

   > [!NOTE]
   > 按鈕中還有內嵌的文字物件。 請確定您並未不慎選擇該物件作為文字輸出 (或者，請使用 [偵測器]  視窗中的 [名稱]  欄位將其中一個文字物件重新命名，以避免混淆)。

## <a name="run-the-application-in-the-unity-editor"></a>在 Unity 編輯器中執行應用程式

現在您已經準備好在 Unity 編輯器內執行應用程式。

1. 在 Unity 編輯器工具列 (位於功能表列下方) 中，選取 [播放]  按鈕 (指向右方的三角形)。

1. 移至 [[遊戲]  檢視](https://docs.unity3d.com/Manual/GameView.html)，並等候 [文字]  物件顯示 [按一下按鈕以辨識語音]  。 (當應用程式尚未啟動或尚未準備好回應時，則會顯示 [新增文字]  )。

1. 選取該按鈕，然後對電腦的麥克風說出英文片語或句子。 您的語音會傳送到語音服務並轉譯為文字，文字則會出現在 [遊戲]  檢視中。

   [![Unity 編輯器中的 [遊戲] 檢視](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. 查看 [[主控台]  視窗](https://docs.unity3d.com/Manual/Console.html)中的偵錯訊息。 如果 [主控台]  視窗並未出現，請移至功能表列，然後選取 [視窗]   > [一般]   > [主控台]  來顯示該視窗。

1. 完成語音辨識後，請選取 [Unity 編輯器] 工具列中的 [播放]  按鈕以停止應用程式。

## <a name="additional-options-to-run-this-application"></a>執行此應用程式的其他選項

此應用程式也可部署為 Android 應用程式、Windows 的獨立應用程式或 UWP 應用程式。
如需詳細資訊，請參閱[存放庫範例](https://aka.ms/csspeech/samples)。 `quickstart/csharp-unity` 資料夾會說明這些額外目標的設定。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

