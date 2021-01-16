---
title: 擴大 Azure Service Fabric 主要節點類型
description: 藉由新增新的節點類型並移除上一個節點類型，垂直調整您的 Service Fabric 叢集。
ms.date: 12/11/2020
ms.author: pepogors
ms.topic: how-to
ms.openlocfilehash: 325ece761481077171a670c52e9d98071237601a
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251164"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>擴大 Service Fabric 叢集主要節點類型

本文說明如何以最短的停機時間擴大 Service Fabric 叢集的主要節點類型。 升級 Service Fabric 叢集節點類型的一般策略是：

1. 將新的節點類型新增到您的 Service Fabric 叢集，其受升級的 (支援，或已修改) 的虛擬機器擴展集 SKU 和設定。 此步驟也包含為擴展集設定新的負載平衡器、子網和公用 IP。

1. 一旦原始和升級的擴展集同時執行，就會一次停用一個原始節點實例，讓系統服務 (或具狀態服務的複本) 遷移至新的擴展集。

1. 確認叢集和新節點的狀況良好，然後移除已刪除節點的原始擴展集 (和相關資源) 和節點狀態。

下列程式將逐步引導您使用具有 [銀級持久性](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)的單一擴展集來更新具有銀級的範例叢集之主要節點類型 VM 的 VM 大小和作業系統，以獲得五個節點的支援。 我們將升級主要節點類型：

- 從 VM 大小 *Standard_D2_V2* 到 *標準 D4_V2*，以及
- 從 VM 作業系統的 *Windows server 2016 datacenter （含容器* ）到 *具有容器的 Windows server 2019 datacenter*。

> [!WARNING]
> 在對生產叢集嘗試此程序之前，建議您先研究範本範例，並確認要對測試叢集所進行的程序。 叢集在短時間內也可能無法使用。
>
> 如果叢集狀態為狀況不良，請不要嘗試使用主要節點類型擴大程式，因為這只會進一步使叢集不穩定。

以下是我們將用來完成此範例升級案例的逐步 Azure 部署範本： https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade

## <a name="set-up-the-test-cluster"></a>設定測試叢集

讓我們設定初始 Service Fabric 測試叢集。 首先，請 [下載](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade) 將用來完成此案例的 Azure Resource Manager 範例範本。

接著，登入您的 Azure 帳戶。

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

接著，開啟檔案 [*parameters.js*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/parameters.json) ，並將的值更新 `clusterName` 為 Azure) 內的唯一 (。

下列命令會引導您產生新的自我簽署憑證，並部署測試叢集。 如果您已經有想要使用的憑證，請略過 [使用現有的憑證來部署](#use-an-existing-certificate-to-deploy-the-cluster)叢集。

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>產生自我簽署憑證及部署叢集

首先，指派 Service Fabric 叢集部署所需的變數。 針對您的特定帳戶和環境，調整 `resourceGroupName`、`certSubjectName`、`parameterFilePath` 和 `templateFilePath` 的值：

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$parameterFilePath = "C:\parameters.json"
$templateFilePath = "C:\Initial-TestClusterSetup.json"
```

> [!NOTE]
> 執行 `certOutputFolder` 命令以部署新的 Service Fabric 叢集之前，請先確定您的本機電腦上有位置。

然後部署 Service Fabric 測試叢集：

```powershell
# Deploy the initial test cluster
New-AzServiceFabricCluster `
    -ResourceGroupName $resourceGroupName `
    -CertificateOutputFolder $certOutputFolder `
    -CertificatePassword $certPassword `
    -CertificateSubjectName $certSubjectName `
    -TemplateFile $templateFilePath `
    -ParameterFile $parameterFilePath
```

部署完成後，請在本機電腦上找出 *.pfx* 檔案 (`$certPfx`)，並將其匯入到您的憑證存放區：

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

此作業會傳回憑證指紋，您現在可以使用它來連線 [到新](#connect-to-the-new-cluster-and-check-health-status) 叢集並檢查其健康情況狀態。  (略過下一節，這是叢集部署的替代方法。 ) 

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>使用現有的憑證來部署叢集

或者，您可以使用現有的 Azure Key Vault 憑證來部署測試叢集。 若要這樣做，您必須 [取得 Key Vault](#obtain-your-key-vault-references) 和憑證指紋的參考。

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

接下來，指定叢集的資源組名，並設定 `templateFilePath` 和 `parameterFilePath` 位置：

> [!NOTE]
> 指定的資源群組必須已存在，且與您的 Key Vault 位於相同的區域中。

```powershell
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Initial-TestClusterSetup.json"
$parameterFilePath = "C:\parameters.json"
```

最後，執行下列命令以部署初始測試叢集：

```powershell
# Deploy the initial test cluster
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>連線到新叢集並檢查健全狀態

連線到叢集，並確保其所有節點的狀況良好 (`clusterName` `thumb` 以您自己的值取代和變數) ：

```powershell
# Connect to the cluster
$clusterName = "sftestupgrade.southcentralus.cloudapp.azure.com:19000"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"

Connect-ServiceFabricCluster `
    -ConnectionEndpoint $clusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My

# Check cluster health
Get-ServiceFabricClusterHealth
```

如此一來，我們就可以開始進行升級程式。

## <a name="deploy-a-new-primary-node-type-with-upgraded-scale-set"></a>使用升級的擴展集部署新的主要節點類型

為了升級 (節點類型) 垂直調整，我們必須先部署新的節點類型，並支援新的擴展集和支援的資源。 新的擴展集將會標示為主要 (`isPrimary: true`) ，就像原始擴展集一樣 (除非您要進行非主要節點類型升級) 。 在下一節中建立的資源最終會成為叢集中的新主要節點類型，而且會刪除原始的主要節點類型資源。

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>以升級的擴展集更新叢集範本

以下是用來新增新的主要節點類型和支援資源之原始叢集部署範本的章節修改。

您已在 [*Step1-AddPrimaryNodeType.js*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/Step1-AddPrimaryNodeType.json) 的範本檔案中為您完成此步驟所需的變更，以下將詳細說明這些變更。 如果您想要的話，可以略過說明，並繼續 [取得 Key Vault 的參考](#obtain-your-key-vault-references) ，然後 [部署更新的範本](#deploy-the-updated-template) ，以將新的主要節點類型新增至您的叢集中。

> [!Note]
> 請確定您使用原始節點類型、擴展集、負載平衡器、公用 IP 和原始主要節點類型子網中唯一的名稱，因為這些資源會在程式稍後的步驟中刪除。

#### <a name="create-a-new-subnet-in-the-existing-virtual-network"></a>在現有的虛擬網路中建立新的子網

```json
{
    "name": "[variables('subnet1Name')]",
    "properties": {
        "addressPrefix": "[variables('subnet1Prefix')]"
    }
}
```

#### <a name="create-a-new-public-ip-with-a-unique-domainnamelabel"></a>使用唯一的 domainNameLabel 建立新的公用 IP

```json
{
    "apiVersion": "[variables('publicIPApiVersion')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))]",
    "location": "[variables('computeLocation')]",
    "properties": {
    "dnsSettings": {
        "domainNameLabel": "[concat(variables('dnsName'),'-','nt1')]"
    },
    "publicIPAllocationMethod": "Dynamic"
    },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
    }
}
```

#### <a name="create-a-new-load-balancer-for-the-public-ip"></a>為公用 IP 建立新的負載平衡器

```json
"dependsOn": [
    "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType1Name')))]"
]
```

#### <a name="create-a-new-virtual-machine-scale-set-with-upgraded-vm-and-os-skus"></a>使用升級後的 VM 和 OS Sku 來建立新的虛擬機器擴展集 () 

節點類型參考

```json
"nodeTypeRef": "[variables('vmNodeType1Name')]"
```

VM SKU

```json
"sku": {
    "name": "[parameters('vmNodeType1Size')]",
    "capacity": "[parameters('nt1InstanceCount')]",
    "tier": "Standard"
}
```

作業系統 SKU

```json
"imageReference": {
    "publisher": "[parameters('vmImagePublisher1')]",
    "offer": "[parameters('vmImageOffer1')]",
    "sku": "[parameters('vmImageSku1')]",
    "version": "[parameters('vmImageVersion1')]"
}
```

此外，請確定您包含工作負載所需的任何其他擴充功能。

#### <a name="add-a-new-primary-node-type-to-the-cluster"></a>將新的主要節點類型新增至叢集

現在，新的節點類型 (vmNodeType1Name) 有自己的名稱、子網、IP、負載平衡器和擴展集，它可以重複使用原始節點 (類型的所有其他變數，例如 `nt0applicationEndPort` 、 `nt0applicationStartPort` 和 `nt0fabricTcpGatewayPort`) ：

```json
"name": "[variables('vmNodeType1Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt1InstanceCount')]"
```

在您完成範本和參數檔案中的所有變更之後，請繼續下一節，以取得您的 Key Vault 參考，並將更新部署至您的叢集。

### <a name="obtain-your-key-vault-references"></a>取得您的 Key Vault 參考

若要部署更新的設定，您將需要數個儲存在 Key Vault 中的叢集憑證參考。 尋找這些值最簡單的方式是透過 Azure 入口網站。 您將需要：

* **叢集憑證的 Key Vault URL。** 從 Azure 入口網站的 Key Vault 中，選取 [**憑證**  >  *您需要的憑證*  >  **秘密識別碼**：

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **叢集憑證的指紋。** 如果您已 [連線至初始](#connect-to-the-new-cluster-and-check-health-status)叢集以檢查其健康狀態， (您可能已經有這種情況 ) 。請從相同的憑證分頁 (**憑證**  >  ) 在 Azure 入口網站中，複製 **x.509 sha-1 指紋 (，以十六進位)**：

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **Key Vault 的資源識別碼。** 從 Azure 入口網站的 Key Vault 中，選取 [**屬性**  >  **資源識別碼**：

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>部署更新的範本

`templateFilePath`視需要調整，然後執行下列命令：

```powershell
# Deploy the new node type and its resources
$templateFilePath = "C:\Step1-AddPrimaryNodeType.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

當部署完成時，請再次檢查叢集健康情況，並確定兩個節點類型上的所有節點都狀況良好。

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-node-type"></a>將種子節點遷移至新的節點類型

我們現在已經準備好將原始節點類型更新為非主要節點，並開始停用其節點。 當節點停用時，叢集的系統服務和種子節點會遷移至新的擴展集。

### <a name="unmark-the-original-node-type-as-primary"></a>將原始節點類型取消標示為主要

先 `isPrimary` 從原始節點類型移除範本中的指定。

```json
{
    "isPrimary": false,
}
```

然後使用更新來部署範本。 這會起始將種子節點遷移至新的擴展集。

```powershell
$templateFilePath = "C:\Step2-UnmarkOriginalPrimaryNodeType.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

> [!Note]
> 這需要一些時間才能完成將種子節點遷移至新的擴展集。 為了保證資料一致性，一次只能變更一個種子節點。 每次種子節點變更都需要叢集更新；因此取代種子節點需要兩次叢集升級 (節點新增和移除各需要一次)。 升級此範例案例中的五個種子節點，將會導致 10 次叢集升級。

使用 Service Fabric Explorer 來監視將種子節點遷移至新的擴展集。 原始節點類型 (nt0vm) 的節點在 [**是種子節點**] 資料行中都應該是 *false* ，而且新節點類型 (nt1vm) 的節點將會是 *true*。

### <a name="disable-the-nodes-in-the-original-node-type-scale-set"></a>停用原始節點類型擴展集中的節點

當所有的種子節點都遷移至新的擴展集之後，您就可以停用原始擴展集的節點。

```powershell
# Disable the nodes in the original scale set.
$nodeType = "nt0vm"
$nodes = Get-ServiceFabricNode

Write-Host "Disabling nodes..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
  }
}
```

使用 Service Fabric Explorer 來監視原始擴展集中節點的進展，而不是 *停**用為停用* 狀態。

:::image type="content" source="./media/scale-up-primary-node-type/service-fabric-explorer-node-status.png" alt-text="顯示已停用節點狀態的 Service Fabric Explorer":::

針對銀級和金級持久性，某些節點會進入停用狀態，而其他節點可能會維持在 *停* 用狀態。 在 Service Fabric Explorer 中，檢查節點處於停用狀態的 [ **詳細資料** ] 索引標籤。 如果它們顯示 *EnsurePartitionQuorem* 種類的 *擱置安全檢查*， (確保基礎結構服務分割區的仲裁) ，則可以安全地繼續。

:::image type="content" source="./media/scale-up-primary-node-type/service-fabric-explorer-node-status-disabling.png" alt-text="如果節點顯示 ' EnsurePartitionQuorum ' 類型的擱置安全檢查，您可以繼續停止資料，並移除停滯處於「停用」狀態的節點。":::

如果您的叢集具有銅級持久性，請等候所有節點達到 *停用* 狀態。

### <a name="stop-data-on-the-disabled-nodes"></a>停止停用節點上的資料

現在您可以停止停用節點上的資料。

```powershell
# Stop data on the disabled nodes.
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
  }
}
```

## <a name="remove-the-original-node-type-and-cleanup-its-resources"></a>移除原始節點類型，並清除其資源

我們已準備好移除原始節點類型與其相關聯的資源，以結束垂直調整程式。

### <a name="remove-the-original-scale-set"></a>移除原始擴展集

先移除節點類型的支援擴展集。

```powershell
$scaleSetName = "nt0vm"
$scaleSetResourceType = "Microsoft.Compute/virtualMachineScaleSets"

Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
```

### <a name="delete-the-original-ip-and-load-balancer-resources"></a>刪除原始 IP 和負載平衡器資源

您現在可以刪除原始 IP 和負載平衡器資源。 在此步驟中，您也會更新 DNS 名稱。

> [!Note]
> 如果您已經使用 *標準* SKU 公用 IP 和負載平衡器，則此步驟是選擇性的。 在此情況下，同一個負載平衡器下可能會有多個擴展集/節點類型。

執行下列命令， `$lbname` 並視需要修改值。

```powershell
# Delete the original IP and load balancer resources
$lbName = "LB-sftestupgrade-nt0vm"
$lbResourceType = "Microsoft.Network/loadBalancers"
$ipResourceType = "Microsoft.Network/publicIPAddresses"
$oldPublicIpName = "PublicIP-LB-FE-nt0vm"
$newPublicIpName = "PublicIP-LB-FE-nt1vm"

$oldPrimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $resourceGroupName
$primaryDNSName = $oldPrimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldPrimaryPublicIP.DnsSettings.Fqdn

Remove-AzResource -ResourceName $lbName -ResourceType $lbResourceType -ResourceGroupName $resourceGroupName -Force
Remove-AzResource -ResourceName $oldPublicIpName -ResourceType $ipResourceType -ResourceGroupName $resourceGroupName -Force

$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $resourceGroupName
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
```

### <a name="remove-node-state-from-the-original-node-type"></a>從原始節點類型中移除節點狀態

原始節點類型節點現在會顯示其 **健全狀況狀態** 的 *錯誤*。 從叢集中移除其節點狀態。

```powershell
# Remove state of the obsolete nodes from the cluster
$nodeType = "nt0vm"
$nodes = Get-ServiceFabricNode

Write-Host "Removing node state..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
  }
}
```

Service Fabric Explorer 現在應該只會反映新節點類型的五個節點 (nt1vm) ，且健康狀態值為 *OK*。 您的叢集健康狀態仍會顯示 [ *錯誤*]。 接下來我們會更新範本以反映最新的變更和重新部署，以進行補救。

### <a name="update-the-deployment-template-to-reflect-the-newly-scaled-up-primary-node-type"></a>更新部署範本以反映新向上擴充的主要節點類型

您已在 [*Step3-CleanupOriginalPrimaryNodeType.js*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/Step3-CleanupOriginalPrimaryNodeType.json) 的範本檔案中為您完成此步驟所需的變更，以下各節將詳細說明這些範本變更。 如果您想要的話，可以略過說明，並繼續 [部署更新的範本](#deploy-the-finalized-template) 並完成教學課程。

#### <a name="update-the-cluster-management-endpoint"></a>更新叢集管理端點

`managementEndpoint`使用 *vmNodeType1Name*) 更新 *vmNodeType0Name* ，以更新部署範本上的叢集，以參考新的 IP (。

```json
  "managementEndpoint": "[concat('https://',reference(concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))).dnsSettings.fqdn,':',variables('nt0fabricHttpGatewayPort'))]",
```

#### <a name="remove-the-original-node-type-reference"></a>移除原始節點類型參考

從部署範本的 Service Fabric 資源中移除原始節點類型參考：

```json
"name": "[variables('vmNodeType0Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt0InstanceCount')]"
```

#### <a name="configure-health-policies-to-ignore-existing-errors"></a>設定健全狀況原則以忽略現有的錯誤

僅適用于銀級和更高的持久性叢集，請更新範本中的叢集資源，並設定健康狀態原則，藉 `fabric:/System` 由在叢集資源屬性下新增 *applicationDeltaHealthPolicies* 來忽略應用程式健康情況，如下所示。 下列原則會忽略現有的錯誤，但不允許新的健康情況錯誤。

```json
"upgradeDescription":  
{ 
 "forceRestart": false, 
 "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
 "healthCheckWaitDuration": "00:05:00", 
 "healthCheckStableDuration": "00:05:00", 
 "healthCheckRetryTimeout": "00:45:00", 
 "upgradeTimeout": "12:00:00", 
 "upgradeDomainTimeout": "02:00:00", 
 "healthPolicy": { 
   "maxPercentUnhealthyNodes": 100, 
   "maxPercentUnhealthyApplications": 100 
 }, 
 "deltaHealthPolicy":  
 { 
   "maxPercentDeltaUnhealthyNodes": 0, 
   "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
   "maxPercentDeltaUnhealthyApplications": 0, 
   "applicationDeltaHealthPolicies":  
   { 
       "fabric:/System":  
       { 
           "defaultServiceTypeDeltaHealthPolicy":  
           { 
                   "maxPercentDeltaUnhealthyServices": 0 
           } 
       } 
   } 
 } 
}
```

#### <a name="remove-supporting-resources-for-the-original-node-type"></a>移除原始節點類型的支援資源

從 ARM 範本和參數檔案中移除所有與原始節點類型相關的其他資源。 刪除下面這一行：

```json
    "vmImagePublisher": {
      "value": "MicrosoftWindowsServer"
    },
    "vmImageOffer": {
      "value": "WindowsServer"
    },
    "vmImageSku": {
      "value": "2016-Datacenter-with-Containers"
    },
    "vmImageVersion": {
      "value": "latest"
    },
```

#### <a name="deploy-the-finalized-template"></a>部署已完成的範本

最後，部署修改過的 Azure Resource Manager 範本。

```powershell
# Deploy the updated template file
$templateFilePath = "C:\Step3-CleanupOriginalPrimaryNodeType"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

> [!NOTE]
> 此步驟需要一些時間，通常最多需要兩個小時。

升級會將設定變更為 *InfrastructureService*;因此需要重新開機節點。 在此情況下，會忽略 *forceRestart* 。 參數 `upgradeReplicaSetCheckTimeout` 會指定 Service Fabric 等候分割區處於安全狀態（如果尚未處於安全狀態）的最長時間。 一旦為節點上的所有資料分割傳遞安全性檢查，Service Fabric 會在該節點上繼續進行升級。 參數的值 `upgradeTimeout` 可以縮減為6小時，但應使用最大的安全12小時。

部署完成後，請在 Azure 入口網站中，確認 Service Fabric 資源狀態為 [ *就緒*]。 確認您可以連線到新的 Service Fabric Explorer 端點、叢集 **健全狀況狀態** 為 *[確定]*，而且任何已部署的應用程式都能正常運作。

這樣一來，您就可以垂直調整叢集的主要節點類型！

## <a name="next-steps"></a>後續步驟

* 了解如何[向群集新增節點類型](virtual-machine-scale-set-scale-node-type-scale-out.md)
* 深入了解[應用程式延展性](service-fabric-concepts-scalability.md)。
* [將 Azure 叢集相應縮小或相應放大](service-fabric-tutorial-scale-cluster.md)。
* 使用 Fluent Azure 計算 SDK，[以程式設計方式調整 Azure 叢集](service-fabric-cluster-programmatic-scaling.md)。
* [將獨立叢集相應縮小或相應放大](service-fabric-cluster-windows-server-add-remove-nodes.md)。
