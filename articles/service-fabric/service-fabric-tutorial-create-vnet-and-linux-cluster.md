---
title: 在 Azure 中建立 Linux Service Fabric 叢集
description: 了解如何使用 Azure CLI 將 Linux Service Fabric 叢集部署到現有的 Azure 虛擬網路。
ms.topic: conceptual
ms.date: 02/14/2019
ms.custom: mvc
ms.openlocfilehash: f5788f07dd4a4f03a95efaea4b741cd64c930ac5
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251775"
---
# <a name="deploy-a-linux-service-fabric-cluster-into-an-azure-virtual-network"></a>將 Linux Service Fabric 叢集部署到 Azure 虛擬網路

在此文章中，您學到如何使用 Azure CLI 與範本將 Linux Service Fabric 叢集部署到 [Azure 虛擬網路 (VNET)](../virtual-network/virtual-networks-overview.md)。 完成時，您會有在您可以部署應用程式的雲端中執行的叢集。 若要使用 PowerShell 建立 Windows 叢集，請參閱[在 Azure 上建立安全的 Windows 叢集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)。

## <a name="prerequisites"></a>必要條件

開始之前：

* 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* 安裝 [Service Fabric CLI](service-fabric-cli.md)
* 安裝 [Azure CLI](/cli/azure/install-azure-cli)
* 若要學習叢集的主要概念，請閱讀 [Azure 叢集概觀](service-fabric-azure-clusters-overview.md)
* [規劃及準備](service-fabric-cluster-azure-deployment-preparation.md)生產環境的叢集部署。

下列程序會建立含七個節點的 Service Fabric 叢集。 若要計算在 Azure 中執行 Service Fabric 叢集產生的成本，請使用 [Azure 價格計算機](https://azure.microsoft.com/pricing/calculator/)。

## <a name="download-and-explore-the-template"></a>下載並瀏覽範本

下載下列 Resource Manager 範本檔案：

* [Azuredeploy.parameters.json」 json][template]
* [Azuredeploy.parameters.json」. Parameters. json][parameters]

此範本會將一個由七部虛擬機器和三個節點類型組成的安全叢集部署到虛擬網路中。  您可以在 [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates) 上找到其他範例範本。 [Azuredeploy.parameters.json」][template]會部署一些資源，包括下列各項。

### <a name="service-fabric-cluster"></a>Service Fabric 叢集

在 **Microsoft.ServiceFabric/clusters** Linux 叢集會以下列特性部署：

* 三個節點類型
* 主要節點類型中的 五個節點 (可在範本參數中設定)，其他節點類型各有一個節點
* 作業系統：Ubuntu 16.04 LTS (可在範本參數中設定)
* 受保護的憑證 (可在範本參數中設定)
* 啟用 [DNS 服務](service-fabric-dnsservice.md)
* Bronze 的[耐久性層級](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) (可在範本參數中設定)
* Silver 的[可靠性層級](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) (可在範本參數中設定)
* 用戶端連線端點：19000 (可在範本參數中設定)
* HTTP 閘道端點：19080 (可在範本參數中設定)

### <a name="azure-load-balancer"></a>Azure Load Balancer

在 **Microsoft.Network/loadBalancers** 資源中，會為下列連接埠設定負載平衡器，並進行探查和規則的設定：

* 用戶端連線端點：19000
* HTTP 閘道端點：19080
* 應用程式連接埠：80
* 應用程式連接埠：443

### <a name="virtual-network-and-subnet"></a>虛擬網路和子網路

虛擬網路和子網路的名稱會在範本參數中宣告。  虛擬網路和子網路的位址空間也會在範本參數中宣告，並設定於 **Microsoft.Network/virtualNetworks** 資源中：

* 虛擬網路位址空間：10.0.0.0/16
* Service Fabric 子網路位址空間：10.0.2.0/24

如果需要任何其他應用程式連接埠，則您必須調整 Microsoft.Network/loadBalancers 資源，以允許流量進入。

## <a name="set-template-parameters"></a>設定範本參數

[Azuredeploy.parameters.json」][parameters]參數檔案會宣告許多用來部署叢集和相關聯資源的值。 您可能需要為自己的部署修改某些參數：

|參數|範例值|注意事項|
|---|---||
|adminUserName|vmadmin| 叢集 VM 的系統管理員使用者名稱。 |
|adminPassword|Password#1234| 叢集 VM 的系統管理員密碼。|
|clusterName|mysfcluster123| 叢集的名稱。 |
|位置|southcentralus| 叢集的位置。 |
|certificateThumbprint|| <p>如果建立自我簽署憑證或提供憑證檔案，則值應該空白。</p><p>若要使用先前上傳至金鑰保存庫的現有憑證，請填入憑證 SHA1 指紋值。 例如 "6190390162C988701DB5676EB81083EA608DCCF3"。 </p>|
|certificateUrlValue|| <p>如果建立自我簽署憑證或提供憑證檔案，則值應該空白。</p><p>若要使用先前上傳至金鑰保存庫的現有憑證，請填入憑證 URL。 例如，"https:\//mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346"。</p>|
|sourceVaultValue||<p>如果建立自我簽署憑證或提供憑證檔案，則值應該空白。</p><p>若要使用先前上傳至金鑰保存庫的現有憑證，請填入來源保存庫值。 例如 "/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT"。</p>|

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>部署虛擬網路和叢集

接下來，請設定網路拓撲並部署 Service Fabric 叢集。 [Azuredeploy.parameters.json」][template] Resource Manager 範本會為 Service Fabric 建立虛擬網路（VNET）和子網。 範本也會部署啟用憑證安全性的叢集。  對於生產叢集，請使用憑證授權單位 (CA) 提供的憑證作為叢集憑證。 自我簽署憑證可用來保護測試叢集。

此文章中的範本會部署使用憑證指紋來識別叢集憑證的叢集。  憑證的指紋皆不相同，因而使憑證管理更為困難。 將使用憑證指紋的已部署叢集切換為使用憑證通用名稱，有助於大幅簡化憑證管理作業。  若要了解如何更新叢集以使用憑證通用名稱進行憑證管理，請參閱[將叢集變更為使用憑證通用名稱進行管理](service-fabric-cluster-change-cert-thumbprint-to-cn.md)。

### <a name="create-a-cluster-using-an-existing-certificate"></a>使用現有的憑證建立叢集

下列指令碼會使用 [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest) 命令和範本，部署以現有憑證保護的新叢集。 此命令也會在 Azure 中建立新的金鑰保存庫，並上傳您的憑證。

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates\MyCertificate.pem"

# sign in to your Azure account and select your subscription
az login
az account set --subscription <guid>

# Create a new resource group for your deployment and give it a name and a location.
az group create --name $ResourceGroupName --location $Location

# Create the Service Fabric cluster.
az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-password $Password --certificate-file $CertPath \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file AzureDeploy.json --parameter-file AzureDeploy.Parameters.json
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>使用新的自我簽署憑證建立叢集

下列指令碼會使用 [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest) 命令和範本，在 Azure 中部署新的叢集。 此命令也會在 Azure 中建立新的金鑰保存庫、將新的自我簽署憑證新增至金鑰保存庫，並將憑證檔案下載至本機。

```azurecli
ResourceGroupName="sflinuxclustergroup"
ClusterName="sflinuxcluster"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates"

az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --cluster-name $ClusterName --template-file C:\temp\cluster\AzureDeploy.json \
   --parameter-file C:\temp\cluster\AzureDeploy.Parameters.json --certificate-password $Password \
   --certificate-output-folder $CertPath --certificate-subject-name $ClusterName.$Location.cloudapp.azure.com \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName
```

## <a name="connect-to-the-secure-cluster"></a>連線到安全的叢集

使用 Service Fabric CLI 命令 `sfctl cluster select` 搭配您的金鑰來連線到叢集。  請注意，只能針對自我簽署憑證使用 **--no-verify** 選項。

```console
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

使用 `sfctl cluster health` 命令來檢查您連接的叢集是否狀況良好。

```console
sfctl cluster health
```

## <a name="clean-up-resources"></a>清除資源

如果您現在不打算繼續閱讀下一篇文章，您可能要[刪除該叢集](service-fabric-cluster-delete.md)以避免產生費用。

## <a name="next-steps"></a>後續步驟

了解如何[調整叢集規模](service-fabric-tutorial-scale-cluster.md)。

此文章中的範本會部署使用憑證指紋來識別叢集憑證的叢集。  憑證的指紋皆不相同，因而使憑證管理更為困難。 將使用憑證指紋的已部署叢集切換為使用憑證通用名稱，有助於大幅簡化憑證管理作業。  若要了解如何更新叢集以使用憑證通用名稱進行憑證管理，請參閱[將叢集變更為使用憑證通用名稱進行管理](service-fabric-cluster-change-cert-thumbprint-to-cn.md)。

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.Parameters.json
