---
title: 容器區域對應的 Azure 監視器
description: 本文說明容器、Log Analytics 工作區和自訂計量的 Azure 監視器之間支援的區域對應。
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: a058f9cac987bb5c7130019f50370c6a176b09ac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75403415"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>容器的 Azure 監視器支援的區域對應

 啟用容器的 Azure 監視器時，只有特定區域支援連結 Log Analytics 工作區和 AKS 叢集，以及收集提交至 Azure 監視器的自訂計量。

## <a name="log-analytics-workspace-supported-mappings"></a>Log Analytics 工作區支援的對應

AKS 叢集資源或 Log Analytics 工作區可位於其他區域，下表顯示我們的對應。

|**AKS 叢集區域** | **Log Analytics 工作區區域** |
|-----------------------|------------------------------------|
|**非洲** | |
|SouthAfricaNorth |WestEurope |
|SouthAfricaWest |WestEurope |
|**澳大利亞** | |
|AustraliaEast |AustraliaEast |
|AustraliaCentral |AustraliaCentral |
|AustraliaCentral2 |AustraliaCentral |
|AustraliaEast |AustraliaEast |
|**亞太地區** | |
|EastAsia |EastAsia |
|SoutheastAsia |SoutheastAsia |
|**巴西** | |
|BrazilSouth | SouthCentralUS |
|**Canada** ||
|CanadaCentral |CanadaCentral |
|CanadaEast |CanadaCentral |
|**歐洲** | |
|FranceCentral |FranceCentral |
|FranceSouth |FranceCentral |
|NorthEurope |NorthEurope |
|UKSouth |UKSouth |
|UKWest |UKSouth |
|WestEurope |WestEurope |
|**印度** | |
|CentralIndia |CentralIndia |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**日本** | |
|JapanEast |JapanEast |
|JapanWest |JapanEast |
|**韓國** | |
|KoreaCentral |KoreaCentral |
|KoreaSouth |KoreaCentral |
|**回饋** | |
|CentralUS |CentralUS|
|EastUS |EastUS |
|EastUS2 |EastUS2 |
|WestUS |WestUS |
|WestUS2 |WestUS2 |
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|
|US Gov 維吉尼亞州 |US Gov 維吉尼亞州 |

<sup>1</sup>由於容量限制，建立新資源時無法使用區域。 這包括 Log Analytics 工作區。 不過，區域中預先存在的連結資源應該會繼續工作。

## <a name="custom-metrics-supported-regions"></a>自訂計量支援的區域

僅支援從 Azure Kubernetes Services （AKS）叢集節點和 pod 收集計量，以便在下列[Azure 區域](../platform/metrics-custom-overview.md#supported-regions)中發佈為自訂計量。

## <a name="next-steps"></a>後續步驟

若要開始監視您的 AKS 叢集，請參閱[如何啟用容器的 Azure 監視器](container-insights-onboard.md)，以瞭解啟用監視的需求和可用方法。  
