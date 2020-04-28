---
title: 將自我裝載的 Azure API 管理閘道部署至 Kubernetes |Microsoft Docs
description: 瞭解如何將自我裝載的 Azure API 管理閘道部署至 Kubernetes
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 109316acb73d3c5f00186298c1f8840c516e5790
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "73513831"
---
# <a name="deploy-a-self-hosted-azure-api-management-gateway-to-kubernetes"></a>將自我裝載的 Azure API 管理閘道部署至 Kubernetes

本文提供將自我裝載的 Azure API 管理閘道部署到 Kubernetes 叢集中的步驟。

> [!NOTE]
> 自我裝載閘道功能目前為預覽狀態。 在預覽期間，自我裝載閘道僅適用于開發人員和進階層，不需要額外付費。 開發人員層僅限於單一自我裝載的閘道部署。


## <a name="prerequisites"></a>Prerequisites

- 完成下列快速入門：[建立 AZURE API 管理實例](get-started-create-service-instance.md)
- 建立 Kubernetes 叢集。 [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/)是適用于開發和評估用途的絕佳選項。 針對生產工作負載，您可以在外部雲端使用[Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/)或 Kubernetes 叢集。
- [在您的 API 管理實例中布建閘道資源](api-management-howto-provision-self-hosted-gateway.md)。

## <a name="deploy-the-gateway-to-kubernetes"></a>將閘道部署至 Kubernetes

1. 從 [**設定**] 底下選取 [**閘道**]。
2. 選取您想要部署的閘道資源。
3. 選取 [**部署**]。
4. 請注意，[**權杖**] 文字方塊中的新權杖會使用預設的**到期**和**秘密金鑰**值自動產生。 如有需要，請調整任一或兩者，然後選取 [**產生**] 以建立新的權杖。
5. 請確定已在 [**部署腳本**] 底下選取 [ **Kubernetes** ]。
6. 選取 [**部署**] 旁**的<閘道-名稱> yml**檔案連結以下載檔案。
7. 視需要調整 yml 檔案中的埠對應和容器名稱。
8. 選取位於 [**部署**] 文字方塊右邊的`kubectl` **複製**圖示，將命令儲存到剪貼簿。 
9. 將命令貼到 [終端機] （或命令）視窗。 請注意，此命令會預期所下載的環境檔案會出現在目前的目錄中。
```console
    kubectl apply -f <gateway-name>.yaml
```
10. 執行命令。 命令會指示您的 Kubernetes 叢集執行容器，使用從 Microsoft Container Registry 下載的自我裝載閘道映射，並將容器設定為公開 HTTP （8080）和 HTTPS （443）埠。
11. 執行下列命令，以檢查閘道 pod 是否正在執行。 請注意，您的 pod 名稱會不同。 
```console
kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
local-gateway-55f774f844-bv9wt    1/1     Running   0          1m
```
12. 執行下列命令來檢查閘道服務是否正在執行。 請注意，您的服務名稱會不同。 
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
localgateway     NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
13. 回到 [Azure 入口網站]，並確認您剛才部署的 [閘道] 節點回報 [狀況良好] 狀態。

![閘道狀態](media/api-management-howto-deploy-self-hosted-gateway-to-k8s/status.png)

> [!TIP]
> 使用<code>kubectl logs <gateway-pod-name></code>命令來查看自我裝載閘道記錄的快照集。

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解自我裝載閘道，請參閱[AZURE API 管理自我裝載閘道總覽](self-hosted-gateway-overview.md)
* 深入瞭解[Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/intro-kubernetes)


