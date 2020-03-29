---
title: 停止使用託管標識 VM 擴展 - Azure AD
description: 逐步說明停止使用 VM 擴展並開始使用 Azure 實例中繼資料服務 （IMDS） 進行身份驗證。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/25/2018
ms.author: markvi
ms.openlocfilehash: 01b8e1dbc290bed86ccfc3c7016e8bd9168e427a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049073"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>如何停止使用虛擬機器託管標識擴展並開始使用 Azure 實例中繼資料服務

## <a name="virtual-machine-extension-for-managed-identities"></a>託管標識的虛擬機器擴展

託管標識的虛擬機器擴展用於為虛擬機器中的託管標識請求權杖。 工作流包括以下步驟：

1. 首先，資源中的工作負載調用本地終結點`http://localhost/oauth2/token`以請求訪問權杖。
2. 然後，虛擬機器擴展使用託管標識的憑據從 Azure AD 請求訪問權杖。 
3. 訪問權杖將返回到調用方，並可用於對支援 Azure AD 身份驗證的服務（如 Azure 金鑰保存庫或 Azure 存儲）進行身份驗證。

由於下一節中概述了幾個限制，託管標識 VM 擴展已被棄用，轉而在 Azure 實例中繼資料服務 （IMDS） 中使用等效終結點

### <a name="provision-the-extension"></a>預配擴展 

將虛擬機器或虛擬機器規模集配置為具有託管標識時，可以選擇選擇使用`-Type`[Set-AzVM 擴展](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension)Cmdlet 上的參數為 Azure 資源 VM 擴展預配託管標識。 您可以傳遞 或`ManagedIdentityExtensionForWindows``ManagedIdentityExtensionForLinux`，具體取決於虛擬機器的類型，並使用 參數`-Name`命名它。 `-Settings` 參數會指定 OAuth 權杖端點所使用的連接埠，以用來取得權杖：

```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

您還可以使用 Azure 資源管理器部署範本來預配 VM 擴展，將以下 JSON 添加到範本的`resources`分區（用於`ManagedIdentityExtensionForLinux`Linux 版本的名稱和類型元素）。

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
    
    
如果使用虛擬機器縮放集，還可以使用[Add-AzVms 擴展](/powershell/module/az.compute/add-azvmssextension)Cmdlet 為 Azure 資源虛擬機器擴展預配託管標識。 您可以傳遞 或`ManagedIdentityExtensionForWindows``ManagedIdentityExtensionForLinux`，具體取決於虛擬機器縮放集的類型，並使用 參數`-Name`命名它。 `-Settings` 參數會指定 OAuth 權杖端點所使用的連接埠，以用來取得權杖：

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
要使用 Azure 資源管理器部署範本預配虛擬機器縮放集擴展，將以下 JSON 添加到範本`extensionpProfile`的分區（用於`ManagedIdentityExtensionForLinux`Linux 版本的名稱和類型元素）。

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

由於 DNS 查找失敗，虛擬機器擴展的預配可能會失敗。 如果發生這種情況，請重新開機虛擬機器，然後重試。 

### <a name="remove-the-extension"></a>移除擴充功能 
要`-n ManagedIdentityExtensionForWindows`刪除擴展，請使用或`-n ManagedIdentityExtensionForLinux`切換（取決於虛擬機器的類型），以及 az vm[擴展刪除](https://docs.microsoft.com/cli/azure/vm/)，或使用 Azure CLI 或`Remove-AzVMExtension`Powershell 刪除的虛擬機器縮放集的 az [vmss 擴展](https://docs.microsoft.com/cli/azure/vmss)：

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```powershell
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>使用虛擬機器擴展獲取權杖

以下是使用 Azure 資源 VM 擴展終結點的託管標識的示例請求：

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| 元素 | 描述 |
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

| 元素 | 描述 |
| ------- | ----------- |
| `access_token` | 所要求的存取權杖。 呼叫受保護的 REST API 時，權杖會內嵌在 `Authorization` 要求標頭欄位中成為「持有人」權杖，以允許 API 驗證呼叫端。 | 
| `refresh_token` | 並未由 Azure 資源受控識別使用。 |
| `expires_in` | 存取權杖從發行到過期之前持續有效的秒數。 在權杖的 `iat` 宣告中可找到發行時間。 |
| `expires_on` | 存取權杖到期的時間範圍。 日期以 "1970-01-01T0:0:0Z UTC" 起算的秒數表示 (對應至權杖的 `exp` 宣告)。 |
| `not_before` | 存取權杖生效且可被接受的時間範圍。 日期以 "1970-01-01T0:0:0Z UTC" 起算的秒數表示 (對應至權杖的 `nbf` 宣告)。 |
| `resource` | 要求存取權杖所針對的資源，符合要求的 `resource` 查詢字串參數。 |
| `token_type` | 權杖的類型，即「持有人」存取權杖，表示此權杖的持有人可以存取資源。 |


### <a name="troubleshoot-the-virtual-machine-extension"></a>解決虛擬機器擴展故障 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>故障後重新開機虛擬機器擴展

在特定版本的 Windows 和 Linux 上，擴充功能停止時，可手動用下列 Cmdlet 來重新啟動：

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

其中： 
- Windows 的副檔名稱和類型是：`ManagedIdentityExtensionForWindows`
- Linux 的副檔名稱和類型是：`ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>嘗試使用 Azure 資源適用受控識別擴充功能的結構描述匯出時，「自動化指令碼」會失敗

在虛擬機器上啟用 Azure 資源的託管標識時，嘗試對虛擬機器或其資源組使用"自動化腳本"功能時將顯示以下錯誤：

![Azure 資源適用受控識別自動化指令碼匯出錯誤](./media/howto-migrate-vm-extension/automation-script-export-error.png)

Azure 資源虛擬機器擴展的託管標識當前不支援將其架構匯出到資源組範本的能力。 因此，產生的範本不會顯示可在資源上啟用 Azure 資源適用受控識別的設定參數。 可以通過按照[使用範本在 Azure 虛擬機器上為 Azure 資源配置託管標識](qs-configure-template-windows-vm.md)中的示例手動添加這些部分。

當架構匯出功能可用於 Azure 資源虛擬機器擴展的託管標識（計畫于 2019 年 1 月棄用），它將在包含 VM[擴展的匯出資源組中](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions)列出。

## <a name="limitations-of-the-virtual-machine-extension"></a>虛擬機器擴展的限制 

使用虛擬機器擴展有幾個主要限制。 

 * 最嚴重的限制是用於請求權杖的憑據存儲在虛擬機器上。 成功破壞虛擬機器的攻擊者可以洩露憑據。 
 * 此外，虛擬機器擴展仍然不受多個 Linux 發行版本的支援，在每個發行版本上修改、構建和測試擴展需要巨大的開發成本。 目前，僅支援以下 Linux 發行版本： 
    * CoreOS Stable
    * CentOS 7.1 
    * Red Hat 7.2 
    * Ubuntu 15.04 
    * Ubuntu 16.04
 * 使用託管標識部署虛擬機器會影響性能，因為還必須預配虛擬機器擴展。 
 * 最後，虛擬機器擴展只能支援每個虛擬機器具有 32 個使用者分配的託管標識。 

## <a name="azure-instance-metadata-service"></a>Azure 執行個體中繼資料服務

[Azure 實例中繼資料服務 （IMDS）](/azure/virtual-machines/windows/instance-metadata-service)是一個 REST 終結點，提供有關運行虛擬機器實例的資訊，可用於管理和配置虛擬機器。 終結點在已知不可路由 IP 位址 （`169.254.169.254`） 上可用，該位址只能在虛擬機器內訪問。

使用 Azure IMDS 請求權杖有幾個優點。 

1. 該服務是虛擬機器的外部，因此託管標識使用的憑據不再存在於虛擬機器上。 相反，它們託管並固定在 Azure 虛擬機器的主機上。   
2. Azure IaaS 上支援的所有 Windows 和 Linux 作業系統都可以使用託管標識。
3. 部署更快、更容易，因為不再需要預配 VM 擴展。
4. 使用 IMDS 終結點，最多可以將 1000 個使用者分配的託管標識分配給單個虛擬機器。
5. 與使用虛擬機器擴展的請求相比，使用 IMDS 的請求沒有重大更改，因此，在目前使用虛擬機器擴展的現有部署上移植起來相當簡單。

出於這些原因，一旦虛擬化電腦擴展被棄用，Azure IMDS 服務將是請求權杖的實際方式。 


## <a name="next-steps"></a>後續步驟

* [如何在 Azure 虛擬機器上對 Azure 資源使用託管標識來獲取訪問權杖](how-to-use-vm-token.md)
* [Azure 實例中繼資料服務](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)
