---
title: 語言偵測庫伯內斯配置和部署步驟
titleSuffix: Azure Cognitive Services
description: 語言偵測庫伯內斯配置和部署步驟
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: dc344bd6e4927d39b72ba9af3ae2eeb61d9cec95
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877799"
---
### <a name="deploy-the-language-detection-container-to-an-aks-cluster"></a>將語言偵測容器部署到 AKS 叢集

1. 打開 Azure CLI,然後登錄到 Azure。

    ```azurecli
    az login
    ```

1. 登錄到 AKS 群集。 使用`your-cluster-name`適當的`your-resource-group`值替換和替換。

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    執行此指令後,它會報告類似於以下內容的消息:

    ```output
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > 如果 Azure 帳號上有多個訂閱可用`az aks get-credentials`, 並且該命令傳回時出現錯誤,則常見問題是使用了錯誤的訂閱。 將 Azure CLI 工作階段的上下文設置為使用與創建資源相同的訂閱,然後重試。
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. 開啟您選擇的文字編輯器。 此示例使用可視化工作室代碼。

    ```console
    code .
    ```

1. 在文字編輯器中,創建名為 *「語言.yaml」* 的新檔案,並將下面的 YAML 貼上到其中。 請務必替換`billing/value`並`apikey/value`使用您自己的資訊。

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: language
    spec:
      template:
        metadata:
          labels:
            app: language-app
        spec:
          containers:
          - name: language
            image: mcr.microsoft.com/azure-cognitive-services/language
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
      name: language
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: language-app
    ```

1. 儲存檔案,然後關閉文字編輯器。
1. 以*語言.yaml*`apply`檔案為目標運行 Kubernetes 命令:

    ```console
    kubectl apply -f language.yaml
    ```

    命令成功應用部署設定後,將顯示一條訊息,類似於以下輸出:

    ```output
    deployment.apps "language" created
    service "language" created
    ```
1. 驗證是否部署了該窗格:

    ```console
    kubectl get pods
    ```

    窗格執行狀態的輸出:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    language-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. 驗證服務是否可用,並獲取 IP 位址。

    ```console
    kubectl get services
    ```

    窗格中*語言*服務執行狀態的輸出:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    language     LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
