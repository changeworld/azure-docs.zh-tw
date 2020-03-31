---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 12/12/2018
ms.author: erhopf
ms.openlocfilehash: 777b2608cf5f326556dfaea307f4f3e9346213f8
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "66482391"
---
1. 啟動 Visual Studio 2019。

1. 請確定 **.NET 跨平台開發**工作負載可用。 從 Visual Studio 功能表列選擇 [工具]   > [取得工具和功能]  ，以開啟 Visual Studio 安裝程式。 如果已啟用此工作負載，請關閉對話方塊。

   ![Visual Studio 安裝程式的螢幕擷取畫面，其中 [工作負載] 索引標籤已醒目提示](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-net-core-workload.png)

   否則，選取 [.NET Core 跨平台開發]  旁邊的方塊，然後選取對話方塊右下角的 [修改]  。 安裝新功能需要一些時間。

1. 建立新的 Visual C# .NET Core 主控台應用程式。 在 [新增專案]  對話方塊中，從左窗格展開 [已安裝]   >  [Visual C#]   >  [.NET Core]  。 然後選取 [主控台應用程式 (.NET Core)]  。 針對專案名稱，請輸入 *helloworld*。

   ![新增專案對話方塊的螢幕擷取畫面](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "建立 Visual C# 主控台應用程式 (.NET Core)")

1. 安裝並參考 [Speech SDK NuGet 封裝](https://aka.ms/csspeech/nuget)。 在 [方案總管] 中，以滑鼠右鍵按一下解決方案，然後選取 [管理解決方案的 NuGet 套件]  。

   ![方案總管的螢幕擷取畫面，其中 [管理解決方案的 NuGet 套件] 已醒目提示](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "管理解決方案的 NuGet 套件")

1. 在右上角的 [套件來源]  欄位中，選取 [nuget.org]  。搜尋 `Microsoft.CognitiveServices.Speech` 套件，然後將它安裝到 **helloworld** 專案。

   ![管理解決方案套件對話方塊的螢幕擷取畫面](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "安裝 NuGet 套件")

1. 接受顯示的授權，才會開始安裝 NuGet 套件。

   ![接受授權對話方塊的螢幕擷取畫面](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "接受授權")

安裝套件之後，[套件管理員] 主控台上會出現確認訊息。
