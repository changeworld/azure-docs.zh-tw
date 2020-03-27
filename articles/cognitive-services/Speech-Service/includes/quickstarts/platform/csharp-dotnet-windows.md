---
title: 快速入門：適用於 .NET Framework (Windows) 的語音 SDK 平台設定 - 語音服務
titleSuffix: Azure Cognitive Services
description: 使用本指南來設定您的平台，以在適用於 Windows 的 .NET Framework 下搭配使用 C# 與語音服務 SDK。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/10/2019
ms.author: erhopf
ms.openlocfilehash: a09b969ee3e11aeb04f338cf035b21b5da9bd952
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "78924833"
---
本指南說明如何安裝適用於 .NET Framework (Windows) 的[語音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)。 如果您想直接開始使用套件名稱，請在 NuGet 主控台中執行 `Install-Package Microsoft.CognitiveServices.Speech`。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Prerequisites

本快速入門需要：

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>建立 Visual Studio 專案並安裝語音 SDK

您需要安裝 [語音 SDK NuGet 套件](https://aka.ms/csspeech/nuget)，以便在程式碼中加以參考。 若要這麼做，您可能必須先建立 **helloworld** 專案。 如果您已經有 **.NET 桌面開發**工作負載的專案，您可以使用該專案，並跳至 [使用 NuGet 套件管理員來安裝語音 SDK](#use-nuget-package-manager-to-install-the-speech-sdk)。

### <a name="create-helloworld-project"></a>建立 helloworld 專案

1. 開啟 Visual Studio 2019。

1. 在 [開始] 視窗中，選取 [建立新專案]  。 

1. 在 [建立新專案]  視窗中，選擇 [主控台應用程式 (.NET Framework)]  ，然後選取 [下一步]  。

1. 在 [設定您的新專案]  視窗中，在 [專案名稱]  中輸入 *helloworld*、在 [位置]  中選擇或建立目錄路徑，然後選取 [建立]  。

1. 從 [Visual Studio] 功能表列，選取 [工具]   > [取得工具和功能]  ，以開啟 Visual Studio 安裝程式並顯示 [修改]  對話方塊。

1. 請檢查 [.NET 桌面開發]  工作負載是否可用。 如果尚未安裝此工作負載，請選取旁邊的核取方塊，然後選取 [修改]  以開始安裝。 下載及安裝可能需要幾分鐘的時間。

   如果 [.NET 桌面開發]  旁邊的核取方塊已經選取，請選取 [關閉]  來結束對話方塊。

   ![啟用 .NET 桌面開發](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. 關閉 Visual Studio 安裝程式。

### <a name="use-nuget-package-manager-to-install-the-speech-sdk"></a>使用 NuGet 套件管理員來安裝語音 SDK

1. 在 [方案總管] 中，以滑鼠右鍵按一下 **helloworld** 專案，然後選取 [管理 NuGet 套件]  以顯示 NuGet 套件管理員。

   ![NuGet 套件管理員](~/articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. 在右上角找到 [套件來源]  下拉式方塊，並確定已選取 **`nuget.org`** 。

1. 選取左上角的 [瀏覽]  。

1. 在搜尋方塊中，鍵入 *Microsoft.CognitiveServices.Speech*，然後選取 **Enter**。

1. 從搜尋結果中，選取 [Microsoft.CognitiveServices.Speech]  套件，然後選取 [安裝]  以安裝最新的穩定版本。

   ![安裝 Microsoft.CognitiveServices.Speech NuGet 套件](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. 接受所有合約和授權，即可開始安裝。

   安裝套件之後，[套件管理員主控台]  視窗中會出現確認訊息。

### <a name="choose-target-architecture"></a>選擇目標架構

若要建置及執行主控台應用程式，請建立符合電腦架構的平台組態。

1. 從功能表列選取 [建置]   > [組態管理員]  。 [組態管理員]  對話方塊隨即出現。

   ![組態管理員對話方塊](~/articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. 在 [使用中的方案平台]  下拉式方塊中，選取 [新增]  。 [新增方案平台]  對話方塊隨即出現。

1. 在 [輸入或選取新平台]  下拉式方塊中：
   - 如果您執行 64 位元的 Windows，請選取 **x64**。
   - 如果您執行 32 位元的 Windows，請選取 **x86**。

1. 選取 [確定]  ，然後選取 [關閉]  。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [windows](../quickstart-list.md)]
