---
title: 使用 Azure 應用程式閘道計量自動調整 AKS pod
description: 本文提供如何使用應用程式閘道計量和 Azure Kubernetes 公制介面卡來調整 AKS 後端 pod 的指示
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: b98ab8d3c4d03115ea689b4dfd3d8dee753f019d
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715073"
---
# <a name="autoscale-your-aks-pods-using-application-gateway-metrics-beta"></a>使用應用程式閘道計量（搶鮮版（Beta））自動調整您的 AKS pod

隨著連入流量的增加，根據需求擴充應用程式會變得很重要。

在下列教學課程中，我們會說明如何使用應用程式閘道的 `AvgRequestCountPerHealthyHost` 度量來相應增加您的應用程式。 `AvgRequestCountPerHealthyHost` 會測量傳送至特定後端集區的平均要求和後端 HTTP 設定組合。

我們將使用下列兩個元件：

* [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) -我們將使用計量介面卡，透過計量伺服器公開應用程式閘道計量。 Azure Kubernetes 公制介面卡是 Azure 底下的開放原始碼專案，類似于應用程式閘道輸入控制器。 
* [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) -我們將使用 HPA 來使用應用程式閘道計量，並以部署為目標來進行調整。

## <a name="setting-up-azure-kubernetes-metric-adapter"></a>設定 Azure Kubernetes 公制介面卡

1. 我們會先建立 Azure AAD 服務主體，並將其指派 `Monitoring Reader` 透過應用程式閘道資源群組的存取權。 

    ```bash
        applicationGatewayGroupName="<application-gateway-group-id>"
        applicationGatewayGroupId=$(az group show -g $applicationGatewayGroupName -o tsv --query "id")
        az ad sp create-for-rbac -n "azure-k8s-metric-adapter-sp" --role "Monitoring Reader" --scopes applicationGatewayGroupId
    ```

1. 現在，我們將使用上面建立的 AAD 服務主體來部署[`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) 。

    ```bash
    kubectl create namespace custom-metrics
    # use values from service principle created above to create secret
    kubectl create secret generic azure-k8s-metrics-adapter -n custom-metrics \
        --from-literal=azure-tenant-id=<tenantid> \
        --from-literal=azure-client-id=<clientid> \
        --from-literal=azure-client-secret=<secret>
    kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/Azure/azure-k8s-metrics-adapter/master/deploy/adapter.yaml -n custom-metrics
    ```

1. 我們將建立名為 `appgw-request-count-metric`的 `ExternalMetric` 資源。 此資源會指示計量介面卡公開 `myResourceGroup` 資源群組中 `myApplicationGateway` 資源的 `AvgRequestCountPerHealthyHost` 計量。 您可以使用 [`filter`] 欄位，以應用程式閘道中的特定後端集區和後端 HTTP 設定為目標。

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

您現在可以向計量伺服器提出要求，以查看我們的新度量是否已公開：
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

## <a name="using-the-new-metric-to-scale-up-the-deployment"></a>使用新度量來相應增加部署

一旦可以透過計量伺服器公開 `appgw-request-count-metric` 之後，我們就可以開始使用[`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler)來相應增加目標部署。

在下列範例中，我們會將目標設為範例部署 `aspnet`。 當每個 Pod `appgw-request-count-metric` > 200 時，我們會相應增加 Pod，最多可達 `10` Pod 的最大值。

取代您的目標部署名稱，並套用下列自動調整設定：
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

使用 apache 工作臺之類的負載測試工具來測試您的設定：
```bash
ab -n10000 http://<applicaiton-gateway-ip-address>/
```

## <a name="next-steps"></a>後續步驟
- 針對輸入[**控制器問題進行疑難排解**](ingress-controller-troubleshoot.md)：針對輸入控制器的任何問題進行疑難排解。