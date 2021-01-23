---
title: 'Azure 雲端服務 (延伸支援的可用大小) '
description: Azure 雲端服務 (延伸支援) 部署的可用大小
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: c3515b559ef647b9a043a04282b4edfc6e7fa9be
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744206"
---
# <a name="available-sizes-for-azure-cloud-services-extended-support"></a>Azure 雲端服務 (延伸支援的可用大小) 

本文說明雲端服務的可用虛擬機器大小 (延伸支援) 實例。   

| SKU 系列 |  ACU/核心 | 
|---|---|
| [A5-7](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#a-series)| 100 |
|[A8-A11](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#a-series---compute-intensive-instances) | 225* |
|[Av2](https://docs.microsoft.com/azure/virtual-machines/av2-series) | 100 | 
|[D](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#d-series) | 160 | 
|[Dv2](https://docs.microsoft.com/azure/virtual-machines/dv2-dsv2-series) | 160 - 190* |
|[Dv3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series) | 160 - 190* |
|[Ev3](https://docs.microsoft.com/azure/virtual-machines/ev3-esv3-series) | 160 - 190*
|[G](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#g-series) | 180-240 * |
|[H](https://docs.microsoft.com/azure/virtual-machines/h-series) | 290 - 300* | 

>[!NOTE]
> 以 * 標示的 ACU 使用了「Intel® 渦輪」技術來增加 CPU 頻率及提升效能。 提升的程度會依 VM 大小、工作負載及在相同主機上執行的其他工作負載而有所不同。

## <a name="configure-sizes-for-cloud-services-extended-support"></a>設定雲端服務的大小 (延伸支援) 

您可以在服務定義檔中，將角色實例的虛擬機器大小指定為服務模型的一部分。 角色的大小會決定 CPU 核心數目、記憶體容量和本機檔案系統大小。

例如，將 web 角色實例大小設定為 `Standard_D2` ： 

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2"> 
</WorkerRole> 
```

## <a name="change-the-size-of-an-existing-role"></a>變更現有角色的大小

若要變更現有角色的大小，請變更服務定義檔中的虛擬機器大小 (的) 、重新封裝您的雲端服務，然後重新部署。 

## <a name="get-a-list-of-available-sizes"></a>取得可用大小的清單 

若要取得可用大小的清單，請參閱 [資源 sku-列出](https://docs.microsoft.com/rest/api/compute/resourceskus/list) 並套用下列篩選：


`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


## <a name="next-steps"></a>後續步驟 
- 請參閱雲端服務的 [部署必要條件](deploy-prerequisite.md) (延伸支援) 。
- 請 [參閱雲端服務的常見問題，](faq.md) (延伸支援) 。
- 使用 [Azure 入口網站](deploy-portal.md)、 [PowerShell](deploy-powershell.md)、 [範本](deploy-template.md) 或 [Visual Studio](deploy-visual-studio.md)，將雲端服務部署 (延伸支援) 。
