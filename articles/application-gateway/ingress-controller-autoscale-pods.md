---
title: 使用 Azure 應用程式閘道計量自動調整 AKS pod
description: 本文提供的指示說明如何使用應用程式閘道計量和 Azure Kubernetes 計量介面卡調整 AKS 後端 pod
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 5e0533a44db269229b2f26fa8d2f2b4f84f4d0b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85125458"
---
# <a name="autoscale-your-aks-pods-using-application-gateway-metrics-beta"></a>使用應用程式閘道計量自動調整 AKS pod (Beta) 

當連入流量增加時，根據需求擴充您的應用程式就變得非常重要。

在下列教學課程中，我們會說明如何使用應用程式閘道的 `AvgRequestCountPerHealthyHost` 度量來擴大您的應用程式。 `AvgRequestCountPerHealthyHost` 測量傳送至特定後端集區和後端 HTTP 設定組合的平均要求。

我們將使用下列兩個元件：

* [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) -我們將使用計量介面卡，透過計量伺服器公開應用程式閘道計量。 Azure Kubernetes 計量介面卡是 Azure 下的開放原始碼專案，類似于應用程式閘道輸入控制器。 
* [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) -我們將使用 HPA 來使用應用程式閘道計量，並以部署為目標來調整規模。

## <a name="setting-up-azure-kubernetes-metric-adapter"></a>設定 Azure Kubernetes 計量介面卡

1. 我們會先建立 Azure AAD 服務主體，並將它指派給 `Monitoring Reader` 應用程式閘道的資源群組存取權。 

    ```azurecli
        applicationGatewayGroupName="<application-gateway-group-id>"
        applicationGatewayGroupId=$(az group show -g $applicationGatewayGroupName -o tsv --query "id")
        az ad sp create-for-rbac -n "azure-k8s-metric-adapter-sp" --role "Monitoring Reader" --scopes applicationGatewayGroupId
    ```

1. 現在，我們將 [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) 使用上面建立的 AAD 服務主體來部署。

    ```bash
    kubectl create namespace custom-metrics
    # use values from service principal created above to create secret
    kubectl create secret generic azure-k8s-metrics-adapter -n custom-metrics \
        --from-literal=azure-tenant-id=<tenantid> \
        --from-literal=azure-client-id=<clientid> \
        --from-literal=azure-client-secret=<secret>
    kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/Azure/azure-k8s-metrics-adapter/master/deploy/adapter.yaml -n custom-metrics
    ```

1. 我們會建立 `ExternalMetric` 名稱為的資源 `appgw-request-count-metric` 。 此資源會指示計量介面卡公開 `AvgRequestCountPerHealthyHost` `myApplicationGateway` 資源群組中資源的度量 `myResourceGroup` 。 您可以使用此 `filter` 欄位將目標設為應用程式閘道中的特定後端集區和後端 HTTP 設定。

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

您現在可以對計量伺服器提出要求，以查看我們的新計量是否已公開：
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

## <a name="using-the-new-metric-to-scale-up-the-deployment"></a>使用新度量來擴大部署

一旦我們能夠透過計量伺服器公開之後 `appgw-request-count-metric` ，我們就可以開始將 [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) 目標部署擴大。

在下列範例中，我們將以範例部署為目標 `aspnet` 。 我們將在 `appgw-request-count-metric` 每個 Pod > 200 時擴大 Pod，最多可達 Pod `10` 。

取代您的目標部署名稱，並套用下列自動調整規模設定：
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

使用像是 apache 工作臺的負載測試工具來測試您的設定：
```bash
ab -n10000 http://<applicaiton-gateway-ip-address>/
```

## <a name="next-steps"></a>後續步驟
- 針對輸入[**控制器問題進行疑難排解**](ingress-controller-troubleshoot.md)：針對輸入控制器的任何問題進行疑難排解。