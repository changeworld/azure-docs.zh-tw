---
title: 容器區域映射的 Azure 監視器
description: 本文介紹了容器 Azure 監視器、日誌分析工作區和自訂指標之間支援的區域映射。
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: a058f9cac987bb5c7130019f50370c6a176b09ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75403415"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Azure 監視器支援的容器區域映射

 為容器啟用 Azure 監視器時，僅支援某些區域連結日誌分析工作區和 AKS 群集，以及收集提交到 Azure 監視器的自訂指標。

## <a name="log-analytics-workspace-supported-mappings"></a>日誌分析工作區支援的映射

AKS 群集資源或日誌分析工作區可以駐留在其他區域，下表顯示了我們的映射。

|**AKS 群集區域** | **日誌分析工作區區域** |
|-----------------------|------------------------------------|
|**非洲** | |
|南非北部 |WestEurope |
|南非西部 |WestEurope |
|**澳大利亞** | |
|澳大利亞東部 |澳大利亞東部 |
|澳大利亞中部 |澳大利亞中部 |
|澳大利亞中心2 |澳大利亞中部 |
|澳大利亞東部 |澳大利亞東部 |
|**亞太** | |
|EastAsia |EastAsia |
|SoutheastAsia |SoutheastAsia |
|**巴西** | |
|巴西南部 | 中南部 |
|**Canada** ||
|CanadaCentral |CanadaCentral |
|CanadaEast |CanadaCentral |
|**歐洲** | |
|法國中部 |法國中部 |
|法國南部 |法國中部 |
|北歐 |北歐 |
|UKSouth |UKSouth |
|英國西部 |UKSouth |
|WestEurope |WestEurope |
|**印度** | |
|CentralIndia |CentralIndia |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**日本** | |
|JapanEast |JapanEast |
|日本西部 |JapanEast |
|**韓國** | |
|KoreaCentral |KoreaCentral |
|KoreaSouth |KoreaCentral |
|**我們** | |
|中央 |中央|
|EastUS |EastUS |
|EastUS2 |EastUS2 |
|韋斯特烏斯 |韋斯特烏斯 |
|WestUS2 |WestUS2 |
|西中烏斯<sup>1</sup>|東US<sup>1</sup>|
|US Gov 維吉尼亞州 |US Gov 維吉尼亞州 |

<sup>1</sup>由於容量限制，在創建新資源時該區域不可用。 這包括日誌分析工作區。 然而，該區域已有的相互聯繫資源應繼續發揮作用。

## <a name="custom-metrics-supported-regions"></a>受支援的自訂指標區域

支援從 Azure Kubernetes 服務 （AKS） 叢集節點和 pod 收集指標，僅支援在以下[Azure 區域](../platform/metrics-custom-overview.md#supported-regions)中作為自訂指標發佈。

## <a name="next-steps"></a>後續步驟

要開始監視 AKS 群集，請查看[如何啟用容器的 Azure 監視器](container-insights-onboard.md)以瞭解啟用監視的要求和可用方法。  
