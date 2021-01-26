---
title: '部署 Azure 雲端服務 (延伸支援的必要條件) '
description: '部署 Azure 雲端服務 (延伸支援的必要條件) '
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: f112d0e96c6ff0caf3c5e3762304158f70963f14
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787024"
---
# <a name="prerequisites-for-deploying-azure-cloud-services-extended-support"></a>部署 Azure 雲端服務 (延伸支援的必要條件) 

> [!IMPORTANT]
> 雲端服務 (延伸支援) 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

為確保雲端服務成功 (延伸支援) 部署請參閱下列步驟，並在嘗試進行任何部署之前完成每個專案。 

## <a name="register-the-cloudservices-feature"></a>註冊 CloudServices 功能
註冊訂用帳戶的功能。 註冊可能需要幾分鐘的時間才能完成。 

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```

使用下列程式來檢查註冊狀態：  
```powershell
Get-AzProviderFeature 

#Sample output
FeatureName               ProviderName      RegistrationState
CloudServices           Microsoft.Compute    Registered
```

## <a name="required-service-configuration-cscfg-file-updates"></a>必要的服務設定 ( .cscfg) 檔案更新

### <a name="1-virtual-network"></a>1) 虛擬網路
雲端服務 (延伸支援) 部署必須在虛擬網路中。 您可以透過 [Azure 入口網站](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)、 [PowerShell](https://docs.microsoft.com/azure/virtual-network/quick-create-powershell)、 [Azure CLI](https://docs.microsoft.com/azure/virtual-network/quick-create-cli) 或 [ARM 範本](https://docs.microsoft.com/azure/virtual-network/quick-create-template)來建立虛擬網路。 您也必須在服務設定中參考虛擬網路和子網 ( .cscfg) 在 [NetworkConfiguration](schema-cscfg-networkconfiguration.md) 區段下。 

如果虛擬網路屬於與雲端服務相同的資源群組，則只參考服務設定中的虛擬網路名稱 ( .cscfg) 檔案已足夠。 如果虛擬網路和雲端服務位於兩個不同的資源群組中，則必須在服務設定 ( .cscfg) 檔案中指定虛擬網路的完整 Azure Resource Manager 識別碼。
 
#### <a name="virtual-network-located-in-same-resource-group"></a>位於相同資源群組中的虛擬網路
```xml
<VirtualNetworkSite name="<vnet-name>"/> 
  <AddressAssignments> 
    <InstanceAddress roleName="<role-name>"> 
     <Subnets> 
       <Subnet name="<subnet-name>"/> 
     </Subnets> 
    </InstanceAddress> 
```

#### <a name="virtual-network-located-in-different-resource-group"></a>位於不同資源群組中的虛擬網路
```xml
<VirtualNetworkSite name="/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.Network/virtualNetworks/<vnet-name>"/> 
   <AddressAssignments> 
     <InstanceAddress roleName="<role-name>"> 
       <Subnets> 
        <Subnet name="<subnet-name>"/> 
       </Subnets> 
     </InstanceAddress> 
```
### <a name="2-remove-the-old-plugins"></a>2) 移除舊的外掛程式

從服務設定 ( .cscfg) 檔案中移除舊的遠端桌面設定。  

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="gachandw" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="XXXX" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="2021-12-17T23:59:59.0000000+05:30" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" /> 
```

## <a name="required-service-definition-file-csdef-updates"></a>必要的服務定義檔 ( 的) 更新

### <a name="1-virtual-machine-sizes"></a>1) 虛擬機器大小
以下是 Azure Resource Manager 中已淘汰的大小。 但是，如果您想要繼續使用它們，請使用 `vmsize` 相關聯的 Azure Resource Manager 命名慣例來更新名稱。  

| 先前的大小名稱 | 更新的大小名稱 | 
|---|---|
| 特小型 | Standard_A0 | 
| 小 | Standard_A1 |
| 中 | Standard_A2 | 
| 大 | Standard_A3 | 
| 特大型 | Standard_A4 | 
| A5 | Standard_A5 | 
| A6 | Standard_A6 | 
| A7 | Standard_A7 |  
| A8 | Standard_A8 | 
| A9 | Standard_A9 |
| A10 | Standard_A10 | 
| A11 | Standard_A11 | 
| MSODSG5 | Standard_MSODSG5 | 

 例如，`<WorkerRole name="WorkerRole1" vmsize="Medium"` 會變成 `<WorkerRole name="WorkerRole1" vmsize="Standard_A2"`。
 
> [!NOTE]
> 若要取得可用大小的清單，請參閱 [資源 sku-列出](https://docs.microsoft.com/rest/api/compute/resourceskus/list) 並套用下列篩選： <br>
`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


### <a name="2-remove-old-remote-desktop-plugins"></a>2) 移除舊的遠端桌面外掛程式
使用舊的遠端桌面外掛程式的部署，必須將模組從服務定義移除 ( 的) 檔案和任何相關聯的憑證。 

```xml
<Imports> 
<Import moduleName="RemoteAccess" /> 
<Import moduleName="RemoteForwarder" /> 
</Imports> 
```

## <a name="key-vault-creation"></a>Key Vault 建立 

Key Vault 是用來儲存與雲端服務相關聯的憑證， (延伸支援) 。 將憑證新增至 Key Vault，然後參考服務設定檔中的憑證指紋。 您也需要啟用適當許可權的 Key Vault，讓雲端服務 (延伸支援) 資源可以從 Key Vault 中取出儲存為秘密的憑證。 Key Vault 可以透過 [Azure 入口網站](https://docs.microsoft.com/azure/key-vault/general/quick-create-portal)和  [PowerShell](https://docs.microsoft.com/azure/key-vault/general/quick-create-powershell)建立。 您必須在與雲端服務相同的區域和訂用帳戶中建立 Key Vault。 如需詳細資訊，請參閱 [使用憑證搭配 Azure 雲端服務 (延伸支援) ](certificates-and-key-vault.md)。

## <a name="next-steps"></a>後續步驟 
- 請參閱雲端服務的 [部署必要條件](deploy-prerequisite.md) (延伸支援) 。
- 使用 [Azure 入口網站](deploy-portal.md)、 [PowerShell](deploy-powershell.md)、 [範本](deploy-template.md) 或 [Visual Studio](deploy-visual-studio.md)，將雲端服務部署 (延伸支援) 。
- 請 [參閱雲端服務的常見問題，](faq.md) (延伸支援) 。
