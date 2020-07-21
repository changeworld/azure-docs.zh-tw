---
title: 在 Azure 中部署 OpenShift 容器平臺3.11
description: 在 Azure 中部署 OpenShift 容器平臺3.11。
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 04/05/2020
ms.author: haroldw
ms.openlocfilehash: bc30275b2ee24af7bb526b3b43618c706bc027ca
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86502090"
---
# <a name="deploy-openshift-container-platform-311-in-azure"></a>在 Azure 中部署 OpenShift 容器平臺3.11

您可以使用下列其中一種方法，在 Azure 中部署 OpenShift 容器平臺3.11：

- 您可以手動部署必要的 Azure 基礎結構元件，然後依照[OpenShift 容器平臺檔](https://docs.openshift.com/container-platform)進行。
- 您也可以使用能夠簡化 OpenShift 容器平台叢集部署的現有 [Resource Manager 範本](https://github.com/Microsoft/openshift-container-platform/) \(英文\)。
- 另一個選項是使用[Azure Marketplace 的供應](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy)專案。

對於所有選項，都需要有 Red Hat 訂用帳戶。 在部署期間，Red Hat Enterprise Linux 執行個體會向 Red Hat 訂用帳戶註冊，並且附加至包含 OpenShift 容器平台之權利的集區識別碼。
請確定您具備有效的 Red Hat Subscription Manager (RHSM) 使用者名稱、密碼和集區識別碼。 您可以使用啟用金鑰、組織識別碼和集區識別碼。 您可以登入 https://access.redhat.com 來驗證這項資訊。


## <a name="deploy-using-the-openshift-container-platform-resource-manager-311-template"></a>使用 OpenShift 容器平臺 Resource Manager 3.11 範本進行部署

### <a name="private-clusters"></a>私人叢集

部署私人 OpenShift 叢集所需的不只是與主要負載平衡器（web 主控台）或基礎負載平衡器（路由器）相關聯的公用 IP。  私人叢集通常會使用自訂的 DNS 伺服器（而非預設 Azure DNS）、自訂功能變數名稱（例如 contoso.com）和預先定義的虛擬網路。  針對私人叢集，您必須事先設定具有所有適當子網和 DNS 伺服器設定的虛擬網路。  然後使用**existingMasterSubnetReference**、 **existingInfraSubnetReference**、 **existingCnsSubnetReference**和**existingNodeSubnetReference** ，指定要供叢集使用的現有子網。

如果已選取 [私人主機] （**masterClusterType**= 私用），就必須指定**masterPrivateClusterIp**的靜態私人 IP。  此 IP 會指派給主要負載平衡器的前端。  此 IP 必須位於主要子網的 CIDR 內，而非使用中。  **masterClusterDnsType**必須設定為 "custom"，且必須為**MASTERCLUSTERDNS**提供主要 DNS 名稱。  DNS 名稱必須對應至靜態私人 IP，並會用來存取主要節點上的主控台。

如果已選取 [私人路由器] （**routerClusterType**= 私用），就必須指定**routerPrivateClusterIp**的靜態私人 IP。  此 IP 會指派給基礎負載平衡器的前端。  IP 必須位於基礎網的 CIDR 內，而非使用中。  **routingSubDomainType**必須設定為 "custom"，且必須為**routingSubDomain**提供路由的萬用字元 DNS 名稱。  

如果選取 [私人主機] 和 [私人路由器]，則也必須為**domainName**輸入自訂功能變數名稱

部署成功之後，防禦節點是唯一具有公用 IP 的節點，您可以透過 ssh 連線。  即使主要節點已設定為公用存取，它們也不會針對 ssh 存取公開。

若要使用 Resource Manager 範本進行部署，請使用參數檔案來提供輸入參數。 若要進一步自訂部署，請分支處理 GitHub 存放庫並變更適當的項目。

某些常見的自訂選項包括但不限於：

- 防禦 VM 大小 (azuredeploy.json 中的變數)
- 命名慣例 (azuredeploy.json 中的變數)
- OpenShift 叢集詳細規格，已透過主機檔案 (deployOpenShift.sh) 修改

### <a name="configure-the-parameters-file"></a>設定參數檔案

[OpenShift 容器平台範本](https://github.com/Microsoft/openshift-container-platform)有多個分支可用於不同版本的 OpenShift 容器平台。  根據您的需求，您可以直接從存放庫部署或可以分支處理存放庫，並且在部署前，先對範本或指令碼進行自訂變更。

使用您稍早針對 `aadClientId` 參數所建立之服務主體中的 `appId` 值。

下列範例顯示名為 azuredeploy.parameters.json，且具有所有必要輸入的參數檔案。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "_artifactsLocation": {
            "value": "https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master"
        },
        "location": {
            "value": "eastus"
        },
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_D4s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_D4s_v3"
        },
        "cnsVmSize": {
            "value": "Standard_E4s_v3"
        },
        "osImageType": {
            "value": "defaultgallery"
        },
        "marketplaceOsImage": {
            "value": {
                "publisher": "RedHat",
                "offer": "RHEL",
                "sku": "7-RAW",
                "version": "latest"
            }
        },
        "storageKind": {
            "value": "changeme"
        },
        "openshiftClusterPrefix": {
            "value": "changeme"
        },
        "minorVersion": {
            "value": "69"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 3
        },
        "nodeInstanceCount": {
            "value": 3
        },
        "cnsInstanceCount": {
            "value": 3
        },
        "osDiskSize": {
            "value": 64
        },
        "dataDiskSize": {
            "value": 64
        },
        "cnsGlusterDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "changeme"
        },
        "enableMetrics": {
            "value": "false"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "changeme"
        },
        "rhsmPoolId": {
            "value": "changeme"
        },
        "rhsmBrokerPoolId": {
            "value": "changeme"
        },
        "sshPublicKey": {
            "value": "GEN-SSH-PUB-KEY"
        },
        "keyVaultSubscriptionId": {
            "value": "255a325e-8276-4ada-af8f-33af5658eb34"
        },
        "keyVaultResourceGroup": {
            "value": "changeme"
        },
        "keyVaultName": {
            "value": "changeme"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "changeme"
        },
        "domainName": {
            "value": "contoso.com"
        },
        "masterClusterDnsType": {
            "value": "default"
        },
        "masterClusterDns": {
            "value": "console.contoso.com"
        },
        "routingSubDomainType": {
            "value": "nipio"
        },
        "routingSubDomain": {
            "value": "apps.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "changeme"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "changeme"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "changeme"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "changeme"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/infrasubnet"
        },
        "existingCnsSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/cnssubnet"
        },
        "existingNodeSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/nodesubnet"
        },
        "masterClusterType": {
            "value": "public"
        },
        "masterPrivateClusterIp": {
            "value": "10.1.0.200"
        },
        "routerClusterType": {
            "value": "public"
        },
        "routerPrivateClusterIp": {
            "value": "10.2.0.200"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        }
    }
}
```

以您的特定資訊取代參數。

不同版本可能有不同的參數，因此請確認您所用分支的必要參數。

### <a name="azuredeployparametersjson-file-explained"></a>檔案上的 azuredeploy.Parameters.js說明

| 屬性 | 描述 | 有效選項 | 預設值 |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | 成品的 URL （json、腳本等） |  |  HTTPs： \/ /raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | 要部署資源的 Azure 區域 |  |  |
| `masterVmSize` | 主要 VM 的大小。 從檔案 azuredeploy.js列出的其中一個允許的 VM 大小中選取 |  | Standard_E2s_v3 |
| `infraVmSize` | 基礎 VM 的大小。 從檔案 azuredeploy.js列出的其中一個允許的 VM 大小中選取 |  | Standard_D4s_v3 |
| `nodeVmSize` | 應用程式節點 VM 的大小。 從檔案 azuredeploy.js列出的其中一個允許的 VM 大小中選取 |  | Standard_D4s_v3 |
| `cnsVmSize` | 容器原生儲存體（CN）節點 VM 的大小。 從檔案 azuredeploy.js列出的其中一個允許的 VM 大小中選取 |  | Standard_E4s_v3 |
| `osImageType` | 要使用的 RHEL 映射。 defaultgallery：隨選;marketplace：協力廠商映射 | defaultgallery <br> Marketplace | defaultgallery |
| `marketplaceOsImage` | 如果 `osImageType` 是 marketplace，則輸入 marketplace 供應專案的「發行者」、「供應專案」、「sku」、「版本」的適當值。 這個參數是物件類型 |  |  |
| `storageKind` | 要使用的儲存體類型  | 受控<br> Unmanaged | 受控 |
| `openshiftClusterPrefix` | 用來為所有節點設定主機名稱的叢集首碼。  介於1到20個字元之間 |  | mycluster |
| `minoVersion` | 要部署的 OpenShift 容器平臺3.11 次要版本 |  | 69 |
| `masterInstanceCount` | 要部署的主機節點數目 | 1，3，5 | 3 |
| `infraInstanceCount` | 要部署的基礎節點數目 | 1, 2, 3 | 3 |
| `nodeInstanceCount` | 要部署的節點數目 | 1、2、3、4、5、6、7、8、9、10、11、12、13、14、15、16、17、18、19、20、21、22、23、24、25、26、27、28、29、30 | 2 |
| `cnsInstanceCount` | 要部署的 CN 節點數目 | 3、4 | 3 |
| `osDiskSize` | VM 的 OS 磁片大小（以 GB 為單位） | 64、128、256、512、1024、2048 | 64 |
| `dataDiskSize` | 要附加至 Docker 磁片區節點的資料磁片大小（以 GB 為單位） | 32、64、128、256、512、1024、2048 | 64 |
| `cnsGlusterDiskSize` | 要附加至 CN 節點以供 glusterfs 使用的資料磁片大小（以 GB 為單位） | 32、64、128、256、512、1024、2048 | 128 |
| `adminUsername` | OS （VM）登入和初始 OpenShift 使用者的系統管理員使用者名稱 |  | ocpadmin |
| `enableMetrics` | 啟用計量。 計量需要更多資源，因此請為基礎 VM 選取適當的大小 | true <br> false | false |
| `enableLogging` | 啟用記錄。 elasticsearch pod 需要 8 GB 的 RAM，因此請為基礎 VM 選取適當的大小 | true <br> false | false |
| `enableCNS` | 啟用容器原生儲存體 | true <br> false | false |
| `rhsmUsernameOrOrgId` | Red Hat 訂用帳戶管理員使用者名稱或組織識別碼 |  |  |
| `rhsmPoolId` | 包含計算節點 OpenShift 權利的 Red Hat 訂用帳戶管理員集區識別碼 |  |  |
| `rhsmBrokerPoolId` | Red Hat 訂用帳戶管理員集區識別碼，其中包含主機和基礎節點的 OpenShift 權利。 如果您沒有不同的集區識別碼，請輸入與 ' rhsmPoolId ' 相同的集區識別碼 |  |
| `sshPublicKey` | 在此複製您的 SSH 公開金鑰 |  |  |
| `keyVaultSubscriptionId` | 包含 Key Vault 之訂用帳戶的訂用帳戶識別碼 |  |  |
| `keyVaultResourceGroup` | 包含 Key Vault 的資源組名 |  |  |
| `keyVaultName` | 您所建立 Key Vault 的名稱 |  |  |
| `enableAzure` | 啟用 Azure 雲端提供者 | true <br> false | true |
| `aadClientId` | Azure Active Directory 用戶端識別碼，也稱為服務主體的應用程式識別碼 |  |  |
| `domainName` | 要使用的自訂功能變數名稱名稱（如果適用的話）。 如果未部署完整的私用叢集，則設定為 "none" |  | 無 |
| `masterClusterDnsType` | OpenShift web 主控台的網欄位型別。 「預設」會使用主要基礎公用 IP 的 DNS 標籤。 「自訂」可讓您定義自己的名稱 | 預設 <br> 自訂 | 預設 |
| `masterClusterDns` | 要用來存取 OpenShift web 主控台的自訂 DNS 名稱（如果您已選取 [自訂]）`masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | 如果設定為 ' nipio '， `routingSubDomain` 將會使用 nip.io。  如果您有想要用於路由的自己網域，請使用 [自訂] | nipio <br> 自訂 | nipio |
| `routingSubDomain` | 您想要用於路由的萬用字元 DNS 名稱（如果您已選取 [自訂]）`routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | 選取要使用現有的虛擬網路還是建立新的虛擬網路 | 已 <br> new | new |
| `virtualNetworkResourceGroupName` | 新虛擬網路的資源組名（如果您已選取 [新增]）`virtualNetworkNewOrExisting` |  | resourceGroup （）. name |
| `virtualNetworkName` | 如果您已選取 [新增]，要建立的新虛擬網路名稱`virtualNetworkNewOrExisting` |  | openshiftvnet |
| `addressPrefixes` | 新虛擬網路的位址首碼 |  | 10.0.0.0/14 |
| `masterSubnetName` | 主要子網的名稱 |  | mastersubnet |
| `masterSubnetPrefix` | 用於主要子網的 CIDR-必須是 addressPrefix 的子集 |  | 10.1.0.0/16 |
| `infraSubnetName` | 基礎網的名稱 |  | infrasubnet |
| `infraSubnetPrefix` | 用於基礎網的 CIDR-必須是 addressPrefix 的子集 |  | 10.2.0.0/16 |
| `nodeSubnetName` | 節點子網的名稱 |  | nodesubnet |
| `nodeSubnetPrefix` | 用於節點子網的 CIDR-必須是 addressPrefix 的子集 |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | 主要節點之現有子網的完整參考。 建立新的 vNet/子網時不需要 |  |  |
| `existingInfraSubnetReference` | 基礎節點之現有子網的完整參考。 建立新的 vNet/子網時不需要 |  |  |
| `existingCnsSubnetReference` | 對 CN 節點的現有子網的完整參考。 建立新的 vNet/子網時不需要 |  |  |
| `existingNodeSubnetReference` | 計算節點之現有子網的完整參考。 建立新的 vNet/子網時不需要 |  |  |
| `masterClusterType` | 指定叢集是否使用私用或公用主要節點。 如果選擇 [私人]，主要節點將不會透過公用 IP 向網際網路公開。 相反地，它會使用在中指定的私人 IP`masterPrivateClusterIp` | public <br> private | public |
| `masterPrivateClusterIp` | 如果選取私人主要節點，則必須指定私人 IP 位址，以供主要節點的內部負載平衡器使用。 此靜態 IP 必須位於主要子網的 CIDR 區塊內，而且尚未使用。 如果選取了 [公用主要節點]，則不會使用此值，但仍然必須指定 |  | 10.1.0.200 |
| `routerClusterType` | 指定叢集是使用私用或公用基礎節點。 如果選擇 [私人]，則基礎節點將不會透過公用 IP 向網際網路公開。 相反地，它會使用在中指定的私人 IP`routerPrivateClusterIp` | public <br> private | public |
| `routerPrivateClusterIp` | 如果選取私人基礎節點，則必須指定私人 IP 位址，以供基礎節點的內部負載平衡器使用。 此靜態 IP 必須位於基礎網的 CIDR 區塊內，而且尚未使用。 如果選取公用基礎節點，則不會使用此值，但仍然必須指定 |  | 10.2.0.200 |
| `routingCertType` | 將自訂憑證用於路由網域或預設的自我簽署憑證-遵循**自訂憑證**一節中的指示 | lnk-selfsigned 之類 <br> 自訂 | lnk-selfsigned 之類 |
| `masterCertType` | 將自訂憑證用於主要網域或預設的自我簽署憑證-遵循**自訂憑證**一節中的指示 | lnk-selfsigned 之類 <br> 自訂 | lnk-selfsigned 之類 |

<br>

### <a name="deploy-using-azure-cli"></a>使用 Azure CLI 部署

> [!NOTE] 
> 下列命令需要 Azure CLI 2.0.8 或更新版本。 您可以使用 `az --version` 命令來確認 CLI 版本。 若要更新 CLI 版本，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latesti)。

下列範例會使用 myOpenShiftCluster 的部署名稱，將 OpenShift 叢集和所有相關的資源部署到名為 openshiftrg 的資源群組。 範本直接參考自 GitHub 儲存機制，而且會使用名為 azuredeploy.parameters.json 檔案的本機參數檔案。

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

根據部署的節點總數和設定的選項，部署需要至少60分鐘的時間才能完成。 部署完成時，OpenShift 主控台的防禦 DNS FQDN 和 URL 會列印到終端機。

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

如果您不想困在命令列等候部署完成，請新增 `--no-wait` 作為群組部署的選項之一。 您可以從 Azure 入口網站，在資源群組的部署區段中擷取部署中的輸出。

## <a name="connect-to-the-openshift-cluster"></a>連線到 OpenShift 叢集

部署完成時，請從部署的輸出區段中擷取連線。 使用**OpenShift 主控台 URL**，透過您的瀏覽器連線至 OpenShift 主控台。 您也可以透過 SSH 連線到防禦主機。 在以下範例中，管理員使用者名稱是 clusteradmin，而防禦公用 IP DNS FQDN 是 bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com：

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>清除資源

當不再需要資源時，您可以使用 [az group delete](/cli/azure/group) 命令，移除資源群組、OpenShift 叢集和所有相關資源。

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>後續步驟

- [部署後工作](./openshift-container-platform-3x-post-deployment.md)
- [針對 Azure 中的 OpenShift 部署進行疑難排解](./openshift-container-platform-3x-troubleshooting.md)
- [開始使用 OpenShift 容器平臺](https://docs.openshift.com)
