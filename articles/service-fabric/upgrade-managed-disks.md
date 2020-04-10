---
title: 升級叢集節點以使用 Azure 託管磁碟
description: 下面瞭解如何升級現有 Service Fabric 群集,以使用群集很少或沒有停機時間的 Azure 託管磁碟。
ms.topic: how-to
ms.date: 4/07/2020
ms.openlocfilehash: 5f4698718a35970e47de2a0ee6d053802c8ef919
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991206"
---
# <a name="upgrade-cluster-nodes-to-use-azure-managed-disks"></a>升級叢集節點以使用 Azure 託管磁碟

[Azure 託管磁碟](../virtual-machines/windows/managed-disks-overview.md)是推薦的磁碟存儲產品,用於與 Azure 虛擬機一起持久存儲數據。 您可以通過升級作為節點類型的基礎的虛擬機器擴展集來使用託管磁碟,從而提高 Service Fabric 工作負載的彈性。 下面瞭解如何升級現有 Service Fabric 群集,以使用群集很少或沒有停機時間的 Azure 託管磁碟。

將 Service Fabric 叢集節點升級到使用託管磁碟的一般策略是:

1. 部署該節點類型的重複虛擬機縮放集,但將[託管磁碟](https://docs.microsoft.com/azure/templates/microsoft.compute/2019-07-01/virtualmachinescalesets/virtualmachines#ManagedDiskParameters)物件添加到虛擬機縮放集部署範本`osDisk`的部分。 新的規模集應綁定到與原始負載平衡器/IP 相同的負載平衡器/IP,以便您的客戶在遷移期間不會遇到服務中斷。

2. 一旦原始擴展集和升級的比例集同時運行,請一次禁用一個原始節點實例,以便系統服務(或狀態服務的副本)遷移到新的規模集。

3. 驗證群集和新節點是否正常,然後刪除已刪除節點的原始比例集和節點狀態。

本文將引導您完成升級示例群集的主節點類型以使用託管磁碟的步驟,同時避免任何群集停機(請參閱下面的註釋)。 示例測試群集的初始狀態由一個節點類型的[Silver 持久性](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)組成,由具有五個節點的單個比例集支援。

> [!CAUTION]
> 僅當對群集 DNS 具有依賴項(例如存[取 服務結構資源管理員](service-fabric-visualizing-your-cluster.md))時,才會遇到此過程中斷。 [前端服務的體系結構最佳做法](https://docs.microsoft.com/azure/architecture/microservices/design/gateway)是在節點類型前面設置某種[負載均衡器](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview),使節點交換成為可能,而不會中斷。

下面是用於完成升級方案的 Azure 資源管理器的[範本和 cmdlet。](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage) 樣本更改將在[「部署以下主節點類型的升級規模集](#deploy-an-upgraded-scale-set-for-the-primary-node-type)」中解釋。

## <a name="set-up-the-test-cluster"></a>設定測試叢集

讓我們設置初始服務結構測試群集。 首先,[下載](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage)我們將用來完成此方案的 Azure 資源管理器範例樣本。

接下來,登錄到 Azure 帳戶。

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

以下命令將指導您生成新的自簽名證書並部署測試群集。 如果您已經擁有要使用的憑證,請跳到[使用現有憑證來部署群組 。](#use-an-existing-certificate-to-deploy-the-cluster)

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>產生自簽署憑證並部署叢集

首先,分配服務結構群集部署所需的變數。 調整`resourceGroupName`、、`certSubjectName``parameterFilePath`以及特定帳戶和`templateFilePath`環境的值:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Initial-1NodeType-UnmanagedDisks.json"
$parameterFilePath = "C:\Initial-1NodeType-UnmanagedDisks.parameters.json"
```

> [!NOTE]
> 在運行命令`certOutputFolder`以部署新的 Service Fabric 群集之前,請確保該位置存在於本地電腦上。

接下來打開[*初始 1NodeType-非託管磁碟.parameters.json*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json)檔,並調整`clusterName`的值`dnsName`,並 對應於您在 PowerShell 中設置的動態值並保存更改。

然後部署服務結構測試叢集:

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

部署完成後,在本地電腦上找到 *.pfx*檔`$certPfx`(), 並將其導入證書存儲:

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

該操作將返回證書指紋,您將使用該指紋[連接到新群集](#connect-to-the-new-cluster-and-check-health-status)並檢查其運行狀況。 (跳過以下部分,這是群集部署的替代方法。

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>使用現有憑證部署叢集

您還可以使用現有的 Azure 金鑰保管庫證書來部署測試群集。 為此,您需要[獲取對密鑰保管庫](#obtain-your-key-vault-references)和證書指紋的引用。

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

打開[*初始-1NodeType-非託管磁碟.parameters.json*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json)檔,並將值更改`clusterName`為`dnsName`和 更改為唯一的值。

最後,為群組指定一個資源群組名稱,`templateFilePath`並設定初始`parameterFilePath` *1NodeType-非託管磁碟*檔案的和位置:

> [!NOTE]
> 指定的資源組必須已經存在,並且位於與密鑰保管庫相同的區域。

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"
```

最後,執行以下指令以部署初始測試群組:

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>連線到新叢集並檢查執行狀況

連接到叢集並確保所有五個節點都正常執行(取代叢集的`clusterName`與`thumb`變數):

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

這樣,我們就可以開始升級過程了。

## <a name="deploy-an-upgraded-scale-set-for-the-primary-node-type"></a>主積節點類型部署升級的規模集

為了升級或*垂直縮放*節點類型,我們需要部署該節點類型的虛擬機比例集的副本,該副本與原始規模集(包括對同`nodeTypeRef`一`subnet`個`loadBalancerBackendAddressPools`的引用 )相同,並且除外,它包括所需的升級/更改及其自己的單獨的子網和入站 NAT 位址池。 由於我們正在升級主節點類型,因此新比例集將標記為主`isPrimary: true`( ),就像原始比例集一樣。 (對於非主節點類型升級,只需省略此。

為方便起見,已在*升級-1NodeType-2ScaleSet-管理磁碟*[範本](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.json)和[參數](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json)檔中為您進行了所需的更改。

以下各節將詳細解釋範本更改。 如果您願意,可以跳過說明並繼續升級[過程的下一步](#obtain-your-key-vault-references)。

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>使用升級的規模集更新叢集範本

以下是原始群集部署範本的逐節修改,用於添加主節點類型的升級規模集。

#### <a name="parameters"></a>參數

為新比例集的實例名稱、計數和大小添加參數。 請注意,`vmNodeType1Name`新比例集是唯一的,而計數和大小值與原始比例集相同。

**樣本檔案**

```json
"vmNodeType1Name": {
    "type": "string",
    "defaultValue": "NTvm2",
    "maxLength": 9
},
"nt1InstanceCount": {
    "type": "int",
    "defaultValue": 5,
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType1Size": {
    "type": "string",
    "defaultValue": "Standard_D2_v2"
},
```

**參數檔案**

```json
"vmNodeType1Name": {
    "value": "NTvm2"
},
"nt1InstanceCount": {
    "value": 5
},
"vmNodeType1Size": {
    "value": "Standard_D2_v2"
}
```

### <a name="variables"></a>變數

在部署範本`variables`部分中,為新規模集的入站 NAT 位址池添加一個條目。

**樣本檔案**

```json
"lbNatPoolID1": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool1')]", 
```

### <a name="resources"></a>資源

在部署樣本*資源*部分中,添加新的虛擬機器規模集,請記住以下事項:

* 新的比例集引用與原始節點類型相同的節點類型:

    ```json
    "nodeTypeRef": "[parameters('vmNodeType0Name')]",
    ```

* 新的比例集引用相同的負載均衡器後端位址和子網(但使用不同的負載均衡器入站 NAT 池):

   ```json
    "loadBalancerBackendAddressPools": [
        {
            "id": "[variables('lbPoolID0')]"
        }
    ],
    "loadBalancerInboundNatPools": [
        {
            "id": "[variables('lbNatPoolID1')]"
        }
    ],
    "subnet": {
        "id": "[variables('subnet0Ref')]"
    }
   ```

* 與原始比例集一樣,新比例集標記為主節點類型。 (升級非主節點類型時,省略此更改。

    ```json
    "isPrimary": true,
    ```

* 與原始規模集不同,新規模集將升級為使用託管磁碟。

    ```json
    "managedDisk": {
        "storageAccountType": "[parameters('storageAccountType')]"
    }
    ```

實現範本和參數檔中的所有更改後,請繼續下一節以獲取密鑰保管庫引用並將更新部署到群集。

### <a name="obtain-your-key-vault-references"></a>取得金鑰保存

要部署更新的配置,首先您將獲得對存儲在密鑰保管庫中的群集證書的多個引用。 查找這些值的最簡單方法是通過 Azure 門戶。 您需要：

* **叢集證書的金鑰保管庫 URL。** 從 Azure 門戶的金鑰保存**Certificates** > 庫中,選擇*所需的憑證* > **金鑰識別碼**:

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **群集證書的指紋。** (如果您[連接到初始群集](#connect-to-the-new-cluster-and-check-health-status)以檢查其運行狀況,則您可能已經擁有此情況。從 Azure 門**Certificates** > 戶中的 相同憑證邊欄選項卡(*憑證、所需憑證*)中複製**X.509 SHA-1 拇指列印(十六進位):**

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **金鑰保管庫的資源 ID。** 從 Azure 門戶中的關鍵保管庫中,選擇**屬性** > **資源代碼**:

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>部署更新的範本

根據需要調整`parameterFilePath``templateFilePath`和,然後運行以下命令:

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

部署完成後,請再次檢查群集運行狀況,並確保所有 10 個節點(原始節點上的 5 個節點和新規模集中的 5 個節點)都正常。

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-scale-set"></a>將 feed 節點移至新的規模集

現在,我們已準備好開始禁用原始比例集的節點。 當這些節點被禁用時,系統服務和種子節點將遷移到新規模集的 VM,因為它也被標記為主節點類型。

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

使用 Service Fabric 資源管理器監視種子節點遷移到新比例集以及原始比例集中的節點從*禁用*狀態到*禁用*狀態的進度。

![顯示已關閉節點狀態的服務結構資源管理員](./media/upgrade-managed-disks/service-fabric-explorer-node-status.png)

> [!NOTE]
> 完成原始規模集的所有節點的禁用操作可能需要一些時間。 為了保證數據的一致性,一次只能更改一個種子節點。 每次種子節點更改都需要群集更新;因此,替換種子節點需要兩個群集升級(每個群集升級用於節點添加和刪除)。 升級此示例方案中的五個種子節點將導致 10 個群集升級。

## <a name="remove-the-original-scale-set"></a>移除原始比例集

關閉操作完成後,刪除縮放集。

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

在 Service Fabric 資源管理器中,已刪除的節點(因此是*叢集運行狀況狀態*)現在將顯示為*錯誤*狀態。

![服務結構資源管理員顯示處處錯誤狀態的關閉節點](./media/upgrade-managed-disks/service-fabric-explorer-disabled-nodes-error-state.png)

從 Service Fabric 群集中刪除過時的節點,將群集運行狀況狀態還原為 *"確定*"。

```powershell
# Remove node states for the deleted scale set
foreach($name in $nodeNames){
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

![已移除關閉節點的維修結構資源管理員](./media/upgrade-managed-disks/service-fabric-explorer-healthy-cluster.png)

## <a name="next-steps"></a>後續步驟

在本演練中,您學習了如何升級 Service Fabric 群集的虛擬機縮放集以使用託管磁碟,同時避免在此過程中的服務中斷。 有關相關主題的詳細資訊,請查看以下資源。

了解如何：

* [相應放大 Service Fabric 叢集主要節點類型](service-fabric-scale-up-node-type.md)

* [將規模集樣本轉換為使用託管磁碟](../virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md.md)

* [移除 Service Fabric 節點類型](service-fabric-how-to-remove-node-type.md)

另請參閱：

* [範例:升級叢集節點以使用 Azure 託管磁碟](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage)

* [垂直調整考量](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations)