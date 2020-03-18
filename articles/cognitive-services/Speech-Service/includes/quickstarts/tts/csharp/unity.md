---
title: 快速入門：合成語音，C# (Unity) - 語音服務
titleSuffix: Azure Cognitive Services
description: 使用本指南，可透過 Unity 和適用於 Unity 的語音 SDK 建立文字轉語音的應用程式。 完成後，您將可從文字合成語音並即時傳至裝置的喇叭。
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 9/19/2019
ms.author: yinhew
ms.openlocfilehash: 73a4477547c562a9960fe72b352e906676eadae5
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78924805"
---
> [!NOTE]
> Unity 支援 Windows Desktop (x86 和 x64) 或通用 Windows 平台 (x86、x64、ARM/ARM64)、Android (x86、ARM32/64) 以及 iOS (x64 模擬器、ARM32 和 ARM64)。

## <a name="prerequisites"></a>Prerequisites

開始之前，請務必：

> [!div class="checklist"]
> * [建立 Azure 語音資源](../../../../get-started.md)
> * [設定您的開發環境並建立空白專案](../../../../quickstarts/setup-platform.md?tabs=unity)

## <a name="add-a-ui"></a>加入 UI

我們僅在場景中新增最低限度的 UI，其中包含輸入合成文字的輸入欄位、觸發語音合成的按鈕，和顯示結果的文字欄位。

* 在[階層視窗](https://docs.unity3d.com/Manual/Hierarchy.html) (依預設位於左側) 中，會顯示 Unity 建立的範例場景，內含新專案。
* 選取 [階層視窗]  頂端的 [建立]  按鈕，然後選取 [UI]   > [輸入欄位]  。
* 此選項會建立三個可在 [階層]  視窗中檢視的遊戲物件：內嵌在**畫布**物件中的**輸入欄位**物件，和**事件系統**物件。
* [瀏覽場景檢視](https://docs.unity3d.com/Manual/SceneViewNavigation.html)，以清楚檢視畫布和[場景檢視](https://docs.unity3d.com/Manual/UsingTheSceneView.html)中的輸入欄位。
* 選取 [階層]  視窗中的**輸入欄位**物件，以在[偵測器視窗](https://docs.unity3d.com/Manual/UsingTheInspector.html) (依預設位於右側) 中顯示其設定。
* 將 [位置 X]  和 [位置 Y]  屬性設為 **0**，使輸入欄位置於畫布中央。
* 再次按一下 [階層]  視窗頂端的 [建立]  按鈕。 選取 [UI]   > [按鈕]  以建立按鈕。
* 按一下 [階層]  視窗中的 [按鈕]  物件，以在[偵測器視窗](https://docs.unity3d.com/Manual/UsingTheInspector.html) (依預設位於右側) 中顯示其設定。
* 將 [位置 X]  和 [位置 Y]  屬性設為 **0**和 **-48**。 並將 [寬度]  和 [高度]  屬性分別設為 **160** 和 **30**，以確保按鈕和輸入欄位不會重疊。
* 再次按一下 [階層]  視窗頂端的 [建立]  按鈕。 選取 [UI]   > [文字]  以建立文字欄位。
* 選取 [階層]  視窗中的 [文字]  物件，以在[偵測器視窗](https://docs.unity3d.com/Manual/UsingTheInspector.html) (依預設位於右側) 中顯示其設定。
* 將 [位置 X]  和 [位置 Y]  屬性設為 **0** 和 **80**。 並將 [寬度]  和 [高度]  屬性分別設為 **320** 和 **80**，以確保文字欄位和輸入欄位不會重疊。
* 再次按一下 [階層]  視窗頂端的 [建立]  按鈕。 選取 [音訊]   > [音訊來源]  來建立音訊來源。

完成之後，UI 應該會如下列螢幕擷取畫面所示：

[![快速入門使用者介面在 Unity 編輯器中的螢幕擷取畫面](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>新增範例程式碼

1. 在[專案視窗](https://docs.unity3d.com/Manual/ProjectView.html) (依預設位於左下方) 中選取 [建立]  按鈕，然後選取 [C# 指令碼]  。 將指令碼命名為 `HelloWorld`。

1. 按兩下該指令碼加以編輯。

   > [!NOTE]
   > 您可以藉由選取 [編輯]   > [偏好設定]  來設定要啟動的程式碼編輯器。 如需詳細資訊，請參閱 [Unity 使用者手冊](https://docs.unity3d.com/Manual/Preferences.html)。

1. 將所有程式碼取代為下列內容：

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/text-to-speech/Assets/Scripts/HelloWorld.cs#code)]

1. 找出字串 `YourSubscriptionKey` 並將其取代為您的語音服務訂用帳戶金鑰。

1. 找出字串 `YourServiceRegion` 並將其取代為與您的訂用帳戶相關聯的[區域](~/articles/cognitive-services/Speech-Service/regions.md)。 例如，如果您使用的是免費試用版，區域將是 `westus`。

1. 儲存指令碼的變更。

1. 返回 Unity 編輯器，將指令碼新增為其中一個遊戲物件的元件。

   * 選取 [階層]  視窗中的 [畫布]  物件，以在[偵測器視窗](https://docs.unity3d.com/Manual/UsingTheInspector.html) (依預設位於右側) 中開啟設定。
   * 在 [偵測器]  視窗中，選取 [新增元件]  按鈕。 然後搜尋先前建立的 `HelloWorld` 指令碼來加以新增。
   * Hello World 元件具有 [輸出文字]  、[輸入欄位]  、[說出按鈕]  和 [音訊來源]  這四個未初始化的屬性，與 `HelloWorld` 類別的公用屬性相符。
     若要將這些項目連線，請選取 [物件選擇器]\(屬性右邊的小圓圈圖示\)。 選取您稍早建立的文字和按鈕物件。

     > [!NOTE]
     > 輸入欄位和按鈕中還有內嵌的文字物件。 請確定您不會不小心將其挑選為要輸出的文字。 或者，您可以在 [偵測器]  視窗中重新命名使用 [名稱]  欄位的文字物件，以避免發生混淆。

## <a name="run-the-application-in-the-unity-editor"></a>在 Unity 編輯器中執行應用程式

* 在 [Unity 編輯器] 工具列 (在功能表列下方) 選取 [播放]  按鈕。
* 應用程式啟動後，請在輸入欄位中輸入一些文字，然後選取按鈕。 您的文字會傳送至語音服務並合成為語音，然後以喇叭播放。

  [![在 Unity 遊戲視窗中執行快速入門的螢幕擷取畫面](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-inline.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* 查看 [[主控台]](https://docs.unity3d.com/Manual/Console.html) 視窗中的偵錯訊息。

## <a name="additional-options-to-run-this-application"></a>執行此應用程式的其他選項

此應用程式也可部署至 Android，作為 Windows 的獨立應用程式或 UWP 應用程式。
請參閱 quickstart/csharp-unity 資料夾中的[範例存放庫](https://aka.ms/csspeech/samples)，其中包含這些其他目標的設定說明。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>另請參閱

- [建立自訂語音](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [錄製自訂語音樣本](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
