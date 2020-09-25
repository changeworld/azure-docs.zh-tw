---
title: 容器區域對應的 Azure 監視器
description: 描述容器、Log Analytics 工作區和自訂計量 Azure 監視器之間所支援的區域對應。
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: 9e3b8635c70dfdf33b0a062be80c948cd77923cc
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91272900"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>容器 Azure 監視器支援的區域對應

 啟用容器的 Azure 監視器時，僅支援某些區域以連結 Log Analytics 工作區和 AKS 叢集，並收集提交至 Azure 監視器的自訂計量。

## <a name="log-analytics-workspace-supported-mappings"></a>Log Analytics 工作區支援的對應

[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)中會列出支援的 AKS 區域。 Log Analytics 工作區必須位於下表所欄區域以外的相同區域中。 觀看 [AKS 的版本](https://github.com/Azure/AKS/releases) 資訊以取得更新。


|**AKS 叢集區域** | **Log Analytics 工作區區域** |
|-----------------------|------------------------------------|
|**非洲** | |
|SouthAfricaNorth |WestEurope |
|SouthAfricaWest |WestEurope |
|**澳大利亞** | |
|AustraliaCentral2 |AustraliaCentral |
|**巴西** | |
|BrazilSouth | SouthCentralUS |
|**加拿大** ||
|CanadaEast |CanadaCentral |
|**歐洲** | |
|FranceSouth |FranceCentral |
|UKWest |UKSouth |
|**印度** | |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**日本** | |
|JapanWest |JapanEast |
|**南韓** | |
|KoreaSouth |KoreaCentral |
|**美國** | |
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|


<sup>1</sup> 因為容量限制，所以在建立新資源時無法使用該區域。 這包括 Log Analytics 工作區。 不過，區域中預先存在的連結資源應該會繼續運作。

## <a name="custom-metrics-supported-regions"></a>自訂計量支援的區域

從 Azure Kubernetes Services (AKS) 叢集節點和 pod 收集計量，僅支援在下列 [Azure 區域](../platform/metrics-custom-overview.md#supported-regions)中發佈為自訂計量。

## <a name="next-steps"></a>後續步驟

若要開始監視您的 AKS 叢集，請參閱 [如何啟用容器的 Azure 監視器](container-insights-onboard.md) ，以瞭解啟用監視的需求和可用方法。  
