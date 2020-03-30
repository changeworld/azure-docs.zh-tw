---
title: Azure 紅帽開放Shift 4.3 的 Azure 監視器集成
description: 瞭解如何在 Microsoft Azure 紅帽開放Shift 群集上啟用 Azure 監視器。
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: a784fc070400995c56d16a3bc264d589bcb1f64e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082841"
---
# <a name="azure-monitor-integration-for-azure-red-hat-openshift-43"></a>Azure 紅帽開放Shift 4.3 的 Azure 監視器集成

> [!IMPORTANT] 
> 請注意，Azure 紅帽 OpenShift 4.3 目前僅在美國東部提供私人預覽版。 專用預覽接受僅通過邀請。 在嘗試啟用此功能之前，請務必註冊訂閱[：Azure 紅帽開放移位專用預覽註冊](https://aka.ms/aro-preview-register)

> [!NOTE]
> 預覽功能是自助服務，提供時和可用，不在服務等級協定 （SLA） 和有限保修中。 因此，這些功能不適合生產用途。

本文介紹如何為 OpenShift 4.3 群集啟用 Azure 監視器的專用預覽，這些群集託管在 prem 上或任何雲環境中。 相同的說明也適用于啟用 Azure 紅帽 OpenShift （ARO） 4.3 群集的監視。  

## <a name="prerequisites"></a>Prerequisites

- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [頭盔 3](https://helm.sh/docs/intro/install/)
- 訪問庫貝內斯群集的庫貝康格
- Azure 訂用帳戶的存取權
- 訪問 OpenShift 4.3 群集以安裝容器頭盔圖的 Azure 監視器
- Azure 訂閱上的最小參與者 RBAC 角色許可權  
- 監視代理需要以下出站埠 - 域將監視資料發送到 Azure 監視器後端（如果代理/防火牆阻止）：
  - +.ods.opinsights.azure.com 443
  - +.oms.opinsights.azure.com 443
  - *.blob.core.windows.net 443
  - dc.services.visualstudio.com 443

## <a name="onboarding"></a>登入

> [!TIP]
> 該腳本使用 bash 4 功能，因此請確保您的 bash 是最新的。 您可以使用 檢查當前版本。 `bash --version`

### <a name="download-the-onboarding-script"></a>下載載入腳本

```bash
curl -LO  https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/openshiftV4/onboarding_azuremonitor_for_containers.sh
```

使用庫伯奈斯群集的 azure 訂閱 Id、工作區區域、群集名稱和上下文執行以下腳本。

```bash
bash onboarding_azuremonitor_for_containers.sh <azureSubscriptionId> <azureRegionforLogAnalyticsWorkspace> <clusterName> <kubeconfigContextNameOftheCluster>
```

例如：

```bash
 bash onboarding_azuremonitor_for_containers.sh 27ac26cf-a9f0-4908-b300-9a4e9a0fb205 eastus myocp42 admin 
```

## <a name="configure-agent-data-collection"></a>配置代理資料收集

預設情況下，監視代理收集除 kube 系統之外在所有命名空間中運行的所有容器的 [stdout; sderr] 容器日誌。  如果要配置特定于特定命名空間或命名空間的容器日誌集合，可以參考[容器見解代理配置](../azure-monitor/insights/container-insights-agent-config.md)。 在這裡，您可以使用配置映射配置具有所需資料收集設置的監視代理。

## <a name="configure-scraping-of-prometheus-metrics"></a>配置普羅米杜斯指標的刮擦

容器的 Azure 監視器會刮掉 Prometheus 指標並引入 Azure 監視器後端。 有關如何配置普羅米塞斯刮擦的說明，請參閱[容器見解普羅米塞斯配置](../azure-monitor/insights/container-insights-prometheus-integration.md)。

成功加入後，導航到[混合監視](https://aka.ms/azmon-containers-hybrid)，並將"**全部"** 選擇"環境"以查看新加入的 OpenShift v4 群集。

## <a name="disable-monitoring"></a>停用監視

如果要禁用監視，可以使用以下命令刪除容器蠕默監視器圖表，以停止將監視資料收集和引入到 Azure 監視器中，以訪問容器後端。

``` bash
helm del azmon-containers-release-1
```

## <a name="update-monitoring"></a>更新監視

使用相同的參數重新運行[載入](#onboarding)腳本，以更新到最新的 Helm 圖表。

## <a name="after-successful-onboarding"></a>成功加入後

導航到[混合監視](https://aka.ms/azmon-containers-hybrid)，您可以在 **"監視群集"** 選項卡中看到新啟用的 OpenShift/ARO v4 群集狀態。在那裡，您可以通過按一下 **"群集"** 列深入瞭解指標、庫存和日誌等。

## <a name="supported-features"></a>支援的功能

有關支援的特性和功能的詳細資訊，請參閱[容器見解概述](../azure-monitor/insights/container-insights-overview.md)。

聯繫我們，askcoin@microsoft.com瞭解回饋和問題。

## <a name="next-steps"></a>後續步驟

要瞭解有關監視的更多詳細資訊，請參閱：
- [容器洞察概述](../azure-monitor/insights/container-insights-overview.md)

- [日誌查詢概述](../azure-monitor/log-query/log-query-overview.md)
