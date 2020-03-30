---
title: 使用應用程式見解監視 Azure 庫伯奈斯服務 （AKS） 或其他庫伯奈斯托管應用程式 - Azure 監視器 |微軟文檔
description: Azure 監視器在 Kubernetes 群集上使用服務網格技術 Istio 為任何 Kubernetes 託管應用程式提供應用程式監視。 這允許您收集與群集中運行的 Pod 的傳入和傳出請求相關的應用程式見解遙測資料。
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: 56a0cb66f5b54c817067970ab369d7ca471a1696
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132352"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications"></a>庫伯內斯托管應用程式為零檢測應用程式監控

> [!IMPORTANT]
> 此功能當前處於公共預覽版中。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure 監視器現在利用 Kubernetes 群集上的服務網格技術，為任何 Kubernetes 託管應用提供開箱即用的應用程式監視。 使用預設應用程式洞察功能（如[應用程式映射](../../azure-monitor/app/app-map.md)）來建模您的依賴項、即時監視[的即時指標流](../../azure-monitor/app/live-stream.md)、具有[預設儀表板](../../azure-monitor/app/overview-dashboard.md)的強大視覺化效果、[指標資源管理器](../../azure-monitor/platform/metrics-getting-started.md)和[活頁簿](../../azure-monitor/app/usage-workbooks.md)。 此功能將説明使用者在選定的 Kubernetes 命名空間內發現其所有 Kubernetes 工作負載的性能瓶頸和故障熱點。 通過將現有服務網格投資與 Istio 等技術結合使用，Azure 監視器可在不修改應用程式代碼的情況下實現自動檢測的應用監視。

> [!NOTE]
> 這是對 Kubernetes 執行應用程式監視的眾多方法之一。您還可以使用[應用程式見解 SDK](../../azure-monitor/azure-monitor-app-hub.yml)檢測 Kubernetes 中託管的任何應用，而無需使用服務網格。 要監視 Kubernetes 而不使用 SDK 檢測應用程式，可以使用以下方法。

## <a name="prerequisites"></a>Prerequisites

- [庫伯內斯集群](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads)。
- 主控台訪問群集以運行*kubectl*。
- [應用程式洞察資源](create-new-resource.md)
- 具有服務網格。 如果群集未部署 Istio，則可以瞭解如何[在 Azure Kubernetes 服務 中安裝和使用 Istio。](https://docs.microsoft.com/azure/aks/istio-install)

## <a name="capabilities"></a>功能

通過使用 Kubernetes 託管應用程式的零檢測應用程式監視，您將能夠使用：

- [應用程式映射](../../azure-monitor/app/app-map.md)
- [即時流指標](../../azure-monitor/app/live-stream.md)
- [儀錶 板](../../azure-monitor/app/overview-dashboard.md)
- [指標資源管理器](../../azure-monitor/platform/metrics-getting-started.md)
- [分散式跟蹤](../../azure-monitor/app/distributed-tracing.md)
- [端到端事務監控](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>安裝步驟

為了啟用該解決方案，我們將執行以下步驟：
- 部署應用程式（如果尚未部署）。
- 確保應用程式是服務網格的一部分。
- 觀察收集的遙測資料。

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>將應用配置為使用服務網格

Istio 支援兩種[檢測吊艙](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/)的方法。
在大多數情況下，使用*istio 注入*標籤標記包含應用程式的 Kubernetes 命名空間是最容易的：

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> 由於服務網格將資料從線路上提升，因此無法攔截加密的流量。 對於不離開群集的流量，請使用未加密的協定（例如 HTTP）。 對於必須加密的外部流量，請考慮在入口控制器[設置 TLS 端接](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls)。

在服務網格之外運行的應用程式不受影響。

### <a name="deploy-your-application"></a>部署應用程式

- 將應用程式部署到*我的應用命名空間*命名空間。 如果應用程式已部署，並且您遵循了上述自動側車噴射方法，則需要重新創建吊艙以確保 Istio 注入其側車;啟動滾動更新或刪除單個窗格，並等待重新創建它們。
- 確保您的應用程式符合[Istio 要求](https://istio.io/docs/setup/kubernetes/prepare/requirements/)。

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>為庫伯內斯托管應用程式部署零檢測應用程式監視

1. 下載並提取[*應用程式見解配接器*版本](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/)。
2. 導航到 */src/kubernetes/* 發佈資料夾中。
3. 編輯*應用程式-見解-istio-混合器-配接器-部署。*
    - 編輯*ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY*環境變數的值以包含 Azure 門戶中應用程式見解資源的檢測鍵以包含遙測資料。
    - 如有必要，編輯*ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES*環境變數的值，以包含要為其啟用監視的命名空間的逗號分隔清單。 將其留空以監視所有命名空間。
4. 應用*src/kubernetes 下**找到的每個*YAML 檔/通過運行以下內容（您仍必須仍在 */src/kubernetes/*）

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>驗證部署

- 確保已部署應用程式見解配接器：

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> 在某些情況下，需要微調調優。 要包含或排除收集單個 pod 的遙測資料，請在該窗格上使用*appinsights/監視。* 這將優先于所有基於命名空間的配置。 將*應用見解/監視設置為* *true*以包括窗格，並將設置為*false*以排除它。

### <a name="view-application-insights-telemetry"></a>查看應用程式見解遙測

- 針對應用程式生成示例請求，以確認監視工作正常。
- 在 3-5 分鐘內，您應該開始看到 Azure 門戶中顯示的遙測資料。 請務必在門戶中查看應用程式見解資源*的應用程式映射*部分。

## <a name="troubleshooting"></a>疑難排解

下面是遙測未按預期在 Azure 門戶中顯示時使用的故障排除流。

1. 確保應用程式負載不足，並在普通 HTTP 中發送/接收請求。 由於遙測從線路上解除，因此不支援加密流量。 如果沒有傳入或傳出請求，則也不會有遙測資料。
2. 確保*在應用程式-見解-istio-混合器-配接器部署**ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY環境變數*中提供了正確的檢測金鑰。 檢測鍵位於 Azure 門戶中應用程式見解資源的 *"概述"* 選項卡上。
3. 確保在*應用程式-見解-istio-混合器-配接器部署**ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES環境變數*中提供了正確的 Kubernetes 命名空間。 將其留空以監視所有命名空間。
4. 確保應用程式的吊艙已由 Istio 注入側車。 驗證 Istio 的側車是否存在於每個吊艙上。

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   驗證在窗格上運行名為*istio 代理*的容器。

5. 查看應用程式見解配接器的跟蹤。

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   每分鐘更新一次收到的遙測項的計數。 如果每分鐘不增長一分鐘 - Istio 不會向配接器發送遙測資料。
   查找日誌中的任何錯誤。
6. 如果已確定*Kubernetes 配接器的應用程式見解*未饋送遙測資料，請檢查 Istio 的混頻器日誌，找出為什麼它不向配接器發送資料：

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   查找任何錯誤，特別是與*應用程式見解配接器的*通信。

## <a name="faq"></a>常見問題集

有關此專案進度的最新資訊，請訪問[Istio Mixer 專案的 GitHub 的應用程式見解配接器](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq)。

## <a name="uninstall"></a>解除安裝

要卸載產品，對於在*src/kubernets 下*找到*的每個*YAML 檔/運行：

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>後續步驟

要瞭解有關 Azure 監視器和容器如何協同工作的詳細資訊，請訪問[Azure 監視器以瞭解容器概述](../../azure-monitor/insights/container-insights-overview.md)
