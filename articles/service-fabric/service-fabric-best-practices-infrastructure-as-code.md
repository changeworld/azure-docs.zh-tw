---
title: Azure 服務結構基礎結構作為代碼最佳實踐
description: 將 Azure 服務結構作為基礎結構作為代碼管理的最佳做法和設計注意事項。
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 1c044d5fd973d3c577088a887f2fac413d2ab79d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551806"
---
# <a name="infrastructure-as-code"></a>基礎結構即程式碼

在生產案例中，使用 Resource Manager 範本建立 Azure Service Fabric 叢集。 Resource Manager 範本可提供更好的資源屬性控制，並可確保您擁有一致的資源模型。

您可以在 [GitHub 上的 Azure 範例](https://github.com/Azure-Samples/service-fabric-cluster-templates)中取得可供 Windows 和 Linux 使用的 Resource Manager 範本範例。 這些範本可以用作叢集範本的起點。 下載 `azuredeploy.json` 和 `azuredeploy.parameters.json`，並編輯它們以符合您的自訂需求。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要部署您在上面下載的 `azuredeploy.json` 和 `azuredeploy.parameters.json` 範例，請使用下列 Azure CLI 命令：

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az group deployment create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

使用 PowerShell 建立資源

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Azure Service Fabric 資源

您可以透過 Azure Resource Manager 將應用程式和服務部署到 Service Fabric 叢集。 請參閱[將應用程式和服務視為 Azure Resource Manager 資源進行管理](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-arm-resource)了解詳細資訊。 以下是在 Resource Manager 範本資源中包含 Service Fabric 應用程式特定資源的最佳做法。

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

若要 Azure Resource Manager 部署您的應用程式，必須先[建立 sfpkg](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg) Service Fabric 應用程式封裝。 以下 python 指令碼為示範如何建立 sfpkg 的範例：

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(
    self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(
            root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Azure 虛擬機器作業系統自動升級配置 
升級虛擬機器是使用者啟動的操作，建議您使用[虛擬機器縮放集自動作業系統升級](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)進行 Azure 服務結構群集主機修補程式管理;修補程式業務流程應用程式是一種替代解決方案，用於在 Azure 外部託管時，儘管 POA 可在 Azure 中使用，而在 Azure 中託管 POA 的開銷是首選虛擬機器作業系統自動升級而不是 POA 的常見原因。 以下是啟用自動作業系統升級的計算虛擬機器規模集資源管理器範本屬性：

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
將自動作業系統升級與服務交換矩陣一起使用時，將一次推出一個更新域，以保持服務交換矩陣中運行的服務的高可用性。 若要在 Service Fabric 中利用自動 OS 升級，您的叢集必須設定為使用銀級耐久性層或更高。

確保以下登錄機碼設置為 false，以防止視窗主機啟動不協調的更新：HKEY_LOCAL_MACHINE_SOFTWARE_策略\Microsoft_Windows_WindowsUpdate_AU。

以下是計算虛擬機器縮放集資源管理器範本屬性，用於將 WindowsUpdate 登錄機碼設置為 false：
```json
"osProfile": {
        "computerNamePrefix": "{vmss-name}",
        "adminUsername": "{your-username}",
        "secrets": [],
        "windowsConfiguration": {
          "provisionVMAgent": true,
          "enableAutomaticUpdates": false
        }
      },
```

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Azure 服務結構群集升級配置
以下是啟用自動升級的服務結構群集資源管理器範本屬性：
```json
"upgradeMode": "Automatic",
```
要手動升級群集，請將 cab/deb 分發下載到群集虛擬機器，然後調用以下 PowerShell：
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>後續步驟

* 在執行 Windows Server 的 VM 或電腦上建立叢集： [建立適用於 Windows Server 的 Service Fabric 叢集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* 在 VM 或執行 Linux 的電腦上建立叢集：[建立 Linux 叢集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* 瞭解[服務交換矩陣支援選項](service-fabric-support.md)
