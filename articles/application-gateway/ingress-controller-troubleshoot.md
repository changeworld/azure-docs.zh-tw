---
title: 應用程式閘道入口控制器故障排除
description: 本文提供有關如何解決應用程式閘道入口控制器的常見問題和/或問題的文檔。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a64a9ce5e080308674893273e90a0e83686e339e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795501"
---
# <a name="troubleshoot-common-questions-or-issues-with-ingress-controller"></a>與入口控制器解決常見問題或問題

[Azure 雲外殼](https://shell.azure.com/)是解決 AKS 和 AGIC 安裝中任何問題的最便捷方法。 從[shell.azure.com](https://shell.azure.com/)啟動外殼，或通過按一下連結：

[![嵌入啟動](https://shell.azure.com/images/launchcloudshell.png "啟動 Azure Cloud Shell")](https://shell.azure.com)


## <a name="test-with-a-simple-kubernetes-app"></a>使用簡單的庫伯奈斯應用程式進行測試

以下步驟假定：
  - 您擁有 AKS 群集，啟用了高級網路
  - AGIC 已安裝在 AKS 群集上
  - 您已在與 AKS 群集共用的 VNET 上擁有應用程式閘道

要驗證應用程式閘道 + AKS + AGIC 安裝設置正確，請部署最簡單的應用：

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

一次從上面的腳本複製並粘貼所有行到[Azure 雲外殼](https://shell.azure.com/)中。 請確保複製整個命令 - 從`cat`中開始，包括最後`EOF`一個 。

![apply](./media/application-gateway-ingress-controller-troubleshooting/tsg--apply-config.png)

成功部署 AKS 群集上方的應用後，將具有新的 Pod、服務和入口。

獲取具有[雲外殼](https://shell.azure.com/)的窗格清單：。 `kubectl get pods -o wide`
我們期望創建名為"測試-應用-pod"的窗格。 它將有一個IP位址。 此位址必須位於與 AKS 一起使用的應用程式閘道的 VNET 中。

![豆莢](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-pods.png)

獲取服務清單： `kubectl get services -o wide`. 我們期望看到一個名為"測試-應用服務"的服務。

![豆莢](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-services.png)

獲取入口清單： `kubectl get ingress`。 我們期望創建名為"測試-應用入口"的入口資源。 資源將具有主機名稱"test.agic.contoso.com"。

![豆莢](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-ingress.png)

其中一個吊艙將是AGIC。 `kubectl get pods`將顯示一個窗格清單，其中一個將從"入口-azure"開頭。 獲取該窗格的所有日誌，`kubectl logs <name-of-ingress-controller-pod>`以驗證我們已成功部署。 成功部署會向日志添加以下行：
```
I0927 22:34:51.281437       1 process.go:156] Applied Application Gateway config in 20.461335266s
I0927 22:34:51.281585       1 process.go:165] cache: Updated with latest applied config.
I0927 22:34:51.282342       1 process.go:171] END AppGateway deployment
```

或者，從[雲外殼](https://shell.azure.com/)，我們只能檢索指示成功使用`kubectl logs <ingress-azure-....> | grep 'Applied App Gateway config in'`應用程式閘道配置的行，`<ingress-azure....>`其中應應是 AGIC pod 的確切名稱。

應用程式閘道將應用以下配置：

- 偵聽![器：攔截器](./media/application-gateway-ingress-controller-troubleshooting/tsg--listeners.png)

- 路由規則：routing_rule ![](./media/application-gateway-ingress-controller-troubleshooting/tsg--rule.png)

- 後端池：
  - 後端位址集區中將有一個 IP 位址，它將與前面`kubectl get pods -o wide`
![觀察到的 Pod 的 IP 位址匹配backend_pool](./media/application-gateway-ingress-controller-troubleshooting/tsg--backendpools.png)


最後，我們可以使用`cURL`[雲殼](https://shell.azure.com/)內的命令來建立與新部署的應用的 HTTP 連接：

1. 用於`kubectl get ingress`獲取應用程式閘道的公共 IP 位址
2. 使用`curl -I -H 'test.agic.contoso.com' <publitc-ip-address-from-previous-command>`

![豆莢](./media/application-gateway-ingress-controller-troubleshooting/tsg--curl.png)

結果表明`HTTP/1.1 200 OK`，應用程式閘道 + AKS + AGIC 系統按預期工作。


## <a name="inspect-kubernetes-installation"></a>檢查庫伯內斯安裝

### <a name="pods-services-ingress"></a>艙，服務，入口
應用程式閘道入口控制器 （AGIC） 持續監視以下庫伯內特斯資源：[部署](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment)或[Pod、](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod)[服務](https://kubernetes.io/docs/concepts/services-networking/service/)、[入口](https://kubernetes.io/docs/concepts/services-networking/ingress/)


AGIC 必須按預期運行：
  1. AKS必須有一個或多個健康**吊艙**。
     `kubectl get pods -o wide --show-labels`使用["雲外殼](https://shell.azure.com/)"驗證這一點，如果您的 Pod 具有 ，`apsnetapp`您的輸出可能如下所示：
     ```bash
     delyan@Azure:~$ kubectl get pods -o wide --show-labels

     NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE                       NOMINATED NODE   READINESS GATES   LABELS
     aspnetapp              1/1     Running   0          17h   10.0.0.6    aks-agentpool-35064155-1   <none>           <none>            app=aspnetapp
     ```

  2. 一個或多個**服務**，通過匹配`selector`的標籤引用上面的窗格。
     使用[雲外殼](https://shell.azure.com/)驗證此情況`kubectl get services -o wide`
     ```bash
     delyan@Azure:~$ kubectl get services -o wide --show-labels

     NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE   SELECTOR        LABELS
     aspnetapp           ClusterIP   10.2.63.254    <none>        80/TCP    17h   app=aspnetapp   <none>     
     ```

  3. **入口**，用`kubernetes.io/ingress.class: azure/application-gateway`中帶，引用上面的服務 從[雲殼](https://shell.azure.com/)中驗證此功能`kubectl get ingress -o wide --show-labels`
     ```bash
     delyan@Azure:~$ kubectl get ingress -o wide --show-labels

     NAME        HOSTS   ADDRESS   PORTS   AGE   LABELS
     aspnetapp   *                 80      17h   <none>
     ```

  4. 查看上述入口的注釋：（`kubectl get ingress aspnetapp -o yaml`替換為`aspnetapp`入口名稱）
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

     入口資源必須使用`kubernetes.io/ingress.class: azure/application-gateway`進行進沒。
 

### <a name="verify-observed-namespace"></a>驗證觀察到的命名空間

* 獲取庫伯內斯群集中的現有命名空間。 你的應用在哪些命名空間中運行？ AGIC 在觀看該命名空間嗎？ 有關如何正確配置觀察到的命名空間，請參閱[多個命名空間支援](./ingress-controller-multiple-namespace-support.md#enable-multiple-namespace-support)文檔。

    ```bash
    # What namespaces exist on your cluster
    kubectl get namespaces
    
    # What pods are currently running
    kubectl get pods --all-namespaces -o wide
    ```


* AGIC 窗格應位於`default`命名空間中（請參閱列）。 `NAMESPACE` 列中`Running``STATUS`會有一個健康的窗格。 應至少有一個 AGIC 吊艙。

    ```bash
    # Get a list of the Application Gateway Ingress Controller pods
    kubectl get pods --all-namespaces --selector app=ingress-azure
    ```


* 如果 AGIC pod 不正常（`STATUS`上述命令中的列不是`Running`）：
  - 獲取日誌，瞭解原因：`kubectl logs <pod-name>`
  - 對於 Pod 的上一個實例：`kubectl logs <pod-name> --previous`
  - 描述 pod 以獲取更多上下文：`kubectl describe pod <pod-name>`


* 您是否擁有庫伯奈斯[服務和](https://kubernetes.io/docs/concepts/services-networking/service/)[入口](https://kubernetes.io/docs/concepts/services-networking/ingress/)資源？
    
    ```bash
    # Get all services across all namespaces
    kubectl get service --all-namespaces -o wide
    
    # Get all ingress resources across all namespaces
    kubectl get ingress --all-namespaces -o wide
    ```


* 您的[入口](https://kubernetes.io/docs/concepts/services-networking/ingress/)是否帶有`kubernetes.io/ingress.class: azure/application-gateway`：？ AGIC 將僅監視具有此注釋的庫伯內斯入口資源。
    
    ```bash
    # Get the YAML definition of a particular ingress resource
    kubectl get ingress --namespace  <which-namespace?>  <which-ingress?>  -o yaml
    ```


* AGIC 針對某些嚴重錯誤發出 Kubernetes 事件。 您可以查看以下功能：
  - 在你的終端通過`kubectl get events --sort-by=.metadata.creationTimestamp`
  - 在瀏覽器中使用[庫伯奈斯 Web UI（儀表板）](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)


## <a name="logging-levels"></a>記錄層級

AGIC 有 3 個日誌記錄級別。 級別 1 是預設級別，它顯示最少的日誌行數。
另一方面，級別 5 將顯示所有日誌，包括應用於 ARM 的配置的經過消毒的內容。

庫伯內斯社區已經為[庫布克特爾](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging)工具建立了9個級別的日誌記錄。 在此存儲庫中，我們利用了其中 3 個，具有類似的語義：


| 詳細程度 | 描述 |
|-----------|-------------|
|  1        | 預設日誌級別;顯示啟動詳細資訊、警告和錯誤 |
|  3        | 有關事件和更改的擴展資訊;已創建物件的清單 |
|  5        | 記錄封送物件;顯示應用於 ARM 的經過消毒的 JSON 配置 |


詳細程度水準可通過`verbosityLevel`[helm-config.yaml](#sample-helm-config-file)檔中的變數進行調整。 增加詳細程度級別，`5`以便將 JSON 配置發送到[ARM：](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)
  - 在`verbosityLevel: 5`[掌舵-config.yaml](#sample-helm-config-file)中自行添加一行，然後重新安裝
  - 獲取日誌與`kubectl logs <pod-name>`

### <a name="sample-helm-config-file"></a>示例頭盔設定檔
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

