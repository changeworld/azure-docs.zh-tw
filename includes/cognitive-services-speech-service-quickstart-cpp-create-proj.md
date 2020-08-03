---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: 517b092f933bedae0f254461d9bfee4d4640db8b
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374868"
---
若要建立 C++ 桌面開發的 Visual Studio 專案，您必須設定 Visual Studio 的開發選項、建立專案、選取目標架構，然後安裝語音 SDK。

### <a name="set-up-visual-studio-development-options"></a>設定 Visual Studio 開發選項

若要開始，請確定您在 Visual Studio 中已正確地設定，以進行 C++桌面開發：

1. 開啟 Visual Studio 2019 以顯示 [開始]  視窗。

   ![[開始] 視窗 - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-start-window.png)

1. 選取 [不使用程式碼繼續]  ，以移至 Visual Studio IDE。

1. 從 [Visual Studio] 功能表列中，選取 [工具]   > [取得工具和功能]  以開啟 Visual Studio 安裝程式並檢視 [修改]  對話方塊。

   ![[工作負載] 索引標籤、[修改] 對話方塊、Visual Studio 安裝程式](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

1. 在 [工作負載]  索引標籤的 [Windows]  底下，尋找 [使用 C++ 的桌面開發]  工作負載。 如果尚未選取該工作負載旁的核取方塊，請加以選取。

1. 在 [個別元件] 索引標籤中，尋找 [NuGet 套件管理員] 核取方塊。 如果尚未選取核取方塊，請加以選取。

1. 選取角落標示為 [關閉]  或 [修改]  的按鈕。 (按鈕名稱會根據您是否選取了任何安裝功能而有所不同。)如果您選取 [修改]  ，則會開始安裝，這可能需要一些時間。

1. 關閉 Visual Studio 安裝程式。

### <a name="create-the-project-and-select-the-target-architecture"></a>建立專案並選取目標架構

接下來，建立您的專案：

1. 在 [Visual Studio] 功能表列中，選擇 [檔案]   > [新增]   > [專案]  以顯示 [建立新專案]  視窗。

   ![建立新專案，C++ - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. 尋找並選取 [容器應用程式]  。 請確定您選取的是此專案類型的 C++ 版本 (而不是 C# 或 Visual Basic)。

1. 選取 [下一步]  以顯示 [設定您的新專案]  畫面。

   ![設定您的新專案，C++ - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-configure-your-new-project.png)

1. 在 [專案名稱]  中，輸入 `helloworld`。

1. 在 [位置]  中，瀏覽至用來儲存專案的資料夾，然後選取或建立。

現在，請選取您的目標平台架構。 在 [Visual Studio] 工具列中， 尋找 [方案平台]  下拉式方塊。 (如果您沒有看到它，請選擇 [檢視]   > [工具列]   > [標準]  以顯示包含 [方案平台]  的工具列。)如果您執行的是 64 位元 Windows，請在下拉式方塊中選擇 [x64]  。 64 位元 Windows 也可執行 32 位元應用程式，因此您可以依需求選擇 [x86]  。

### <a name="install-the-speech-sdk"></a>安裝語音 SDK

最後，安裝[語音 SDK NuGet 套件](https://aka.ms/csspeech/nuget) \(英文\)，並在您的專案中參考語音 SDK：

1. 在 [方案總管] 中，以滑鼠右鍵按一下您的解決方案，然後選擇 [管理解決方案的 NuGet 套件] 以移至 [NuGet - 解決方案] 視窗。

1. 選取 [瀏覽]。

   ![[NuGet - 解決方案] 索引標籤，Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. 在 [套件來源]  中，選擇 [nuget.org]  。

1. 在 [搜尋]  方塊中，輸入 `Microsoft.CognitiveServices.Speech`，然後在搜尋結果中出現該套件後加以選擇。

   ![Microsoft.CognitiveServices.Speech C++ 套件安裝 - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

1. 在搜尋結果旁的 [套件狀態] 窗格中，選取您的 **helloworld** 專案。

1. 選取 [安裝]  。

1. 在 [預覽變更]  對話方塊中，選取 [確定]  。

1. 在 [接受授權]  對話方塊中，檢視授權，然後選取 [我接受]  。 套件安裝隨即開始，而且當安裝完成時，[輸出]  窗格會顯示類似下列文字的訊息：`Successfully installed 'Microsoft.CognitiveServices.Speech 1.13.0' to helloworld`。
