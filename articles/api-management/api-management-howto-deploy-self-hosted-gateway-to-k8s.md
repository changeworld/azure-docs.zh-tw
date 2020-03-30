---
title: 將自託管的 Azure API 管理閘道部署到庫伯內斯 |微軟文檔
description: 瞭解如何將自託管的 Azure API 管理閘道部署到庫貝內特斯
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513831"
---
# <a name="deploy-a-self-hosted-azure-api-management-gateway-to-kubernetes"></a>將自託管的 Azure API 管理閘道部署到庫貝內內斯

本文提供了將自託管 Azure API 管理閘道部署到 Kubernetes 群集的步驟。

> [!NOTE]
> 自託管閘道功能處於預覽狀態。 在預覽期間，自託管閘道僅在開發人員和高級層中可用，無需支付額外費用。 開發人員層僅限於單個自託管閘道部署。


## <a name="prerequisites"></a>Prerequisites

- 完成以下快速入門：[創建 Azure API 管理實例](get-started-create-service-instance.md)
- 創建庫伯奈斯群集。 [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/)是開發和評估目的的好選擇。 對於生產工作負載，可以在外國雲中使用[Azure 庫伯奈斯服務](https://azure.microsoft.com/services/kubernetes-service/)或庫伯奈斯群集。
- [在 API 管理實例中預配閘道資源](api-management-howto-provision-self-hosted-gateway.md)。

## <a name="deploy-the-gateway-to-kubernetes"></a>將閘道部署到庫伯內斯

1. 從 **"設置"** 下選擇**閘道**。
2. 選擇要部署的閘道資源。
3. 選擇**部署**。
4. 請注意，使用預設**的"過期"** 和"**金鑰**"值自動生成**權杖**文字方塊中的新權杖。 根據需要調整任一或兩者，然後選擇 **"生成"** 以創建新權杖。
5. 確保**庫伯內斯**是在**部署腳本**下選擇的。
6. 選擇 **"部署****"旁邊的<閘道名稱>.yml**檔連結以下載該檔。
7. 根據需要調整 yml 檔中的埠映射和容器名稱。
8. 選擇位於 **"部署**文本"框右端`kubectl`**的副本**圖示，將命令保存到剪貼簿。 
9. 將命令粘貼到終端（或命令）視窗。 請注意，該命令希望下載的環境檔存在於目前的目錄中。
```console
    kubectl apply -f <gateway-name>.yaml
```
10. 執行命令。 該命令指示 Kubernetes 群集使用從 Microsoft 容器註冊表下載的自託管閘道映射運行容器，並將容器配置為公開 HTTP （8080） 和 HTTPS （443） 埠。
11. 運行以下命令以檢查閘道窗格是否正在運行。 請注意，您的 pod 名稱將不同。 
```console
kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
local-gateway-55f774f844-bv9wt    1/1     Running   0          1m
```
12. 運行以下命令以檢查閘道服務是否正在運行。 請注意，服務名稱將不同。 
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
localgateway     NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
13. 返回 Azure 門戶，確認剛剛部署的閘道節點報告狀態正常。

![閘道狀態](media/api-management-howto-deploy-self-hosted-gateway-to-k8s/status.png)

> [!TIP]
> 使用<code>kubectl logs <gateway-pod-name></code>命令查看自承載閘道日誌的快照。

## <a name="next-steps"></a>後續步驟

* 要瞭解有關自託管閘道的詳細資訊，請參閱 Azure [API 管理自託管閘道概述](self-hosted-gateway-overview.md)
* 瞭解有關[Azure 庫伯奈斯服務](https://docs.microsoft.com/azure/aks/intro-kubernetes)的更多


