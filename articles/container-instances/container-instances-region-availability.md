---
title: 資源可用性(按區域)
description: Azure 容器執行個體服務的計算和記憶體資源在不同 Azure 區域中的可用性。
ms.topic: article
ms.date: 02/19/2020
ms.author: danlep
ms.openlocfilehash: c84ab7833f7c90d5d4b3c340c268fd9f75e20da9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399408"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Azure 容器執行個體在 Azure 區域中的資源可用性

本文詳細介紹了 Azure 區域和目標作業系統中 Azure 容器實例計算、記憶體和存儲資源的可用性。 

顯示的值為每個[容器群組](container-instances-container-groups.md)部署可用的最大資源。 以下是本文章發行時的值。 

> [!NOTE]
> 在這些資源限制下建立的容器群組會受限於部署區域的可用性。 地區負載過重時，您在部署執行個體時可能會失敗。 要緩解此類部署失敗,請嘗試使用較低的資源設置部署實例,或稍後嘗試部署,或在具有可用資源的不同區域中嘗試部署。

如需部署中的配額和其他限制的相關資訊，請參閱 [Azure 容器執行個體的配額和限制](container-instances-quotas.md)。

## <a name="availability---general"></a>可用性 - 一般

以下區域和最大資源可用於具有 Linux[和支援的](container-instances-faq.md#what-windows-base-os-images-are-supported)基於 Windows Server 2016 的容器組。

| 區域 | OS | 最大 CPU | 最大記憶體 (GB) | 儲存體 (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| 巴西南部、加拿大中部、印度中部、美國中部、東亞、美國東部、美國東部 2、北歐、美國中南部、東南亞、南印度、英國南部、西歐、美國西部、美國西部 2 | Linux | 4 | 16 | 50 |
| 澳大利亞東部、日本東部 | Linux | 2 | 8 | 50 |
| 美國中北部 | Linux | 2 | 3.5 | 50 |
| 巴西 南部、日本東部、西歐 | Windows | 4 | 16 | 20 |
| 美國東部、美國西部 | Windows | 4 | 14 | 20 |
| 澳大利亞東部、加拿大中部、印度中部、美國中部、東亞、美國東部 2、美國中北部、北歐、美國中南部、東南亞、南印度、英國南部、美國西部 2 | Windows | 2 | 3.5 | 20 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>可用性 - Windows Server 2019 LTSC，1809年部署 (預覽)

以下區域和最大資源可用於基於 Windows Server 2019 的容器組(預覽)。

| 區域 | OS | 最大 CPU | 最大記憶體 (GB) | 儲存體 (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| 澳大利亞東部、巴西南部、加拿大中部、印度中部、美國中部、東亞、美國東部、日本東部、美國中北部、北歐、美國中南部、東南亞、南印度、英國南部、西歐 | Windows | 4 | 16 | 20 |
| 美國東部 2, 西美國 2 | Windows | 2 | 3.5 | 20 |


## <a name="availability---virtual-network-deployment"></a>可用性 - 虛擬網路部署

部署在[Azure 虛擬網路中](container-instances-vnet.md)的容器組可以使用以下區域和最大資源。

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>可用性 - GPU 資源 (預覽)

使用[GPU 資源](container-instances-gpu.md)部署的容器組可以使用以下區域和最大資源(預覽)。

> [!IMPORTANT]
> GPU 資源僅應要求可用。 要要求存取 GPU 資源,請提交[Azure 支援要求][azure-support]。

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>後續步驟

如果您想要了解其他區域或增加的資源可用性，請經由 [aka.ms/aci/feedback](https://aka.ms/aci/feedback) 告知小組人員。

如需對容器執行個體部署疑難排解的相關資訊，請參閱[對 Azure 容器執行個體的部署問題進行疑難排解](container-instances-troubleshooting.md)。


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
