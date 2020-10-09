---
title: 將 Azure Kinect 程式庫新增至您的 Visual Studio 專案
description: 瞭解如何將 Azure Kinect NuGet 套件新增至您的 Visual Studio 專案。
author: wes-b
ms.author: wesbarc
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect、azure、感應器、sdk、visual studio 2017、visual studio 2019、nuget
ms.openlocfilehash: b0395118481cbaecd5ad0b6a3a6b3e89cc29dfaf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276497"
---
# <a name="add-azure-kinect-library-to-your-visual-studio-project"></a>將 Azure Kinect 程式庫新增至您的 Visual Studio 專案

本文將逐步引導您完成將 Azure Kinect NuGet 套件新增至 Visual Studio 專案的程式。

## <a name="install-azure-kinect-nuget-package"></a>安裝 Azure Kinect NuGet 套件

若要安裝 Azure Kinect NuGet 套件：

1. 您可以在 [快速入門：在 Visual Studio 中安裝和使用套件](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio)的 Visual Studio 中，找到安裝 NuGet 套件的詳細指示。
2. 若要新增封裝，您可以使用滑鼠右鍵按一下 [參考]，然後從方案總管選擇 [管理 NuGet 套件]，以使用封裝管理員 UI。
3. 選擇 [ [nuget.org](https://www.nuget.org) ] 做為 [套件來源]，選取 [流覽] 索引標籤，然後搜尋 `Microsoft.Azure.Kinect.Sensor` 。
4. 從清單中選取該套件，然後安裝。

## <a name="use-azure-kinect-nuget-package"></a>使用 Azure Kinect NuGet 套件

新增封裝之後，請將標頭檔包含在原始程式碼中，例如：

- `#include <k4a/k4a.h>`
- `#include <k4arecord/record.h>`
- `#include <k4arecord/playback.h>`

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
>[現在您已準備好建立您的第一個應用程式](build-first-app.md)
