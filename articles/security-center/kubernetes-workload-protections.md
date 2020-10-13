---
title: Kubernetes 工作負載的工作負載保護
description: 瞭解如何使用 Azure 資訊安全中心的 Kubernetes 工作負載保護安全性建議集合
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 09/12/2020
ms.author: memildin
ms.openlocfilehash: 8a387adde6c74b8eb1ff950c5e6b5183e43d1f4f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448667"
---
# <a name="protect-your-kubernetes-workloads"></a>保護 Kubernetes 工作負載

此頁面說明如何使用 Azure 資訊安全中心一組專門用來 Kubernetes 工作負載保護的安全性建議。

深入瞭解[使用 Kubernetes 許可控制的工作負載保護最佳做法](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)中的這些功能

如果您啟用 Azure Defender，資訊安全中心會提供更多容器安全性功能。 尤其是：

- 使用[適用于容器登錄的 Azure Defender](defender-for-container-registries-introduction.md)來掃描您的容器登錄是否有弱點
- 為您的 K8s 叢集[Azure Defender For Kubernetes](defender-for-kubernetes-introduction.md)取得即時威脅偵測警示

> [!TIP]
> 如需 Kubernetes 叢集和節點可能出現的 *所有* 安全性建議清單，請參閱建議參考表的 [容器一節](recommendations-reference.md#recs-containers) 。



## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|版本狀態：|預覽|
|定價：|免費|
|必要的角色和權限：|用來編輯指派的**擁有**者或**安全性系統管理員**<br>查看建議的**讀者**|
|支援的叢集|需要 Kubernetes v 1.14 (或更高的) <br>叢集上沒有任何 PodSecurityPolicy 資源 (舊的 PSP 模型) <br>不支援 Windows 節點|
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![否](./media/icons/no-icon.png) 國家/地區/主權 (US Gov、中國 Gov、其他 Gov)|
|||


## <a name="set-up-your-workload-protection"></a>設定您的工作負載保護

Azure 資訊安全中心包含安裝 **適用于 Kubernetes 的 Azure 原則附加**元件時可用的建議組合。

1. 若要設定建議，您必須先安裝 [加入]：

    1. 在 [建議] 頁面中，搜尋名為 Azure 原則 Kubernetes 附加元件的建議，並在您的叢集 **上安裝和啟用**。

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes.png" alt-text="建議 * * 應在您的叢集上安裝並啟用 Kubernetes 的 Azure 原則附加元件 * *":::

        > [!TIP]
        > 這項建議包含在五個不同的安全性控制項中，而您在下一個步驟中所選取的是什麼。

    1. 從任何安全性控制項中，選取建議以查看您可以在其上安裝新增的資源，然後選取 [ **修復**]。 

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes-details.png" alt-text="建議 * * 應在您的叢集上安裝並啟用 Kubernetes 的 Azure 原則附加元件 * *":::

1. 附加元件安裝完成後約30分鐘，資訊安全中心會針對下列建議顯示叢集的健全狀況狀態，每個都出現在相關的安全性控制中，如下所示：

    > [!TIP]
    > 某些建議有必須透過 Azure 原則自訂的參數，才能有效使用它們。 例如，若要從建議的容器映射中獲益，請 **只從信任的**登錄中部署，您必須定義受信任的登錄。
    > 
    > 如果您未輸入需要設定之建議的必要參數，您的工作負載將會顯示為狀況不良。

    | 建議名稱                                                                   | 安全性控制                         | 需要設定 |
    |---------------------------------------------------------------------------------------|------------------------------------------|------------------------|
    | 應 (預覽版來強制執行容器 CPU 和記憶體限制)                           | 保護應用程式免于遭受 DDoS 攻擊 | 否                     |
    | 應避免 (預覽的特殊許可權容器)                                      | 管理存取權和許可權            | 否                     |
    | 不可變的 (唯讀) 根目錄檔案系統應針對容器強制執行 (預覽)      | 管理存取權和許可權            | 否                     |
    | 應避免 (預覽版的容器具有許可權擴大)                        | 管理存取權和許可權            | 否                     |
    | 以根使用者的形式執行容器應避免 (預覽)                            | 管理存取權和許可權            | 否                     |
    | 共用機密主機命名空間的容器應避免 (預覽)               | 管理存取權和權限            | 否                     |
    |  (預覽的容器應強制執行最低許可權 Linux 功能)        | 管理存取權和許可權            | **是**                |
    | Pod HostPath 磁片區掛接的使用應限制為已知清單 (預覽)     | 管理存取權和權限            | **是**                |
    | 容器應該只在允許的埠上接聽 (預覽)                               | 限制未經授權的網路存取     | **是**                |
    | 服務應該只在允許的埠上接聽 (預覽)                                 | 限制未經授權的網路存取     | **是**                |
    | 使用主機網路和埠應受限於 (預覽版)                      | 限制未經授權的網路存取     | **是**                |
    | 覆寫或停用容器 AppArmor 設定檔應受限於 (預覽)  | 修復安全性設定        | **是**                |
    | 容器映射只應從信任的登錄部署 (預覽版)             | 補救弱點                | **是**                |


1. 對於必須自訂參數的建議，請設定參數：

    1. 從 [安全性中心] 功能表中，選取 [ **安全性原則**]。
    1. 選取相關的訂用帳戶。
    1. 從 [ **安全性中心預設原則** ] 區段中，選取 [ **查看有效原則**]。
    1. 選取 [ASC 預設值]。
    1. 開啟 [ **參數** ] 索引標籤，並視需要修改值。
    1. 選取 [檢閱 + 儲存]。
    1. 選取 [儲存]****。


1. 若要強制執行任何建議， 

    1. 開啟 [建議詳細資料] 頁面，然後選取 [ **拒絕**：

        :::image type="content" source="./media/defender-for-kubernetes-usage/enforce-workload-protection-example.png" alt-text="建議 * * 應在您的叢集上安裝並啟用 Kubernetes 的 Azure 原則附加元件 * *":::

        這會開啟您設定範圍的窗格。 

    1. 設定領域之後，請選取 [ **變更為拒絕**]。

1. 若要查看哪些建議適用于您的叢集：

    1. 開啟 [安全性中心的 [資產清查](asset-inventory.md) ] 頁面，並使用資源類型篩選器來 **Kubernetes 服務**。

    1. 選取要調查的叢集，並查看可用的建議。 

1. 從工作負載保護集查看建議時，您會看到受影響的 pod 數目 ( 「Kubernetes 元件」 ) 與叢集一起列出。 如需特定 pod 的清單，請選取叢集，然後選取 [ **採取動作**]。

    :::image type="content" source="./media/defender-for-kubernetes-usage/view-affected-pods-for-recommendation.gif" alt-text="建議 * * 應在您的叢集上安裝並啟用 Kubernetes 的 Azure 原則附加元件 * *"::: 

1. 若要測試強制，請使用下列兩個 Kubernetes 部署：

    - 其中一個適用于狀況良好的部署，符合工作負載保護建議的組合。
    - 另一個適用于狀況不良的部署，不符合 *任何* 建議。

    依原樣部署 yaml 檔案，或使用它們作為參考，以補救您自己的工作負載 (步驟 VIII)   


## <a name="healthy-deployment-example-yaml-file"></a>狀況良好的部署範例 yaml 檔案

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-healthy-deployment
  labels:
    app: redis
spec:
  replicas: 3
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
      annotations:
        apparmor.security.beta.kubernetes.io/pod: runtime/default
        container.apparmor.security.beta.kubernetes.io/redis: runtime/default
    spec:
      containers:
      - name: redis
        image: healthyClusterRegistry.azurecr.io/redis:latest
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: 100m
            memory: 250Mi
        securityContext:
          privileged: false
          readOnlyRootFilesystem: true
          allowPrivilegeEscalation: false
          runAsNonRoot: true
          runAsUser: 1000
---
apiVersion: v1
kind: Service
metadata:
  name: redis-healthy-service
spec:
  type: LoadBalancer
  selector:
    app: redis
  ports:
  - port: 80
    targetPort: 80
```

## <a name="unhealthy-deployment-example-yaml-file"></a>狀況不良的部署範例 yaml 檔案

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-unhealthy-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:      
      labels:
        app: nginx
    spec:
      hostNetwork: true
      hostPID: true 
      hostIPC: true
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 9001
          hostPort: 9001
        securityContext:
          privileged: true
          readOnlyRootFilesystem: false
          allowPrivilegeEscalation: true
          runAsUser: 0
          capabilities:
            add:
              - NET_ADMIN
        volumeMounts:
        - mountPath: /test-pd
          name: test-volume
          readOnly: true
      volumes:
      - name: test-volume
        hostPath:
          # directory location on host
          path: /tmp
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-unhealthy-service
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
  - port: 6001
    targetPort: 9001
```



## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何設定 Kubernetes 工作負載保護。 

如需其他相關材質，請參閱下列頁面： 

- [適用于容器的安全性中心建議](recommendations-reference.md#recs-containers)
- [AKS 叢集層級的警示](alerts-reference.md#alerts-akscluster)
- [容器主機層級的警示](alerts-reference.md#alerts-containerhost)