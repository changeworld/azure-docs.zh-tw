---
title: 優化 Azure Service Fabric 網狀 Visual Studio
description: 本文說明如何將 Service Fabric Mesh 專案的 Visual Studio 效能最佳化，以大幅加快您執行第一次偵錯 (F5) 的速度。
author: dkkapur
ms.author: dekapur
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 40c4e9972a3d83a5dd8247bacac12e9d67a15f66
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75497973"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>將 Service Fabric Mesh 專案的 Visual Studio 效能最佳化

本文說明如何將 Service Fabric Mesh 專案的 Visual Studio 效能最佳化，以大幅加快您執行第一次偵錯 (F5) 的速度。  

## <a name="change-visual-studio-settings"></a>變更 Visual Studio 設定
 
在 Visual Studio 中， **Tools**  >  您可以在 [工具**選項**]   >  **Service Fabric**[一般] [工具]  >  **[一般**] 下調整下列設定：

- **在專案開啟時提取所需的 Docker 映像**可藉由在專案載入時啟動映像下載程序，使您執行第一次偵錯 (F5) 的速度更快。  
- **在專案開啟時部署應用程式**可藉由在專案開啟後啟動部署程序，使您執行第一次偵錯 (F5) 的速度更快。  
- **在專案關閉時移除應用程式**會在專案關閉時移除 Mesh 應用程式，以回收配置給應用程式的資源 (CPU、RAM)。  

當您在 Service Fabric 工具輸出視窗中看到 Visual Studio 正在「提取映像」、「暖機」或「移除應用程式」的訊息時，這所指涉的是前述設定的相關資訊。 您可以關閉這些設定。

## <a name="next-steps"></a>後續步驟

完整閱讀[對 Mesh 應用程式進行偵錯的教學課程](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)