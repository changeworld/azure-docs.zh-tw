---
title: 設定 Azure Key Vault 防火牆和虛擬網路 - Azure Key Vault
description: 用於設定 Key Vault 防火牆和虛擬網路的逐步指示
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 04/22/2020
ms.author: sudbalas
ms.custom: devx-track-azurecli
ms.openlocfilehash: d416c6fdef85b83d91ca0e341659f87618d268e0
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87504512"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>設定 Azure Key Vault 防火牆和虛擬網路

本文提供設定 Azure Key Vault 防火牆和虛擬網路的逐步指示，以限制金鑰保存庫的存取權。 [Key Vault 的虛擬網路服務端點](overview-vnet-service-endpoints.md)) 可讓您將存取權限制為指定的虛擬網路和 IPv4 (網際網路通訊協定第 4 版) 位址範圍集合。

> [!IMPORTANT]
> 防火牆規則生效後，使用者只能在其要求源自允許的虛擬網路或 IPV4 位址範圍時，才可以執行 Key Vault [資料平面](secure-your-key-vault.md#data-plane-access-control)作業。 這也適用於從 Azure 入口網站存取 Key Vault。 雖然使用者可以從 Azure 入口網站瀏覽金鑰保存庫，但是如果其用戶端電腦不在允許的清單中，他們就無法列出金鑰、祕密或憑證。 這也會影響其他 Azure 服務的 [金鑰保存庫選擇器]。 使用者可以看到金鑰保存庫清單，但是如果防火牆規則阻止其用戶端電腦，則不會列出金鑰。

> [!NOTE]
> 請留意下列組態限制：
> * 最多允許 127 條虛擬網路規則和 127 條 IPv4 規則。 
> * 不支援使用 "/31" 或 "/32" 前置詞大小的小型位址範圍。 而是，使用個別 IP 位址規則設定這些範圍。
> * 只有公用 IP 位址允許使用 IP 網路規則。 IP 規則中不允許保留私人網路的 IP 位址範圍 (如 RFC 1918 中所定義)。 私人網路包括以 **10.** 、**172.16-31** 和 **192.168.** 開頭的位址。 
> * 目前僅支援 IPv4 位址。

## <a name="use-the-azure-portal"></a>使用 Azure 入口網站

以下說明如何使用 Azure 入口網站設定 Key Vault 防火牆和虛擬網路：

1. 瀏覽至您要保護的金鑰保存庫。
2. 選取 [網絡]****，然後選取 [防火牆與虛擬網路]**** 索引標籤。
3. 在 [允許存取來源]**** 之下，選取 [選取的網路]****。
4. 若要將現有的虛擬網路新增至防火牆和虛擬網路規則，請選取 [+ 新增現有的虛擬網路]****。
5. 在開啟的新刀鋒視窗中，選取您要允許存取此金鑰保存庫的訂用帳戶、虛擬網路和子網路。 如果您選取的虛擬網路和子網路並未啟用服務端點，請確認您要啟用服務端點，然後選取 [啟用]****。 可能需要 15 分鐘的時間才會生效。
6. 在 [IP 網路]**** 下方，輸入採用 [CIDR (無類別網域間路由選擇) 表示法](https://tools.ietf.org/html/rfc4632)的 IPv4 位址範圍或個別的 IP 位址，以新增 IPv4 位址範圍。
7. 如果您要允許 Microsoft 信任的服務略過 Key Vault 防火牆，請選取 [是]。 如需目前 Key Vault 信任服務的完整清單，請參閱下列連結。 [Azure Key Vault 信任的服務](https://docs.microsoft.com/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services)
7. 選取 [儲存]****。

您可以也新增虛擬網路和子網路，然後選取 [+ 新增虛擬網路]****，為新建立的虛擬網路和子網路啟用服務端點。 然後遵循提示。

## <a name="use-the-azure-cli"></a>使用 Azure CLI 

以下說明如何使用 Azure CLI 來設定 Key Vault 防火牆和虛擬網路

1. [安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 並[登入](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)。

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

1. 安裝最新的 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)，然後[登入](https://docs.microsoft.com/powershell/azure/authenticate-azureps)。

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
* ARM 範本參考：[Azure Key Vault ARM 範本參考](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/vaults)
* Azure CLI 命令：[az keyvault network-rule](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Azure PowerShell Cmdlet：[Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault)、[Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule)、[Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule)、[Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>後續步驟

* [Key Vault 的虛擬網路服務端點](overview-vnet-service-endpoints.md))
* [保護您的金鑰保存庫](secure-your-key-vault.md))
