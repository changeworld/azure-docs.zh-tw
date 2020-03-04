---
title: 關鍵片語擷取 Kubernetes config 和 deploy 步驟
titleSuffix: Azure Cognitive Services
description: 關鍵片語擷取 Kubernetes config 和 deploy 步驟
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 1a96b5e4d03ce72bac29126028ca61e11e8c7324
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78261951"
---
### <a name="deploy-the-key-phrase-extraction-container-to-an-aks-cluster"></a>將關鍵片語擷取容器部署至 AKS 叢集

1. 開啟 Azure CLI，然後登入 Azure。

    ```azurecli
    az login
    ```

1. 登入 AKS 叢集。 以適當的值取代 `your-cluster-name` 和 `your-resource-group`。

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    執行此命令之後，它會報告類似下列的訊息：

    ```output
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > 如果您的 Azure 帳戶有多個可用的訂用帳戶，且 `az aks get-credentials` 命令傳回錯誤，則常見的問題是您使用錯誤的訂閱。 將 Azure CLI 會話的內容設定為使用您用來建立資源的相同訂用帳戶，然後再試一次。
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. 開啟選擇的文字編輯器。 這個範例會使用 Visual Studio Code。

    ```console
    code .
    ```

1. 在文字編輯器中，建立名為*keyphrase*的新檔案，並在其中貼上下列 yaml。 請務必以您自己的資訊取代 `billing/value` 和 `apikey/value`。

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: keyphrase
    spec:
      template:
        metadata:
          labels:
            app: keyphrase-app
        spec:
          containers:
          - name: keyphrase
            image: mcr.microsoft.com/azure-cognitive-services/keyphrase
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
      name: keyphrase
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: keyphrase-app
    ```

1. 儲存檔案，然後關閉文字編輯器。
1. 執行 Kubernetes `apply` 命令，並將*keyphrase. yaml*檔案作為其目標：

    ```console
    kubectl apply -f keyphrase.yaml
    ```

    命令成功套用部署設定後，會出現類似下列輸出的訊息：

    ```output
    deployment.apps "keyphrase" created
    service "keyphrase" created
    ```
1. 確認已部署 pod：

    ```console
    kubectl get pods
    ```

    Pod 執行狀態的輸出：

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    keyphrase-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. 確認服務可供使用，並取得 IP 位址。

    ```console
    kubectl get services
    ```

    Pod 中*keyphrase*服務執行狀態的輸出：

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    keyphrase    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
