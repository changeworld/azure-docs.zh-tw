---
title: Azure 監視器中的容器監視解決方案 |Microsoft Docs
description: Azure 監視器中的容器監視解決方案，可協助您在單一位置中查看及管理您的 Docker 和 Windows 容器主機。
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/06/2020
ms.openlocfilehash: 14fa6859a16dc173e75091983abee717bf813220
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86499015"
---
# <a name="container-monitoring-solution-in-azure-monitor"></a>Azure 監視器中的容器監視解決方案

![容器符號](./media/containers/containers-symbol.png)

本文說明如何在 Azure 監視器中設定及使用容器監視解決方案，這可協助您在單一位置中查看及管理您的 Docker 和 Windows 容器主機。 Docker 是用來建立容器的軟體虛擬化系統，自動將軟體部署至其 IT 基礎結構。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

解決方案會顯示哪些容器正在執行、它們正在執行的是哪些容器映像，以及容器執行的位置。 您可以檢視詳細的稽核資訊，其中顯示搭配容器使用的命令。 而且，藉由檢視及搜尋集中式記錄，而不需從遠端檢視 Docker 或 Windows 主機，即可針對容器進行疑難排解。 您可能會找到有雜訊且耗用過多主機資源的容器。 而且，您可以檢視容器的集中式 CPU、記憶體、儲存體以及網路使用量和效能資訊。 您可以在執行 Windows 的電腦上集中管理，並從 Windows Server、HYPER-V 和 Docker 容器比較記錄。 解決方案支援下列容器協調者：

- Docker Swarm
- DC/OS
- Kubernetes
- Service Fabric
- Red Hat OpenShift

如果您已在[Azure Service Fabric](../../service-fabric/service-fabric-overview.md)中部署容器，建議您同時啟用[Service Fabric 解決方案](../../service-fabric/service-fabric-diagnostics-oms-setup.md)和此解決方案，以包含監視叢集事件。 在啟用 Service Fabric 解決方案之前，請先參閱[使用 Service Fabric 解決方案](../../service-fabric/service-fabric-diagnostics-event-analysis-oms.md)，以瞭解其提供的內容和使用方式。

對於部署到 Azure Container Service (AKS) 上所裝載 Kubernetes 環境的工作負載，若要監視其效能，請參閱[監視 Azure Kubernetes Service](../../azure-monitor/insights/container-insights-overview.md)。 容器監視解決方案不支援監視該平臺。  

下圖顯示各種容器主機與具有 Azure 監視器之代理程式之間的關聯性。

![容器圖表](./media/containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>系統需求和支援的平台

開始之前請檢閱下列詳細資料，以確認符合必要條件。

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>容器監視解決方案支援 Docker Orchestrator 和作業系統平台

下表概述具有 Azure 監視器之容器清查、效能和記錄的 Docker 協調流程和作業系統監視支援。   

|Docker 協調流程 | ACS | Linux | Windows | 容器<br>清查 | 映像<br>清查 | 節點<br>清查 | 容器<br>效能 | 容器<br>事件 | 事件<br>Log | 容器<br>Log |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosphere<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Swarm | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| 服務<br>網狀架構 | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Red Hat 開啟<br>Shift | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>(獨立) | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Linux 伺服器<br>(獨立) | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |

### <a name="docker-versions-supported-on-linux"></a>在 Linux 上支援的 Docker 版本

- Docker 1.11 至 1.13
- Docker CE 和 EE v17.06

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>x64 Linux 散發套件可支援作為容器主機

- Ubuntu 14.04 LTS 和 16.04 LTS
- CoreOS (穩定版)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- openSUSE LEAP 42.2
- CentOS 7.2 和 7.3
- SLES 12
- RHEL 7.2 和 7.3
- Red Hat OpenShift Container Platform (OCP) 3.4 和 3.5
- ACS Mesosphere DC/OS 1.7.3 至 1.8.8
- ACS Kubernetes 1.4.5 至 1.6
    - Kubernetes 事件、Kubernetes 清查以及容器流程僅支援搭配適用於 Linux 的 Log Analytics 代理程式 1.4.1-45 版和更新版本來使用
- ACS Docker Swarm

[!INCLUDE [log-analytics-agent-note.md](../../../includes/log-analytics-agent-note.md)] 

### <a name="supported-windows-operating-system"></a>支援的 Windows 作業系統

- Windows Server 2016
- Windows 10 年度版 (Professional 或 Enterprise)

### <a name="docker-versions-supported-on-windows"></a>在 Windows 上支援 Docker 版本

- Docker 1.12 和 1.13
- Docker 17.03.0 和更新版本

## <a name="installing-and-configuring-the-solution"></a>安裝和設定方案

請使用下列資訊來安裝和設定方案。

1. 從[Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) ，或使用[從方案庫新增監視解決方案](../../azure-monitor/insights/solutions.md)中所述的程式，將容器監視解決方案新增至您的 Log Analytics 工作區。

2. 安裝和使用 Docker 搭配 Log Analytics 代理程式。 根據您的作業系統和 Docker Orchestrator，您可以使用下列方法來設定代理程式。
   - 若為獨立式主機：
     - 在支援的 Linux 作業系統上，安裝和執行 Docker，然後再安裝和設定[適用於 Linux 的 Log Analytics 代理程式](../../azure-monitor/learn/quick-collect-linux-computer.md)。  
     - 在 CoreOS 上，您無法執行適用於 Linux 的 Log Analytics 代理程式。 相反地，您可以執行適用於 Linx 的 Log Analytics 代理程式容器化版本。 檢閱 Linux 容器主機，包括 CoreOS 或 Azure Government Linux 容器主機，包括 CoreOS，如果您正在使用 Azure Government Cloud 中的容器。
     - 在 Windows Server 2016 和 Windows 10 上，安裝 Docker 引擎和用戶端，然後連接代理程式來收集資訊，並將其傳送至 Azure 監視器。 如果您有 Windows 環境，請檢閱[安裝和設定 Windows 容器主機](#install-and-configure-windows-container-hosts)。
   - 若為 Docker 多主機協調流程：
     - 如果您有 Red Hat OpenShift 環境，請參閱為 Red Hat OpenShift 設定 Log Analytics 代理程式。
     - 如果您有使用 Azure Container Service 的 Kubernetes 叢集：
       - 參閱[為 Kubernetes 設定 Log Analytics Linux 代理程式](#configure-a-log-analytics-linux-agent-for-kubernetes)。
       - 參閱[為 Kubernetes 設定 Log Analytics Windows 代理程式](#configure-a-log-analytics-windows-agent-for-kubernetes)。
       - 參閱使用 Helm 在 Linux Kubernetes 上部署 Log Analytics 代理程式。
     - 如果您有 Azure Container Service DC/OS 叢集，請在[使用 Azure 監視器監視 AZURE CONTAINER SERVICE DC/os](/previous-versions/azure/container-service/dcos-swarm/container-service-monitoring-oms)叢集中深入瞭解。
     - 如果您有 Docker Swarm 模式環境，請參閱為 Docker Swarm 設定 Log Analytics 代理程式，以進一步瞭解。
     - 如果您有 Service Fabric 叢集，請在[使用 Azure 監視器監視容器](../../service-fabric/service-fabric-diagnostics-oms-containers.md)中深入瞭解。

檢閱 [Windows 上的 Docker 引擎](/virtualization/windowscontainers/manage-docker/configure-docker-daemon)文章，以取得有關如何在執行 Windows 的電腦上安裝和設定您 Docker 引擎的資訊。

> [!IMPORTANT]
> 在容器主機上安裝 [適用於 Linux 的 Log Analytics 代理程式](../../azure-monitor/learn/quick-collect-linux-computer.md)**之前**，Docker 必須已在執行中。 如果您在安裝 Docker 前，已安裝此代理程式，則必須重新安裝適用於 Linux 的 Log Analytics 代理程式。 如需 Docker 的詳細資訊，請參閱 [Docker 網站](https://www.docker.com)。

### <a name="install-and-configure-linux-container-hosts"></a>安裝和設定 Linux 容器主機

在您安裝 Docker 之後，使用容器主機的下列設定來設定可搭配 Docker 使用的代理程式。 首先您需要 Log Analytics 工作區識別碼和金鑰，這些可以在 Azure 入口網站中找到。 在您的工作區中，按一下 [**快速啟動**  >  **電腦**] 以查看您的**工作區識別碼**和**主要金鑰**。  將兩者複製並貼到您最愛的編輯器。

**適用於 CoreOS 以外的所有 Linux 容器主機：**

- 如需如何安裝適用於 Linux 的 Log Analytics 代理程式詳細資訊和步驟，請參閱 [Log Analytics 代理程式概觀](../../azure-monitor/platform/log-analytics-agent.md)。

**適用於包含 CoreOS 的所有 Linux 容器主機：**

啟動您要監視的容器。 修改並使用下列範例：

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**針對所有 Azure Government Linux 容器主機，包括 CoreOS：**

啟動您要監視的容器。 修改並使用下列範例：

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**從使用已安裝的代理程式切換為使用容器中的 Linux 代理程式**

如果您先前使用了直接安裝的代理程式，而且想要改為使用在容器中執行的代理程式，則必須先移除適用於 Linux 的 Log Analytics 代理程式。 請參閱[為混合式環境中的 Linux 電腦設定 Log Analytics 代理程式](../../azure-monitor/learn/quick-collect-linux-computer.md)，以了解如何順利解除安裝代理程式。  

#### <a name="configure-a-log-analytics-agent-for-docker-swarm"></a>為 Docker Swarm 設定 Log Analytics 代理程式

您可在 Docker Swarm 上，以全域服務的方式執行 Log Analytics 代理程式。 使用下列資訊建立 Log Analytics 代理程式服務。 您必須提供 Log Analytics 工作區識別碼和主要金鑰。

- 在主要節點上執行下列命令。

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>保護 Docker Swarm 的祕密

針對 Docker Swarm，在建立了工作區識別碼和主要金鑰的祕密後，請使用下列資訊來建立您的祕密資訊。

1. 在主要節點上執行下列命令。

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. 確認已正確建立祕密。

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. 執行下列命令，將祕密掛接至容器化的 Log Analytics 代理程式。

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-a-log-analytics-agent-for-red-hat-openshift"></a>為 Red Hat OpenShift 設定 Log Analytics 代理程式

有三種方式可將 Log Analytics 代理程式新增至 Red Hat OpenShift，以開始收集容器監視資料。

* 直接在每個 OpenShift 節點上[安裝適用于 Linux 的 Log Analytics 代理程式](../../azure-monitor/learn/quick-collect-linux-computer.md)  
* 在位於 Azure 中的每個 OpenShift 節點上[啟用記錄分析 VM 延伸模組](../../azure-monitor/learn/quick-collect-azurevm.md)  
* 將 Log Analytics 代理程式安裝為 OpenShift daemon-set  

在本節中，我們會討論安裝 Log Analytics 代理程式作為 OpenShift 精靈集所需的步驟。  

1. 登入 OpenShift 主要節點，並從 GitHub 複製 YAML 檔 [ocp-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) 到您的主要節點，然後以 Log Analytics 工作區識別碼和只要金鑰修改值。
2. 執行下列命令來建立 Azure 監視器的專案，並設定使用者帳戶。

    ```
    oc adm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. 若要部署精靈集，請執行下列命令：

    `oc create -f ocp-omsagent.yaml`

4. 若要驗證它已設定並正常運作，請輸入下列命令：

    `oc describe daemonset omsagent`  

    輸出會像下面這樣：

    ```
    [ocpadmin@khm-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

如果在使用 Log Analytics 代理程式精靈集 YAML 檔案時，您想要使用祕密來保護您的 Log Analytics 工作區識別碼及主要金鑰，請執行下列步驟。

1. 登入 OpenShift 主要節點，並從 GitHub 複製 YAML 檔 [ocp-ds-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) 和密碼產生指令碼 [ocp-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh)。  這個指令碼會產生 Log Analytics 工作區識別碼及主要金鑰的密碼 YAML 檔案，來保護您的密碼資訊。  
2. 執行下列命令來建立 Azure 監視器的專案，並設定使用者帳戶。 祕密產生指令碼會要求您的 Log Analytics 工作區識別碼 `<WSID>` 和主要金鑰 `<KEY>`，並且在完成時，建立 ocp-secret.yaml 檔案。  

    ```
    oc adm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. 執行下列命令以開啟密碼檔案：

    `oc create -f ocp-secret.yaml`

4. 執行下列命令以驗證部署：

    `oc describe secret omsagent-secret`  

    輸出會像下面這樣：  

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe secret omsagent-secret  
    Name:           omsagent-secret  
    Namespace:      omslogging  
    Labels:         <none>  
    Annotations:    <none>  

    Type:   Opaque  

    Data  
    ====  
    KEY:    89 bytes  
    WSID:   37 bytes  
    ```

5. 執行下列命令以部署 Log Analytics 代理程式精靈集 YAML 檔案：

    `oc create -f ocp-ds-omsagent.yaml`  

6. 執行下列命令以驗證部署：

    `oc describe ds oms`

    輸出會像下面這樣：

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

#### <a name="configure-a-log-analytics-linux-agent-for-kubernetes"></a>為 Kubernetes 設定 Log Analytics Linux 代理程式

針對 Kubernetes，您可使用指令碼為工作區 ID 與主索引鍵產生秘密 YAML 檔案，以安裝適用於 Linux 的 Log Analytics 代理程式。 [Log Analytics Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) (英文) 頁面含有您可以在有或無祕密資訊的情況下使用的檔案。

- 適用於 Linux 的預設 Log Analytics 代理程式 DaemonSet 沒有祕密資訊 (omsagent.yaml)
- 適用於 Linux 的 Log Analytics 代理程式 DaemonSet YAML 檔案會使用祕密資訊 (omsagent-ds-secrets.yaml)，搭配祕密產生指令碼來產生祕密 YAML (omsagentsecret.yaml) 檔案。

您可以選擇在有或無祕密的情況下建立 omsagent DaemonSet。

**沒有祕密的預設 OMSagent DaemonSet yaml 檔案**

- 針對預設 Log Analytics 代理程式 DaemonSet yaml 檔案，請使用您的 WSID 和索引鍵取代 `<WSID>` 和 `<KEY>`。 將檔案複製到您的主要節點，並執行下列命令：

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**有祕密的預設 OMSagent DaemonSet yaml 檔案**

1. 若要使用具有祕密資訊的 Log Analytics 代理程式 DaemonSet，請先建立祕密。
    1. 複製指令碼和祕密範本檔案，並確定它們位於相同的目錄。
        - 祕密產生指令碼 - secret-gen.sh
        - 祕密範本 - secret-template.yaml
    2. 執行指令碼，如下列範例所示。 指令碼會要求 Log Analytics 工作區識別碼與主要金鑰，並在您輸入這兩個資訊之後，指令碼會建立一個祕密的 YAML 檔案以供您執行。   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. 執行以下命令來建立祕密 Pod：
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. 若要確認，請執行下列命令：

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        輸出會像下面這樣：

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        輸出會像下面這樣：

        ```
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. 執行 ```sudo kubectl create -f omsagent-ds-secrets.yaml``` 以建立您的 omsagent daemon-set

2. 確認 Log Analytics 代理程式 DaemonSet 正在執行，如下所示：

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```

針對 Kubernetes，請使用指令碼來產生適用於 Linux 的 Log Analytics 代理程式工作區 ID 與主索引鍵的祕密 YAML 檔案。 搭配 [omsagent yaml 檔案](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml)使用下列範例資訊來保護您的祕密資訊。

```
keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
Name:           omsagent-secret
Namespace:      default
Labels:         <none>
Annotations:    <none>

Type:   Opaque

Data
====
WSID:   36 bytes
KEY:    88 bytes
```

#### <a name="configure-a-log-analytics-windows-agent-for-kubernetes"></a>為 Kubernetes 設定 Log Analytics Windows 代理程式

針對 Windows Kubernetes，您可使用指令碼為工作區 ID 與主索引鍵產生秘密 YAML 檔案，以安裝 Log Analytics 代理程式。 [Log Analytics Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) (英文) 頁面含有您可在具祕密資訊的情況下使用的檔案。  您必須分別為主要節點和代理程式節點安裝 Log Analytics 代理程式。  

1. 若要使用在主要節點上具有祕密資訊的 Log Analytics 代理程式 DaemonSet，請先登入並建立祕密。
    1. 複製指令碼和祕密範本檔案，並確定它們位於相同的目錄。
        - 祕密產生指令碼 - secret-gen.sh
        - 祕密範本 - secret-template.yaml

    2. 執行指令碼，如下列範例所示。 指令碼會要求 Log Analytics 工作區識別碼與主要金鑰，並在您輸入這兩個資訊之後，指令碼會建立一個祕密的 YAML 檔案以供您執行。

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. 執行 ```kubectl create -f omsagentsecret.yaml``` 以建立您的 omsagent daemon-set
    4. 若要檢查，請執行下列命令：

        ```
        root@ubuntu16-13db:~# kubectl get secrets
        ```

        輸出會像下面這樣：

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. 執行 ```kubectl create -f ws-omsagent-de-secrets.yaml``` 以建立您的 omsagent daemon-set

2. 確認 Log Analytics 代理程式 DaemonSet 正在執行，如下所示：

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. 若要在執行 Windows 的背景工作節點上安裝代理程式，請遵循[安裝和設定 Windows 容器主機](#install-and-configure-windows-container-hosts)一節中的步驟。

#### <a name="use-helm-to-deploy-log-analytics-agent-on-linux-kubernetes"></a>使用 Helm 在 Linux Kubernetes 上部署 Log Analytics 代理程式

若要使用 Helm 在 Linux Kubernetes 環境下部署 Log Analytics 代理程式，請執行下列步驟。

1. 執行 ```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms``` 以建立您的 omsagent daemon-set
2. 結果將看起來如下所示：

    ```
    NAME:   omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED

    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     3s

    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         3s
    ```

3. 您可以執行 ```helm status "omsagent"``` 來檢查 omsagent 的狀態，而輸出將看起來如下所示：

    ```
    keiko@k8s-master-3814F33-0:~$ helm status omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED
 
    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     17m
 
    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         17m
    ```
   
    如需進一步的資訊，請造訪[容器解決方案 Helm Chart](https://aka.ms/omscontainerhelm)。

### <a name="install-and-configure-windows-container-hosts"></a>安裝和設定 Windows 容器主機

使用章節中的資訊來安裝和設定 Windows 容器主機。

#### <a name="preparation-before-installing-windows-agents"></a>安裝 Windows 代理程式之前的準備動作

在執行 Windows 的電腦上安裝代理程式之前，您需要設定 Docker 服務。 此設定可讓 Windows 代理程式或 Azure 監視器的虛擬機器擴充功能使用 Docker TCP 通訊端，讓代理程式可以從遠端存取 Docker daemon，並捕捉資料進行監視。

##### <a name="to-configure-the-docker-service"></a>若要設定 Docker 服務  

執行下列 PowerShell 命令，以啟用 Windows Server 的 TCP 管道和具名管道：

```
Stop-Service docker
dockerd --unregister-service
dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
Start-Service docker
```

如需 Windows 容器使用之 Docker 精靈組態的詳細資訊，請參閱 [Windows 上的 Docker 引擎](/virtualization/windowscontainers/manage-docker/configure-docker-daemon)。

#### <a name="install-windows-agents"></a>安裝 Windows 代理程式

若要啟用 Windows 和 Hyper-V 容器監視，請在容器主機的 Windows 電腦上安裝 Microsoft Monitoring Agent (MMA)。 針對在您的內部部署環境中執行 Windows 的電腦，請參閱[將 Windows 電腦連線到 Azure 監視器](../../azure-monitor/platform/agent-windows.md)。 針對在 Azure 中執行的虛擬機器，使用[虛擬機器擴充](../../azure-monitor/learn/quick-collect-azurevm.md)功能將它們連接到 Azure 監視器。

您可以監視 Service Fabric 上執行的 Windows 容器。 不過，Service Fabric 目前只支援 [Azure 中執行的虛擬機器](../../azure-monitor/learn/quick-collect-azurevm.md)和[在內部部署環境中執行 Windows 的電腦](../../azure-monitor/platform/agent-windows.md)。

您可以確認容器監視解決方案已針對 Windows 正確設定。 若要檢查管理組件是否正確下載，請找出 ContainerManagement.xxx**。 這些檔案應在 C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs 資料夾。

## <a name="solution-components"></a>方案元件

從 Azure 入口網站，瀏覽至 [方案庫]** 並新增 [容器監視解決方案]****。 如果您是使用 Windows 代理程式，當您新增這個解決方案時，就會使用代理程式在每部電腦上安裝下列管理組件。 管理組件不需要任何設定或維護。

- C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs 中所安裝的 ContainerManagement.xxx**

## <a name="container-data-collection-details"></a>容器資料收集詳細資料

容器監視解決方案會從使用您已啟用之代理程式的容器主機和容器中，收集各種效能計量和記錄資料。

每隔三分鐘會依下列代理程式類型收集資料。

- [Log Analytics Linux 代理程式](../../azure-monitor/learn/quick-collect-linux-computer.md)
- [Windows 代理程式](../../azure-monitor/platform/agent-windows.md)
- [Log Analytics VM 擴充功能](../../azure-monitor/learn/quick-collect-azurevm.md)

### <a name="container-records"></a>容器資料列

下表顯示的範例是容器監視解決方案所收集的資料列，以及記錄搜尋結果中所顯示之資料類型。

| 資料類型 | 記錄檔搜尋中的資料類型 | 欄位 |
| --- | --- | --- |
| 主機和容器的效能 | `Perf` | Computer、ObjectName、CounterName &#40;%Processor Time、Disk Reads MB、Disk Writes MB、Memory Usage MB、Network Receive Bytes、Network Send Bytes、Processor Usage sec、Network&#41;、CounterValue、TimeGenerated、CounterPath、SourceSystem |
| 容器清查 | `ContainerInventory` | TimeGenerated、Computer、container name、ContainerHostname、Image、ImageTag、ContainerState、ExitCode、EnvironmentVar、Command、CreatedTime、StartedTime、FinishedTime、SourceSystem、ContainerID、ImageID |
| 容器映像清查 | `ContainerImageInventory` | TimeGenerated、Computer、Image、ImageTag、ImageSize、VirtualSize、Running、Paused、Stopped、Failed、SourceSystem、ImageID、TotalContainer |
| 容器記錄檔 | `ContainerLog` | TimeGenerated、Computer、image ID、container name、LogEntrySource、LogEntry、SourceSystem、ContainerID |
| 容器服務記錄檔 | `ContainerServiceLog`  | TimeGenerated、Computer、TimeOfCommand、Image、Command、SourceSystem、ContainerID |
| 容器節點清查 | `ContainerNodeInventory_CL`| TimeGenerated、Computer、ClassName_s、DockerVersion_s、OperatingSystem_s、Volume_s、Network_s、NodeRole_s、OrchestratorType_s、InstanceID_g、SourceSystem|
| Kubernetes 清查 | `KubePodInventory_CL` | TimeGenerated、Computer、PodLabel_deployment_s、PodLabel_deploymentconfig_s、PodLabel_docker_registry_s、Name_s、Namespace_s、PodStatus_s、PodIp_s、PodUid_g、PodCreationTimeStamp_t、SourceSystem |
| 容器流程 | `ContainerProcess_CL` | TimeGenerated、Computer、Pod_s、Namespace_s、ClassName_s、InstanceID_s、Uid_s、PID_s、PPID_s、C_s、STIME_s、Tty_s、TIME_s、Cmd_s、Id_s、Name_s、SourceSystem |
| Kubernetes 事件 | `KubeEvents_CL` | TimeGenerated、Computer、Name_s、ObjectKind_s、Namespace_s、Reason_s、Type_s、SourceComponent_s、SourceSystem、Message |

附加到 PodLabel** 資料類型的標籤是您自己的自訂標籤。 資料表中所顯示的附加 PodLabel 標籤就是範例。 因此，`PodLabel_deployment_s`、`PodLabel_deploymentconfig_s`、`PodLabel_docker_registry_s` 在環境的資料集中會有所不同，且一般而言會類似 `PodLabel_yourlabel_s`。

## <a name="monitor-containers"></a>監視容器
在 Azure 入口網站中啟用解決方案之後，[**容器**] 圖格會顯示容器主機和主機中執行之容器的相關摘要資訊。

![容器圖格](./media/containers/containers-title.png)

此圖格顯示環境中有多少容器以及其為失敗、執行中或已停止的概觀。

### <a name="using-the-containers-dashboard"></a>使用容器儀表板

按一下 [容器]**** 圖格。 在這裡，您會看到依下列各項組織的檢視︰

- **容器事件** - 會顯示容器狀態和包含失敗容器的電腦。
- **容器記錄** - 會顯示一段時間所產生的容器記錄圖表，和包含最大量記錄的電腦清單。
- **Kubernetes 事件** - 會顯示一段時間所產生的 Kubernetes 事件圖表，和 Pod 產生事件的原因清單。 只有在 Linux 環境中才會使用此資料集。**
- **Kubernetes 命名空間清查** - 會顯示命名空間和 Pod 的數目，並顯示其階層。 只有在 Linux 環境中才會使用此資料集。**
- **容器節點清查** - 會顯示容器節點/主機上使用的協調流程類型數目。 還會依容器數目列出電腦節點/主機。 只有在 Linux 環境中才會使用此資料集。**
- **容器映像庫存** - 會顯示已使用的容器映像總數和映像類型的數目。 還會依影像標籤列出映像數目。
- **容器狀態** - 會顯示具有執行中容器的容器節點/主機電腦總數。 還會依執行中主機的數目列出電腦。
- **容器流程** - 會顯示一段時間執行的容器流程折線圖。 還會依容器內的執行命令/流程列出容器。 只有在 Linux 環境中才會使用此資料集。**
- **容器 CPU 效能** - 會顯示一段時間電腦節點/主機的平均 CPU 使用率折線圖。 還會以平均 CPU 使用率作為基礎列出電腦節點/主機。
- **容器記憶體效能** - 會顯示一段時間的記憶體使用量折線圖。 還會以執行個體名稱作為基礎列出電腦記憶體使用率。
- **電腦效能** - 會顯示一段時間的 CPU 效能百分比、一段時間的記憶體使用量百分比，以及一段時間的可用磁碟空間 MB 等折線圖。 您可以將滑鼠停留在圖表中的任一行，以檢視更多詳細資料。

儀表板中的每個區域都是對所收集資料執行之搜尋的視覺表示方式。

![容器儀表板](./media/containers/containers-dash01.png)

![容器儀表板](./media/containers/containers-dash02.png)

在 [容器狀態]**** 區域中，按一下頂端區域，如下所示。

![容器狀態](./media/containers/containers-status.png)

[Log Analytics] 隨即開啟，顯示容器狀態的相關資訊。

![適用于容器的 Log Analytics](./media/containers/containers-log-search.png)

在這裡，您可以編輯搜尋查詢來進行修改，以尋找您感興趣的特定資訊。 如需記錄查詢的詳細資訊，請參閱[Azure 監視器中的記錄查詢](../log-query/log-query-overview.md)。

## <a name="troubleshoot-by-finding-a-failed-container"></a>尋找失敗的容器以進行疑難排解

如果容器已透過非零結束代碼結束，則 Log Analytics 會將容器標示為 [失敗]****。 您可以在 [失敗的容器]**** 區域中，大致了解環境中的錯誤和失敗。

### <a name="to-find-failed-containers"></a>尋找失敗的容器

1. 按一下 [容器狀態]**** 區域。  
   ![容器狀態](./media/containers/containers-status.png)
2. Log Analytics 會開啟並顯示您的容器狀態，如下所示。  
   ![容器狀態](./media/containers/containers-log-search.png)
3. 展開失敗的行，然後按一下 [+] 將其準則加入至查詢。 然後將查詢中的總結行加上批註。
   ![失敗的容器](./media/containers/containers-state-failed-select.png)  
1. 執行查詢，然後在結果中展開一行來查看影像識別碼。  
   ![失敗的容器](./media/containers/containers-state-failed.png)  
1. 在記錄查詢中輸入下列文字。 `ContainerImageInventory | where ImageID == <ImageID>`可查看關於映像的詳細資料，例如停止和失敗映像的映像大小與數目。  
   ![失敗的容器](./media/containers/containers-failed04.png)

## <a name="query-logs-for-container-data"></a>容器資料的查詢記錄

當您針對特定錯誤進行疑難排解時，這助於查看您的環境中發生此錯誤的位置。 下列記錄檔類型將協助您建立查詢，以傳回您想要的資訊。

- **ContainerImageInventory** – 當您嘗試尋找依照映像組織的資訊，以及檢視映像資訊 (例如映像識別碼或大小) 時，請使用這個類型。
- **ContainerInventory** – 當您需要有關容器位置、其名稱，以及所執行映像的資訊時，請使用這個類型。
- **ContainerLog** – 當您想要尋找特定錯誤記錄檔資訊和項目時，請使用這個類型。
- **ContainerNodeInventory_CL**  當您需要容器所在之主機/節點的相關資訊時，請使用這個類型。 它會提供 Docker 版本、協調流程類型、儲存體和網路資訊。
- **ContainerProcess_CL** 使用此類型可快速查看容器內執行的流程。
- **ContainerServiceLog** – 當您嘗試尋找 Docker 精靈的稽核追蹤資訊 (例如啟動、停止、刪除或提取命令) 時，請使用這個類型。
- **KubeEvents_CL**  使用此類型可查看 Kubernetes 事件。
- **KubePodInventory_CL**  當您需要了解叢集階層資訊時，請使用此類型。

### <a name="to-query-logs-for-container-data"></a>查詢容器資料的記錄

* 選擇您知道最近失敗的映像並尋找其錯誤記錄。 首先，透過 **ContainerInventory** 搜尋來尋找正在執行該映像的容器名稱。 例如，搜尋 `ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`  
    ![搜尋 Ubuntu 容器](./media/containers/search-ubuntu.png)

  展開結果中的任何一列，以查看該容器的詳細資料。

## <a name="example-log-queries"></a>範例記錄查詢

從一或兩個範例開始建置查詢，然後加以修改以符合您的環境，通常很實用。 做為起點，您可以試驗 [方案] 頁面最右邊的 [**範例查詢**] 區域，以協助您建立更先進的查詢。

![容器查詢](./media/containers/containers-queries.png)

## <a name="saving-log-queries"></a>儲存記錄查詢

儲存查詢是 Azure 監視器中的標準功能。 藉由儲存查詢，您可將您覺得有用的查詢放在容易取得的地方，以便日後使用。

建立您覺得有用的查詢之後，按一下 [記錄檔搜尋] 頁面頂端的 [我的最愛]**** 來儲存它。 然後您稍後即可輕易地從 [我的儀表板]**** 頁面進行存取。

## <a name="next-steps"></a>後續步驟

[查詢記錄](../log-query/log-query-overview.md)以查看詳細的容器資料記錄。
