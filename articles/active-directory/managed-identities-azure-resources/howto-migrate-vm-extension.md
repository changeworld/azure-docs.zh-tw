---
title: 停止使用受控識別 VM 擴充功能-Azure AD
description: 停止使用 VM 擴充功能，並開始使用 Azure Instance Metadata Service (IMDS) 進行驗證的逐步指示。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/25/2018
ms.author: barclayn
ms.openlocfilehash: 5b298767f9814f76dd606bab29bd0b245dad6937
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89260181"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>如何停止使用虛擬機器受控識別擴充功能，並開始使用 Azure Instance Metadata Service

## <a name="virtual-machine-extension-for-managed-identities"></a>受控識別的虛擬機器擴充功能

受控識別的虛擬機器擴充功能是用來在虛擬機器內要求受控識別的權杖。 工作流程包含下列步驟：

1. 首先，資源內的工作負載會呼叫本機端點 `http://localhost/oauth2/token` 來要求存取權杖。
2. 虛擬機器擴充功能接著會使用受控識別的認證，以向 Azure AD 要求存取權杖。 
3. 存取權杖會傳回給呼叫者，可用來向支援 Azure AD authentication 的服務（例如 Azure Key Vault 或 Azure 儲存體）進行驗證。

由於下一節中所述的數個限制，受控識別 VM 擴充功能已被取代，以使用 Azure Instance Metadata Service (IMDS 中的對等端點) 

### <a name="provision-the-extension"></a>布建延伸模組 

當您將虛擬機器或虛擬機器擴展集設定為具有受控識別時，您可以選擇使用 >remove-azvmextension 指令程式上的參數來布建 Azure 資源受控識別 VM 擴充 `-Type` [功能](/powershell/module/az.compute/set-azvmextension) 。 您可以傳遞 `ManagedIdentityExtensionForWindows` 或 `ManagedIdentityExtensionForLinux` （視虛擬機器的類型而定），並使用 `-Name` 參數命名。 `-Settings` 參數會指定 OAuth 權杖端點所使用的連接埠，以用來取得權杖：

```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

您也可以使用 Azure Resource Manager 部署範本來布建 VM 擴充功能，方法是將下列 JSON 新增至 `resources` 範本的區段中 (用於 `ManagedIdentityExtensionForLinux` Linux 版本) 的名稱和類型元素。

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
    "apiVersion": "2018-06-01",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.ManagedIdentity",
        "type": "ManagedIdentityExtensionForWindows",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "port": 50342
        }
    }
}
```
    
    
如果您使用的是虛擬機器擴展集，也可以使用 [>add-azvmssextension 指令程式](/powershell/module/az.compute/add-azvmssextension) 來布建 Azure 資源受控識別虛擬機器擴展集擴充功能。 您可以傳遞 `ManagedIdentityExtensionForWindows` 或 `ManagedIdentityExtensionForLinux` ，視虛擬機器擴展集的類型而定，然後使用參數將它命名為 `-Name` 。 `-Settings` 參數會指定 OAuth 權杖端點所使用的連接埠，以用來取得權杖：

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
若要使用 Azure Resource Manager 部署範本布建虛擬機器擴展集擴充功能，請將下列 JSON 新增至範本的區段中， `extensionpProfile` (用於 `ManagedIdentityExtensionForLinux` Linux 版本) 的名稱和類型元素。

```json
"extensionProfile": {
    "extensions": [
        {
            "name": "ManagedIdentityWindowsExtension",
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
                },
                "protectedSettings": {}
            }
        }
```

布建虛擬機器擴充功能可能會因為 DNS 查閱失敗而失敗。 如果發生這種情況，請重新開機虛擬機器，然後再試一次。 

### <a name="remove-the-extension"></a>移除擴充功能 
若要移除擴充功能，請使用 `-n ManagedIdentityExtensionForWindows` 或 `-n ManagedIdentityExtensionForLinux` 切換 (，視使用 [az vm extension delete](/cli/azure/vm/)的虛擬機器) 類型，或使用 Azure CLI 或適用于 Powershell 的虛擬機器擴展集的 [az vmss 延伸模組刪除](/cli/azure/vmss) 而定 `Remove-AzVMExtension` ：

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```powershell
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>使用虛擬機器擴充功能取得權杖

以下是使用 Azure 資源受控識別 VM 擴充功能端點的範例要求：

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| 項目 | 描述 |
| ------- | ----------- |
| `GET` | HTTP 指令動詞，指出您想要擷取端點中的資料。 在此案例中是 OAuth 存取權杖。 | 
| `http://localhost:50342/oauth2/token` | Azure 資源受控識別端點，其中 50342 是預設連接埠且可設定。 |
| `resource` | 查詢字串參數，指出目標資源的應用程式識別碼 URI。 也會出現在所核發權杖的 `aud` (對象) 宣告中。 此範例會要求用來存取 Azure Resource Manager 的權杖，其中包含應用程式識別碼 URI `https://management.azure.com/`。 |
| `Metadata` | HTTP 要求標頭欄位，Azure 資源受控識別需要此元素以減輕伺服器端偽造要求 (SSRF) 攻擊。 此值必須設定為 "true" (全部小寫)。|
| `object_id` | (選擇性) 查詢字串參數，指出要使用權杖的受控識別 object_id。 如果您的 VM 有多個使用者指派的受控識別，這會是必要項目。|
| `client_id` | (選擇性) 查詢字串參數，指出要使用權杖的受控識別 client_id。 如果您的 VM 有多個使用者指派的受控識別，這會是必要項目。|


範例回應：

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| 項目 | 描述 |
| ------- | ----------- |
| `access_token` | 所要求的存取權杖。 呼叫受保護的 REST API 時，權杖會內嵌在 `Authorization` 要求標頭欄位中成為「持有人」權杖，以允許 API 驗證呼叫端。 | 
| `refresh_token` | 並未由 Azure 資源受控識別使用。 |
| `expires_in` | 存取權杖從發行到過期之前持續有效的秒數。 在權杖的 `iat` 宣告中可找到發行時間。 |
| `expires_on` | 存取權杖到期的時間範圍。 日期以 "1970-01-01T0:0:0Z UTC" 起算的秒數表示 (對應至權杖的 `exp` 宣告)。 |
| `not_before` | 存取權杖生效且可被接受的時間範圍。 日期以 "1970-01-01T0:0:0Z UTC" 起算的秒數表示 (對應至權杖的 `nbf` 宣告)。 |
| `resource` | 要求存取權杖所針對的資源，符合要求的 `resource` 查詢字串參數。 |
| `token_type` | 權杖的類型，即「持有人」存取權杖，表示此權杖的持有人可以存取資源。 |


### <a name="troubleshoot-the-virtual-machine-extension"></a>針對虛擬機器擴充功能進行疑難排解 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>在失敗後重新開機虛擬機器擴充功能

在特定版本的 Windows 和 Linux 上，擴充功能停止時，可手動用下列 Cmdlet 來重新啟動：

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

其中： 
- Windows 的擴充功能名稱和類型為： `ManagedIdentityExtensionForWindows`
- Linux 的擴充功能名稱和類型是： `ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>嘗試使用 Azure 資源適用受控識別擴充功能的結構描述匯出時，「自動化指令碼」會失敗

當虛擬機器上啟用適用于 Azure 資源的受控識別時，當您嘗試使用虛擬機器或其資源群組的「自動化腳本」功能時，會顯示下列錯誤：

![Azure 資源適用受控識別自動化指令碼匯出錯誤](./media/howto-migrate-vm-extension/automation-script-export-error.png)

適用于 Azure 資源的受控識別虛擬機器擴充功能目前不支援將其架構匯出至資源群組範本的功能。 因此，產生的範本不會顯示可在資源上啟用 Azure 資源適用受控識別的設定參數。 您可以遵循 [使用範本在 azure 虛擬機器上設定 azure 資源受控](qs-configure-template-windows-vm.md)識別中的範例，手動新增這些區段。

當架構匯出功能變成可供適用于 Azure 資源的受控識別虛擬機器擴充功能 (預計在2019年1月) 淘汰時，將會在 [匯出包含 VM 擴充功能的資源群組](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions)中列出。

## <a name="limitations-of-the-virtual-machine-extension"></a>虛擬機器擴充功能的限制 

使用虛擬機器擴充功能有幾個主要的限制。 

 * 最嚴重的限制就是用來要求權杖的認證會儲存在虛擬機器上。 成功入侵虛擬機器的攻擊者可以竊取認證。 
 * 此外，數個 Linux 散發套件仍不支援虛擬機器擴充功能，在這些散發套件上修改、建立及測試擴充功能的開發成本相當龐大。 目前僅支援下列 Linux 發行版本： 
    * CoreOS Stable
    * CentOS 7.1 
    * Red Hat 7.2 
    * Ubuntu 15.04 
    * Ubuntu 16.04
 * 部署具有受控識別的虛擬機器會對效能造成影響，因為也必須布建虛擬機器延伸模組。 
 * 最後，虛擬機器延伸模組只能支援每部虛擬機器有32個使用者指派的受控識別。 

## <a name="azure-instance-metadata-service"></a>Azure 執行個體中繼資料服務

[Azure Instance Metadata Service (IMDS) ](../../virtual-machines/windows/instance-metadata-service.md)是 REST 端點，可提供執行虛擬機器實例的相關資訊，可用來管理和設定您的虛擬機器。 端點可以在已知的非可路由 IP 位址上取得， (只能 `169.254.169.254` 從虛擬機器記憶體取) 。

使用 Azure IMDS 來要求權杖有幾個優點。 

1. 這是虛擬機器外部的服務，因此受控識別所使用的認證將不再存在於虛擬機器上。 相反地，它們會在 Azure 虛擬機器的主機電腦上託管和保護。   
2. Azure IaaS 支援的所有 Windows 和 Linux 作業系統都可以使用受控識別。
3. 因為不再需要布建 VM 擴充功能，所以部署速度更快且更容易。
4. 使用 IMDS 端點，最多可將1000個使用者指派的受控識別指派給單一虛擬機器。
5. 使用 IMDS 而不是使用虛擬機器擴充功能對要求進行重大變更，因此使用虛擬機器擴充功能來移植現有的部署相當簡單。

基於這些理由，當虛擬機器擴充功能淘汰之後，Azure IMDS 服務將會是要求權杖的實際方法。 


## <a name="next-steps"></a>後續步驟

* [如何在 Azure 虛擬機器上使用 Azure 資源的受控識別來取得存取權杖](how-to-use-vm-token.md)
* [Azure 執行個體中繼資料服務](../../virtual-machines/windows/instance-metadata-service.md)