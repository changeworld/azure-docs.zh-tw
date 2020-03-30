---
title: Azure 快速路由：配置 MACsec
description: 本文可説明您配置 MACsec，以確保邊緣路由器和 Microsoft 邊緣路由器之間的連接。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: cherylmc
ms.openlocfilehash: 626302845dfb4b19deb921675601818b35ab8edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083556"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>在快速路由直接埠上配置 MACsec

本文可説明您配置 MACsec，以使用 PowerShell 保護您的邊緣路由器和 Microsoft 邊緣路由器之間的連接。

## <a name="before-you-begin"></a>開始之前

在開始進行設定之前，請先確認下列情況︰

* 您瞭解[ExpressRoute 直接預配工作流](expressroute-erdirect-about.md)。
* 您已經創建了一個[ExpressRoute 直接埠資源](expressroute-howto-erdirect.md)。
* 如果要在本地運行 PowerShell，請驗證電腦上是否安裝了最新版本的 Azure PowerShell。

### <a name="working-with-azure-powershell"></a>使用 Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>登錄並選擇正確的訂閱

要啟動配置，請登錄到 Azure 帳戶並選擇要使用的訂閱。

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1. 創建 Azure 金鑰保存庫、MACsec 機密和使用者標識

1. 創建金鑰保存庫實例以在新資源組中存儲 MACsec 機密。

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    如果您已經擁有金鑰保存庫或資源組，則可以重用它們。 但是，在現有的金鑰保存庫上啟用[**虛刪除**功能](../key-vault/key-vault-ovw-soft-delete.md)至關重要。 如果未啟用虛刪除，則可以使用以下命令啟用它：

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. 創建使用者標識。

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    如果 New-AzUser 分配標識未識別為有效的 PowerShell Cmdlet，請安裝以下模組（在管理員模式下），然後重新運行上述命令。

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. 創建連接關聯金鑰 （CAK） 和連接關聯金鑰名稱 （CKN），並將它們存儲在金鑰保存庫中。

    ```azurepowershell-interactive
    $CAK = ConvertTo-SecureString "your_key" -AsPlainText -Force
    $CKN = ConvertTo-SecureString "your_key_name" -AsPlainText -Force
    $MACsecCAKSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CAK_name" -SecretValue $CAK
    $MACsecCKNSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CKN_name" -SecretValue $CKN
    ```
4. 將 GET 許可權分配給使用者標識。

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName "your_key_vault_name" -PermissionsToSecrets get -ObjectId $identity.PrincipalId
    ```

   現在，此標識可以從金鑰保存庫獲取機密，例如 CAK 和 CKN。
5. 將此使用者標識設置為 ExpressRoute 使用。

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2. 在快速路由直接埠上配置 MACsec

### <a name="to-enable-macsec"></a>啟用 MACsec

每個 ExpressRoute Direct 實例都有兩個物理埠。 您可以選擇同時在兩個埠上啟用 MACsec，也可以選擇一次在一個埠上啟用 MACsec。 一次執行一個埠（在維修另一個埠時將流量切換到工作端口）有助於將中斷降至最低，如果您的 ExpressRoute Direct 已在服務中。

1. 設置 MACsec 機密和密碼，並將使用者標識與埠相關聯，以便 ExpressRoute 管理代碼在需要時可以訪問 MACsec 機密。

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
2. （可選）如果埠處於"管理關閉"狀態，則可以運行以下命令來啟動埠。

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = "Enabled"
    $erDirect.Links[1].AdminState = "Enabled"
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    此時，MACsec 在 Microsoft 端的 ExpressRoute 直接埠上啟用。 如果尚未在邊緣設備上配置它，則可以繼續使用相同的 MACsec 機密和密碼來配置它們。

### <a name="to-disable-macsec"></a>禁用 MACsec

如果您的 ExpressRoute Direct 實例不再需要 MACsec，則可以運行以下命令來禁用它。

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

此時，MACsec 在 Microsoft 端的 ExpressRoute 直接埠上被禁用。

### <a name="test-connectivity"></a>測試連線能力
在 ExpressRoute Direct 埠上配置 MACsec（包括 MACsec 金鑰更新）後，[請檢查](expressroute-troubleshooting-expressroute-overview.md)電路的 BGP 會話是否啟動並運行。 如果埠上沒有任何電路，請先創建一個電路，並設置電路的 Azure 專用對等互連或 Microsoft 對等互連。 如果 MACsec 配置錯誤（包括 MACsec 金鑰不匹配）網路設備和 Microsoft 的網路設備之間，您將不會在第 2 層和第 3 層的 BGP 建立中看到 ARP 解析度。 如果所有內容都配置正確，您應該會看到雙向正確通告的 BGP 路由，以及應用程式資料流程通過 ExpressRoute 相應地進行。

## <a name="next-steps"></a>後續步驟
1. [在快速路由直接上創建快速路由電路](expressroute-howto-erdirect.md)
2. [將 ExpressRoute 線路連結到 Azure 虛擬網路](expressroute-howto-linkvnet-arm.md)
3. [驗證快速路由連接](expressroute-troubleshooting-expressroute-overview.md)
