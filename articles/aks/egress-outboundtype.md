---
title: 在 Azure 庫伯奈斯服務 (AKS) 中自訂使用者定義的路由 (UDR)
description: 瞭解如何在 Azure 庫伯奈斯服務 (AKS) 中定義自定義出口路由
services: container-service
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 30b7b6bae92221b268d40977f5b299e9b0b267b0
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637833"
---
# <a name="customize-cluster-egress-with-a-user-defined-route-preview"></a>使用使用者定義的路由自訂群集出口(預覽)

可以自定義 AKS 群集的出入口以適應特定方案。 默認情況下,AKS將預配要設置的標準 SKU 負載均衡器並用於出口。 但是,如果不允許使用公共 IP 或出口需要額外的躍點,則預設設置可能無法滿足所有方案的要求。

本文介紹如何自定義群集的出口路由以支援自定義網路方案,例如那些不允許公共 IP 並且要求群集位於網路虛擬設備 (NVA) 後面的方案。

> [!IMPORTANT]
> AKS 預覽功能是自助服務,可選擇加入。 預覽版*以現有*和*可用狀態*提供,不在服務級別協定 (SLA) 和有限保修中。 客戶支援會*盡力*提供 AKS 預覽。 因此,這些功能不適合生產用途。 有關詳細資訊,請參閱以下支援文章:
>
> * [AKS 支援原則](support-policies.md)
> * [Azure 支援常見問題集](faq.md)

## <a name="prerequisites"></a>Prerequisites
* Azure CLI 版本 2.0.81 或更高版本
* Azure CLI 預覽延伸版 0.4.28 或更高版本
* API`2020-01-01`版本或更高版本

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>安裝最新的 Azure CLI AKS 預覽延伸
要設置群集的出站類型,需要 Azure CLI AKS 預覽擴展版本 0.4.18 或更高版本。 使用 az 延伸新增指令安裝 Azure CLI AKS 預覽擴充,然後使用以下 az 延伸更新命令檢查是否有任何可用更新:

```azure-cli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="limitations"></a>限制
* 在預覽期間`outboundType`,只能在群集創建時間定義,之後無法更新。
* 在預覽期間`outboundType`,AKS 群集應使用 Azure CNI。 Kubenet 是可配置的,使用需要路由表的手錶的手錶的動關聯到 AKS 子網。
* 設置`outboundType``vm-set-type`需要具有`VirtualMachineScaleSets`和`load-balancer-sku`的`Standard`AKS 群集。
* 設置為`outboundType`的值`UDR`需要具有群集有效出站連接的使用者定義的路由。
* 設定為`outboundType`的值`UDR`意味著路由到負載均衡器的入口來源 IP 可能與叢集的傳出出口目標位址**不匹配**。

## <a name="overview-of-outbound-types-in-aks"></a>AKS 中的出站類型概述

AKS 群集可以使用`outboundType`唯 一的類型負載均衡器或使用者定義的路由進行自定義。

> [!IMPORTANT]
> 出站類型僅影響群集的出口流量。 有關詳細資訊[,請參閱設定入口控制器](ingress-basic.md)。

### <a name="outbound-type-of-loadbalancer"></a>出站式負載平衡器

如果`loadBalancer`設置,AKS 將自動完成以下設置。 負載均衡器用於通過分配的公共 IP 的 AKS 出口。 一種`loadBalancer`出站類型支援 Kubernetes`loadBalancer`類型的服務,它期望從 AKS 資源提供程式創建的負載均衡器中流出。

以下設置由 AKS 完成。
   * 為群集出口預配公共 IP 位址。
   * 公共 IP 位址分配給負載均衡器資源。
   * 為群集中的代理節點設置負載均衡器的後端池。

下面是預設情況下部署在 AKS 群集中的網路拓撲,該拓撲`outboundType``loadBalancer`使用 的 。

![出站型磅](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>出站類型的使用者定義路由

> [!NOTE]
> 使用出站類型是一種高級網路方案,需要適當的網路配置。

如果`userDefinedRouting`設置,AKS不會自動配置出口路徑。 使用者應完成以下**操作。**

叢集必須部署到具有已配置的子網的現有虛擬網路中。 具有出站連接的子網上必須存在有效的使用者定義的路由 (UDR)。

AKS 資源提供程式將部署標準負載均衡器 (SLB)。 負載均衡器未設定任何規則,[在放置規則之前不會產生費用](https://azure.microsoft.com/pricing/details/load-balancer/)。 AKS**不會**自動為 SLB 前端預配公共 IP 位址。 AKS**不會**自動配置負載均衡器後端池。

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>部署具有出站類型的 UDR 和 Azure 防火牆的群集

為了說明使用使用者定義的路由使用出站類型的群集的應用程式,可以在使用 Azure 防火牆對等的虛擬網路上配置群集。

![鎖定拓撲](media/egress-outboundtype/outboundtype-udr.png)

* 入口被迫透過防火牆過濾器
   * 隔離子網路包含內部負載均衡器,用於路由到代理節點
   * 代理節點在專用子網中隔離
* 出站要求使用使用者定義的路由從代理節點啟動到 Azure 防火牆內部 IP
   * 來自 AKS 代理節點的請求遵循已放置在 AKS 群集部署到的子網上的 UDR。
   * Azure 防火牆從公共 IP 前端退出虛擬網路
   * 對 AKS 控制平面的存取受 NSG 保護,該 NSG 啟用了防火牆前端 IP 位址
   * 存取公開 Internet 或其他 Azure 服務串流自防火牆前端 IP 位址

### <a name="set-configuration-via-environment-variables"></a>以環境變數設定設定

定義一組要用於資源創建的環境變數。

```bash
PREFIX="contosofin"
RG="${PREFIX}-rg"
LOC="eastus"
NAME="${PREFIX}outboundudr"
AKS_NAME="${PREFIX}aks"
VNET_NAME="${PREFIX}vnet"
AKSSUBNET_NAME="${PREFIX}akssubnet"
SVCSUBNET_NAME="${PREFIX}svcsubnet"
# DO NOT CHANGE FWSUBNET_NAME - This is currently a requirement for Azure Firewall.
FWSUBNET_NAME="AzureFirewallSubnet"
FWNAME="${PREFIX}fw"
FWPUBLICIP_NAME="${PREFIX}fwpublicip"
FWIPCONFIG_NAME="${PREFIX}fwconfig"
FWROUTE_TABLE_NAME="${PREFIX}fwrt"
FWROUTE_NAME="${PREFIX}fwrn"
FWROUTE_NAME_INTERNET="${PREFIX}fwinternet"
DEVSUBNET_NAME="${PREFIX}dev"
```

接下來,設置訂閱代碼。

```azure-cli
# Get ARM Access Token and Subscription ID - This will be used for AuthN later.

ACCESS_TOKEN=$(az account get-access-token -o tsv --query 'accessToken')

# NOTE: Update Subscription Name
# Set Default Azure Subscription to be Used via Subscription ID

az account set -s <SUBSCRIPTION_ID_GOES_HERE>

# NOTE: Update Subscription Name for setting SUBID

SUBID=$(az account show -s '<SUBSCRIPTION_NAME_GOES_HERE>' -o tsv --query 'id')
```

## <a name="create-a-virtual-network-with-multiple-subnets"></a>建立有多個子網路的虛擬網路

配置一個虛擬網路,其中有三個單獨的子網,一個用於群集,一個用於防火牆,一個用於服務入口。

![空網路拓撲](media/egress-outboundtype/empty-network.png)

建立一個資源組來保存所有資源。

```azure-cli
# Create Resource Group

az group create --name $RG --location $LOC
```

創建兩個虛擬網路來承載 AKS 群集和 Azure 防火牆。 每個都將有自己的子網。 讓我們從 AKS 網路開始。

```
# Dedicated virtual network with AKS subnet

az network vnet create \
    --resource-group $RG \
    --name $VNET_NAME \
    --address-prefixes 100.64.0.0/16 \
    --subnet-name $AKSSUBNET_NAME \
    --subnet-prefix 100.64.1.0/24

# Dedicated subnet for K8s services

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $SVCSUBNET_NAME \
    --address-prefix 100.64.2.0/24

# Dedicated subnet for Azure Firewall (Firewall name cannot be changed)

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $FWSUBNET_NAME \
    --address-prefix 100.64.3.0/24
```

## <a name="create-and-setup-an-azure-firewall-with-a-udr"></a>使用 UDR 建立與設定 Azure 防火牆

必須配置 Azure 防火牆入站和出站規則。 防火牆的主要目的是使組織能夠設置精細的入口和出口流量規則進出 AKS 群集。

![防火牆和 UDR](media/egress-outboundtype/firewall-udr.png)

創建標準 SKU 公共 IP 資源,這些資源將用作 Azure 防火牆前端位址。

```azure-cli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

註冊預覽 cli 擴展以創建 Azure 防火牆。
```azure-cli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC
```

之前創建的 IP 位址現在可以分配給防火牆前端。
> [!NOTE]
> 將公共 IP 位址設置為 Azure 防火牆可能需要幾分鐘時間。
> 
> 如果以下命令反覆收到錯誤,請刪除現有防火牆和公共 IP,並同時通過門戶預配公共 IP 和 Azure 防火牆。

```azure-cli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

當前一個命令成功時,保存防火牆前端 IP 位址以供以後配置。

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>使用 Azure 防火牆的躍點建立 UDR

Azure 會自動路由傳送 Azure 子網路、虛擬網路及內部部署網路之間的流量。 如果您想要變更任何 Azure 的預設路由，您可藉由建立路由表來執行此動作。

創建要與給定子網關聯的空路由表。 路由表將下一個躍點定義為上面創建的 Azure 防火牆。 每個子網路可以有零個或一個與其相關聯的路由表。

```azure-cli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

有關如何覆寫 Azure 的預設系統路由或向子網路的路由表添加其他路由的[虛擬網路路由表文檔](../virtual-network/virtual-networks-udr-overview.md#user-defined)。

## <a name="adding-network-firewall-rules"></a>新增網路防火牆規則

> [!WARNING]
> 下面顯示了添加防火牆規則的一個示例。 必須透過應用程式防火牆規則啟用[在所需出口終結點](egress.md)中定義的所有出口終結點,以便 AKS 群集正常運行。 如果啟用這些終結點,群集將無法運行。

下面是網路和應用程式規則的範例。 我們添加一個網路規則,允許任何協定、源位址、目標地址和目標埠。 我們還為 AKS 所需的**某些**終結點添加應用程式規則。

在生產方案中,應僅啟用對應用程式所需的終結點和[在 AKS 中定義的終結點的訪問。](egress.md)

```
# Add Network FW Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'netrules' --protocols 'Any' --source-addresses '*' --destination-addresses '*' --destination-ports '*' --action allow --priority 100

# Add Application FW Rules
# IMPORTANT: Add AKS required egress endpoints

az network firewall application-rule create -g $RG -f $FWNAME \
    --collection-name 'AKS_Global_Required' \
    --action allow \
    --priority 100 \
    -n 'required' \
    --source-addresses '*' \
    --protocols 'http=80' 'https=443' \
    --target-fqdns \
        'aksrepos.azurecr.io' \
        '*blob.core.windows.net' \
        'mcr.microsoft.com' \
        '*cdn.mscr.io' \
        '*.data.mcr.microsoft.com' \
        'management.azure.com' \
        'login.microsoftonline.com' \
        'ntp.ubuntu.com' \
        'packages.microsoft.com' \
        'acs-mirror.azureedge.net'
```

請參閱[Azure 防火牆文檔](https://docs.microsoft.com/azure/firewall/overview)以瞭解有關 Azure 防火牆服務的資訊。

## <a name="associate-the-route-table-to-aks"></a>將路由表關聯到 AKS

要將群集與防火牆關聯,群集子網的專用子網必須引用上面創建的路由表。 可以通過向同時持有群集和防火牆的虛擬網路發出命令來更新群集子網的路由表,從而完成關聯。

```azure-cli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

## <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>將具有出站型 UDR 的 AKS 部署到現有網路

現在,AKS 群集可以部署到現有的虛擬網路設置中。 為了將群集出站類型設置為使用者定義的路由,必須向 AKS 提供現有子網。

![aks 部署](media/egress-outboundtype/outboundtype-udr.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>建立具有在現有虛擬網路內進行預配的服務主體

AKS 使用服務主體創建群集資源。 在創建時傳遞的服務主體用於創建基礎 AKS 資源,如 AKS 使用的 VM、存儲和負載均衡器。 如果授予的許可權太少,它將無法預配 AKS 群集。

```azure-cli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

現在,將`APPID``PASSWORD`和下替換為服務主體應用id和服務主體密碼,這些密碼由以前的命令輸出自動生成。 我們將引用 VNET 資源 ID 向服務主體授予許可權,以便 AKS 可以將資源部署到其中。

```azure-cli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role Contributor

# View Role Assignment
az role assignment list --assignee $APPID --all -o table
```

### <a name="deploy-aks"></a>部署 AKS

最後,AKS 群集可以部署到我們專用於群集的現有子網中。 要部署到的目標子網使用環境變數`$SUBNETID`定義。 在前面的步驟中,我們沒有`$SUBNETID`定義變數。 要設定子網 ID 的值,可以使用以下指令:

```azurecli
SUBNETID="/subscriptions/$SUBID/resourceGroups/$RG/providers/Microsoft.Network/virtualNetworks/$VNET_NAME/subnets/$AKSSUBNET_NAME"
```

我們將定義出站類型,以遵循子網上存在的 UDR,使 AKS 能夠跳過負載均衡器的設置和 IP 配置,現在可以嚴格內部設置和 IP 配置。

可以添加 API[伺服器授權的 IP 範圍](api-server-authorized-ip-ranges.md)的 AKS 功能,以將 API 伺服器存取限制為防火牆的公共終結點。 授權 IP 範圍功能在關係圖中表示為 NSG,必須傳遞以訪問控制平面。 啟用授權 IP 範圍功能以限制 API 伺服器存取時,開發人員工具必須使用防火牆虛擬網路中的跳轉框,否則必須將所有開發人員終結點添加到授權的 IP 範圍。

> [!TIP]
> 可以將其他功能添加到群集部署,例如(專用群集)*。 使用授權 IP 範圍時,群集網路內部需要一個跳轉框才能訪問 API 伺服器。

```azure-cli
az aks create -g $RG -n $AKS_NAME -l $LOC \
  --node-count 3 \
  --network-plugin azure --generate-ssh-keys \
  --service-cidr 192.168.0.0/16 \
  --dns-service-ip 192.168.0.10 \
  --docker-bridge-address 172.22.0.1/29 \
  --vnet-subnet-id $SUBNETID \
  --service-principal $APPID \
  --client-secret $PASSWORD \
  --load-balancer-sku standard \
  --outbound-type userDefinedRouting \
  --api-server-authorized-ip-ranges $FWPUBLIC_IP
  ```

### <a name="enable-developer-access-to-the-api-server"></a>開啟開發人員對 API 伺服器的存取

由於群集的 IP 範圍設定,您必須將開發人員工具 IP 位址添加到已批准 IP 範圍的 AKS 叢集清單中才能存取 API 伺服器。 另一個選項是在防火牆虛擬網路中的單獨子網內配置具有所需工具的跳轉框。

使用以下指令將另一個 IP 位址到已批准的範圍

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKS_NAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 使用 [az aks 獲取認證》 [az-aks 取得`kubectl`認證) 命令 進行配置以連接到新建立的 Kubernetes 群集。 

 ```azure-cli
 az aks get-credentials -g $RG -n $AKS_NAME
 ```

### <a name="setup-the-internal-load-balancer"></a>設定內部負載均衡器

AKS 已部署了一個負載均衡器與群集,可以設置為[內部負載均衡器](internal-lb.md)。

要建立內部負載均衡器,請使用服務類型 LoadBalancer 和 azure 負載均衡器內部註釋建立名為內部-lb.yaml 的服務清單,如以下範例所示:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "contosofinsvcsubnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

使用庫布ectl應用部署內部負載均衡器並指定 YAML 清單的名稱:

```bash
kubectl apply -f internal-lb.yaml
```

## <a name="deploy-a-kubernetes-service"></a>部署庫伯奈斯服務

由於群集出站類型設置為 UDR,因此 AKS 在群集創建時不會自動完成將代理節點關聯為負載均衡器的後端池。 但是,在部署庫伯奈斯服務時,後端池關聯由庫伯內斯 Azure 雲提供程式處理。

通過將下面的 yaml 複製`example.yaml`到名為的檔來部署 Azure 投票應用應用程式。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "contosofinsvcsubnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

透過執行服務部署服務:

```bash
kubectl apply -f example.yaml
```

## <a name="add-a-dnat-rule-to-azure-firewall"></a>向 Azure 防火牆添加 DNAT 規則

要配置入站連接,必須將 DNAT 規則寫入 Azure 防火牆。 為了測試與群集的連接,為防火牆前端公共 IP 位址定義了一個規則,用於路由到內部服務公開的內部 IP。

可以自定義目標位址,因為它是要訪問的防火牆上的埠。 翻譯的位址必須是內部負載均衡器的 IP 位址。 翻譯後的埠必須是庫伯內斯服務的外露埠。

您需要指定分配給 Kubernetes 服務創建的負載均衡器的內部 IP 位址。 通過執行來檢索位址:

```bash
kubectl get services
```

所需的 IP 位址將列在「外部 IP」欄中,如下所示。

```bash
NAME               TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
azure-vote-back    ClusterIP      192.168.92.209   <none>        6379/TCP       23m
azure-vote-front   LoadBalancer   192.168.19.183   100.64.2.5    80:32106/TCP   23m
kubernetes         ClusterIP      192.168.0.1      <none>        443/TCP        4d3h
```

```azure-cli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address <INSERT IP OF K8s SERVICE>
```

## <a name="clean-up-resources"></a>清除資源

> [!NOTE]
> 刪除 Kubernetes 內部服務時,如果內部負載均衡器不再被任何服務使用,Azure 雲提供程式將刪除內部負載均衡器。 在下一個服務部署中,如果找不到請求的配置,將部署負載均衡器。

要清理 Azure 資源,請刪除 AKS 資源組。

```azure-cli
az group delete -g $RG
```

## <a name="validate-connectivity"></a>驗證連線能力

在瀏覽器中導航到 Azure 防火牆前端 IP 位址以驗證連接。

您應該會看到 Azure 投票應用的圖像。

## <a name="next-steps"></a>後續步驟

請參考[Azure 網路 UDR 概述](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)。

[瞭解如何建立、變更或刪除路由表](https://docs.microsoft.com/azure/virtual-network/manage-route-table)格 。
