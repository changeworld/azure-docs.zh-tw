---
title: 瞭解 Azure Stack Edge 裝置上的 Kubernetes 網路功能 |Microsoft Docs
description: 描述 Kubernetes 網路功能如何在 Azure Stack Edge 裝置上運作。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: alkohli
ms.openlocfilehash: 8394ddbc4247eb992532fb11d06d8f5432edd1c7
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89083406"
---
# <a name="kubernetes-networking-in-your-azure-stack-edge-device"></a>Azure Stack Edge 裝置中的 Kubernetes 網路

在您的 Azure Stack Edge 裝置上，當您設定計算角色時，會建立 Kubernetes 叢集。 建立 Kubernetes 叢集之後，就可以將容器化應用程式部署到 pod 中的 Kubernetes 叢集。 有不同的方式可將網路功能用於 Kubernetes 叢集中的 pod。 

本文通常會說明 Kubernetes 叢集中的網路功能，尤其是在 Azure Stack Edge 裝置的內容中。 

## <a name="networking-requirements"></a>網路需求

以下是部署至 Kubernetes 叢集的典型2層應用程式範例。

- 應用程式的後端中有 web 伺服器前端和資料庫應用程式。 
- 系統會為每個 pod 指派一個 IP，但這些 ip 在 pod 重新開機和容錯移轉時可能會變更。 
- 每個應用程式是由多個 pod 所組成，而且應該在所有 pod 複本之間進行流量的負載平衡。 

![Kubernetes 網路需求](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-1.png)

上述案例會產生下列網路需求：

 - Kubernetes 叢集外部的應用程式使用者必須透過名稱或 IP 位址來存取外部面向應用程式。 
 - 例如，Kubernetes 叢集中的應用程式（例如前端和後端 pod）應該能夠互相溝通。

為了解決上述需求，我們引進了 Kubernetes 服務。 


## <a name="networking-services"></a>網路服務

Kubernetes 服務有兩種類型： 

- 叢集**IP 服務**-將此視為提供應用程式 pod 的常數端點。 無法從 Kubernetes 叢集外部存取任何與這些服務相關聯的 pod。 與這些服務搭配使用的 IP 位址來自私人網路的位址空間。 
- **負載平衡器類似 ip** 服務，但是相關聯的 ip 來自于外部網路，而且可以從 Kubernetes 叢集外部存取。


<!--## Networking example for an app


![Kubernetes networking example](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-2.png)

Each of these applications pods has a label associated with it. For example, the web server application pods have a label `app = WS` and the service has a label selector which the same as `app = WS`. Whenever a service of type load balancer or cluster IP is created, there is a control loop that runs in the master and publishes an endpoint corresponding to this service. This service uses a combination of labels and label selectors to discover the pods associated with this service. As a pod gets created, the new endpoint for the pod is added to the endpoint mapping. Whenever a pod is deleted, it gets deleted from the endpoint mapping. Using this endpoint controller, the service has a most up-to-date view of the pods that make up this application.

For discovery of applications within the cluster, Kubernetes cluster has an inbuilt DNS server pod. This is a cluster DNS that resolves service names to cluster IP. Anytime a cluster IP service is created, a DNS record is added to the DNS server that maps the name of the service to the cluster internal IP. That is how the applications within the cluster can discover each other. For load balancing, there is also the `kube-proxy`. This runs on every node and captures the traffic that comes in through the cluster IP and then distributes the traffic across the pods. 

When an application or the end user would first use the IP address associated with the service of type load balancer to discover the service. Then it would use the label select `app = WS` to discover the pods associated with the application. The `kube-proxy` component would then distribute the traffic and ensure that it hits one of the web server application pods. If the web server app wanted to talk to the database app, then it would simply use the name of the service and using the name and the DNS server pod, resolve the name to an IP address. Again using labels and label selector, it would discover the pods associated with the database application. The `kube-proxy` would then distribute the traffic across each of the database app nodes.-->


## <a name="kubernetes-networking-on-azure-stack-edge"></a>Azure Stack Edge 上的 Kubernetes 網路

Calico、Metallb 和 Core DNS 都是針對 Azure Stack Edge 上的網路功能安裝的元件。 

- **Calico** 會從私人 ip 範圍將 ip 位址指派給每個 pod，並為這些 pod 設定網路功能，讓某個節點上的 pod 可以與另一個節點上的 pod 進行通訊。 
- **Metallb** 會在叢集中的 pod 上執行，並將 IP 位址指派給類型為「負載平衡器」的服務。 負載平衡器 IP 位址是從透過本機 UI 提供的服務 Ip 範圍中選擇。 
- **核心 DNS** -此附加元件會設定 DNS 記錄，將服務名稱對應至叢集 IP 位址。

用於 Kubernetes 節點和外部服務的 IP 位址會透過裝置本機 UI 中的 [ **計算網路** ] 頁面來提供。

![本機 UI 中的 Kubernetes IP 指派](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-ip-assignment-local-ui-1.png)

IP 指派適用于：

- **Kubernetes 節點 ip**：此 IP 範圍用於 Kubernetes 主機和背景工作節點。 當 Kubernetes 節點彼此通訊時，會使用這些 IP。

- **Kubernetes 外部服務 ip**：此 IP 範圍適用于外部服務 (也稱為在 Kubernetes 叢集外部公開的 Load Balancer 服務) 。  


## <a name="next-steps"></a>後續步驟

若要在您的 Azure Stack Edge 上設定 Kubernetes 網路，請參閱：

- 透過[IoT Edge 在您的 Azure Stack Edge 外部公開無狀態應用程式](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md)。

- 透過[kuebctl 在您的 Azure Stack Edge 外部公開無狀態應用程式](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md)。
