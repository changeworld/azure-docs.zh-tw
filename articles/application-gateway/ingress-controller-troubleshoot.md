---
title: 應用程式閘道輸入控制器疑難排解
description: 本文提供有關如何針對應用程式閘道輸入控制器的常見問題和/或問題進行疑難排解的檔。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 06/18/2020
ms.author: caya
ms.openlocfilehash: d6bcb9125cdfc07eb249353cb85b40a22d3e468c
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397360"
---
# <a name="troubleshoot-common-questions-or-issues-with-ingress-controller"></a>針對輸入控制器的常見問題或問題進行疑難排解

[Azure Cloud Shell](https://shell.azure.com/) 是對 AKS 和 AGIC 安裝問題進行疑難排解的最便利方式。 從 [shell.azure.com](https://shell.azure.com/) 啟動您的 shell，或按一下連結：

[![內嵌啟動](https://shell.azure.com/images/launchcloudshell.png "啟動 Azure Cloud Shell")](https://shell.azure.com)


## <a name="test-with-a-simple-kubernetes-app"></a>使用簡單的 Kubernetes 應用程式進行測試

下列步驟假設：
  - 您有已啟用 Advanced 網路的 AKS 叢集
  - AGIC 已安裝在 AKS 叢集上
  - 您在與 AKS 叢集共用的 VNET 上已有應用程式閘道

若要確認應用程式閘道 + AKS + AGIC 安裝是否已正確設定，請部署最簡單的可能應用程式：

```bash
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: test-agic-app-pod
  labels:
    app: test-agic-app
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP
---
apiVersion: v1
kind: Service
metadata:
  name: test-agic-app-service
spec:
  selector:
    app: test-agic-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-agic-app-ingress
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: test.agic.contoso.com
      http:
        paths:
          - path: /
            backend:
              serviceName: test-agic-app-service
              servicePort: 80
EOF
```

從上述腳本一次複製並貼上所有行至 [Azure Cloud Shell](https://shell.azure.com/)。 請確定已複製整個命令-從開始 `cat` ，並包含最後一個 `EOF` 。

![apply](./media/application-gateway-ingress-controller-troubleshooting/tsg--apply-config.png)

在您的 AKS 叢集以上的應用程式部署成功後，將會有新的 Pod、服務和輸入。

取得具有 [Cloud Shell](https://shell.azure.com/)的 pod 清單： `kubectl get pods -o wide` 。
我們預期已建立名為 ' agic-pod ' 的 pod。 它會有一個 IP 位址。 此位址必須在與 AKS 搭配使用的應用程式閘道的 VNET 內。

![Azure Cloud Shell 中 Bash 視窗的螢幕擷取畫面，其中顯示清單中包含測試 agic-pod 的 pod 清單。](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-pods.png)

取得服務清單： `kubectl get services -o wide` 。 我們預期會看到名為 ' agic-app-service ' 的服務。

![Azure Cloud Shell 中 Bash 視窗的螢幕擷取畫面，其中顯示清單中包含測試 agic-應用程式-pod 的服務清單。](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-services.png)

取得 ingresses 的清單： `kubectl get ingress` 。 我們預期已建立名為 ' agic-應用程式-輸入 ' 的輸入資源。 資源將會有主機名稱 ' test.agic.contoso.com '。

![Azure Cloud Shell 中 Bash 視窗的螢幕擷取畫面，其中顯示清單中包含測試 agic 應用程式輸入的 ingresses 清單。](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-ingress.png)

其中一個 pod 將會 AGIC。 `kubectl get pods` 會顯示 pod 清單，其中一個會以 ' 輸入-azure ' 開頭。 取得該 pod 的所有記錄檔， `kubectl logs <name-of-ingress-controller-pod>` 以確認已成功部署。 成功的部署會將下列幾行新增至記錄：
```
I0927 22:34:51.281437       1 process.go:156] Applied Application Gateway config in 20.461335266s
I0927 22:34:51.281585       1 process.go:165] cache: Updated with latest applied config.
I0927 22:34:51.282342       1 process.go:171] END AppGateway deployment
```

或者，您也可以從 [Cloud Shell](https://shell.azure.com/) 取出指出使用成功的應用程式閘道設定的行 `kubectl logs <ingress-azure-....> | grep 'Applied App Gateway config in'` ，其中 `<ingress-azure....>` 應該是 AGIC pod 的確切名稱。

應用程式閘道會套用下列設定：

- 接聽程式 ![ ：接聽程式](./media/application-gateway-ingress-controller-troubleshooting/tsg--listeners.png)

- 路由規則： ![ routing_rule](./media/application-gateway-ingress-controller-troubleshooting/tsg--rule.png)

- 後端集區：
  - 後端位址集區中將會有一個 ip 位址，且將會符合我們先前所觀察到之 Pod 的 ip 位址 `kubectl get pods -o wide` 
 ![ backend_pool](./media/application-gateway-ingress-controller-troubleshooting/tsg--backendpools.png)


最後，我們可以使用 `cURL` [Cloud Shell](https://shell.azure.com/) 內的命令來建立新部署應用程式的 HTTP 連線：

1. 用 `kubectl get ingress` 來取得應用程式閘道的公用 IP 位址
2. 使用`curl -I -H 'test.agic.contoso.com' <publitc-ip-address-from-previous-command>`

![Azure Cloud Shell 中 Bash 視窗的螢幕擷取畫面，其中顯示捲曲命令已成功建立與測試應用程式的 HTTP 連線。](./media/application-gateway-ingress-controller-troubleshooting/tsg--curl.png)

結果 `HTTP/1.1 200 OK` 表示應用程式閘道 + AKS + AGIC 系統正如預期般運作。


## <a name="inspect-kubernetes-installation"></a>檢查 Kubernetes 安裝

### <a name="pods-services-ingress"></a>Pod、服務、輸入
應用程式閘道輸入控制器 (AGIC) 會持續監視下列 Kubernetes 資源： [部署](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment) 或 [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod)、 [服務](https://kubernetes.io/docs/concepts/services-networking/service/) [、輸入](https://kubernetes.io/docs/concepts/services-networking/ingress/)


下列必須備妥，AGIC 才能如預期般運作：
  1. AKS 必須有一或多個 **狀況良好的 pod** 。
     從 [Cloud Shell](https://shell.azure.com/) 確認這一點 `kubectl get pods -o wide --show-labels` ，如果您有一個 Pod `apsnetapp` ，則您的輸出看起來可能像這樣：
     ```bash
     delyan@Azure:~$ kubectl get pods -o wide --show-labels

     NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE                       NOMINATED NODE   READINESS GATES   LABELS
     aspnetapp              1/1     Running   0          17h   10.0.0.6    aks-agentpool-35064155-1   <none>           <none>            app=aspnetapp
     ```

  2. 一或多個 **服務** ，藉由比對標籤來參考上面的 pod `selector` 。
     從 [Cloud Shell](https://shell.azure.com/) 驗證此 `kubectl get services -o wide`
     ```bash
     delyan@Azure:~$ kubectl get services -o wide --show-labels

     NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE   SELECTOR        LABELS
     aspnetapp           ClusterIP   10.2.63.254    <none>        80/TCP    17h   app=aspnetapp   <none>     
     ```

  3. 參考上述服務的 **輸入（加** `kubernetes.io/ingress.class: azure/application-gateway` 上批註），請從 [Cloud Shell](https://shell.azure.com/)`kubectl get ingress -o wide --show-labels`
     ```bash
     delyan@Azure:~$ kubectl get ingress -o wide --show-labels

     NAME        HOSTS   ADDRESS   PORTS   AGE   LABELS
     aspnetapp   *                 80      17h   <none>
     ```

  4. 查看上述輸入的注釋： `kubectl get ingress aspnetapp -o yaml` (`aspnetapp` 以您輸入的名稱取代) 
     ```bash
     delyan@Azure:~$ kubectl get ingress aspnetapp -o yaml

     apiVersion: extensions/v1beta1
     kind: Ingress
     metadata:
       annotations:
         kubernetes.io/ingress.class: azure/application-gateway
       name: aspnetapp
     spec:
       backend:
         serviceName: aspnetapp
         servicePort: 80
     ```

     輸入資源必須以標注 `kubernetes.io/ingress.class: azure/application-gateway` 。
 

### <a name="verify-observed-namespace"></a>確認觀察到的命名空間

* 取得 Kubernetes 叢集中的現有命名空間。 您的應用程式執行所在的命名空間為何？ AGIC 監看該命名空間嗎？ 請參閱 [多個命名空間支援](./ingress-controller-multiple-namespace-support.md#enable-multiple-namespace-support) 檔，以瞭解如何正確設定觀察到的命名空間。

    ```bash
    # What namespaces exist on your cluster
    kubectl get namespaces
    
    # What pods are currently running
    kubectl get pods --all-namespaces -o wide
    ```


* AGIC pod 應位於 `default` 命名空間 (請參閱資料行 `NAMESPACE`) 。 狀況良好的 pod 會 `Running` 在資料 `STATUS` 行中。 至少應該有一個 AGIC pod。

    ```bash
    # Get a list of the Application Gateway Ingress Controller pods
    kubectl get pods --all-namespaces --selector app=ingress-azure
    ```


* 如果 AGIC pod 狀況不良 (不 `STATUS` 會) 上述命令中的資料行 `Running` ：
  - 取得記錄以瞭解原因： `kubectl logs <pod-name>`
  - 針對先前的 pod 實例： `kubectl logs <pod-name> --previous`
  - 描述可取得更多內容的 pod： `kubectl describe pod <pod-name>`


* 您是否有 Kubernetes[服務](https://kubernetes.io/docs/concepts/services-networking/service/)[和輸入](https://kubernetes.io/docs/concepts/services-networking/ingress/)資源？
    
    ```bash
    # Get all services across all namespaces
    kubectl get service --all-namespaces -o wide
    
    # Get all ingress resources across all namespaces
    kubectl get ingress --all-namespaces -o wide
    ```


* [您的](https://kubernetes.io/docs/concepts/services-networking/ingress/)輸入標注為： `kubernetes.io/ingress.class: azure/application-gateway` ？ AGIC 只會監看具有此注釋的 Kubernetes 輸入資源。
    
    ```bash
    # Get the YAML definition of a particular ingress resource
    kubectl get ingress --namespace  <which-namespace?>  <which-ingress?>  -o yaml
    ```


* AGIC 會發出特定重大錯誤的 Kubernetes 事件。 您可以查看這些：
  - 在您的終端機中 via `kubectl get events --sort-by=.metadata.creationTimestamp`
  - 在瀏覽器中使用 [Kubernetes WEB UI (儀表板) ](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)


## <a name="logging-levels"></a>記錄層級

AGIC 有3個記錄層級。 層級1是預設值，而且會顯示最少的記錄行數。
另一方面，層級5會顯示所有記錄，包括套用至 ARM 的已清理設定內容。

Kubernetes 社區已為 [kubectl](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging) 工具建立9個記錄層級。 在此存放庫中，我們會利用類似的語法來使用其中的3個：


| 詳細程度 | 描述 |
|-----------|-------------|
|  1        | 預設記錄層級;顯示啟動詳細資料、警告和錯誤 |
|  3        | 有關事件和變更的擴充資訊;已建立物件的清單 |
|  5        | 記錄已封送處理的物件;顯示套用至 ARM 的已清理 JSON 設定 |


詳細資訊層級可透過 `verbosityLevel` [helm yaml](#sample-helm-config-file) 檔案中的變數來調整。 將詳細資訊層級增加至， `5` 以將 JSON 設定分派至 [ARM](../azure-resource-manager/management/overview.md)：
  - `verbosityLevel: 5`在[helm-yaml](#sample-helm-config-file)中自行加入一行，然後重新安裝
  - 取得記錄檔 `kubectl logs <pod-name>`

### <a name="sample-helm-config-file"></a>範例 Helm 設定檔
```yaml
# This file contains the essential configs for the ingress controller helm chart

# Verbosity level of the App Gateway Ingress Controller
verbosityLevel: 3

################################################################################
# Specify which application gateway the ingress controller will manage
#
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>

    # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
    # This prohibits AGIC from applying config for any host/path.
    # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
    shared: false

################################################################################
# Specify which kubernetes namespace the ingress controller will watch
# Default value is "default"
# Leaving this variable out or setting it to blank or empty string would
# result in Ingress Controller observing all acessible namespaces.
#
# kubernetes:
#   watchNamespace: <namespace>

################################################################################
# Specify the authentication with Azure Resource Manager
#
# Two authentication methods are available:
# - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
armAuth:
    type: aadPodIdentity
    identityResourceID: <identityResourceId>
    identityClientID:  <identityClientId>

## Alternatively you can use Service Principal credentials
# armAuth:
#    type: servicePrincipal
#    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>

################################################################################
# Specify if the cluster is RBAC enabled or not
rbac:
    enabled: false # true/false

# Specify aks cluster related information. THIS IS BEING DEPRECATED.
aksClusterConfiguration:
    apiServerAddress: <aks-api-server-address>
```