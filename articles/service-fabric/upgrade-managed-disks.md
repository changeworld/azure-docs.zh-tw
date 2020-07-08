---
title: 升級叢集節點以使用 Azure 受控磁片
description: 以下說明如何將現有的 Service Fabric 叢集升級為使用 Azure 受控磁片，但您的叢集幾乎不會停機。
ms.topic: how-to
ms.date: 4/07/2020
ms.openlocfilehash: 46dec6ae29fdd8f2a418f695c31900e6df4483e1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85611623"
---
# <a name="upgrade-cluster-nodes-to-use-azure-managed-disks"></a>升級叢集節點以使用 Azure 受控磁片

[Azure 受控磁片](../virtual-machines/windows/managed-disks-overview.md)是建議的磁片儲存體供應專案，可與 azure 虛擬機器搭配使用，以進行資料的持續性儲存。 您可以升級做為節點類型基礎的虛擬機器擴展集，以使用受控磁片，藉此改善 Service Fabric 工作負載的復原能力。 以下說明如何將現有的 Service Fabric 叢集升級為使用 Azure 受控磁片，但您的叢集幾乎不會停機。

將 Service Fabric 叢集節點升級為使用受控磁片的一般策略是：

1. 部署該節點類型的其他重複虛擬機器擴展集，但將[managedDisk](https://docs.microsoft.com/azure/templates/microsoft.compute/2019-07-01/virtualmachinescalesets/virtualmachines#ManagedDiskParameters)物件新增至 `osDisk` 虛擬機器擴展集部署範本的區段。 新的擴展集應系結至與原始相同的負載平衡器/IP，讓您的客戶不會在遷移期間遇到服務中斷的情況。

2. 一旦原始和升級的擴展集並存執行，請一次停用一個原始節點實例，讓系統服務（或具狀態服務的複本）遷移至新的擴展集。

3. 確認叢集和新節點的狀況良好，然後移除已刪除節點的原始擴展集和節點狀態。

本文將逐步引導您升級範例叢集的主要節點類型，以使用受控磁片，同時避免任何叢集停機（請參閱下面的附注）。 範例測試叢集的初始狀態是由一個具有五個節點的單一擴展集所支援的[銀級耐久性](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)節點類型所組成。

> [!CAUTION]
> 只有當您有叢集 DNS 的相依性（例如存取[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)時）時，才會遇到此程式中斷的情況。 [前端服務的架構最佳做法](https://docs.microsoft.com/azure/architecture/microservices/design/gateway)是在您的節點類型前面有某種類型的[負載平衡器](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)，讓節點交換可行而不會中斷。

以下是我們將用來完成升級案例之 Azure Resource Manager 的[範本和 Cmdlet](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage) 。 在下面的[主要節點類型部署升級的擴展集](#deploy-an-upgraded-scale-set-for-the-primary-node-type)時，將會說明範本的變更。

## <a name="set-up-the-test-cluster"></a>設定測試叢集

讓我們設定初始的 Service Fabric 測試叢集。 首先，[下載](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage)要用來完成此案例的 Azure Resource Manager 範例範本。

接著，登入您的 Azure 帳戶。

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

下列命令會引導您產生新的自我簽署憑證，並部署測試叢集。 如果您已經有想要使用的憑證，請跳至[使用現有的憑證來部署](#use-an-existing-certificate-to-deploy-the-cluster)叢集。

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>產生自我簽署憑證及部署叢集

首先，指派 Service Fabric 叢集部署所需的變數。 針對您的特定帳戶和環境，調整 `resourceGroupName`、`certSubjectName`、`parameterFilePath` 和 `templateFilePath` 的值：

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
> 執行命令之前，確保 `certOutputFolder` 位置存在於本機電腦上，以部署新的 Service Fabric 叢集。

接下來，開啟檔案[*上的Initial-1NodeType-UnmanagedDisks.parameters.js*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) ，並調整和的值， `clusterName` `dnsName` 以對應至您在 PowerShell 中設定的動態值，並儲存您的變更。

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

作業會傳回憑證指紋，您將用它來連線[到新](#connect-to-the-new-cluster-and-check-health-status)叢集並檢查其健康狀態。 （略過下一節，這是叢集部署的替代方法）。

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>使用現有的憑證來部署叢集

您也可以使用現有的 Azure Key Vault 憑證來部署測試叢集。 若要這麼做，您必須[取得 Key Vault](#obtain-your-key-vault-references)和憑證指紋的參考。

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

開啟檔案[*上的Initial-1NodeType-UnmanagedDisks.parameters.js*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) ，並將和的值變更 `clusterName` `dnsName` 為唯一的。

最後，指定叢集的資源組名，並設定 `templateFilePath` `parameterFilePath` *初始 1NodeType UnmanagedDisks*檔案的和位置：

> [!NOTE]
> 指定的資源群組必須已存在，且位於與您 Key Vault 相同的區域中。

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"
```

最後，執行下列命令來部署初始測試叢集：

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

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>連線到新叢集並檢查健全狀態

連接到叢集，並確定其所有節點都是狀況良好（取代您叢集的 `clusterName` 和 `thumb` 變數）：

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

## <a name="deploy-an-upgraded-scale-set-for-the-primary-node-type"></a>為主要節點類型部署升級的擴展集

為了升級或*垂直調整*節點類型，我們必須部署該節點類型的虛擬機器擴展集複本，這與原始的擴展集相同（包括相同 `nodeTypeRef` 、和的參考）， `subnet` `loadBalancerBackendAddressPools` 不同之處在于它包含所需的升級/變更和自己的個別子網和輸入 NAT 位址集區。 因為我們要升級主要節點類型，所以新的擴展集將會標示為主要（ `isPrimary: true` ），就像原始的擴展集一樣。 （針對非主要節點類型升級，請直接省略此程式）。

為了方便起見，您已在*1NodeType-2ScaleSets-ManagedDisks* [範本](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.json)和[參數](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json)檔案中為您進行必要的變更。

下列各節將詳細說明範本的變更。 如果您想要的話，可以略過說明，並繼續進行升級程式的[下一個步驟](#obtain-your-key-vault-references)。

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>以升級的擴展集更新叢集範本

以下是針對主要節點類型新增升級擴展集的原始叢集部署範本的逐區段修改。

#### <a name="parameters"></a>參數

為新擴展集的實例名稱、計數和大小新增參數。 請注意， `vmNodeType1Name` 對於新的擴展集而言是唯一的，而 [計數] 和 [大小] 值則與原始的擴展集相同。

**範本檔案**

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

在 [部署範本] `variables` 區段中，為新擴展集的輸入 NAT 位址集區新增專案。

**範本檔案**

```json
"lbNatPoolID1": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool1')]", 
```

### <a name="resources"></a>資源

在 [部署範本*資源*] 區段中，新增新的虛擬機器擴展集，並記住下列事項：

* 新的擴展集會參考與原始相同的節點類型：

    ```json
    "nodeTypeRef": "[parameters('vmNodeType0Name')]",
    ```

* 新的擴展集會參考相同的負載平衡器後端位址和子網（但使用不同的負載平衡器輸入 NAT 集區）：

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

* 就像原始的擴展集一樣，新的擴展集會標示為主要節點類型。 （升級非主要節點類型時，請省略此變更。）

    ```json
    "isPrimary": true,
    ```

* 不同于原始擴展集，新的擴展集會升級為使用受控磁片。

    ```json
    "managedDisk": {
        "storageAccountType": "[parameters('storageAccountType')]"
    }
    ```

在您完成範本和參數檔案中的所有變更之後，請繼續下一節以取得您的 Key Vault 參照，並將更新部署至您的叢集。

### <a name="obtain-your-key-vault-references"></a>取得您的 Key Vault 參考

若要部署更新的設定，您必須先取得 Key Vault 中儲存的叢集憑證的幾個參考。 尋找這些值最簡單的方式是透過 Azure 入口網站。 您需要：

* **叢集憑證的 Key Vault URL。** 從您在 Azure 入口網站的 Key Vault 中，選取 [**憑證**  >  *您所需的憑證*  >  **秘密識別碼**]：

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **叢集憑證的指紋。** （如果您[連線到初始](#connect-to-the-new-cluster-and-check-health-status)叢集以檢查其健康狀態，可能就已經有這種情況）。在 Azure 入口網站中，從相同的憑證分頁（**憑證**  >  *您所需的憑證*）複製**x.509 sha-1 指紋（十六進位）**：

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **Key Vault 的資源識別碼。** 從您在 Azure 入口網站的 Key Vault 中，選取 [**屬性**] [  >  **資源識別碼**]：

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>部署更新的範本

視需要調整 `parameterFilePath` 和 `templateFilePath`，然後執行以下命令：

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

當部署完成時，請再次檢查叢集健康情況，並確保所有十個節點（在新擴展集的原始和5上為5個）狀況良好。

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-scale-set"></a>將種子節點遷移至新的擴展集

我們現在已準備好開始停用原始擴展集的節點。 隨著這些節點變為停用，系統服務和種子節點會遷移到新擴展集的 VM，因為其也會標示為主要節點類型。

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

使用 Service Fabric Explorer 來監視種子節點至新擴展集的移轉，以及原始擴展集中節點從 [停用] 到 [停用] 狀態的進度。

![顯示已停用節點狀態的 Service Fabric Explorer](./media/upgrade-managed-disks/service-fabric-explorer-node-status.png)

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

![顯示處於錯誤狀態的已停用節點 Service Fabric Explorer](./media/upgrade-managed-disks/service-fabric-explorer-disabled-nodes-error-state.png)

從 Service Fabric 叢集移除過時的節點，將叢集健全狀況狀態還原為 *[確定]*。

```powershell
# Remove node states for the deleted scale set
foreach($name in $nodeNames){
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

![已移除錯誤狀態下的關閉節點的 Service Fabric Explorer](./media/upgrade-managed-disks/service-fabric-explorer-healthy-cluster.png)

## <a name="next-steps"></a>後續步驟

在本逐步解說中，您已瞭解如何將 Service Fabric 叢集的虛擬機器擴展集升級為使用受控磁片，同時避免在處理期間發生服務中斷。 如需相關主題的詳細資訊，請參閱下列資源。

了解如何：

* [擴大 Service Fabric 叢集主要節點類型](service-fabric-scale-up-node-type.md)

* [將擴展集範本轉換為使用受控磁片](../virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md.md)

* [移除 Service Fabric 節點類型](service-fabric-how-to-remove-node-type.md)

另請參閱：

* [範例：升級叢集節點以使用 Azure 受控磁片](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage)

* [垂直調整考量](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations)