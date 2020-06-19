---
title: 使用 Application Insights 來監視您的 Azure Kubernetes Service (AKS) 或其他 Kubernetes 裝載的應用程式 - Azure 監視器 | Microsoft Docs
description: Azure 監視器會在您的 Kubernetes 叢集上使用服務網格技術 Istio，為任何 Kubernetes 裝載的應用程式提供應用程式監視。 這讓您能夠收集與在叢集中執行之 Pod 的傳入和傳出要求相關的 Application Insights 遙測。
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: a5e73039db541023b1fd4a9b75e7c14030c8e219
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797893"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications-with-istio---deprecated"></a>使用 Istio 針對 Kubernetes 裝載的應用程式進行的零檢測設備應用程式監視 - 已過時

> [!IMPORTANT]
> 此功能目前即將淘汰，將於 2020 年 8 月 1 日之後不再支援。
> 目前，只有[透過獨立代理程式的 Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) \(部分機器翻譯\)，才能啟用無程式碼監視。 針對其他語言，請使用 SDK 來監視 AKS 上的應用程式：[ASP.Net Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) \(部分機器翻譯\)、[ASP.Net](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)、[Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs) \(部分機器翻譯\)、[JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript) \(部分機器翻譯\) 及 [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python) \(部分機器翻譯\)。

Azure 監視器現在會在您的 Kubernetes 叢集上運用服務網格技術 Istio，為任何 Kubernetes 裝載的應用程式提供全新的應用程式監視。 使用預設的 Application Insight 功能，例如，使用[應用程式對應](../../azure-monitor/app/app-map.md)來建立相依性模型、使用[即時計量資料流](../../azure-monitor/app/live-stream.md)進行即時監視，以及利用[預設儀表板](../../azure-monitor/app/overview-dashboard.md)、[計量瀏覽器](../../azure-monitor/platform/metrics-getting-started.md)和[活頁簿](../../azure-monitor/platform/workbooks-overview.md)來產生功能強大的視覺效果。 此功能將協助使用者，在選取的 Kubernetes 命名空間中，找出其所有 Kubernetes 工作負載之間的效能瓶頸和失敗作用區。 藉由使用 Istio 之類的技術來讓您的現有服務網格投資發揮最大效益，Azure 監視器就能夠自動檢測應用程式監視，而不需對您應用程式的程式碼進行任何修改。

> [!NOTE]
> 這是在 Kubernetes 上執行應用程式監視的眾多方式之一。 您也可以使用 [Application Insights SDK](../../azure-monitor/azure-monitor-app-hub.yml) 來檢測裝載於 Kubernetes 的任何應用程式，而不需使用服務網格。 若要在不使用 SDK 檢測應用程式的情況下監視 Kubernetes，您可以使用下列方法。

## <a name="prerequisites"></a>Prerequisites

- 一個[Kubernetes 叢集](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads) \(部分機器翻譯\)。
- 對要執行 *kubectl* 之叢集的主控台存取權。
- 一個 [Application Insights 資源](create-new-resource.md)。
- 具有一個服務網格。 如果您的叢集並未部署 Istio，則您可以了解如何[在 Azure Kubernetes Service 中安裝和使用 Istio](https://docs.microsoft.com/azure/aks/istio-install) \(部分機器翻譯\)。

## <a name="capabilities"></a>功能

針對 Kubernetes 裝載的應用程式使用零檢測設備應用程式監視，您將能夠使用：

- [應用程式對應](../../azure-monitor/app/app-map.md)
- [即時資料流計量](../../azure-monitor/app/live-stream.md)
- [儀表板](../../azure-monitor/app/overview-dashboard.md)
- [計量瀏覽器](../../azure-monitor/platform/metrics-getting-started.md)
- [分散式追蹤](../../azure-monitor/app/distributed-tracing.md)
- [端對端交易監視](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>安裝步驟

為了啟用此解決方案，我們將執行下列步驟：
- 部署應用程式 (如果尚未部署)。
- 確定應用程式是服務網格的一部分。
- 觀察收集到的遙測。

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>設定應用程式以使用服務網格

Istio 支援兩種[檢測 Pod](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/) \(英文\) 的方式。
在大部分情況下，最簡單的方式是使用 *istio-injection* 標籤，來標記包含您應用程式的 Kubernetes 命名空間：

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> 由於服務網格會透過有線網路提取資料，因此，我們無法攔截加密的流量。 對於未離開叢集的流量，請使用未加密的通訊協定 (例如 HTTP)。 對於必須加密的外部流量，則考慮在輸入控制器上[設定 TLS 終止](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) \(英文\)。

在服務網格外部執行的應用程式不會受到影響。

### <a name="deploy-your-application"></a>部署應用程式

- 將您的應用程式部署到 *my-app-namespace* 命名空間。 如果已部署應用程式，而且您已遵循上述自動側車插入方法，則必須重新建立 Pod，以確保 Istio 會插入其側車；起始滾動式更新，或刪除個別 Pod 並等候其重新建立。
- 確定您的應用程式符合 [Istio 需求](https://istio.io/docs/setup/kubernetes/prepare/requirements/) \(英文\)。

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>針對 Kubernetes 裝載的應用程式部署零檢測設備應用程式監視

1. 下載並解壓縮 [Application Insights 配接器版本](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/) \(英文\)。
2. 瀏覽至發行資料夾中的 */src/kubernetes/* 。
3. 編輯 *application-insights-istio-mixer-adapter-deployment.yaml*
    - 編輯 *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* 環境變數的值，以包含 Azure 入口網站中要包含遙測之 Application Insights 資源的檢測金鑰。
    - 如有需要，請編輯 *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* 環境變數的值，以包含您想要啟用監視的命名空間清單 (以逗號分隔)。 將其保留為空白以監視所有命名空間。
4. 執行下列程式碼，以套用在 *src/kubernetes/* 下找到的「每個」YAML 檔案 (您仍然必須位於 */src/kubernetes/* )：

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>驗證部署

- 確定已部署 Application Insights 配接器：

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> 在某些情況下，微調調整是必要的。 若要在收集個別 Pod 時包含或排除其遙測，請在該 Pod 上使用 *appinsights/monitoring.enabled* 標籤。 這將優先於所有以命名空間為基礎的設定。 將 *appinsights/monitoring.enabled* 設定為 *True* 以包含 Pod，設定為 *False* 則可將其排除。

### <a name="view-application-insights-telemetry"></a>檢視 Application Insights 遙測

- 針對您的應用程式產生範例要求，以確認監視可正常運作。
- 您應該會在 3-5 分鐘內，開始看到遙測出現在 Azure 入口網站中。 請務必檢查入口網站中 Application Insights 資源的 [應用程式對應] 區段。

## <a name="troubleshooting"></a>疑難排解

以下是當遙測未如預期般出現在 Azure 入口網站時要使用的疑難排解流程。

1. 確定應用程式處於負載狀態，且會以一般 HTTP 傳送/接收要求。 由於遙測會透過有線網路來提取，因此，不支援加密的流量。 如果沒有傳入或傳出要求，則不會有任何遙測。
2. 確定 *application-insights-istio-mixer-adapter-deployment.yaml* 內的 *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* 環境變數中提供正確的檢測金鑰。 檢測金鑰可於 Azure 入口網站中 Application Insights 資源的 [概觀] 索引標籤上找到。
3. 確定 *application-insights-istio-mixer-adapter-deployment.yaml* 內的 *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* 環境變數中提供正確的 Kubernetes 命名空間。 將其保留為空白以監視所有命名空間。
4. 確定 Istio 已透過側車插入您應用程式的 Pod。 確認 Istio 的側車存在於每個 Pod 上。

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   確認有一個名為 *istio-proxy* 的容器正在 Pod 上執行。

5. 檢視 Application Insights 配接器的追蹤。

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   已接收的遙測項目計數每分鐘會更新一次。 如果此計數並非每分鐘增加，則表示 Istio 未將任何遙測傳送到配接器。
   在記錄中尋找任何錯誤。
6. 如果已確定未將遙測傳送到「適用於 Kubernetes 的 Application insights」配接器，請檢查 Istio 的 Mixer 記錄，以找出為什麼其不會將資料傳送到該配接器：

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   尋找是否有任何錯誤，特別是與 *applicationinsightsadapter* 配接器通訊有關的錯誤。

## <a name="faq"></a>常見問題集

如需此專案進度的最新資訊，請瀏覽[適用於 Istio Mixer 專案之 Application Insights 配接器的 GitHub](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq) \(英文\)。

## <a name="uninstall"></a>解除安裝

若要解除安裝產品，請針對 *src/kubernetes/* 底下的「每個」YAML 檔案執行：

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 監視器和容器如何搭配使用，請瀏覽[適用於容器的 Azure 監視器概觀](../../azure-monitor/insights/container-insights-overview.md)
