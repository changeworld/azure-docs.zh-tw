---
title: 在 Azure 中部署 OpenShift 容器平臺 3.11
description: 在 Azure 中部署 OpenShift 容器平臺 3.11。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 615d9a3c5c359174ef15028e82044a85da0dd733
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75561281"
---
# <a name="deploy-openshift-container-platform-311-in-azure"></a>在 Azure 中部署 OpenShift 容器平臺 3.11

可以使用以下幾種方法之一在 Azure 中部署 OpenShift 容器平臺 3.11：

- 您可以手動部署必要的 Azure 基礎結構元件，然後按照[OpenShift 容器平臺文檔](https://docs.openshift.com/container-platform)操作。
- 您也可以使用能夠簡化 OpenShift 容器平台叢集部署的現有 [Resource Manager 範本](https://github.com/Microsoft/openshift-container-platform/) \(英文\)。
- 另一個選項是使用[Azure 應用商店產品/服務](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy)。

對於所有選項，都需要有 Red Hat 訂用帳戶。 在部署期間，Red Hat Enterprise Linux 執行個體會向 Red Hat 訂用帳戶註冊，並且附加至包含 OpenShift 容器平台之權利的集區識別碼。
請確定您具備有效的 Red Hat Subscription Manager (RHSM) 使用者名稱、密碼和集區識別碼。 您可以使用啟用金鑰、組織識別碼和集區識別碼。 您可以登入 https://access.redhat.com 來驗證這項資訊。


## <a name="deploy-using-the-openshift-container-platform-resource-manager-311-template"></a>使用 OpenShift 容器平臺資源管理器 3.11 範本進行部署

### <a name="private-clusters"></a>專用群集

部署專用 OpenShift 群集需要的不僅僅是沒有與主負載等化器（Web 主控台）或紅外負載等化器（路由器）關聯的公共 IP。  專用群集通常使用自訂 DNS 伺服器（不是預設的 Azure DNS）、自訂功能變數名稱（如contoso.com）和預定義的虛擬網路。  對於專用群集，您需要提前使用所有適當的子網和 DNS 伺服器設置配置虛擬網路。  然後使用**現有的主子網參考**、**現有的InfraSubnet參考**、**現有的CnsSubnet參考**和**現有的NodeSubnet參考**來指定現有的子網供群集使用。

如果選擇了私有主機（**主群集類型**=私有），則需要為**主私有群集Ip**指定靜態專用 IP。  此 IP 將分配給主負載等化器的前端。  IP 必須在主子網的 CIDR 內，並且不在使用中。  **主群集Dns類型**必須設置為"自訂"，並且必須為**主群集Dns**提供主 DNS 名稱。  DNS 名稱必須映射到靜態專用 IP，並將用於訪問主節點上的主控台。

如果選擇了專用路由器（**路由器群集類型**=專用），則需要為**路由器專用群集Ip**指定靜態專用 IP。  此 IP 將分配給紅外負載等化器的前端。  IP 必須在紅外線網的 CIDR 內，並且未使用。  **路由子欄位型別**必須設置為"自訂"，並且必須為**路由提供**路由的萬用字元 DNS 名稱。  

如果選擇了專用主機和專用路由器，則還必須為**功能變數名稱**輸入自訂功能變數名稱

成功部署後，堡壘節點是唯一具有公共 IP 的節點。該節點可以放入其中。  即使主節點配置為公共訪問，也不會公開用於 ssh 訪問。

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

### <a name="azuredeployparametersjson-file-explained"></a>azure 部署。參數.json 檔解釋

| 屬性 | 描述 | 有效選項 | 預設值 |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | 專案 URL（json、腳本等） |  |  HTTPs：\//raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | 要將資源部署到 |  |  |
| `masterVmSize` | 主 VM 的大小。 從 azuredeploy.json 檔中列出的允許的 VM 大小之一中進行選擇 |  | Standard_E2s_v3 |
| `infraVmSize` | Infra VM 的大小。 從 azuredeploy.json 檔中列出的允許的 VM 大小之一中進行選擇 |  | Standard_D4s_v3 |
| `nodeVmSize` | 應用節點 VM 的大小。 從 azuredeploy.json 檔中列出的允許的 VM 大小之一中進行選擇 |  | Standard_D4s_v3 |
| `cnsVmSize` | 容器本機存儲 （CNS） 節點 VM 的大小。 從 azuredeploy.json 檔中列出的允許的 VM 大小之一中進行選擇 |  | Standard_E4s_v3 |
| `osImageType` | 要使用的 RHEL 映射。 預設庫：按需;市場：協力廠商形象 | 預設庫 <br> Marketplace | 預設庫 |
| `marketplaceOsImage` | 如果是`osImageType`市場，則輸入市場報價的"發行者"、"優惠"、"sku"和"版本"的適當值。 此參數是物件類型 |  |  |
| `storageKind` | 要使用的存儲類型  | 受控<br> Unmanaged | 受控 |
| `openshiftClusterPrefix` | 用於配置所有節點的主機名稱的群集首碼。  1 到 20 個字元之間 |  | 我的集群 |
| `minoVersion` | 要部署的 OpenShift 容器平臺 3.11 的次要版本 |  | 69 |
| `masterInstanceCount` | 要部署的主機節點數 | 1, 3, 5 | 3 |
| `infraInstanceCount` | 要部署的紅外線節點數 | 1, 2, 3 | 3 |
| `nodeInstanceCount` | 要部署的節點數 | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | 要部署的 CNS 節點數 | 3, 4 | 3 |
| `osDiskSize` | VM OS 磁片的大小（以 GB） | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | 要附加到 Docker 卷節點的資料磁片的大小（以 GB 表示） | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | 要附加到 CNS 節點的資料磁片大小，供 glusterfs 使用（以 GB 表示） | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | 管理兩個作業系統 （VM） 登錄名和初始 OpenShift 使用者的管理員使用者名 |  | 奧cpadmin |
| `enableMetrics` | 啟用指標。 指標需要更多的資源，因此為 Infra VM 選擇適當的大小 | true <br> false | false |
| `enableLogging` | 啟用日誌記錄。 彈性搜索窗格需要 8 GB RAM，因此為 Infra VM 選擇適當的大小 | true <br> false | false |
| `enableCNS` | 啟用容器本機存儲 | true <br> false | false |
| `rhsmUsernameOrOrgId` | 紅帽訂閱管理器使用者名或組織 ID |  |  |
| `rhsmPoolId` | 包含計算節點的 OpenShift 許可權的紅帽訂閱管理器池 ID |  |  |
| `rhsmBrokerPoolId` | 紅帽訂閱管理器池 ID，其中包含主節點和紅外線節點的 OpenShift 許可權。 如果您沒有不同的池 ID，請輸入與"rhsmPoolId"相同的池 ID |  |
| `sshPublicKey` | 在此處複製您的 SSH 公開金鑰 |  |  |
| `keyVaultSubscriptionId` | 包含金鑰保存庫的訂閱的訂閱 ID |  |  |
| `keyVaultResourceGroup` | 包含金鑰保存庫的資源組的名稱 |  |  |
| `keyVaultName` | 您創建的金鑰保存庫的名稱 |  |  |
| `enableAzure` | 啟用 Azure 雲提供程式 | true <br> false | true |
| `aadClientId` | Azure 活動目錄用戶端 ID 也稱為服務主體的應用程式 ID |  |  |
| `domainName` | 要使用的自訂功能變數名稱的名稱（如果適用）。 如果未部署完全專用群集，則設置為"無" |  | 無 |
| `masterClusterDnsType` | OpenShift Web 主控台的欄位型別。 "預設"將使用主紅外公共 IP 的 DNS 標籤。 "自訂"允許您定義自己的名稱 | default <br> custom | default |
| `masterClusterDns` | 自訂 DNS 名稱用於訪問 OpenShift Web 主控台（如果選擇"自訂"）`masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | 如果設置為"nipio"，`routingSubDomain`將使用nip.io。  如果您有自己的域要用於路由，請使用"自訂" | 尼皮奧 <br> custom | 尼皮奧 |
| `routingSubDomain` | 如果選擇"自訂"，則要用於路由的萬用字元 DNS 名稱`routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | 選擇是使用現有虛擬網路還是創建新的虛擬網路 | 現有 <br> new | new |
| `virtualNetworkResourceGroupName` | 如果為`virtualNetworkNewOrExisting` |  | 資源組（）名稱 |
| `virtualNetworkName` | 選擇"新"時要創建的新虛擬網路的名稱`virtualNetworkNewOrExisting` |  | 開移vnet |
| `addressPrefixes` | 新虛擬網路的位址首碼 |  | 10.0.0.0/14 |
| `masterSubnetName` | 主子網的名稱 |  | 主子網 |
| `masterSubnetPrefix` | 用於主子網的 CIDR - 需要是位址首碼的子集 |  | 10.1.0.0/16 |
| `infraSubnetName` | 紅外線網的名稱 |  | 紅外線 |
| `infraSubnetPrefix` | 用於紅外線網的 CIDR - 需要是位址首碼的子集 |  | 10.2.0.0/16 |
| `nodeSubnetName` | 節點子網的名稱 |  | 節點子網 |
| `nodeSubnetPrefix` | 用於節點子網的 CIDR - 需要是位址首碼的子集 |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | 主節點對現有子網的完整引用。 如果創建新的 vNet / 子網，則不需要 |  |  |
| `existingInfraSubnetReference` | 對紅外線節點的現有子網的完整引用。 如果創建新的 vNet / 子網，則不需要 |  |  |
| `existingCnsSubnetReference` | 完全引用 CNS 節點的現有子網。 如果創建新的 vNet / 子網，則不需要 |  |  |
| `existingNodeSubnetReference` | 對計算節點的現有子網的完整引用。 如果創建新的 vNet / 子網，則不需要 |  |  |
| `masterClusterType` | 指定群集是使用專用節點還是公共主節點。 如果選擇私有節點，主節點將不會通過公共 IP 向 Internet 公開。 相反，它將使用`masterPrivateClusterIp` | public <br> private | public |
| `masterPrivateClusterIp` | 如果選擇了專用主節點，則必須指定私人 IP 位址，供主節點的內部負載等化器使用。 此靜態 IP 必須位於主子網的 CIDR 塊內，並且尚未使用。 如果選擇了公共主節點，則不會使用此值，但仍必須指定 |  | 10.1.0.200 |
| `routerClusterType` | 指定群集是使用專用節點還是公共紅外線節點。 如果選擇私有，則紅外線節點不會通過公共 IP 向 Internet 公開。 相反，它將使用`routerPrivateClusterIp` | public <br> private | public |
| `routerPrivateClusterIp` | 如果選擇了專用紅外線節點，則必須指定私人 IP 位址，供內部負載等化器用於紅外線節點。 此靜態 IP 必須位於主子網的 CIDR 塊內，並且尚未使用。 如果選擇了公共紅外線節點，則此值不會使用，但仍必須指定 |  | 10.2.0.200 |
| `routingCertType` | 將自訂證書用於路由域或預設自簽章憑證 - 按照**自訂證書**部分中的說明操作 | 自簽名 <br> custom | 自簽名 |
| `masterCertType` | 對主域或預設自簽章憑證使用自訂證書 - 按照**自訂證書**部分中的說明操作 | 自簽名 <br> custom | 自簽名 |

<br>

### <a name="deploy-using-azure-cli"></a>使用 Azure CLI 部署

> [!NOTE] 
> 下列命令需要 Azure CLI 2.0.8 或更新版本。 您可以使用 `az --version` 命令來確認 CLI 版本。 若要更新 CLI 版本，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti)。

下列範例會使用 myOpenShiftCluster 的部署名稱，將 OpenShift 叢集和所有相關的資源部署到名為 openshiftrg 的資源群組。 範本直接參考自 GitHub 儲存機制，而且會使用名為 azuredeploy.parameters.json 檔案的本機參數檔案。

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

根據部署的節點總數和配置的選項，部署至少需要 60 分鐘才能完成。 部署完成時，OpenShift 主控台的防禦 DNS FQDN 和 URL 會列印到終端機。

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

如果您不想困在命令列等候部署完成，請新增 `--no-wait` 作為群組部署的選項之一。 您可以從 Azure 入口網站，在資源群組的部署區段中擷取部署中的輸出。

## <a name="connect-to-the-openshift-cluster"></a>連線到 OpenShift 叢集

部署完成時，請從部署的輸出區段中擷取連線。 使用**OpenShift 主控台 URL**使用瀏覽器連接到 OpenShift 主控台。 您還可以 SSH 到堡壘主機。 在以下範例中，管理員使用者名稱是 clusteradmin，而防禦公用 IP DNS FQDN 是 bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com：

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
- [使用 OpenShift 容器平臺入門](https://docs.openshift.com)
