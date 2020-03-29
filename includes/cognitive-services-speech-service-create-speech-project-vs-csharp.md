---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 08/30/2019
ms.author: wolfma
ms.openlocfilehash: c7332177795a45331749225e0f3dc3856e28a6a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "71327036"
---
若要建立 Visual Studio 專案以開發 Windows，您必須建立專案、設定 Visual Studio 以開發 .NET 桌面、安裝語音 SDK，並選擇目標架構。

### <a name="create-the-project-and-add-the-workload"></a>建立專案並新增工作負載

若要開始，請在 Visual Studio 中建立專案，並確定 Visual Studio 已針對 .NET 桌面開發進行設定：

1. 開啟 Visual Studio 2019。

1. 在 [開始] 視窗中，選取 [建立新專案]****。 

1. 在 [建立新專案]**** 視窗中，選擇 [主控台應用程式 (.NET Framework)]****，然後選取 [下一步]****。

1. 在 [設定您的新專案]**** 視窗中，在 [專案名稱]**** 中輸入 *helloworld*、在 [位置]**** 中選擇或建立目錄路徑，然後選取 [建立]****。

1. 在"視覺工作室"功能表列中，選擇 **"工具** > **獲取工具和功能**"，該選項將打開視覺化工作室安裝程式並顯示 **"修改"** 對話方塊。

1. 請檢查 [.NET 桌面開發]**** 工作負載是否可用。 如果尚未安裝此工作負載，請選取旁邊的核取方塊，然後選取 [修改]**** 以開始安裝。 下載及安裝可能需要幾分鐘的時間。

   如果 [.NET 桌面開發]**** 旁邊的核取方塊已經選取，請選取 [關閉]**** 來結束對話方塊。

   ![啟用 .NET 桌面開發](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. 關閉 Visual Studio 安裝程式。

### <a name="install-the-speech-sdk"></a>安裝語音 SDK

下一個步驟是安裝 [語音 SDK NuGet 套件](https://aka.ms/csspeech/nuget)，所以您可在程式碼中參考它。

1. 在 [方案總管] 中，以滑鼠右鍵按一下 **helloworld** 專案，然後選取 [管理 NuGet 套件]**** 以顯示 NuGet 套件管理員。

   ![NuGet 套件管理員](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. 在右上角找到 [套件來源]**** 下拉式方塊，並確定已選取 [nuget.org]****。

1. 選取左上角的 [瀏覽]****。

1. 在搜尋方塊中，鍵入 *Microsoft.CognitiveServices.Speech*，然後選取 **Enter**。

1. 從搜尋結果中，選取 [Microsoft.CognitiveServices.Speech]**** 套件，然後選取 [安裝]**** 以安裝最新的穩定版本。

   ![安裝 Microsoft.CognitiveServices.Speech NuGet 套件](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. 接受所有合約和授權，即可開始安裝。

   安裝套件之後，[套件管理員主控台]**** 視窗中會出現確認訊息。

### <a name="choose-the-target-architecture"></a>選擇目標架構

現在，若要建置及執行主控台應用程式，請建立符合電腦架構的平台組態。

1. 在功能表列中，選擇 **"生成** > **組態管理員**"。 [組態管理員]**** 對話方塊隨即出現。

   ![組態管理員對話方塊](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. 在 [使用中的方案平台]**** 下拉式方塊中，選取 [新增]****。 [新增方案平台]**** 對話方塊隨即出現。

1. 在 [輸入或選取新平台]**** 下拉式方塊中：
   - 如果您執行 64 位元的 Windows，請選取 **x64**。
   - 如果您執行 32 位元的 Windows，請選取 **x86**。

1. 選取 [確定]****，然後選取 [關閉]****。
