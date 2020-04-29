---
title: Azure ExpressRoute：設定 MACsec
description: 本文可協助您設定 MACsec，以保護您的邊緣路由器與 Microsoft 邊緣路由器之間的連線。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: cherylmc
ms.openlocfilehash: 572147ca43e9a4dea9d9601dfa1dac8ba1c97ed0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81458227"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>在 ExpressRoute Direct 埠上設定 MACsec

本文可協助您設定 MACsec，以使用 PowerShell 來保護邊緣路由器與 Microsoft 邊緣路由器之間的連線。

## <a name="before-you-begin"></a>開始之前

在開始進行設定之前，請先確認下列情況︰

* 您瞭解[ExpressRoute 直接布建工作流程](expressroute-erdirect-about.md)。
* 您已建立[ExpressRoute Direct 埠資源](expressroute-howto-erdirect.md)。
* 如果您想要在本機執行 PowerShell，請確認電腦上已安裝最新版的 Azure PowerShell。

### <a name="working-with-azure-powershell"></a>使用 Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>登入並選取正確的訂用帳戶

若要開始設定，請登入您的 Azure 帳戶，然後選取您想要使用的訂用帳戶。

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1. 建立 Azure Key Vault、MACsec 秘密和使用者身分識別

1. 建立 Key Vault 實例，以將 MACsec 秘密儲存在新的資源群組中。

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    如果您已經有金鑰保存庫或資源群組，您可以重複使用它們。 不過，在現有的金鑰保存庫上啟用虛[**刪除**功能](../key-vault/general/overview-soft-delete.md)是很重要的。 如果未啟用虛刪除，您可以使用下列命令來啟用它：

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. 建立使用者身分識別。

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    如果 New-azuserassignedidentity 無法辨識為有效的 PowerShell Cmdlet，請安裝下列模組（在系統管理員模式中），然後重新執行上述命令。

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. 建立連線關聯金鑰（CAK）和連線關聯金鑰名稱（CKN），並將其儲存在金鑰保存庫中。

    ```azurepowershell-interactive
    $CAK = ConvertTo-SecureString "your_key" -AsPlainText -Force
    $CKN = ConvertTo-SecureString "your_key_name" -AsPlainText -Force
    $MACsecCAKSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CAK_name" -SecretValue $CAK
    $MACsecCKNSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CKN_name" -SecretValue $CKN
    ```
4. 將 [取得] 許可權指派給使用者身分識別。

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName "your_key_vault_name" -PermissionsToSecrets get -ObjectId $identity.PrincipalId
    ```

   現在，此身分識別可以從金鑰保存庫取得秘密（例如 CAK 和 CKN）。
5. 將此使用者識別設定為可供 ExpressRoute 使用。

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2. 在 ExpressRoute Direct 埠上設定 MACsec

### <a name="to-enable-macsec"></a>若要啟用 MACsec

每個 ExpressRoute Direct 實例都有兩個實體埠。 您可以選擇同時在這兩個埠上啟用 MACsec，或一次啟用一個埠上的 MACsec。 如果您的 ExpressRoute Direct 已在服務中，請在一段時間內執行一個埠（藉由將流量切換到作用中的埠），有助於將中斷的情況降到最低。

1. 設定 MACsec 秘密和密碼，並將使用者身分識別與埠相關聯，讓 ExpressRoute 管理程式碼可以視需要存取 MACsec 的秘密。

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[0]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[1]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.identity = $erIdentity
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```
2. 選擇性如果埠處於 [系統管理] 狀態，您可以執行下列命令來顯示埠。

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = "Enabled"
    $erDirect.Links[1].AdminState = "Enabled"
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    此時，會在 Microsoft 端的 ExpressRoute Direct 埠上啟用 MACsec。 如果您還沒有在邊緣裝置上進行設定，可以繼續使用相同的 MACsec 秘密和密碼來設定它們。

### <a name="to-disable-macsec"></a>若要停用 MACsec

如果您的 ExpressRoute Direct 實例上不再需要 MACsec，您可以執行下列命令來停用它。

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

此時，Microsoft 端的 ExpressRoute Direct 埠上已停用 MACsec。

### <a name="test-connectivity"></a>測試連線能力
在您的 ExpressRoute Direct 埠上設定 MACsec （包括 MACsec 金鑰更新）之後，請[檢查](expressroute-troubleshooting-expressroute-overview.md)線路的 BGP 會話是否已啟動且正在執行。 如果您還沒有埠上的線路，請先建立一個，並設定 Azure 私用對等互連或線路的 Microsoft 對等互連。 如果 MACsec 設定錯誤（包括 MACsec 金鑰不相符），在您的網路裝置與 Microsoft 的網路裝置之間，您將不會看到第2層的 ARP 解析和第3層的 BGP 建立。 如果所有專案都已正確設定，您應該會看到 BGP 路由以雙向方式正確公告，以及您的應用程式資料流程（透過 ExpressRoute）。

## <a name="next-steps"></a>後續步驟
1. [在 ExpressRoute Direct 上建立 ExpressRoute 線路](expressroute-howto-erdirect.md)
2. [將 ExpressRoute 線路連結到 Azure 虛擬網路](expressroute-howto-linkvnet-arm.md)
3. [確認 ExpressRoute 連線能力](expressroute-troubleshooting-expressroute-overview.md)
