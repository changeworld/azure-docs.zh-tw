---
title: 將 Azure Kinect 程式庫新增至您的 Visual Studio 專案
description: 瞭解如何將 Azure Kinect NuGet 封裝新增至您的 Visual Studio 專案。
author: wes-b
ms.author: wesbarc
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect，azure，感應器，sdk，visual studio 2017，visual studio 2019，nuget
ms.openlocfilehash: b0395118481cbaecd5ad0b6a3a6b3e89cc29dfaf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276497"
---
# <a name="add-azure-kinect-library-to-your-visual-studio-project"></a>將 Azure Kinect 程式庫新增至您的 Visual Studio 專案

本文會逐步引導您完成將 Azure Kinect NuGet 封裝新增至 Visual Studio 專案的程式。

## <a name="install-azure-kinect-nuget-package"></a>安裝 Azure Kinect NuGet 套件

若要安裝 Azure Kinect NuGet 套件：

1. 您可以在[快速入門：在 Visual Studio 中安裝和使用套件](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio)的 Visual Studio 中，找到安裝 NuGet 套件的詳細指示。
2. 若要新增套件，您可以使用 [套件管理員] UI，方法是以滑鼠右鍵按一下 [參考]，然後從方案總管選擇 [管理 NuGet 封裝]。
3. 選擇 [ [nuget.org](https://www.nuget.org) ] 作為 [套件來源]，選取 [流覽] 索引標籤，然後搜尋 `Microsoft.Azure.Kinect.Sensor` 。
4. 從清單中選取該套件，並安裝。

## <a name="use-azure-kinect-nuget-package"></a>使用 Azure Kinect NuGet 套件

新增封裝之後，將標頭檔新增至原始程式碼，例如：

- `#include <k4a/k4a.h>`
- `#include <k4arecord/record.h>`
- `#include <k4arecord/playback.h>`

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
>[現在您已準備好建立您的第一個應用程式](build-first-app.md)
