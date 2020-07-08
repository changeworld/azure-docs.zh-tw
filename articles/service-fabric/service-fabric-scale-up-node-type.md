---
title: 擴大 Azure Service Fabric 節點類型
description: 了解如何透過新增虛擬機器擴展集來調整 Service Fabric 叢集的規模。
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: 2d700367049e0bf9bf710aad110c850a78c26220
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610688"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>擴大 Service Fabric 叢集主要節點類型
本文說明如何透過增加虛擬機器資源來擴大 Service Fabric 叢集主要節點類型。 Service Fabric 叢集是一組由網路連接的虛擬或實體機器，可用來將您的微服務部署到其中並進行管理。 屬於叢集一部分的機器或 VM 都稱為節點。 虛擬機器擴展集是一個 Azure 計算資源，可以用來將一組虛擬機器當做一個集合加以部署和管理。 在 Azure 叢集中定義的每個節點類型，會[設定為不同的擴展集](service-fabric-cluster-nodetypes.md)。 隨後，您即可個別管理每個節點類型。 建立 Service Fabric 叢集之後，您可以垂直調整叢集節點類型 (變更節點的資源) 或將節點類型 VM 的作業系統升級。  您可以隨時調整叢集，即使正在叢集上執行工作負載，也是如此。  在叢集進行調整時，您的應用程式也會自動調整。

> [!WARNING]
> 如果叢集狀態狀況不良，請勿嘗試主要節點類型相應增加程式，因為這只會進一步使叢集變得不穩定。
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="process-to-upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>用於將主要節點類型 VM 的大小和作業系統升級的程序
若要將主要節點類型 VM 的 VM 大小和作業系統升級，其程序如下。  升級之後，主要節點類型 VM 的大小為標準 D4_V2，所執行的是 Windows Server 2016 Datacenter with Containers。

> [!WARNING]
> 在對生產叢集嘗試此程序之前，建議您先研究範本範例，並確認要對測試叢集所進行的程序。 叢集也會有一段時間無法使用。 您無法變更平行宣告為相同 NodeType 的多個 VMSS；您必須執行不同的部署作業，將變更個別套用至每個 NodeType VMSS。

1. 使用這些[範本](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json)和[參數](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json)檔案範例，為初始叢集部署兩個節點類型和兩個擴展集 (兩個節點類型各一個擴展集)。  這兩個擴展集的大小為標準 D2_V2，所執行的是 Windows Server 2012 R2 Datacenter。  等候叢集完成基準升級。   
2. 選擇性 - 在叢集中部署具狀態範例。
3. 在決定升級主要節點類型 VM 後，請使用這些[範本](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json)和[參數](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json)檔案範例對主要節點類型新增擴展集，讓主要節點類型現在具有兩個擴展集。  系統服務和使用者應用程式可在兩個不同擴展集內的 VM 之間進行遷移。  新擴展集 VM 的大小為標準 D4_V2，並執行 Windows Server 2016 Datacenter with Containers。  新的擴展集也會一起新增負載平衡器和公用 IP 位址。  
    若要在範本中尋找新的擴展集，請搜尋以 vmNodeType2Name 參數命名的 "Microsoft.Compute/virtualMachineScaleSets" 資源。  若要在主要節點類型中新增擴展集，請使用 [屬性] -> [virtualMachineProfile] -> [extensionProfile] -> [擴充功能] -> [屬性] -> [設定] -> [nodeTypeRef 設定]。
4. 檢查叢集健康情況，並確認所有節點均狀況良好。
5. 停用主要節點類型舊有擴展集內的節點，以便之後移除節點。 您可以將所有節點一併停用，這些作業便會排入佇列中。 請靜候所有節點停用，這可能需要一些時間。  隨著節點類型中的較舊節點停用，系統服務和種子節點會遷移到主要節點類型中的新擴展集 VM。
6. 從主要節點類型移除較舊的擴展集。 (如步驟 5 所示停用節點之後，在 Azure 入口網站的虛擬機器擴展集刀鋒視窗中，逐一解除配置舊節點類型的節點。)
7. 移除與舊擴展集相關聯的負載平衡器。 在為新的擴展集設定新的公用 IP 位址和負載平衡器時，叢集會無法使用。  
8. 在變數中儲存與舊的主要節點類型擴展集相關聯的公用 IP 位址 DNS 設定，然後移除該公用 IP 位址。
9. 使用所刪除公用 IP 位址的 DNS 設定，來取代與新的主要節點類型擴展集相關聯的公用 IP 位址 DNS 設定。  叢集現在可再次連線了。
10. 從叢集中移除節點的節點狀態。  如果舊擴展集的持久性層級為銀級或金級，則系統會自動完成此步驟。
11. 如果您在上一個步驟中部署了具狀態應用程式，請確認應用程式可正常運作。

## <a name="set-up-the-test-cluster"></a>設定測試叢集

首先下載我們在本教學課程中所需的兩組檔案：之前的[範本]()和 [parameters]()，以及之後的[範本]()和 [parameters]()。

接著，登入您的 Azure 帳戶。

```powershell
# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId "<your subscription ID>"
```

本教學課程會逐步解說建立自我簽署憑證的案例。 若要使用 Azure Key Vault 的現有憑證，請略過以下步驟，並改為鏡射[使用現有憑證來部署叢集](https://docs.microsoft.com/azure/service-fabric/upgrade-managed-disks#use-an-existing-certificate-to-deploy-the-cluster)中的步驟。

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>產生自我簽署憑證及部署叢集

首先，指派 Service Fabric 叢集部署所需的變數。 針對您的特定帳戶和環境，調整 `resourceGroupName`、`certSubjectName`、`parameterFilePath` 和 `templateFilePath` 的值：

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Deploy-2NodeTypes-2ScaleSets.json"
$parameterFilePath = "C:\Deploy-2NodeTypes-2ScaleSets.parameters.json"
```

> [!NOTE]
> 執行命令之前，確保 `certOutputFolder` 位置存在於本機電腦上，以部署新的 Service Fabric 叢集。

接下來，開啟 *Deploy-2NodeTypes-2ScaleSets.parameters.json* 檔案，並調整 `clusterName` 和 `dnsName` 的值，以對應至您在 PowerShell 中設定的動態值，然後儲存變更。

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

此作業會傳回憑證指紋，您會將其用來連線到新叢集並檢查其健全狀態。

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>連線到新叢集並檢查健全狀態

連線到叢集，並確保其所有節點的狀況良好 (取代您叢集的 `clusterName` 和 `thumb` 變數)：

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

我們已準備好開始進行升級程序。

## <a name="upgrade-the-primary-node-type-vms"></a>升級主要節點類型 VM

在決定升級主要節點類型 VM 後，對主要節點類型新增擴展集，主要節點類型現在便有兩個擴展集。 已提供範例[範本](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json)和 [parameters](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) 檔案，以顯示所需的變更。 新擴展集 VM 的大小為標準 D4_V2，並執行 Windows Server 2016 Datacenter with Containers。 新的擴展集也會一起新增負載平衡器和公用 IP 位址。 

若要在範本中尋找新的擴展集，請搜尋以 vmNodeType2Name 參數命名的 "Microsoft.Compute/virtualMachineScaleSets" 資源。 若要在主要節點類型中新增擴展集，請使用 [屬性] -> [virtualMachineProfile] -> [extensionProfile] -> [擴充功能] -> [屬性] -> [設定] -> [nodeTypeRef 設定]。

### <a name="deploy-the-updated-template"></a>部署更新的範本

視需要調整 `parameterFilePath` 和 `templateFilePath`，然後執行以下命令：

```powershell
# Deploy the new scale set into the primary node type along with a new load balancer and public IP
$templateFilePath = "C:\Deploy-2NodeTypes-3ScaleSets.json"
$parameterFilePath = "C:\Deploy-2NodeTypes-3ScaleSets.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

部署完成時，請再次檢查叢集健康情況，並確保所有節點 (在原始和新的擴展集上) 都狀況良好。

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-nodes-to-the-new-scale-set"></a>將節點遷移至新的擴展集

我們現在已準備好開始停用原始擴展集的節點。 隨著這些節點變為停用，系統服務和種子節點會遷移到新擴展集的 VM，因為其也會標示為主要節點類型。

針對相應增加非主要節點類型，在此步驟中，您會修改服務放置條件約束以包含新的虛擬機器擴展集/節點類型，然後將舊的虛擬機器擴展集實例計數減少為零，一次一個節點（以確保節點移除不會影響叢集可靠性）。

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

使用 Service Fabric Explorer 來監視種子節點至新擴展集的移轉，以及原始擴展集中節點從 [停用] 到 [停用] 狀態的進度。

> [!NOTE]
> 在原始擴展集的所有節點上完成停用作業可能需要一些時間。 為了保證資料一致性，一次只能變更一個種子節點。 每次種子節點變更都需要叢集更新；因此取代種子節點需要兩次叢集升級 (節點新增和移除各需要一次)。 升級此範例案例中的五個種子節點，將會導致 10 次叢集升級。

## <a name="remove-the-original-scale-set"></a>移除原始擴展集

停用作業完成後，請移除擴展集。

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

在 Service Fabric Explorer 中，已移除的節點 (從而 [叢集健全狀態]) 現在會出現在 [錯誤] 狀態中。

## <a name="remove-the-old-load-balancer-and-update-dns-settings"></a>移除舊的負載平衡器並更新 DNS 設定

現在，我們可以移除與舊主要節點類型相關的資源 (從負載平衡器和舊公用 IP 開始)。 

```powershell
$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

接下來，我們會更新新公用 IP 的 DNS 設定，以從舊版主要節點類型的公用 IP 鏡射設定。

```powershell
# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
```

再次檢查叢集健康情況

```powershell
# Check the cluster health
Get-ServiceFabricClusterHealth
```

最後，移除每個相關節點的節點狀態。 如果舊擴展集的持久性層級為銀級或金級，就會自動進行此動作。

```powershell
# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

叢集的主要節點類型現已升級。 確認所有已部署的應用程式都運作正常，且叢集健康狀態良好。

## <a name="next-steps"></a>後續步驟
* 了解如何[向群集新增節點類型](virtual-machine-scale-set-scale-node-type-scale-out.md)
* 深入了解[應用程式延展性](service-fabric-concepts-scalability.md)。
* [將 Azure 叢集相應縮小或相應放大](service-fabric-tutorial-scale-cluster.md)。
* 使用 Fluent Azure 計算 SDK，[以程式設計方式調整 Azure 叢集](service-fabric-cluster-programmatic-scaling.md)。
* [將獨立叢集相應縮小或相應放大](service-fabric-cluster-windows-server-add-remove-nodes.md)。

