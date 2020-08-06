---
title: 安裝適用于 Unity 的 Azure 空間錨點
description: 設定 Unity 專案以使用 Azure 空間錨點
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/31/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: a23cff8bcef2d62b1e415997ffd6afc80cf47793
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87812289"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>在 Unity 專案中設定 Azure 空間錨點

## <a name="requirements"></a>需求

Azure 空間錨點目前支援使用下列設定的 Unity 2019.4 (LTS) 。

* Azure 空間錨點 2.4.0 + 支援 Unity 2019.4 with AR Foundation 3.1。

## <a name="configuring-a-project"></a>設定專案

適用于 Unity 的 Azure 空間錨點目前是使用 () 的 Unity 資產套件 `.unitypackage` 來散發，此功能可在[GitHub 版本](https://github.com/Azure/azure-spatial-anchors-samples/releases)中找到。

### <a name="import-the-asset-package"></a>匯入資產套件

1. `AzureSpatialAnchors.unitypackage`從[GitHub 版本](https://github.com/Azure/azure-spatial-anchors-samples/releases)下載您要設為目標的版本檔。
2. 依照[這裡](https://docs.unity3d.com/Manual/AssetPackagesImport.html)的指示，將 Unity 資產套件匯入您的專案中。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [如何：在 Unity 中建立和尋找錨點](./create-locate-anchors-unity.md)
