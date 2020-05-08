---
title: 在 Azure Kubernetes Service 中自訂使用者定義的路由（UDR）（AKS）
description: 瞭解如何在 Azure Kubernetes Service 中定義自訂輸出路由（AKS）
services: container-service
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: e7dbde4095fb635180bb1ba663734f8dbfd602f7
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2020
ms.locfileid: "82733493"
---
# <a name="customize-cluster-egress-with-a-user-defined-route-preview"></a>使用使用者定義的路由自訂叢集輸出（預覽）

來自 AKS 叢集的輸出可自訂以符合特定案例。 根據預設，AKS 會布建標準 SKU Load Balancer 設定並用於輸出。 不過，如果不允許公用 Ip 或輸出需要額外的躍點，則預設設定可能不符合所有案例的需求。

本文逐步解說如何自訂叢集的輸出路由，以支援自訂的網路案例，例如不允許公用 Ip，而且需要叢集位於網路虛擬裝置（NVA）的情況。

> [!IMPORTANT]
> AKS 預覽功能是自助服務，並以選擇為基礎提供。 預覽會依*原樣提供，並會*在服務等級協定（SLA *）和有限*擔保中排除。 AKS 預覽會以*最*大的方式由客戶支援部門部分涵蓋。 因此，這些功能不適用於生產用途。 如需詳細資訊，請參閱下列支援文章：
>
> * [AKS 支援原則](support-policies.md)
> * [Azure 支援常見問題集](faq.md)

## <a name="prerequisites"></a>Prerequisites
* Azure CLI 2.0.81 或更高版本
* Azure CLI 的預覽延伸模組版本0.4.28 或更高
* 或更高`2020-01-01`的 API 版本

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>安裝最新的 Azure CLI AKS Preview 擴充功能
若要設定叢集的輸出類型，您需要 Azure CLI AKS Preview 延伸模組版本0.4.18 或更新版本。 使用 az extension add 命令來安裝 Azure CLI AKS Preview 延伸模組，然後使用下列 az extension update 命令來檢查是否有任何可用的更新：

```azure-cli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="limitations"></a>限制
* 在預覽期間`outboundType` ，只能在叢集建立時定義，而且之後無法更新。
* 在預覽期間`outboundType` ，AKS 叢集應該使用 Azure CNI。 Kubenet 是可設定的，使用方式必須手動將路由表關聯至 AKS 子網。
* 設定`outboundType`需要具有`vm-set-type`之`VirtualMachineScaleSets`和`load-balancer-sku`的 AKS 叢集。 `Standard`
* 若`outboundType`將設定為的`UDR`值，則需要具有叢集有效輸出連線能力的使用者定義路由。
* 將`outboundType`設定為的`UDR`值，表示路由至負載平衡器的輸入來源 IP 可能**不符合**叢集的傳出輸出目的地位址。

## <a name="overview-of-outbound-types-in-aks"></a>AKS 中的輸出類型總覽

您可以使用唯一`outboundType`的負載平衡器或使用者定義路由類型來自訂 AKS 叢集。

> [!IMPORTANT]
> 輸出類型只會影響叢集的傳出流量。 如需詳細資訊，請參閱[設定輸入控制器](ingress-basic.md)。

### <a name="outbound-type-of-loadbalancer"></a>LoadBalancer 的輸出類型

如果`loadBalancer`設定，AKS 會自動完成下列安裝程式。 負載平衡器可透過 AKS 指派的公用 IP 用於輸出。 的`loadBalancer`輸出類型支援類型`loadBalancer`的 Kubernetes 服務，這預期會傳出 AKS 資源提供者所建立的負載平衡器。

下列設定是由 AKS 完成。
   * 公用 IP 位址是針對叢集輸出而布建。
   * 公用 IP 位址會指派給負載平衡器資源。
   * 負載平衡器的後端集區會針對叢集中的代理程式節點進行設定。

以下是預設部署在 AKS 叢集中的網路拓撲，其使用`outboundType`的。 `loadBalancer`

![outboundtype-lb](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>UserDefinedRouting 的輸出類型

> [!NOTE]
> 使用輸出類型是一個先進的網路案例，而且需要適當的網路設定。

如果`userDefinedRouting`設定，AKS 將不會自動設定輸出路徑。 **使用者**應該會完成下列作業。

AKS 叢集必須部署到已設定子網的現有虛擬網路中。 使用標準負載平衡器（SLB）架構時，您必須建立明確的輸出。 這需要將輸出要求傳送至應用裝置，例如防火牆、閘道、內部內部部署，或允許透過指派給標準負載平衡器或給定節點的公用 IP 來完成輸出。

AKS 資源提供者會部署標準負載平衡器（SLB）。 負載平衡器不會設定任何規則，而且在[放置規則之前](https://azure.microsoft.com/pricing/details/load-balancer/)，不會產生任何費用。 AKS**不**會自動布建 SLB 前端的公用 IP 位址。 AKS**不**會自動設定負載平衡器後端集區。

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>部署具有 UDR 和 Azure 防火牆輸出類型的叢集

為了說明使用使用者定義路由的輸出類型來應用叢集，可以在具有 Azure 防火牆的虛擬網路對等互連上設定叢集。

![已鎖定拓撲](media/egress-outboundtype/outboundtype-udr.png)

* 輸入會強制流經防火牆篩選器
   * 隔離的子網保存內部負載平衡器，用於路由至代理程式節點
   * 專用子網中的代理程式節點已隔離
* 輸出要求會使用使用者定義的路由，從代理程式節點開始到 Azure 防火牆內部 IP
   * 來自 AKS 代理程式節點的要求會遵循已放在已部署 AKS 叢集之子網上的 UDR。
   * Azure 防火牆從公用 IP 前端的虛擬網路 egresses
   * AKS 控制平面的存取權受到已啟用防火牆前端 IP 位址的 NSG 保護
   * 存取公用網際網路或其他 Azure 服務會流入和流出防火牆前端 IP 位址

### <a name="set-configuration-via-environment-variables"></a>透過環境變數進行設定

定義一組要在資源建立中使用的環境變數。

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

接下來，設定訂用帳戶識別碼。

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

布建具有三個個別子網的虛擬網路，一個用於叢集，一個用於防火牆，另一個用於服務輸入。

![空白網路拓撲](media/egress-outboundtype/empty-network.png)

建立資源群組來保存所有資源。

```azure-cli
# Create Resource Group

az group create --name $RG --location $LOC
```

建立兩個虛擬網路來裝載 AKS 叢集和 Azure 防火牆。 每個都有自己的子網。 讓我們從 AKS 網路開始。

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

## <a name="create-and-setup-an-azure-firewall-with-a-udr"></a>使用 UDR 建立及設定 Azure 防火牆

必須設定 Azure 防火牆的輸入和輸出規則。 防火牆的主要目的是讓組織能夠將細微的輸入和輸出流量規則，設定到 AKS 叢集。

![防火牆和 UDR](media/egress-outboundtype/firewall-udr.png)

建立將做為 Azure 防火牆前端位址的標準 SKU 公用 IP 資源。

```azure-cli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

註冊 preview cli-擴充功能以建立 Azure 防火牆。
```azure-cli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC
```

先前建立的 IP 位址現在可以指派給防火牆前端。
> [!NOTE]
> 將公用 IP 位址設定為 Azure 防火牆可能需要幾分鐘的時間。
> 
> 如果在下列命令中重複收到錯誤，請刪除現有的防火牆和公用 IP，並同時透過入口網站布建公用 IP 和 Azure 防火牆。

```azure-cli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

當上一個命令成功時，請儲存防火牆前端 IP 位址以供稍後設定。

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>建立具有躍點至 Azure 防火牆的 UDR

Azure 會自動路由傳送 Azure 子網路、虛擬網路及內部部署網路之間的流量。 如果您想要變更任何 Azure 的預設路由，您可藉由建立路由表來執行此動作。

建立要與指定的子網相關聯的空路由表。 路由表會將下一個躍點定義為上述建立的 Azure 防火牆。 每個子網路可以有零個或一個與其相關聯的路由表。

```azure-cli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

請參閱[虛擬網路路由表檔](../virtual-network/virtual-networks-udr-overview.md#user-defined)，以瞭解如何覆寫 Azure 的預設系統路由，或將其他路由新增至子網的路由表。

## <a name="adding-network-firewall-rules"></a>新增網路防火牆規則

> [!WARNING]
> 以下顯示新增防火牆規則的其中一個範例。 AKS 叢集要運作的應用程式防火牆規則必須啟用[必要輸出端點中所](egress.md)定義的所有輸出端點。 若未啟用這些端點，您的叢集將無法運作。

以下是網路和應用程式規則的範例。 我們新增了允許任何通訊協定、來源位址、目的地位址和目的地埠的網路規則。 我們也會為 AKS 所需的**某些**端點新增應用程式規則。

在生產案例中，您應該只啟用應用程式所需端點的存取權，以及[AKS 必要](egress.md)輸出中所定義的端點。

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

若要深入瞭解 Azure 防火牆服務，請參閱[Azure 防火牆檔](https://docs.microsoft.com/azure/firewall/overview)。

## <a name="associate-the-route-table-to-aks"></a>將路由表與 AKS 建立關聯

若要將叢集與防火牆建立關聯，叢集子網的專用子網必須參考上面建立的路由表。 藉由發出命令給持有叢集和防火牆的虛擬網路，以更新叢集子網的路由表，即可完成關聯。

```azure-cli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

## <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>將具有 UDR 輸出類型的 AKS 部署到現有的網路

現在，您可以將 AKS 叢集部署到現有的虛擬網路設定。 為了將叢集輸出類型設定為使用者定義的路由，必須提供現有的子網給 AKS。

![aks-部署](media/egress-outboundtype/outboundtype-udr.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>建立可存取現有虛擬網路內布建的服務主體

AKS 會使用服務主體來建立叢集資源。 在建立時所傳遞的服務主體會用來建立基礎 AKS 資源，例如 AKS 所使用的 Vm、儲存體和負載平衡器。 如果授與的許可權太少，就無法布建 AKS 叢集。

```azure-cli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

現在將`APPID`和`PASSWORD`取代為先前命令輸出自動產生的服務主體 appid 和服務主體密碼。 我們會參考 VNET 資源識別碼，以授與許可權給服務主體，讓 AKS 可以將資源部署到其中。

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

最後，AKS 叢集可以部署到我們專用於叢集的現有子網中。 要部署到的目標子網是以環境變數定義`$SUBNETID`。 我們並未在先前`$SUBNETID`的步驟中定義變數。 若要設定子網識別碼的值，您可以使用下列命令：

```azurecli
SUBNETID="/subscriptions/$SUBID/resourceGroups/$RG/providers/Microsoft.Network/virtualNetworks/$VNET_NAME/subnets/$AKSSUBNET_NAME"
```

我們會定義輸出類型，使其遵循子網上的 UDR，讓 AKS 略過負載平衡器的設定和 IP 布建，這現在可以完全為內部。

您可以新增[api 伺服器授權 IP 範圍](api-server-authorized-ip-ranges.md)的 AKS 功能，將 api 伺服器存取限制為僅限防火牆的公用端點。 [授權的 IP 範圍] 功能在圖表中表示為必須傳遞以存取控制平面的 NSG。 啟用授權的 IP 範圍功能以限制 API 伺服器存取時，您的開發人員工具必須使用來自防火牆之虛擬網路的 jumpbox，或者您必須將所有開發人員端點新增至授權的 IP 範圍。

> [!TIP]
> 您可以將其他功能新增至叢集部署，例如（私用叢集） []。 使用授權的 IP 範圍時，叢集網路內必須有 jumpbox，才能存取 API 伺服器。

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

### <a name="enable-developer-access-to-the-api-server"></a>讓開發人員存取 API 伺服器

由於叢集已授權 IP 範圍的設定，您必須將開發人員工具的 IP 位址新增至已核准 IP 範圍的 AKS 叢集清單，才能存取 API 伺服器。 另一個選項是在防火牆的虛擬網路中的個別子網內，使用所需的工具來設定 jumpbox。

使用下列命令，將另一個 IP 位址新增至核准的範圍

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKS_NAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 使用[az aks get-認證][az-aks-get-credentials]命令來設定`kubectl` ，以連線到新建立的 Kubernetes 叢集。 

 ```azure-cli
 az aks get-credentials -g $RG -n $AKS_NAME
 ```

### <a name="setup-the-internal-load-balancer"></a>設定內部負載平衡器

AKS 已部署具有叢集的負載平衡器，可設定為[內部負載平衡器](internal-lb.md)。

若要建立內部負載平衡器，請建立名為 yaml 的服務資訊清單，並使用服務類型 LoadBalancer 和 azure-負載平衡器-內部注釋，如下列範例所示：

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

使用 kubectl apply 部署內部負載平衡器，並指定 YAML 資訊清單的名稱：

```bash
kubectl apply -f internal-lb.yaml
```

## <a name="deploy-a-kubernetes-service"></a>部署 Kubernetes 服務

因為叢集輸出類型設定為 UDR，所以將代理程式節點與負載平衡器的後端集區產生關聯，在叢集建立時，AKS 不會自動完成。 不過，部署 Kubernetes 服務時，Kubernetes Azure 雲端提供者會處理後端集區關聯。

將下列 yaml 複製到名為`example.yaml`的檔案，以部署 Azure 投票應用程式應用程式。

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

執行下列程式來部署服務：

```bash
kubectl apply -f example.yaml
```

## <a name="add-a-dnat-rule-to-azure-firewall"></a>將 DNAT 規則新增至 Azure 防火牆

若要設定輸入連線能力，必須將 DNAT 規則寫入 Azure 防火牆。 若要測試與叢集的連線，防火牆前端公用 IP 位址會定義規則，以路由傳送至內部服務所公開的內部 IP。

目的地位址可以自訂，因為它是防火牆上要存取的埠。 轉譯的位址必須是內部負載平衡器的 IP 位址。 已轉譯的埠必須是 Kubernetes 服務的公開端口。

您將需要指定指派給 Kubernetes 服務所建立之負載平衡器的內部 IP 位址。 執行下列程式來抓取位址：

```bash
kubectl get services
```

所需的 IP 位址會列在 [外部 IP] 資料行中，如下所示。

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
> 刪除 Kubernetes 內部服務時，如果任何服務已不再使用內部負載平衡器，Azure 雲端提供者將會刪除內部負載平衡器。 在下一個服務部署中，如果找不到所要求的設定，便會部署負載平衡器。

若要清除 Azure 資源，請刪除 AKS 資源群組。

```azure-cli
az group delete -g $RG
```

## <a name="validate-connectivity"></a>驗證連線能力

在瀏覽器中流覽至 Azure 防火牆前端 IP 位址，以驗證連線能力。

您應該會看到 Azure 投票應用程式的影像。

## <a name="next-steps"></a>後續步驟

請參閱[Azure 網路 UDR 總覽](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)。

請參閱[如何建立、變更或刪除路由表](https://docs.microsoft.com/azure/virtual-network/manage-route-table)。

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
