---
title: 情緒分析庫伯內斯配置和部署步驟
titleSuffix: Azure Cognitive Services
description: 情緒分析庫伯內斯配置和部署步驟
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 2a99f85cf861c0c36ffac136cdf1f792b40719b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262341"
---
### <a name="deploy-the-sentiment-analysis-container-to-an-aks-cluster"></a>將情緒分析容器部署到 AKS 群集

1. 打開 Azure CLI，然後登錄到 Azure。

    ```azurecli
    az login
    ```

1. 登錄到 AKS 群集。 使用`your-cluster-name`適當的`your-resource-group`值替換和替換。

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    運行此命令後，它會報告類似于以下內容的消息：

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > 如果 Azure 帳戶上有多個訂閱可用，`az aks get-credentials`並且該命令返回時出現錯誤，則常見問題是使用了錯誤的訂閱。 將 Azure CLI 會話的上下文設置為使用與創建資源相同的訂閱，然後重試。
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. 打開您選擇的文字編輯器。 此示例使用視覺化工作室代碼。

    ```console
    code .
    ```

1. 在文字編輯器中，創建名為 *"情緒.yaml"* 的新檔，並將下面的 YAML 粘貼到其中。 請務必替換`billing/value`並`apikey/value`使用您自己的資訊。

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: sentiment
    spec:
      template:
        metadata:
          labels:
            app: sentiment-app
        spec:
          containers:
          - name: sentiment
            image: mcr.microsoft.com/azure-cognitive-services/sentiment
            ports:
            - containerPort: 5000
            resources:
              requests:
                memory: 2Gi
                cpu: 1
              limits:
                memory: 4Gi
                cpu: 1
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # {ENDPOINT_URI}
            - name: apikey
              value: # {API_KEY}
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: sentiment
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: sentiment-app
    ```

1. 保存檔，然後關閉文字編輯器。
1. 以*情緒.yaml* `apply`檔為目標運行 Kubernetes 命令：

    ```console
    kubectl apply -f sentiment.yaml
    ```

    命令成功應用部署配置後，將顯示一條消息，類似于以下輸出：

    ```output
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. 驗證是否部署了該窗格：

    ```console
    kubectl get pods
    ```

    窗格運行狀態的輸出：

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. 驗證服務是否可用，並獲取 IP 位址。

    ```console
    kubectl get services
    ```

    窗格中*情緒*服務運行狀態的輸出：

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
