---
title: 設定 Azure Key Vault 防火牆和虛擬網路 - Azure Key Vault
description: 用於設定 Key Vault 防火牆和虛擬網路的逐步指示
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: sudbalas
ms.custom: devx-track-azurecli
ms.openlocfilehash: d1b1c27fe0136220d5a1851af4a5c24102a37da1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015548"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>設定 Azure Key Vault 防火牆和虛擬網路

本文將為您提供如何設定 Azure Key Vault 防火牆的指引。 本文件將詳細說明 Key Vault 防火牆的不同設定，並提供如何設定 Azure Key Vault 以與其他應用程式和 Azure 服務搭配使用的逐步指示。

## <a name="firewall-settings"></a>防火牆設定

本節將說明可設定 Azure Key Vault 防火牆的不同方式。

### <a name="key-vault-firewall-disabled-default"></a>停用 Key Vault 防火牆 (預設值)

根據預設，當您建立新的金鑰保存庫時，將會停用 Azure Key Vault 防火牆。 所有應用程式和 Azure 服務都可以存取金鑰保存庫，並將要求傳送至金鑰保存庫。 請注意，這項設定並不表示任何使用者都能對您的金鑰保存庫執行作業。 金鑰保存庫仍會藉由要求 Azure Active Directory 驗證和存取原則權限，來限制儲存在金鑰保存庫中的秘密、金鑰和憑證。 若要深入了解金鑰保存庫驗證，請參閱[這裡](./authentication-fundamentals.md)的金鑰保存庫驗證基本概念文件。

### <a name="key-vault-firewall-enabled-trusted-services-only"></a>啟用 Key Vault 防火牆 (僅限信任的服務)

當您啟用 Key Vault 防火牆時，將會有 [允許受信任的 Microsoft 服務略過此防火牆] 的選項。 信任的服務清單不包含每個單一 Azure 服務。 例如，Azure DevOps 就不在信任的服務清單中。 **這並不表示信任的服務清單中未列出的服務就不受信任或不安全。** 信任的服務清單包含執行於服務上的所有程式碼完全受 Microsoft 控制的服務。 由於使用者可在 Azure 服務 (例如 Azure DevOps) 中撰寫自訂程式碼，因此 Microsoft 不會提供為服務建立總括核准的選項。 此外，服務出現在信任的服務清單上，並不表示在任何情況下都允許使用該服務。

若要確認您嘗試使用的服務是否位於信任的服務清單上，請參閱[這裡](./overview-vnet-service-endpoints.md#trusted-services)的文件。

### <a name="key-vault-firewall-enabled-ipv4-addresses-and-ranges---static-ips"></a>啟用 Key Vault 防火牆 (IPv4 位址和範圍 - 靜態 IP)

如果您想要授權讓特定服務可透過 Key Vault 防火牆存取金鑰保存庫，您可以將其 IP 位址新增至金鑰保存庫防火牆的允許清單中。 這種設定最適用於使用靜態 IP 位址或已知範圍的服務。

若要允許 Azure 資源 (例如 Web 應用程式或邏輯應用程式) 的 IP 位址或範圍，請執行下列步驟。

1. 登入 Azure 入口網站
1. 選取資源 (服務的特定執行個體)
1. 按一下 [設定] 底下的 [屬性] 刀鋒視窗
1. 尋找 [IP 位址] 欄位。
1. 複製此值或範圍，並將其輸入金鑰保存庫防火牆允許清單中。

若要允許整個 Azure 服務通過 Key Vault 防火牆，請在[這裡](https://www.microsoft.com/download/details.aspx?id=41653)使用針對 Azure 公開記載的資料中心 IP 位址清單。 在您想要的區域中，尋找與您要使用的服務相關聯的 IP 位址，並使用上述步驟將這些 IP 位址新增至金鑰保存庫防火牆。

### <a name="key-vault-firewall-enabled-virtual-networks---dynamic-ips"></a>啟用 Key Vault 防火牆 (虛擬網路 - 動態 IP)

如果您嘗試允許虛擬機器之類的 Azure 資源存取金鑰保存庫，您可能無法使用靜態 IP 位址，且您可能不想要讓 Azure 虛擬機器的所有 IP 位址都能存取金鑰保存庫。

在此情況下，您應在虛擬網路中建立資源，然後允許來自特定虛擬網路和子網路的流量存取您的金鑰保存庫。 若要這樣做，請執行下列步驟。

1. 登入 Azure 入口網站
1. 選取您要設定的金鑰保存庫
1. 選取 [網路] 刀鋒視窗
1. 選取 [+ 新增現有的虛擬網路]
1. 選取您想要允許其通過金鑰保存庫防火牆的虛擬網路和子網路。

### <a name="key-vault-firewall-enabled-private-link"></a>啟用 Key Vault 防火牆 (私人連結)

若要了解如何在金鑰保存庫上設定私人連結連線，請參閱[這裡](./private-link-service.md)的文件。

> [!IMPORTANT]
> 防火牆規則生效後，使用者只能在其要求源自允許的虛擬網路或 IPV4 位址範圍時，才可以執行 Key Vault [資料平面](secure-your-key-vault.md#data-plane-access-control)作業。 這也適用於從 Azure 入口網站存取 Key Vault。 雖然使用者可以從 Azure 入口網站瀏覽金鑰保存庫，但是如果其用戶端電腦不在允許的清單中，他們就無法列出金鑰、祕密或憑證。 這也會影響其他 Azure 服務的 [金鑰保存庫選擇器]。 使用者可以看到金鑰保存庫清單，但是如果防火牆規則阻止其用戶端電腦，則不會列出金鑰。

> [!NOTE]
> 請留意下列組態限制：
> * 最多允許 127 條虛擬網路規則和 127 條 IPv4 規則。 
> * 只有公用 IP 位址允許使用 IP 網路規則。 IP 規則中不允許保留私人網路的 IP 位址範圍 (如 RFC 1918 中所定義)。 私人網路包括以 **10.** 、**172.16-31** 和 **192.168.** 開頭的位址。 
> * 目前僅支援 IPv4 位址。

## <a name="use-the-azure-portal"></a>使用 Azure 入口網站

以下說明如何使用 Azure 入口網站設定 Key Vault 防火牆和虛擬網路：

1. 瀏覽至您要保護的金鑰保存庫。
2. 選取 [網絡]，然後選取 [防火牆與虛擬網路] 索引標籤。
3. 在 [允許存取來源] 之下，選取 [選取的網路]。
4. 若要將現有的虛擬網路新增至防火牆和虛擬網路規則，請選取 [+ 新增現有的虛擬網路]。
5. 在開啟的新刀鋒視窗中，選取您要允許存取此金鑰保存庫的訂用帳戶、虛擬網路和子網路。 如果您選取的虛擬網路和子網路並未啟用服務端點，請確認您要啟用服務端點，然後選取 [啟用]。 可能需要 15 分鐘的時間才會生效。
6. 在 [IP 網路] 下方，輸入採用 [CIDR (無類別網域間路由選擇) 表示法](https://tools.ietf.org/html/rfc4632)的 IPv4 位址範圍或個別的 IP 位址，以新增 IPv4 位址範圍。
7. 如果您要允許 Microsoft 信任的服務略過 Key Vault 防火牆，請選取 [是]。 如需目前 Key Vault 信任服務的完整清單，請參閱下列連結。 [Azure Key Vault 信任的服務](./overview-vnet-service-endpoints.md#trusted-services)
7. 選取 [儲存]。

您可以也新增虛擬網路和子網路，然後選取 [+ 新增虛擬網路]，為新建立的虛擬網路和子網路啟用服務端點。 然後遵循提示。

## <a name="use-the-azure-cli"></a>使用 Azure CLI 

以下說明如何使用 Azure CLI 來設定 Key Vault 防火牆和虛擬網路

1. [安裝 Azure CLI](/cli/azure/install-azure-cli) 並[登入](/cli/azure/authenticate-azure-cli)。

2. 列出可用的虛擬網路規則。 如果您還沒有為此金鑰保存庫設定任何規則，清單會是空的。
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. 在現有的虛擬網路和子網路上啟用 Key Vault 的服務端點。
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. 為虛擬網路和子網路新增網路規則。
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. 新增允許流量來自的 IP 位址範圍。
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. 如果有任何信任的服務需要存取此金鑰保存庫，請將 `bypass` 設定為 `AzureServices`。
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. 將預設動作設定為 `Deny` 以開啟網路規則。
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>使用 Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

以下說明如何使用 PowerShell 設定 Key Vault 防火牆和虛擬網路：

1. 安裝最新的 [Azure PowerShell](/powershell/azure/install-az-ps)，然後[登入](/powershell/azure/authenticate-azureps)。

2. 列出可用的虛擬網路規則。 如果您還沒有為此金鑰保存庫設定任何規則，清單會是空的。
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. 在現有的虛擬網路和子網路上啟用 Key Vault 的服務端點。
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. 為虛擬網路和子網路新增網路規則。
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. 新增允許流量來自的 IP 位址範圍。
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. 如果有任何信任的服務需要存取此金鑰保存庫，請將 `bypass` 設定為 `AzureServices`。
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. 將預設動作設定為 `Deny` 以開啟網路規則。
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>參考
* ARM 範本參考：[Azure Key Vault ARM 範本參考](/azure/templates/Microsoft.KeyVault/vaults)
* Azure CLI 命令：[az keyvault network-rule](/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Azure PowerShell Cmdlet：[Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault)、[Add-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule)、[Remove-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule)、[Update-AzKeyVaultNetworkRuleSet](/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>後續步驟

* [Key Vault 的虛擬網路服務端點](overview-vnet-service-endpoints.md)
* [保護您的金鑰保存庫](secure-your-key-vault.md)