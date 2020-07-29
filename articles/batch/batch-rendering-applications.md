---
title: 轉譯應用程式
description: 您可以透過 Azure Batch 使用任何轉譯應用程式。 不過，此外也有預先安裝了通用應用程式的 Azure Marketplace VM 映像可供使用。
ms.date: 09/19/2019
ms.topic: how-to
ms.openlocfilehash: f876671c6ac16137c2c1cf8d40bd70860c06975b
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85960481"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>在轉譯的 VM 映像上預先安裝的應用程式

您可以透過 Azure Batch 使用任何轉譯應用程式。 不過，此外也有預先安裝了通用應用程式的 Azure Marketplace VM 映像可供使用。

情況允許時，預先安裝的轉譯應用程式即適用按使用次數付費的授權。 在 Batch 集區建立時，可以指定必要的應用程式，且 VM 和應用程式的成本都會按分鐘計費。 應用程式的價格會列在 [Azure Batch 定價頁面](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering)上。

有些應用程式僅支援 Windows，但大部分都是 Windows 和 Linux 均支援。

## <a name="applications-on-centos-7-rendering-images"></a>CentOS 7 轉譯影像的應用程式

下列清單適用於 CentOS 7.6，1.1.6 版轉譯影像。

* Autodesk Maya I/O 2017 Update 5 (201708032230 版)
* Autodesk Maya I/O 2018 Update 2 (201711281015 版)
* Autodesk Maya I/O 2019 Update 1
* Autodesk Arnold for Maya 2017 (Arnold 5.3.1.1 版) MtoA-3.2.1.1-2017
* Autodesk Arnold for Maya 2018 (Arnold 5.3.1.1 版) MtoA-3.2.1.1-2018
* Autodesk Arnold for Maya 2019 (Arnold 5.3.1.1 版) MtoA-3.2.1.1-2019
* Chaos Group V-Ray for Maya 2017 (3.60.04 版)
* Chaos Group V-Ray for Maya 2018 (3.60.04 版)
* Blender (2.68)
* Blender (2.8)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>最新 Windows Server 2016 轉譯影像的應用程式

下列清單適用於 Windows Server 2016，1.3.8 版轉譯影像。

* Autodesk Maya I/O 2017 Update 5 (17.4.5459 版)
* Autodesk Maya I/O 2018 Update 6 (18.4.0.7622 版)
* Autodesk Maya I/O 2019
* Autodesk 3ds Max I/O 2018 Update 4 (20.4.0.4254 版)
* Autodesk 3ds Max I/O 2019 Update 1 (21.2.0.2219 版)
* Autodesk 3ds Max I/O 2020 Update 2
* Autodesk Arnold for Maya 2017 (Arnold 5.3.0.2 版) MtoA-3.2.0.2-2017
* Autodesk Arnold for Maya 2018 (Arnold 5.3.0.2 版) MtoA-3.2.0.2-2018
* Autodesk Arnold for Maya 2019 (Arnold 5.3.0.2 版) MtoA-3.2.0.2-2019
* Autodesk Arnold for 3ds Max 2018 (Arnold 5.3.0.2 版) (1.2.926 版)
* Autodesk Arnold for 3ds Max 2019 (Arnold 5.3.0.2 版) (1.2.926 版)
* Autodesk Arnold for 3ds Max 2020 (Arnold 5.3.0.2 版) (1.2.926 版)
* Chaos Group V-Ray for Maya 2017 (4.12.01 版)
* Chaos Group V-Ray for Maya 2018 (4.12.01 版)
* Chaos Group V-Ray for Maya 2019 (4.04.03 版)
* Chaos Group V-Ray for 3ds Max 2018 (4.20.01 版)
* Chaos Group V-Ray for 3ds Max 2019 (4.20.01 版)
* Chaos Group V-Ray for 3ds Max 2020 (4.20.01 版)
* Blender (2.79)
* Blender (2.80)
* AZ 10

> [!IMPORTANT]
> 若要在 [Azure Batch 延伸模組範本](https://github.com/Azure/batch-extension-templates)外使用 Maya 執行 V-Ray，請在執行轉譯之前先啟動 `vrayses.exe`。 若要在範本外啟動 vrayses.exe，您可以使用下列命令 `%MAYA_2017%\vray\bin\vrayses.exe"`。
>
> 如需範例，請參閱 GitHub 上 [Maya 和 V-Ray 範本](https://github.com/Azure/batch-extension-templates/blob/master/templates/maya/render-vray-windows/pool.template.json)的開始工作。

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>舊版 Windows Server 2016 轉譯影像的應用程式

下列清單適用於 Windows Server 2016，1.3.7 版轉譯影像。

* Autodesk Maya I/O 2017 Update 5 (17.4.5459 版)
* Autodesk Maya I/O 2018 Update 4 (18.4.0.7622 版)
* Autodesk 3ds Max I/O 2019 Update 1 (21.2.0.2219 版)
* Autodesk 3ds Max I/O 2018 Update 4 (20.4.0.4254 版)
* Autodesk Arnold for Maya 2017 (Arnold 5.2.0.1 版) MtoA-3.1.0.1-2017
* Autodesk Arnold for Maya 2018 (Arnold 5.2.0.1 版) MtoA-3.1.0.1-2018
* Autodesk Arnold for 3ds Max 2018 (Arnold 5.0.2.4 版) (1.2.926 版)
* Autodesk Arnold for 3ds Max 2019 (Arnold 5.0.2.4 版) (1.2.926 版)
* Chaos Group V-Ray for Maya 2018 (3.52.03 版)
* Chaos Group V-Ray for 3ds Max 2018 (3.60.02 版)
* Chaos Group V-Ray for Maya 2019 (3.52.03 版)
* Chaos Group V-Ray for 3ds Max 2019 (4.10.01 版)
* Blender (2.79)

> [!NOTE]
> Chaos Group V-Ray for 3ds Max 2019 (4.10.01 版) 推出 V-Ray 的重大變更。 若要使用先前的版本 (3.60.02 版)，請使用 Windows Server 2016，1.3.2 版轉譯節點。

## <a name="next-steps"></a>後續步驟

若要使用轉譯 VM 映像，則必須在建立集區時在集區組態中指定這些映像；請參閱[用於轉譯的 Batch 集區功能](./batch-rendering-functionality.md#batch-pools)。
