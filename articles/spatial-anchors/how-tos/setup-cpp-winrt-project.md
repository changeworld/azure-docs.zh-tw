---
title: 為 c + +/WinRT HoloLens 應用程式安裝 Azure 空間錨點
description: 設定 c + +/WinRT HoloLens 專案以使用 Azure 空間錨點
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 710f023a64f66e486bb2db9c08807af3431d0016
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87812292"
---
# <a name="configuring-azure-spatial-anchors-in-a-cwinrt-hololens-project"></a>在 c + +/WinRT HoloLens 專案中設定 Azure 空間錨點

## <a name="requirements"></a>需求

* [Visual Studio 2019](https://www.visualstudio.com/downloads/)已安裝**通用 Windows 平臺開發**工作負載和**Windows 10 SDK (10.0.18362.0 或更新版本的) **元件。

## <a name="configuring-a-project"></a>設定專案

HoloLens 和 c + +/WinRT 的 Azure 空間錨點是使用[SpatialAnchors](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) NuGet 套件進行散發。

依照[這裡](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio)的指示，使用 Visual Studio 的 NuGet 套件管理員，將[SpatialAnchors](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) nuget 套件安裝到您的專案中。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [如何：在 c + +/WinRT 中建立和尋找錨點](./create-locate-anchors-cpp-winrt.md)
