---
title: 依區域的資源可用性
description: Azure 容器執行個體服務的計算和記憶體資源在不同 Azure 區域中的可用性。
ms.topic: article
ms.date: 04/27/2020
ms.custom: references_regions
ms.openlocfilehash: e4fbf1023863f9f4c46e6bd2266f72ff2f7d7adc
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395864"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Azure 容器執行個體在 Azure 區域中的資源可用性

本文詳細說明 azure 容器實例計算、記憶體和儲存體資源在 Azure 區域中的可用性，以及目標作業系統的可用性。 如需 Azure 容器實例可用區域的一般清單，請參閱 [可用的區域](https://azure.microsoft.com/regions/services/)。

顯示的值為每個[容器群組](container-instances-container-groups.md)部署可用的最大資源。 以下是本文章發行時的值。

> [!NOTE]
> 在這些資源限制下建立的容器群組會受限於部署區域的可用性。 地區負載過重時，您在部署執行個體時可能會失敗。 若要減輕這類部署失敗的問題，請嘗試部署資源設定較低的實例，或稍後或在具有可用資源的不同區域中試用您的部署。

如需部署中的配額和其他限制的相關資訊，請參閱 [Azure 容器執行個體的配額和限制](container-instances-quotas.md)。

## <a name="linux-container-groups"></a>Linux 容器群組

下欄區域和最大資源可供具有 Linux 容器的容器群組進行一般部署、 [Azure 虛擬網路](container-instances-vnet.md) 部署，以及使用 [GPU 資源](container-instances-gpu.md) 的部署 (預覽) 。

> [!IMPORTANT]
> 區域中的資源上限會根據您的部署而有所不同。 例如，在 Azure 虛擬網路部署中，區域可能會有不同于一般部署的最大 CPU 和記憶體大小。 相同區域對於具有 GPU 資源的部署，也可能會有不同的最大值集。 請先確認您的部署類型，再檢查下表以瞭解您區域中的最大值。

| 區域 | 最大 CPU | 最大記憶體 (GB)  | VNET 最大 CPU | VNET 最大記憶體 (GB)  | 儲存體 (GB) |  (預覽版的 GPU Sku)  |
| -------- | :---: | :---: | :----: | :-----: | :-------: | :----: |
| 澳大利亞東部 | 4 | 16 | 4 | 16 | 50 | N/A |
| 巴西南部 | 4 | 16 | 2 | 8 | 50 | N/A |
| 加拿大中部 | 4 | 16 | 4 | 16 | 50 | N/A |
| 印度中部 | 4 | 16 | N/A | N/A | 50 | V100 |
| 美國中部 | 4 | 16 | 4 | 16 | 50 | N/A |
| 東亞 | 4 | 16 | 4 | 16 | 50 | N/A |
| 美國東部 | 4 | 16 | 4 | 16 | 50 | K80、P100、V100 |
| 美國東部 2 | 4 | 16 | 4 | 16 | 50 | N/A |
| 法國中部 | 4 | 16 | 4 | 16 | 50 | N/A |
| 日本東部 | 2 | 8 | 4 | 16 | 50 | N/A |
| 南韓中部 | 4 | 16 | N/A | N/A | 50 | N/A |
| 美國中北部 | 2 | 3.5 | 4 | 16 | 50 | K80、P100、V100 |
| 歐洲北部 | 4 | 16 | 4 | 16 | 50 | K80 |
| 美國中南部 | 4 | 16 | 4 | 16 | 50 | N/A |
| 東南亞 | 4 | 16 | 4 | 16 | 50 | P100、V100 |
| 印度南部 | 4 | 16 | N/A | N/A | 50 | N/A |
| 英國南部 | 4 | 16 | 4 | 16 | 50 | N/A |
| 美國中西部| 4 | 16 | 4 | 16 | 50 | N/A |
| 西歐 | 4 | 16 | 4 | 16 | 50 | K80、P100、V100 |
| 美國西部 | 4 | 16 | 4 | 16 | 50 | N/A |
| 美國西部 2 | 4 | 16 | 4 | 16 | 50 | K80、P100、V100 |

下列資源可供使用 [GPU 資源](container-instances-gpu.md) 部署 (預覽版) 的容器群組使用。

| GPU Sku | GPU 計數 | 最大 CPU | 最大記憶體 (GB)  | 儲存體 (GB) |
| --- | --- | --- | --- | --- |
| K80 | 1 | 6 | 56 | 50 |
| K80 | 2 | 12 | 112 | 50 |
| K80 | 4 | 24 | 224 | 50 |
| P100、V100 | 1 | 6 | 112 | 50 |
| P100、V100 | 2 | 12 | 224 | 50 |
| P100、V100 | 4 | 24 | 448 | 50 |

## <a name="windows-container-groups"></a>Windows 容器群組

[支援和預覽](container-instances-faq.md#what-windows-base-os-images-are-supported)Windows Server 容器的容器群組可使用下欄區域和資源上限。

###  <a name="windows-server-2016"></a>Windows Server 2016

> [!NOTE]
> 如需1B、2B 和3B 主機的詳細資訊，請參閱 [主機和容器版本相容性](/virtualization/windowscontainers/deploy-containers/update-containers#host-and-container-version-compatibility) 。

| 區域 | 1B/2B CPU 限定 | 1B/2B Max Memory (GB)  |最高 3B CPU | 最高3B 記憶體 (GB)  | 儲存體 (GB) |
| -------- | :---: | :---: | :----: | :-----: | :-------: |
| 澳大利亞東部 | 2 | 8 | 2 | 3.5 | 20 |
| 巴西南部 | 4 | 16 | 4 | 16 | 20 |
| 加拿大中部 | 2 | 3.5 | 2 | 3.5 | 20 |
| 印度中部 | 2 | 3.5 | 2 | 3.5 | 20 |
| 美國中部 | 2 | 3.5 | 2 | 3.5 | 20 |
| 東亞 | 2 | 3.5 | 2 | 3.5 | 20 |
| 美國東部 | 4 | 16 | 2 | 8 | 20 |
| 美國東部 2 | 2 | 3.5 | 4 | 16 | 20 |
| 日本東部 | 4 | 16 | 4 | 16 | 20 |
| 南韓中部 | 4 | 16 | 4 | 16 | 20 |
| 美國中北部 | 4 | 16 | 4 | 16 | 20 |
| 歐洲北部 | 2 | 3.5 | 2 | 8 | 20 |
| 美國中南部 | 2 | 3.5 | 2 | 3.5 | 20 |
| 東南亞 | N/A | N/A | 2 | 3.5 | 20 |
| 印度南部 | 2 | 3.5 | 2 | 3.5 | 20 |
| 英國南部 | 2 | 8 | 2 | 3.5 | 20 |
| 美國中西部 | 4 | 16 | 4 | 16 | 20 |
| 西歐 | 4 | 16 | 4 | 16 | 20 |
| 美國西部 | 4 | 16 | 2 | 8 | 20 |
| 美國西部 2 | 2 | 3.5 | 2 | 3.5 | 20 |


### <a name="windows-server-2019-ltsc"></a>Windows Server 2019 LTSC

> [!NOTE]
> 如需1B、2B 和3B 主機的詳細資訊，請參閱 [主機和容器版本相容性](/virtualization/windowscontainers/deploy-containers/update-containers#host-and-container-version-compatibility) 。

| 區域 | 1B/2B CPU 限定 | 1B/2B Max Memory (GB)  |最高 3B CPU | 最高3B 記憶體 (GB)  | 儲存體 (GB) |
| -------- | :---: | :---: | :----: | :-----: | :-------: |
| 澳大利亞東部 | 4 | 16 | 4 | 16 | 20 |
| 巴西南部 | 4 | 16 | 4 | 16 | 20 |
| 加拿大中部 | 4 | 16 | 4 | 16 | 20 |
| 印度中部 | 4 | 16 | 4 | 16 | 20 |
| 美國中部 | 4 | 16 | 4 | 16 | 20 |
| 東亞 | 4 | 16 | 4 | 16 | 20 |
| 美國東部 | 4 | 16 | 4 | 16 | 20 |
| 美國東部 2 | 2 | 3.5 | 2 | 3.5 | 20 |
| 法國中部 | 4 | 16 | 4 | 16 | 20 |
| 日本東部 | N/A | N/A | 4 | 16 | 20 |
| 南韓中部 | 4 | 16 | 4 | 16 | 20 |
| 美國中北部 | 4 | 16 | 4 | 16 | 20 |
| 歐洲北部 | 4 | 16 | 4 | 16 | 20 |
| 美國中南部 | 4 | 16 | 4 | 16 | 20 |
| 東南亞 | 4 | 16 | 4 | 16 | 20 |
| 印度南部 | 4 | 16 | 4 | 16 | 20 |
| 英國南部 | 4 | 16 | 4 | 16 | 20 |
| 美國中西部 | 4 | 16 | 4 | 16 | 20 |
| 西歐 | 4 | 16 | 4 | 16 | 20 |
| 美國西部 | 4 | 16 | 4 | 16 | 20 |
| 美國西部 2 | 2 | 8 | 4 | 16 | 20 |

## <a name="next-steps"></a>後續步驟

如果您想要了解其他區域或增加的資源可用性，請經由 [aka.ms/aci/feedback](https://aka.ms/aci/feedback) 告知小組人員。

如需對容器執行個體部署疑難排解的相關資訊，請參閱[對 Azure 容器執行個體的部署問題進行疑難排解](container-instances-troubleshooting.md)。


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
