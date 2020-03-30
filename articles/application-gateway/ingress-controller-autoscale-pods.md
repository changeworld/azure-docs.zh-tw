---
title: 使用 Azure 應用程式閘道指標自動縮放 AKS pod
description: 本文提供有關如何使用應用程式閘道指標和 Azure Kubernetes 指標配接器擴展 AKS 後端窗格的說明
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 1169ed0e9a2b970ee0e30d73ea20c87001b62786
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239439"
---
# <a name="autoscale-your-aks-pods-using-application-gateway-metrics-beta"></a>使用應用程式閘道指標（測試版）自動縮放 AKS pod

隨著傳入流量的增加，根據需求擴展應用程式變得至關重要。

在下面的教程中，我們將介紹如何使用應用程式閘道的`AvgRequestCountPerHealthyHost`指標來擴展應用程式。 `AvgRequestCountPerHealthyHost`測量發送到特定後端池和後端 HTTP 設置組合的平均請求。

我們將使用以下兩個元件：

* [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter)- 我們將使用指標配接器通過指標伺服器公開應用程式閘道指標。 Azure 庫伯奈斯公制配接器是 Azure 下的開源專案，類似于應用程式閘道入口控制器。 
* [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler)- 我們將使用 HPA 使用應用程式閘道指標，並針對部署進行擴展。

## <a name="setting-up-azure-kubernetes-metric-adapter"></a>設置 Azure 庫伯內斯公制配接器

1. 我們將首先創建 Azure AAD 服務主體，並`Monitoring Reader`通過應用程式閘道的資源組分配其存取權限。 

    ```azurecli
        applicationGatewayGroupName="<application-gateway-group-id>"
        applicationGatewayGroupId=$(az group show -g $applicationGatewayGroupName -o tsv --query "id")
        az ad sp create-for-rbac -n "azure-k8s-metric-adapter-sp" --role "Monitoring Reader" --scopes applicationGatewayGroupId
    ```

1. 現在，我們將部署[`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter)使用上面創建的 AAD 服務主體。

    ```bash
    kubectl create namespace custom-metrics
    # use values from service principle created above to create secret
    kubectl create secret generic azure-k8s-metrics-adapter -n custom-metrics \
        --from-literal=azure-tenant-id=<tenantid> \
        --from-literal=azure-client-id=<clientid> \
        --from-literal=azure-client-secret=<secret>
    kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/Azure/azure-k8s-metrics-adapter/master/deploy/adapter.yaml -n custom-metrics
    ```

1. 我們將創建一個`ExternalMetric`名稱`appgw-request-count-metric`的資源。 此資源將指示指標配接器公開`AvgRequestCountPerHealthyHost``myApplicationGateway``myResourceGroup`資源組中的資源指標。 您可以使用該`filter`欄位在應用程式閘道中定位特定的後端池和後端 HTTP 設置。

    ```yaml
    apiVersion: azure.com/v1alpha2
    kind: ExternalMetric
    metadata:
    name: appgw-request-count-metric
    spec:
        type: azuremonitor
        azure:
            resourceGroup: myResourceGroup # replace with your application gateway's resource group name
            resourceName: myApplicationGateway # replace with your application gateway's name
            resourceProviderNamespace: Microsoft.Network
            resourceType: applicationGateways
        metric:
            metricName: AvgRequestCountPerHealthyHost
            aggregation: Average
            filter: BackendSettingsPool eq '<backend-pool-name>~<backend-http-setting-name>' # optional
    ```

您現在可以向指標伺服器發出請求，以查看我們的新指標是否公開：
```bash
kubectl get --raw "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric"
# Sample Output
# {
#   "kind": "ExternalMetricValueList",
#   "apiVersion": "external.metrics.k8s.io/v1beta1",
#   "metadata":
#     {
#       "selfLink": "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric",
#     },
#   "items":
#     [
#       {
#         "metricName": "appgw-request-count-metric",
#         "metricLabels": null,
#         "timestamp": "2019-11-05T00:18:51Z",
#         "value": "30",
#       },
#     ],
# }
```

## <a name="using-the-new-metric-to-scale-up-the-deployment"></a>使用新指標擴展部署

一旦我們能夠通過指標伺服器`appgw-request-count-metric`公開，我們就可以使用[`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler)來擴展目標部署。

在下面的示例中，我們將針對示例部署`aspnet`。 我們將擴大 Pods，當`appgw-request-count-metric`每個 Pod > 200 個`10`時，最多為 Pod。

替換目標部署名稱並應用以下自動縮放配置：
```yaml
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: deployment-scaler
spec:
  scaleTargetRef:
    apiVersion: extensions/v1beta1
    kind: Deployment
    name: aspnet # replace with your deployment's name
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: External
    external:
      metricName: appgw-request-count-metric
      targetAverageValue: 200
```

使用像 apache 工作臺這樣的負載測試工具測試您的設置：
```bash
ab -n10000 http://<applicaiton-gateway-ip-address>/
```

## <a name="next-steps"></a>後續步驟
- [**排除入口控制器問題**](ingress-controller-troubleshoot.md)：解決入口控制器的任何問題。