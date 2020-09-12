---
title: Azure ExpressRoute：設定 MACsec
description: 本文可協助您設定 MACsec 來保護邊緣路由器與 Microsoft 邊緣路由器之間的連線。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 10/22/2019
ms.author: duau
ms.openlocfilehash: 8ee7f42a4b05def7c2239c7a1e5bcef54f6f7e50
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89395769"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>在 ExpressRoute Direct 埠上設定 MACsec

本文可協助您設定 MACsec，以使用 PowerShell 保護邊緣路由器與 Microsoft 邊緣路由器之間的連接。

## <a name="before-you-begin"></a>開始之前

在開始進行設定之前，請先確認下列情況︰

* 您瞭解 [ExpressRoute Direct 布建工作流程](expressroute-erdirect-about.md)。
* 您已建立 [ExpressRoute Direct 埠資源](expressroute-howto-erdirect.md)。
* 如果您想要在本機執行 PowerShell，請確認您的電腦上已安裝最新版本的 Azure PowerShell。

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

    如果您已經有金鑰保存庫或資源群組，您可以重複使用它們。 不過，您必須在現有的金鑰保存庫上啟用虛 [**刪除** 功能](../key-vault/general/soft-delete-overview.md) 。 如果未啟用虛刪除，您可以使用下列命令來啟用它：

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. 建立使用者身分識別。

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    如果 >new-azuserassignedidentity 無法辨識為有效的 PowerShell Cmdlet，請在系統管理員模式中安裝下列模組 () 然後重新執行上述命令。

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. 建立連線關聯機碼 (CAK) 和連線關聯索引鍵名稱 (CKN) 並將其儲存在金鑰保存庫中。

    ```azurepowershell-interactive
    $CAK = ConvertTo-SecureString "your_key" -AsPlainText -Force
    $CKN = ConvertTo-SecureString "your_key_name" -AsPlainText -Force
    $MACsecCAKSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CAK_name" -SecretValue $CAK
    $MACsecCKNSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CKN_name" -SecretValue $CKN
    ```
4. 將 GET 許可權指派給使用者身分識別。

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName "your_key_vault_name" -PermissionsToSecrets get -ObjectId $identity.PrincipalId
    ```

   現在，此身分識別可以從金鑰保存庫取得秘密，例如 CAK 和 CKN。
5. 將此使用者識別設定為 ExpressRoute 所使用。

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2. 在 ExpressRoute Direct 埠上設定 MACsec

### <a name="to-enable-macsec"></a>若要啟用 MACsec

每個 ExpressRoute Direct 實例都有兩個實體埠。 您可以選擇同時在這兩個埠上啟用 MACsec，或在一個埠上一次啟用 MACsec。 藉由將流量切換至使用中) 的埠時，將流量切換到使用中的埠，以在一段時間內 (，以在您的 ExpressRoute Direct 已在服務中時，將中斷降到最低

1. 設定 MACsec 秘密和密碼，並將使用者身分識別與埠建立關聯，讓 ExpressRoute 管理程式碼可以視需要存取 MACsec 秘密。

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
2.  (選擇性) 如果埠處於系統管理關閉狀態，您可以執行下列命令來顯示埠。

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = "Enabled"
    $erDirect.Links[1].AdminState = "Enabled"
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    此時，MACsec 會在 Microsoft 端的 ExpressRoute Direct 埠上啟用。 如果您還沒有在 edge 裝置上設定它，您可以使用相同的 MACsec 秘密和加密來進行設定。

### <a name="to-disable-macsec"></a>停用 MACsec

如果您的 ExpressRoute Direct 實例不再需要 MACsec，您可以執行下列命令來停用它。

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

此時，MACsec 會在 Microsoft 端的 ExpressRoute Direct 埠上停用。

### <a name="test-connectivity"></a>測試連線能力
在您設定 MACsec (包括 ExpressRoute Direct 埠上的 MACsec key update) 之後，請 [檢查](expressroute-troubleshooting-expressroute-overview.md) 線路的 BGP 會話是否已啟動且正在執行。 如果您還沒有任何埠線路，請先建立一個，並設定 Azure 私用對等互連或線路的 Microsoft 對等互連。 如果 MACsec 的設定不正確（包括 MACsec 金鑰不相符），您的網路裝置與 Microsoft 的網路裝置之間將不會在第2層和 BGP 建立（第3層）看到 ARP 解析度。 如果一切都設定正確，您應該會看到以雙向方式正確通告的 BGP 路由，以及透過 ExpressRoute 的應用程式資料流程。

## <a name="next-steps"></a>接下來的步驟
1. [在 ExpressRoute Direct 上建立 ExpressRoute 線路](expressroute-howto-erdirect.md)
2. [將 ExpressRoute 線路連結到 Azure 虛擬網路](expressroute-howto-linkvnet-arm.md)
3. [確認 ExpressRoute 連線能力](expressroute-troubleshooting-expressroute-overview.md)
