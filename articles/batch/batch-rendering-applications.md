---
title: 轉譯應用程式 - Azure Batch
description: 您可以透過 Azure Batch 使用任何轉譯應用程式。 不過，此外也有預先安裝了通用應用程式的 Azure Marketplace VM 映像可供使用。
services: batch
ms.service: batch
author: LauraBrenner
ms.author: labrenne
ms.date: 09/19/2019
ms.topic: conceptual
ms.openlocfilehash: 77672534b2aad993a44e9b637fbed58df8610e97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022982"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>在轉譯的 VM 映像上預先安裝的應用程式

您可以透過 Azure Batch 使用任何轉譯應用程式。 不過，此外也有預先安裝了通用應用程式的 Azure Marketplace VM 映像可供使用。

情況允許時，預先安裝的轉譯應用程式即適用按使用次數付費的授權。 在 Batch 集區建立時，可以指定必要的應用程式，且 VM 和應用程式的成本都會按分鐘計費。 應用程式的價格會列在 [Azure Batch 定價頁面](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering)上。

有些應用程式僅支援 Windows，但大部分都是 Windows 和 Linux 均支援。

## <a name="applications-on-centos-7-rendering-images"></a>CentOS 7 渲染圖像上的應用程式

以下清單適用于 CentOS 7.6 版本 1.1.6 渲染圖像。

* Autodesk Maya I/O 2017 Update 5 (201708032230 版)
* Autodesk Maya I/O 2018 Update 2 (201711281015 版)
* 歐特克 Maya I/O 2019 更新 1
* 歐特克·阿諾德瑪雅 2017 （阿諾德版本 5.3.1.1） MtoA-3.2.1.1-2017
* 歐特克·阿諾德瑪雅 2018 （阿諾德版本 5.3.1.1） MtoA-3.2.1.1-2018
* 歐特克阿諾德瑪雅 2019 （阿諾德版本 5.3.1.1） MtoA-3.2.1.1-2019
* Chaos Group V-Ray for Maya 2017 (3.60.04 版)
* Chaos Group V-Ray for Maya 2018 (3.60.04 版)
* Blender (2.68)
* 攪拌機 （2.8）

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>最新 Windows 伺服器 2016 渲染圖像上的應用程式

以下清單適用于 Windows Server 2016，版本 1.3.8 渲染圖像。

* Autodesk Maya I/O 2017 Update 5 (17.4.5459 版)
* 歐特克 Maya I/O 2018 更新 6 （版本 18.4.0.7622）
* 歐特克瑪雅I/O 2019
* Autodesk 3ds Max I/O 2018 Update 4 (20.4.0.4254 版)
* Autodesk 3ds Max I/O 2019 Update 1 (21.2.0.2219 版)
* 歐特克 3ds 最大 I/O 2020 更新 2
* 歐特克阿諾德瑪雅 2017 （阿諾德版本 5.3.0.2） MtoA-3.2.0.2-2017
* 歐特克阿諾德瑪雅 2018 （阿諾德版本 5.3.0.2） MtoA-3.2.0.2-2018
* 歐特克阿諾德瑪雅 2019 （阿諾德版本 5.3.0.2） MtoA-3.2.0.2-2019
* 歐特克阿諾德 3ds 最大 2018 （阿諾德版本 5.3.0.2）（版本 1.2.926）
* 歐特克阿諾德 3ds 最大 2019 （阿諾德版本 5.3.0.2）（版本 1.2.926）
* 歐特克阿諾德 3ds 最大 2020 （阿諾德版本 5.3.0.2）（版本 1.2.926）
* 混沌組 V 射線瑪雅 2017 （版本 4.12.01）
* 混沌組 V 射線瑪雅 2018 （版本 4.12.01）
* 混沌組 V 射線瑪雅 2019 （版本 4.04.03）
* 混沌組 V 射線 3ds 最大 2018 （版本 4.20.01）
* 混沌組 V 射線 3ds 最大 2019 （版本 4.20.01）
* 混沌組 V 射線 3ds 最大 2020 （版本 4.20.01）
* Blender (2.79)
* 攪拌機 （2.80）
* AZ 10

> [!IMPORTANT]
> 要在[Azure Batch 擴展範本](https://github.com/Azure/batch-extension-templates)之外使用 Maya 運行 V-Ray，在運行渲染之前先開始`vrayses.exe`。 要在範本之外啟動 vrayses.exe，可以使用以下命令`%MAYA_2017%\vray\bin\vrayses.exe"`。
>
> 例如，請參閱 GitHub 上的[Maya 和 V-Ray 範本](https://github.com/Azure/batch-extension-templates/blob/master/templates/maya/render-vray-windows/pool.template.json)的開始任務。

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>以前 Windows 伺服器 2016 渲染圖像上的應用程式

以下清單適用于 Windows Server 2016，版本 1.3.7 渲染圖像。

* Autodesk Maya I/O 2017 Update 5 (17.4.5459 版)
* Autodesk Maya I/O 2018 Update 4 (18.4.0.7622 版)
* Autodesk 3ds Max I/O 2019 Update 1 (21.2.0.2219 版)
* Autodesk 3ds Max I/O 2018 Update 4 (20.4.0.4254 版)
* Autodesk Arnold for Maya 2017 (Arnold 5.2.0.1 版) MtoA-3.1.0.1-2017
* Autodesk Arnold for Maya 2018 (Arnold 5.2.0.1 版) MtoA-3.1.0.1-2018
* 歐特克阿諾德 3ds 最大 2018 （阿諾德版本 5.0.2.4）（版本 1.2.926）
* 歐特克阿諾德 3ds 最大 2019 （阿諾德版本 5.0.2.4）（版本 1.2.926）
* 混沌組 V 射線瑪雅 2018 （版本 3.52.03）
* 混沌組 V 射線 3ds 最大 2018 （版本 3.60.02）
* 混沌組V射線瑪雅2019年（版本3.52.03）
* 混沌組 V 射線 3ds 最大 2019 （版本 4.10.01）
* Blender (2.79)

> [!NOTE]
> 混沌組 V 射線 3ds Max 2019 （版本 4.10.01） 引入了 V 射線的中斷更改。 要使用以前的版本（版本 3.60.02），請使用 Windows Server 2016，版本 1.3.2 呈現節點。

## <a name="next-steps"></a>後續步驟

若要使用轉譯 VM 映像，則必須在建立集區時在集區組態中指定這些映像；請參閱[用於轉譯的 Batch 集區功能](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools)。
