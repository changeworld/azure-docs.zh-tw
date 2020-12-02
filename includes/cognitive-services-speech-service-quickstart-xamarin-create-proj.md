---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 6d20df031633df4642ce9fb5cbbc469fd7f0a5da
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188216"
---
若要使用 Xamarin 建立適用於跨平台行動應用程式 .NET 開發的 Visual Studio 專案，您需要：
- 設定 Visual Studio 開發選項。
- 建立專案並選取目標架構。 
- 安裝語音 SDK。

### <a name="set-up-visual-studio-development-options"></a>設定 Visual Studio 開發選項

若要開始，請確定您在 Visual Studio 中已正確地設定，以使用 .NET 進行跨平台行動應用程式開發：

1. 開啟 Visual Studio 2019。

1. 從 [Visual Studio] 功能表列中，選取 [工具]   > [取得工具和功能]  以開啟 Visual Studio 安裝程式並檢視 [修改]  對話方塊。

   ![顯示 [工作負載] 索引標籤、[修改] 對話方塊和 Visual Studio 安裝程式的螢幕擷取畫面](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. 在 [工作負載]  索引標籤的 [Windows]  底下，尋找 [使用 .NET 進行行動開發]  工作負載。 如果已選取該工作負載旁的核取方塊，請關閉 [修改]  對話方塊，然後移至步驟 5。

1. 選取 [使用 .NET 進行行動開發]  核取方塊，然後選取 [修改]  。 從 [在我們開始之前]  對話方塊中選取 [繼續]  ，以安裝使用 .NET 進行行動開發的工作負載。 安裝新功能可能需要一些時間。

1. 關閉 Visual Studio 安裝程式。

### <a name="create-the-project"></a>建立專案

1. 在 [Visual Studio] 功能表列中，選取 [檔案]   > [新增]   > [專案]  以顯示 [建立新專案]  視窗。

   ![顯示如何在 Visual Studio 中建立新專案的螢幕擷取畫面。](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. 尋找並選取 [行動應用程式 (Xamarin.Forms)]  。

1. 選取 [下一步]  以顯示 [設定您的新專案]  畫面。

   ![顯示如何在 Visual Studio 中設定新專案的螢幕擷取畫面。](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. 在 [專案名稱]  中輸入 helloworld  。

1. 在 [位置]  中，移至現有資料夾並加以選取或建立資料夾來儲存專案。

1. 選取 [建立]  以移至 [新增行動應用程式 Xamarin Forms 專案]  視窗。

   ![螢幕擷取畫面：顯示 Visual Studio 中的 [新增行動應用程式 Xamarin Forms 專案] 對話方塊。](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. 選取 **空白** 範本。

1. 在 [平台]  中，選取 [Android]  、[iOS]  與 [Windows (UWP)]  核取方塊。

1. 選取 [確定]  。 您會回到 Visual Studio IDE，新的專案會建立並顯示在 [方案總管]  窗格中。

   ![helloworld 專案 - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

現在，請選取您的目標平台架構與啟始專案。 在 [Visual Studio] 工具列上，尋找 [方案平台]  下拉式方塊。 (如果您沒有看到該方塊，請選取 [檢視]   > [工具列]   > [標準]  以顯示包含 [方案平台]  的工具列。)如果您執行的是 64 位元 Windows，請在下拉式方塊中選取 [x64]  。 您可以視需要選取 [x86]  ，因為 64 位元Windows 也可以執行 32 位元應用程式。 在 [啟始專案]  下拉式方塊中，設定 **helloworld.UWP (Universal Windows)** 。

### <a name="install-the-speech-sdk"></a>安裝語音 SDK

安裝[語音 SDK NuGet 套件](https://aka.ms/csspeech/nuget) \(英文\)，並在您的專案中參考語音 SDK。

1. 在 [方案總管]  中，以滑鼠右鍵按一下您的方案。 選取 [管理方案的 NuGet 套件]  ，以移至 [NuGet - 解決方案]  視窗。

1. 選取 [瀏覽]。 

   ![安裝語音 SDK 時顯示 [管理解決方案套件] 對話方塊的螢幕擷取畫面。](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. 在 [套件來源]  中，選取 [nuget.org]。

1. 在 [搜尋]  方塊中輸入 Microsoft.CognitiveServices.Speech  。 然後當搜尋結果中出現該套件後，將其選取。

   ![反白顯示 Microsoft.CognitiveServices.Speech 套件的螢幕擷取畫面。](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)

   > [!NOTE] 
   > `Microsoft.CognitiveServices.Speech` NuGet 內的 iOS 程式庫未啟用 bitcode。 如果您需要為您的應用程式啟用 bitcode 程式庫，請特別針對 iOS 專案使用 `Microsoft.CognitiveServices.Speech.Xamarin.iOS` NuGet。

1. 在搜尋結果旁的 [套件狀態] 窗格中選取所有專案：**helloworld**、**helloworld.Android**、**helloworld.iOS** 和 **helloworld.UWP**。

1. 選取 [安裝]  。

1. 在 [預覽變更]  對話方塊中，選取 [確定]  。

1. 在 [接受授權]  對話方塊中，檢視授權，然後選取 [我接受]  。 在所有專案中安裝語音 SDK 套件參考。 安裝成功完成之後，您可能會看到 helloworld.iOS 的下列警告。 這是已知問題，而且應該不會影響您的應用程式功能。

   > 無法解析參考 "C:\Users\Default\.nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.core.a"。 若您的程式碼需要此參考，您可能會遇到編譯錯誤。
