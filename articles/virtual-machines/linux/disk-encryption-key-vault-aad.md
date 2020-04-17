---
title: 使用 Azure AD 建立與設定 Azure 磁碟加密的金鑰保管庫(上一版本)
description: 本文提供要對 IaaS VM 使用 Microsoft Azure 磁碟加密所需滿足的先決條件。
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 0ec46a1d2c7fca231b5cf6b045b634af50ee12a7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459826"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release"></a>使用 Azure AD 建立與設定 Azure 磁碟加密的金鑰保管庫(上一版本)

**新版本的 Azure 磁碟加密消除了提供 Azure AD 應用程式參數以啟用 VM 磁碟加密的要求。使用新版本時,不再需要在啟用加密步驟期間提供 Azure AD 認證。使用新版本,必須在沒有 Azure AD 應用程式參數的情況下對所有新 VM 進行加密。要檢視使用新版本啟用 VM 磁碟加密的說明,請參閱 Azure[磁碟加密](disk-encryption-overview.md)。仍支援已使用 Azure AD 應用程式參數加密的 VM,應繼續使用 AAD 語法進行維護。**

Azure 磁碟加密使用 Azure 密鑰保管庫來控制和管理磁碟加密密鑰和機密。  如需金鑰保存庫的詳細資訊，請參閱[開始使用 Azure Key Vault](../../key-vault/key-vault-get-started.md) 和[保護金鑰保存庫](../../key-vault/general/secure-your-key-vault.md)。 

建立與設定金鑰保管庫,以便與 Azure AD 一起使用 Azure 磁碟加密(上一個版本)涉及三個步驟:

1. 建立金鑰保存庫。 
2. 設定 Azure AD 應用程式和服務主體。
3. 設定 Azure AD 應用程式的金鑰保存庫存取原則。
4. 設定金鑰保存庫進階存取原則。
 
如果您願意,還可以生成或導入密鑰加密密鑰 (KEK)。

有關如何[安裝工具和連接到 Azure](disk-encryption-key-vault.md#install-tools-and-connect-to-azure)的步驟,請參閱[Azure 磁碟加密的主要創建和配置密鑰保管庫](disk-encryption-key-vault.md)一文。

> [!Note]
> 本文中的步驟在[Azure 磁碟加密先決條件 CLI 腳本](https://github.com/ejarvi/ade-cli-getting-started)和 Azure[磁碟加密先決條件 PowerShell 腳本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)中自動執行。


## <a name="create-a-key-vault"></a>建立金鑰保存庫 
Azure 磁碟加密會與 [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) 整合，協助您控制及管理金鑰保存庫訂用帳戶中的磁碟加密金鑰與祕密。 針對 Azure 磁碟加密，您可以建立金鑰保存庫，也可以使用現有保存庫。 如需金鑰保存庫的詳細資訊，請參閱[開始使用 Azure Key Vault](../../key-vault/key-vault-get-started.md) 和[保護金鑰保存庫](../../key-vault/general/secure-your-key-vault.md)。 您可以使用 Resource Manager 範本、Azure PowerShell 或 Azure CLI 來建立金鑰保存庫。 


>[!WARNING]
>為了確保加密祕密不會跨出區域界限，Azure 磁碟加密需要讓 Key Vault 和 VM 共置於相同區域中。 請在和所要加密 VM 相同的區域中建立並使用 Key Vault。 


### <a name="create-a-key-vault-with-powershell"></a><a name="bkmk_KVPSH"></a> 使用 PowerShell 建立金鑰保存庫

您可以使用[新 AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) cmdlet 使用 Azure PowerShell 建立金鑰保管庫。 有關金鑰保存庫的其他 cmdlet,請參閱[Az.KeyVault](/powershell/module/az.keyvault/)。 

1. 如果需要,使用[New-AzResourceGroup 建立新的資源群組](/powershell/module/az.Resources/New-azResourceGroup)。  要列出資料中心位置,請使用[抓取-位置](/powershell/module/az.resources/get-azlocation)。 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

1. 使用[新阿茲基庫](/powershell/module/az.keyvault/New-azKeyVault)建立新金鑰保管庫
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. 記下傳回的**保存庫名稱**、**資源群組名稱**、**資源識別碼**、**保存庫 URI** 和**物件識別碼**，以供稍後在加密磁碟時使用。 


### <a name="create-a-key-vault-with-azure-cli"></a><a name="bkmk_KVCLI"></a> 使用 Azure CLI 建立金鑰保存庫
您可以搭配使用 Azure CLI 與 [az keyvault](/cli/azure/keyvault#commands) 命令來管理金鑰保存庫。 若要建立金鑰保存庫，請使用 [az keyvault create](/cli/azure/keyvault#az-keyvault-create)。

1. 如有需要，請使用 [az group create](/cli/azure/group#az-group-create) 建立新的資源群組。 若要列出位置，請使用 [az account list-locations](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. 使用 [az keyvault create](/cli/azure/keyvault#az-keyvault-create) 建立新的金鑰保存庫。
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. 記下傳回的**保存庫名稱** (名稱)、**資源群組名稱**、**資源識別碼** (識別碼)、**保存庫 URI** 和**物件識別碼**，以供稍後使用。 

### <a name="create-a-key-vault-with-a-resource-manager-template"></a><a name="bkmk_KVRM"></a> 使用 Resource Manager 範本建立金鑰保存庫

您可以使用 [Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)來建立金鑰保存庫。

1. 在 Azure 快速入門範本中，按一下 [部署至 Azure]****。
2. 選取訂用帳戶、資源群組、資源群組位置、Key Vault 名稱、物件識別碼、法律條款和協議，然後按一下 [購買]****。 


## <a name="set-up-an-azure-ad-app-and-service-principal"></a><a name="bkmk_ADapp"></a> 設定 Azure AD 應用程式和服務主體 
當您需要在 Azure 中執行中的 VM 上啟用加密時，Azure 磁碟加密會產生並將加密金鑰寫入金鑰保存庫。 在金鑰保存庫中管理加密金鑰需要 Azure AD 驗證。 基於此目的，請建立 Azure AD 應用程式。 出於身份驗證目的,可以使用基於客戶端金鑰的[驗證或基於客戶端憑證的 Azure AD 認證](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)。


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-powershell"></a><a name="bkmk_ADappPSH"></a> 使用 Azure PowerShell 設定 Azure AD 應用程式和服務主體 
若要執行下列命令，請取得並使用 [Azure AD PowerShell 模組](/powershell/azure/active-directory/install-adv2)。 

1. 使用[New-AzAD 應用程式](/powershell/module/az.resources/new-azadapplication)PowerShell cmdlet 創建 Azure AD 應用程式。 MyApplicationHomePage 和 MyApplicationUri 可以是任何您想要的值。

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. $azureAdApplication.ApplicationId 是 Azure AD ClientID，而 $aadClientSecret 是用戶端密碼，您稍後會用此資訊來啟用 Azure 磁碟加密。 適當地保護 Azure AD 用戶端密碼。 執行 `$azureAdApplication.ApplicationId` 會顯示 ApplicationID。


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-cli"></a><a name="bkmk_ADappCLI"></a> 使用 Azure CLI 設定 Azure AD 應用程式和服務主體

您可以搭配使用 Azure CLI 和 [az ad sp](/cli/azure/ad/sp) 命令來管理服務主體。 有關詳細資訊,請參閱創建[Azure 服務主體](/cli/azure/create-an-azure-service-principal-azure-cli)。

1. 建立新的服務主體。
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  所傳回的 appId 是其他命令中所使用的 Azure AD ClientID。 它也是您會用於 az keyvault set-policy 的 SPN。 此密碼是您稍後應該會用來啟用 Azure 磁碟加密的用戶端密碼。 適當地保護 Azure AD 用戶端密碼。
 
### <a name="set-up-an-azure-ad-app-and-service-principal-though-the-azure-portal"></a><a name="bkmk_ADappRM"></a> 透過 Azure 入口網站設定 Azure AD 應用程式和服務主體
請使用[使用入口網站來建立可存取資源的 Active Directory 應用程式和服務主體](../../active-directory/develop/howto-create-service-principal-portal.md)一文中的步驟來建立 Azure AD 應用程式。 下面所列的每個步驟各會帶您直接前往要完成的文章章節。 

1. [確認所需權限](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)
2. [建立 Azure 活動目錄應用程式](../../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - 在建立應用程式時，您可以使用任何您想要的名稱和登入 URL。
3. [取得應用程式識別碼和驗證金鑰](../../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)。 
     - 身份驗證金鑰是客戶端金鑰,用作集-AzVM磁碟加密擴展的 AadClient 秘密。 
        - 應用程式會使用驗證金鑰作為認證來登入 Azure AD。 在 Azure 入口網站中，此密碼稱為金鑰，但實際上與金鑰保存庫並無任何關聯。 請適當地保護這個祕密。 
     - 應用程式 ID 稍後將用作集-AzVM磁碟加密擴展的 AadClientId 和集-AzKeyVaultAccessPolicy的服務主名稱。 

## <a name="set-the-key-vault-access-policy-for-the-azure-ad-app"></a><a name="bkmk_KVAP"></a> 設定 Azure AD 應用程式的金鑰保存庫存取原則
為了將加密祕密寫入指定的 Key Vault，Azure 磁碟加密需要有權將祕密寫入 Key Vault 的 Azure Active Directory 應用程式用戶端識別碼和用戶端密碼。 

> [!NOTE]
> 若要使用 Azure 磁碟加密，您必須對 Azure AD 用戶端應用程式設定下列存取原則：_WrapKey_ 和 _Set_ 權限。

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-powershell"></a><a name="bkmk_KVAPPSH"></a> 使用 Azure PowerShell 設定 Azure AD 應用程式的金鑰保存庫存取原則
您的 Azure AD 應用程式需要權限，才能存取保存庫中的金鑰或密碼。 使用[Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet 向應用程式授予許可權,使用用戶端 ID(在註冊應用程式時生成)作為 __ServiceTheName_參數值。 若要深入了解，請參閱部落格文章 [Azure Key Vault - 逐步解說](https://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx)。 

1. 使用 PowerShell 設定 AD 應用程式的金鑰保存庫存取原則。

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-cli"></a><a name="bkmk_KVAPCLI"></a> 使用 Azure CLI 設定 Azure AD 應用程式的金鑰保存庫存取原則
使用 [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) 來設定存取原則。 如需詳細資訊，請參閱[使用 CLI 2.0 管理 Key Vault](../../key-vault/general/manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret)。

使用下列命令，提供您透過 Azure CLI 存取權所建立的服務主體，來取得祕密並包裝金鑰：
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-the-portal"></a><a name="bkmk_KVAPRM"></a> 使用入口網站設定 Azure AD 應用程式的金鑰保存庫存取原則

1. 使用金鑰保存庫開啟資源群組。
2. 選取金鑰保存庫，移至 [存取原則]****，然後按一下 [新增]****。
3. 在 [選取主體]**** 底下，搜尋您所建立的 Azure AD 應用程式，並加以選取。 
4. 針對 [金鑰權限]****，勾選 [密碼編譯作業]**** 底下的 [包裝金鑰]****。
5. 針對 [祕密權限]****，勾選 [祕密管理作業]**** 底下的 [設定]****。
6. 按一下 [確定]**** 以儲存存取原則。 

![Azure Key Vault 密碼編譯作業 - 包裝金鑰](./media/disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault 密碼權限 - 設定](./media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="set-key-vault-advanced-access-policies"></a><a name="bkmk_KVper"></a>設定金鑰保存管理的管理權
Azure 平台需要存取您金鑰保存庫中的加密金鑰或密碼，讓該資訊可供 VM 用來開機和解密磁碟區。 在金鑰保存庫或部署上啟用磁碟加密將會失敗。  

### <a name="set-key-vault-advanced-access-policies-with-azure-powershell"></a><a name="bkmk_KVperPSH"></a> 使用 Azure PowerShell 設定金鑰保存庫進階存取原則
 使用金鑰保管庫 PowerShell cmdlet[集-AzKeyVault 訪問策略](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)為金鑰保管庫啟用磁碟加密。

  - **針對磁碟加密啟用 Key Vault：** Azure 磁碟加密需要 EnabledForDiskEncryption。
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **視需要針對部署啟用 Key Vault：** 可讓 Microsoft.Compute 資源提供者在資源建立期間 (例如，在建立虛擬機器時) 參考了這個金鑰保存庫的情況下，從這個金鑰保存庫擷取祕密。

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **視需要針對範本部署啟用 Key Vault：** 可讓 Azure Resource Manager 在範本部署參考了這個金鑰保存庫的情況下，從這個金鑰保存庫取得祕密。

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="set-key-vault-advanced-access-policies-using-the-azure-cli"></a><a name="bkmk_KVperCLI"></a> 使用 Azure CLI 設定金鑰保存庫進階存取原則
使用 [az keyvault update](/cli/azure/keyvault#az-keyvault-update) 啟用金鑰保存庫的磁碟加密。 

 - **針對磁碟加密啟用 Key Vault：** 需要 Enabled-for-disk-encryption。 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **視需要針對部署啟用 Key Vault：** 允許虛擬機器從保存庫擷取儲存為祕密的憑證。
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **視需要針對範本部署啟用 Key Vault：** 允許 Resource Manager 從保存庫擷取祕密。
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="set-key-vault-advanced-access-policies-through-the-azure-portal"></a><a name="bkmk_KVperrm"></a> 透過 Azure 入口網站設定金鑰保存庫進階存取原則

1. 選取金鑰保存庫，移至 [存取原則]****，然後**按一下以顯示進階存取原則**。
2. 選取標示為**為磁碟區加密啟用對 Azure 磁碟加密的存取**的方塊。
3. 視需要選取 [為部署啟用對 Azure 虛擬機器的存取]**** 及/或 [為範本部署啟用對 Azure Resource Manager 的存取]****。 
4. 按一下 [檔案]  。

![Azure 金鑰保存庫進階存取原則](./media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-optional"></a><a name="bkmk_KEK"></a>設定金鑰加密金鑰(選擇性的)
如果您想使用金鑰加密金鑰 (KEK) 來為加密金鑰額外添加一層安全性，請將 KEK 新增至金鑰保存庫。 使用[Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) cmdlet 在密鑰保管庫中創建金鑰加密密鑰。 您也可以從內部部署金鑰管理 HSM 匯入 KEK。 關於詳細資訊,請參閱[金鑰保管庫文件](../../key-vault/keys/hsm-protected-keys.md)。 若指定了金鑰加密金鑰，Azure 磁碟加密會先使用該金鑰包裝加密祕密，再寫入 Key Vault。 

* 生成金鑰時,請使用 RSA 金鑰類型。 Azure 磁碟加密尚不支援使用橢圓曲線鍵。

* 您的金鑰保存庫密碼和 KEK URL 必須已設定版本。 Azure 會強制執行設定版本的這項限制。 針對有效的密碼和 KEK URL，請參閱下列範例︰

  * 有效機密網址的範例:*https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * 有效的 KEK 網址 範例:*https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure 磁碟加密不支援將連接埠號碼指定為金鑰保存庫密碼和 KEK URL 的一部分。 如需不支援和支援的金鑰保存庫 URL 範例，請參閱下列範例：

  * 無法接受的金鑰保存的資料庫網址*https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * 可接受的金鑰保管庫網址:*https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="set-up-a-key-encryption-key-with-azure-powershell"></a><a name="bkmk_KEKPSH"></a> 使用 Azure PowerShell 設定金鑰加密金鑰 
在使用 PowerShell 指令碼之前，請先熟悉 Azure 磁碟加密的先決條件，以了解指令碼中的步驟。 範例指令碼可能需要隨環境加以變更。 此指令碼會建立所有 Azure 磁碟加密先決條件，並加密現有 IaaS VM 以使用金鑰加密金鑰包裝磁碟加密金鑰。 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name  $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).VaultUri;
     
 # Step 2: Create the AD application and service principal.
     # Fill in 'MyAADClientSecret', "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
     # MyApplicationHomePage and the MyApplicationUri can be any values you wish.
     
     $aadClientSecret =  'MyAADClientSecret';
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force;
     $azureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId;
     $aadClientID = $azureAdApplication.ApplicationId;
     
 #Step 3: Enable the vault for disk encryption and set the access policy for the Azure AD application.
     
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName  $KVRGname;
     
 #Step 4: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 5: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
      $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```

## <a name="certificate-based-authentication-optional"></a><a name="bkmk_Cert"></a> 憑證式驗證 (選擇性)
如果您想要使用憑證驗證，您可以在金鑰保存庫上傳一個憑證驗證，並將它部署到用戶端。 在使用 PowerShell 指令碼之前，請先熟悉 Azure 磁碟加密的先決條件，以了解指令碼中的步驟。 範例指令碼可能需要隨環境加以變更。

     
 ```powershell

 # Fill in "MyKeyVaultResourceGroup", "MySecureVault", and 'MyLocation' ('My location' only if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption. No need to set 'My location' in this case.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   #Setting some variables with the key vault information 
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId

   # Create the Azure AD application and associate the certificate with it. 
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
   
   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert".  

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for -EnabledForDeployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM'
   $VMRGName = 'MyVirtualMachineResourceGroup'
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
 ```

## <a name="certificate-based-authentication-and-a-kek-optional"></a><a name="bkmk_CertKEK"></a> 憑證式驗證和 KEK (選擇性)

如果您想要使用憑證驗證，並以 KEK 包裝加密金鑰，您可以使用下列指令碼作為範例。 在使用 PowerShell 指令碼之前，請先熟悉所有先前的 Azure 磁碟加密先決條件，以了解指令碼中的步驟。 範例指令碼可能需要隨環境加以變更。

> [!IMPORTANT]
> Linux VM 目前不支援 Azure AD 憑證式驗證。



     
 ```powershell
# Fill in 'MyKeyVaultResourceGroup', 'MySecureVault', and 'MyLocation' (if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   # Create the Azure AD application and associate the certificate with it.  
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   ## Give access for setting secrets and wraping keys
   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname

   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert". 

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for deployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM';
   $VMRGName = 'MyVirtualMachineResourceGroup';
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>後續步驟

[Linux VM 上使用 Azure AD 啟用 Azure 磁碟加密(上一個版本)](disk-encryption-linux-aad.md)
