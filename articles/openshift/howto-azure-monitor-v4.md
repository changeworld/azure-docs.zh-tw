---
title: Azure Red Hat OpenShift 4.3 的 Azure 監視器整合
description: 瞭解如何在您的 Microsoft Azure Red Hat OpenShift 叢集上啟用 Azure 監視器。
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: a784fc070400995c56d16a3bc264d589bcb1f64e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79082841"
---
# <a name="azure-monitor-integration-for-azure-red-hat-openshift-43"></a>Azure Red Hat OpenShift 4.3 的 Azure 監視器整合

> [!IMPORTANT] 
> 請注意，Azure Red Hat OpenShift 4.3 目前僅適用于美國東部的個人預覽版。 私人預覽接受僅限邀請。 請務必先註冊您的訂用帳戶，再嘗試啟用此功能： [Azure Red Hat OpenShift 私人預覽註冊](https://aka.ms/aro-preview-register)

> [!NOTE]
> 預覽功能是自助服務，並依原樣提供，並會在服務等級協定（SLA）和有限擔保中予以排除。 因此，這些功能不適用於生產用途。

本文說明如何針對裝載于-內部部署或任何雲端環境中的 OpenShift 4.3 叢集，啟用容器 Azure 監視器的私人預覽。 相同的指示也適用于啟用 Azure Red Hat OpenShift （ARO）4.3 叢集的監視。  

## <a name="prerequisites"></a>先決條件

- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Helm 3](https://helm.sh/docs/intro/install/)
- 存取 kubernetes 叢集的 kubeconfig
- Azure 訂用帳戶的存取權
- 存取 OpenShift 4.3 叢集以安裝容器的 Azure 監視器 Helm 圖表
- Azure 訂用帳戶的最小參與者 RBAC 角色許可權  
- 監視代理程式需要下列輸出埠和網域，將監視資料傳送至 Azure 監視器後端（如果是由 proxy/防火牆封鎖）：
  - *. ods.opinsights.azure.com 443
  - *. oms.opinsights.azure.com 443
  - *. blob.core.windows.net 443
  - dc.services.visualstudio.com 443

## <a name="onboarding"></a>入門訓練

> [!TIP]
> 此腳本會使用 bash 4 功能，因此請確定您的 bash 是最新的。 您可以使用來檢查目前的`bash --version`版本。

### <a name="download-the-onboarding-script"></a>下載上架腳本

```bash
curl -LO  https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/openshiftV4/onboarding_azuremonitor_for_containers.sh
```

使用 azureSubscriptionId、工作區區域、clusterName 和 Kubernetes 叢集的內容來執行下列腳本。

```bash
bash onboarding_azuremonitor_for_containers.sh <azureSubscriptionId> <azureRegionforLogAnalyticsWorkspace> <clusterName> <kubeconfigContextNameOftheCluster>
```

例如：

```bash
 bash onboarding_azuremonitor_for_containers.sh 27ac26cf-a9f0-4908-b300-9a4e9a0fb205 eastus myocp42 admin 
```

## <a name="configure-agent-data-collection"></a>設定代理程式資料收集

根據預設，監視代理程式會收集所有命名空間（kube 除外）中執行之所有容器的 {stdout; stderr} 容器記錄。  如果您想要設定特定命名空間或命名空間專屬的容器記錄集合，您可以參考[Container Insights 代理程式](../azure-monitor/insights/container-insights-agent-config.md)設定。 在這裡，您可以使用 config map，以所需的資料收集設定來設定監視代理程式。

## <a name="configure-scraping-of-prometheus-metrics"></a>設定 Prometheus 計量的抓取

適用于容器的 Azure 監視器會抓取 Prometheus 計量，並內嵌至 Azure 監視器後端。 如需如何設定 Prometheus 抓取的指示，請參閱[Container Insights Prometheus](../azure-monitor/insights/container-insights-prometheus-integration.md)設定。

成功上架之後，流覽至 [[混合式監視](https://aka.ms/azmon-containers-hybrid)]，然後選取 [環境 **] 為 [全部]** ，以查看您新上架的 OpenShift v4 叢集。

## <a name="disable-monitoring"></a>停用監視

如果您想要停用監視，您可以使用下列命令來停止收集和內嵌監視資料至容器後端的 Azure 監視器，以刪除 Azure 監視器的容器 Helm 圖表。

``` bash
helm del azmon-containers-release-1
```

## <a name="update-monitoring"></a>更新監視

依照[上線區段中所述的相同](#onboarding)參數重新執行上線腳本，以更新為最新的 Helm 圖表。

## <a name="after-successful-onboarding"></a>成功上架之後

流覽至 [[混合式監視](https://aka.ms/azmon-containers-hybrid)]，您就可以在 [**受監視**的叢集] 索引標籤中，看到具有健康狀態的新啟用 OpenShift/ARO v4 叢集。在這裡，您可以**按一下 [叢集**] 資料行，以取得更深入的見解，例如計量、清查和記錄。

## <a name="supported-features"></a>支援的功能

如需支援的功能和功能的詳細資訊，請參閱[Container Insights 總覽](../azure-monitor/insights/container-insights-overview.md)。

請透過與askcoin@microsoft.com我們聯絡，取得意見反應和問題。

## <a name="next-steps"></a>後續步驟

若要深入瞭解監視，請參閱：
- [容器深入解析總覽](../azure-monitor/insights/container-insights-overview.md)

- [記錄查詢總覽](../azure-monitor/log-query/log-query-overview.md)
