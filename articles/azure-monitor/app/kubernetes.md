---
title: 使用 Application Insights 來監視您的 Azure Kubernetes Service （AKS）或其他 Kubernetes 託管應用程式-Azure 監視器 |Microsoft Docs
description: Azure 監視器在您的 Kubernetes 叢集上使用服務網格技術 Istio，為任何 Kubernetes 裝載的應用程式提供應用程式監視。 這可讓您收集與叢集中執行之 pod 的傳入和傳出要求相關的 Application Insights 遙測。
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: c3b2b4e8868b73e22706c684d94371fe37885b9a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83119250"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications-with-istio---deprecated"></a>使用 Istio Kubernetes 裝載應用程式的零檢測應用程式監視-已被取代

> [!IMPORTANT]
> 此功能目前已淘汰，將于2020年8月1日之後不再受支援。
> 目前建議的版本僅適用于[透過獨立代理程式的 JAVA](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)。

Azure 監視器現在會利用 Kubernetes 叢集上的服務網格技術，為任何 Kubernetes 託管應用程式提供現成的應用程式監視。 使用預設的應用程式深入解析功能（例如[應用程式對應](../../azure-monitor/app/app-map.md)）來建立相依性模型，[即時計量資料流](../../azure-monitor/app/live-stream.md)使用[預設儀表板](../../azure-monitor/app/overview-dashboard.md)、計量[瀏覽器](../../azure-monitor/platform/metrics-getting-started.md)和活頁[簿](../../azure-monitor/app/usage-workbooks.md)的即時監視、強大的視覺效果。 這項功能可協助使用者找出所選 Kubernetes 命名空間內所有 Kubernetes 工作負載的效能瓶頸和失敗熱點。 藉由使用 Istio 之類的技術，讓您的現有服務網格投資發揮最大效益，Azure 監視器不需要修改應用程式的程式碼，就能進行自動檢測的應用程式監視。

> [!NOTE]
> 這是在 Kubernetes 上執行應用程式監視的眾多方法之一。您也可以使用[APPLICATION INSIGHTS SDK](../../azure-monitor/azure-monitor-app-hub.yml) ，檢測裝載于 Kubernetes 中的任何應用程式，而不需要服務網格。 若要監視 Kubernetes，而不使用 SDK 檢測應用程式，您可以使用下列方法。

## <a name="prerequisites"></a>先決條件

- [Kubernetes](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads)叢集。
- 主控台存取叢集以執行*kubectl*。
- [應用程式深入解析資源](create-new-resource.md)
- 具有服務網格。 如果您的叢集未部署 Istio，您可以瞭解如何[在 Azure Kubernetes Service 中安裝和使用 Istio](https://docs.microsoft.com/azure/aks/istio-install)。

## <a name="capabilities"></a>功能

藉由對 Kubernetes 裝載的應用程式使用零檢測應用程式監視，您將能夠使用：

- [應用程式對應](../../azure-monitor/app/app-map.md)
- [即時資料流計量](../../azure-monitor/app/live-stream.md)
- [儀表板](../../azure-monitor/app/overview-dashboard.md)
- [計量瀏覽器](../../azure-monitor/platform/metrics-getting-started.md)
- [分散式追蹤](../../azure-monitor/app/distributed-tracing.md)
- [端對端交易監視](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>安裝步驟

為了啟用此解決方案，我們將執行下列步驟：
- 部署應用程式（如果尚未部署）。
- 請確定應用程式是服務網格的一部分。
- 觀察已收集的遙測。

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>設定應用程式以使用服務網格

Istio 支援兩種[檢測 pod](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/)的方式。
在大部分情況下，最簡單的方式是使用*istio 插入*標籤標記包含應用程式的 Kubernetes 命名空間：

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> 因為服務網格會將資料從網路中提起，所以我們無法攔截加密的流量。 對於不離開叢集的流量，請使用未加密的通訊協定（例如 HTTP）。 對於必須加密的外部流量，請考慮在輸入控制器上[設定 TLS 終止](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls)。

在服務網格外執行的應用程式不會受到影響。

### <a name="deploy-your-application"></a>部署應用程式

- 將您的應用程式部署到*my 應用程式命名*空間。 如果已部署應用程式，而且您已遵循上述的自動側車插入方法，則必須重新建立 pod，以確保 Istio 插入其側車;起始輪流更新，或刪除個別 pod 並等候重新建立。
- 請確定您的應用程式符合[Istio 需求](https://istio.io/docs/setup/kubernetes/prepare/requirements/)。

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>針對 Kubernetes 裝載的應用程式部署零檢測應用程式監視

1. 下載並解壓縮[ *Application Insights 介面卡*版本](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/)。
2. 流覽至發行資料夾內的 */src/kubernetes/* 。
3. 編輯*應用程式-insights-istio-混音器-介面卡-部署 yaml*
    - 編輯*ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY*環境變數的值，以包含 Azure 入口網站中要包含遙測之 Application Insights 資源的檢測金鑰。
    - 如有需要，請編輯*ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES*環境變數的值，以包含您想要啟用監視的命名空間清單（以逗號分隔）。 將其保留為空白以監視所有命名空間。
4. 藉由執行下列動作，套用*src/kubernetes*下所找到的*每個*YAML 檔案（您仍然必須在 */src/kubernetes/* 內）：

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>驗證部署

- 請確定已部署 Application Insights 介面卡：

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> 在某些情況下，微調調整是必要的。 若要在收集個別 pod 時包含或排除其遙測，請使用*appinsights/monitoring。* 在該 pod 上啟用標籤。 這會優先于所有以命名空間為基礎的設定。 設定*appinsights/monitoring。啟用*為*true*可包含 pod，而為*false*則會將其排除。

### <a name="view-application-insights-telemetry"></a>查看 Application Insights 遙測

- 針對您的應用程式產生範例要求，以確認監視是否正常運作。
- 在3-5 分鐘內，您應該會開始看到遙測出現在 Azure 入口網站中。 請務必在入口網站中查看 Application Insights 資源的 [*應用程式對應*] 區段。

## <a name="troubleshooting"></a>疑難排解

以下是當遙測未如預期般出現在 Azure 入口網站時，所要使用的疑難排解流程。

1. 請確定應用程式正在進行負載，並以一般 HTTP 傳送/接收要求。 因為遙測已從網路中解除，所以不支援加密的流量。 如果沒有傳入或傳出要求，則不會有任何遙測。
2. 請確定在*ISTIO-混合器-yaml*中的*ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY*環境變數中提供正確的檢測金鑰。 您可在 Azure 入口網站中 Application Insights 資源的 [*總覽*] 索引標籤上找到檢測金鑰。
3. 請確定已在*application insights-ISTIO-yaml*中的*ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES*環境變數中提供正確的 Kubernetes 命名空間。 將其保留為空白以監視所有命名空間。
4. 請確定您的應用程式 pod 已由 Istio 側車插入。 確認 Istio 的側車存在於每個 pod 上。

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   確認在 pod 上有一個名為*istio 的*容器正在執行。

5. 查看 Application Insights 介面卡的追蹤。

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   已接收的遙測專案計數會每分鐘更新一次。 如果每分鐘不會增加分鐘數，則 Istio 不會將遙測傳送至介面卡。
   尋找記錄檔中的任何錯誤。
6. 如果已建立 Kubernetes 介面卡的*應用程式深入*解析未送出遙測，請檢查 Istio 的混音器記錄，以找出為什麼它不會將資料傳送到介面卡：

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   尋找是否有任何錯誤，特別是與*applicationinsightsadapter*介面卡通訊有關。

## <a name="faq"></a>常見問題集

如需此專案進度的最新資訊，請造訪[Application Insights adapter For Istio 混音器專案的 GitHub](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq)。

## <a name="uninstall"></a>解除安裝

若要卸載產品，請針對*src/kubernetes/* run 底下找到的*每個*YAML 檔案：

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>後續步驟

若要深入瞭解 Azure 監視器和容器如何搭配使用，請造訪[容器的 Azure 監視器總覽](../../azure-monitor/insights/container-insights-overview.md)
