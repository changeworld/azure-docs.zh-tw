---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: 5ab0b9abd32ef2e5eb7ea02d6118e387ef753348
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84609270"
---
若要建立適用於通用 Windows 平台 (UWP) 開發的 Visual Studio 專案，您必須設定 Visual Studio 開發選項、建立專案、選取目標架構、設定音訊擷取，以及安裝語音 SDK。

### <a name="set-up-visual-studio-development-options"></a>設定 Visual Studio 開發選項

若要開始，請確定您已在適用於 UWP 開發的 Visual Studio 中正確設定：

1. 開啟 Visual Studio 2019 以顯示 [開始]  視窗。

   ![[開始] 視窗 - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-start-window.png)

1. 選取 [不使用程式碼繼續]  ，以移至 Visual Studio IDE。

1. 從 [Visual Studio] 功能表列中，選取 [工具]   > [取得工具和功能]  以開啟 Visual Studio 安裝程式並檢視 [修改]  對話方塊。

   ![[工作負載] 索引標籤、[修改] 對話方塊、Visual Studio 安裝程式](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

1. 在 [工作負載]  索引標籤的 [Windows]  底下，尋找 [通用 Windows 平台開發]  工作負載。 如果已選取該工作負載旁的核取方塊，請關閉 [修改]  對話方塊，然後移至步驟 6。

1. 選取 [通用 Windows 平台開發]  核取方塊，選取 [修改]  ，然後在 [開始使用之前]  對話方塊中，選取 [繼續]  以安裝 UWP 開發工作負載。 安裝新功能可能需要一些時間。

1. 關閉 Visual Studio 安裝程式。

### <a name="create-the-project-and-select-the-target-architecture"></a>建立專案並選取目標架構

接下來，建立您的專案：

1. 在 [Visual Studio] 功能表列中，選擇 [檔案]   > [新增]   > [專案]  以顯示 [建立新專案]  視窗。

   ![建立新專案 - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-create-new-project.png)

1. 尋找並選取 [空白應用程式 (通用 Windows)]  。 請確定您選取的是此專案類型的 C# 版本 (而不是 Visual Basic)。

1. 選取 [下一步]  以顯示 [設定您的新專案]  畫面。

   ![設定您的新專案 - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-configure-your-new-project.png)

1. 在 [專案名稱]  中，輸入 `helloworld`。

1. 在 [位置]  中，瀏覽至用來儲存專案的資料夾，然後選取或建立。

1. 選取 [建立]  以移至 [新增通用 Windows 平台專案]  視窗。

   ![[新增通用 Windows 平台專案] 對話方塊 - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. 在 [最小版本]  (第二個下拉式方塊) 中，選擇 [Windows 10 Fall Creators Update (10.0; 組建 16299)]  ，這是語音 SDK 的最低需求。

1. 在 [目標版本]  (第一個下拉式方塊) 中，選擇與 [最小版本]  中的值相同或之後的值。

1. 選取 [確定]  。 您會回到 Visual Studio IDE，新的專案會建立並顯示在 [方案總管]  窗格中。

   ![helloworld 專案 - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-helloworld.png)

現在，請選取您的目標平台架構。 在 [Visual Studio] 工具列中， 尋找 [方案平台]  下拉式方塊。 (如果您沒有看到它，請選擇 [檢視]   > [工具列]   > [標準]  以顯示包含 [方案平台]  的工具列。)如果您執行的是 64 位元 Windows，請在下拉式方塊中選擇 [x64]  。 64 位元 Windows 也可執行 32 位元應用程式，因此您可以依需求選擇 [x86]  。

> [!NOTE]
> 語音 SDK 僅支援 Intel 相容處理器。 目前不支援 ARM 處理器。

### <a name="set-up-audio-capture"></a>設定音訊擷取

然後允許專案擷取音訊輸入：

1. 在 [方案總管]  中，按兩下 [Package.appxmanifest]  以開啟套件應用程式資訊清單。

1. 選取 [功能]  索引標籤。

   ![[功能] 索引標籤，套件應用程式資訊清單 - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)

1. 選取 [麥克風]  功能的方塊。

1. 從功能表列中，選擇 [檔案]   > [儲存 Package.appxmanifest]  來儲存變更。

### <a name="install-the-speech-sdk"></a>安裝語音 SDK

最後，安裝[語音 SDK NuGet 套件](https://aka.ms/csspeech/nuget) \(英文\)，並在您的專案中參考語音 SDK：

1. 在 [方案總管]  中，以滑鼠右鍵按一下您的解決方案，然後選擇 [管理解決方案的 NuGet 套件]  以移至 [NuGet - 解決方案]  視窗。

1. 選取 [瀏覽]。 

   ![管理解決方案套件對話方塊的螢幕擷取畫面](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. 在 [套件來源]  中，選擇 [nuget.org]  。

1. 在 [搜尋]  方塊中，輸入 `Microsoft.CognitiveServices.Speech`，然後在搜尋結果中出現該套件後加以選擇。

   ![管理解決方案套件對話方塊的螢幕擷取畫面](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png)

1. 在搜尋結果旁的 [套件狀態] 窗格中，選取您的 **helloworld** 專案。

1. 選取 [安裝]  。

1. 在 [預覽變更]  對話方塊中，選取 [確定]  。

1. 在 [接受授權]  對話方塊中，檢視授權，然後選取 [我接受]  。 套件安裝隨即開始，而且當安裝完成時，[輸出]  窗格會顯示類似下列文字的訊息：`Successfully installed 'Microsoft.CognitiveServices.Speech 1.12.1' to helloworld`。
