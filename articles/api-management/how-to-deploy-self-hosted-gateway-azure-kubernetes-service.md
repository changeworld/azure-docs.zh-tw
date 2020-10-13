---
title: 將自我裝載閘道部署至 Azure Kubernetes Service |Microsoft Docs
description: 瞭解如何將 Azure API 管理的自我裝載閘道元件部署到 Azure Kubernetes Service
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: 02962e9c5be2c4b73d121a53a7b595c573ad6cd0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87015216"
---
# <a name="deploy-to-azure-kubernetes-service"></a>部署到 Azure Kubernetes Service

本文提供將 Azure API 管理的自我裝載閘道元件部署至 [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/)的步驟。 若要將自我裝載閘道部署至 Kubernetes 叢集，請參閱這[份檔](how-to-deploy-self-hosted-gateway-kubernetes.md)。

## <a name="prerequisites"></a>先決條件

- [建立 Azure API 管理執行個體](get-started-create-service-instance.md)
- [建立 Azure Kubernetes 叢集](../aks/kubernetes-walkthrough-portal.md)
- [在您的 API 管理實例中布建閘道資源](api-management-howto-provision-self-hosted-gateway.md)。

## <a name="deploy-the-self-hosted-gateway-to-aks"></a>將自我裝載閘道部署至 AKS

1. 從 [**部署和基礎結構**] 底下選取**閘道**。
2. 選取您想要部署的自我裝載閘道資源。
3. 選取 [ **部署**]。
4. 請注意，您可以使用預設的**到期**和**秘密金鑰**值，自動產生 [**權杖**] 文字方塊中的新權杖。 視需要調整其中一項或兩者，然後選取 [ **產生** ] 以建立新的權杖。
5. 確定已選取 [**部署腳本**] 底下的 [ **Kubernetes** ]。
6. 選取 [**部署**] 旁**的<[閘道名稱]> yml**檔案連結以下載檔案。
7. 視需要調整 yml 檔中的埠對應和容器名稱。
8. 視您的案例而定，您可能需要變更 [服務類型](../aks/concepts-network.md#services)。 預設值為 `NodePort`。
9. 選取位於 [**部署**] 文字方塊右邊的**複製**圖示，將 `kubectl` 命令儲存至剪貼簿。
10. 將命令貼到終端機 (或命令) 視窗。 請注意，此命令預期所下載的環境檔案會存在於目前的目錄中。
```console
    kubectl apply -f <gateway-name>.yaml
```
11. 執行命令。 此命令會指示您的 AKS 叢集執行容器、使用從 Microsoft Container Registry 下載的自我裝載閘道映射，以及將容器設定為公開 HTTP (8080) 和 HTTPS (443) 埠。
12. 執行下列命令來檢查閘道 pod 是否正在執行。 請注意，您的 pod 名稱將會不同。
```console
kubectl get pods
NAME                                   READY     STATUS    RESTARTS   AGE
contoso-apim-gateway-59f5fb94c-s9stz   1/1       Running   0          1m
```
13. 執行下列命令以檢查閘道服務是否正在執行。 請注意，您的服務名稱和 IP 位址將會不同。
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
contosogateway   NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
14. 返回至 Azure 入口網站，並確認您剛剛部署的閘道節點回報狀況良好的狀態。

> [!TIP]
> 使用 <code>kubectl logs <gateway-pod-name></code> 命令來查看自我裝載閘道記錄的快照。

## <a name="next-steps"></a>接下來的步驟

* 若要深入瞭解自我裝載的閘道，請參閱 [AZURE API 管理自我裝載閘道總覽](self-hosted-gateway-overview.md)
* 深入瞭解 [Azure Kubernetes Service](../aks/intro-kubernetes.md)
* 瞭解 [如何在雲端設定和保存記錄](how-to-configure-cloud-metrics-logs.md)
* * 瞭解 [如何在本機設定和保存記錄](how-to-configure-local-metrics-logs.md)
