---
title: 將 Key Vault 與 DigiCert 憑證授權單位整合
description: 如何將 Key Vault 與 DigiCert 憑證授權單位整合
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 06/02/2020
ms.author: sebansal
ms.openlocfilehash: 01383acad9f221e376f814ecf99794eb0431d0cd
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588920"
---
# <a name="integrating-key-vault-with-digicert-certificate-authority"></a>將 Key Vault 與 DigiCert 憑證授權單位整合

Azure Key Vault 可讓您輕鬆地布建、管理及部署網路的數位憑證，以及啟用應用程式的安全通訊。 數位憑證是一種電子認證，可在電子交易中建立身分識別證明。 

Azure Key Vault 使用者可以直接從其 Key Vault 產生 DigiCert 憑證。 Key Vault 會透過 Key Vault 與 DigiCert 憑證授權單位之間的信任關係，確保 DigiCert 所發行憑證的端對端憑證生命週期管理。

如需更多有關憑證的一般資訊，請參閱 [Azure Key Vault 憑證](/azure/key-vault/certificates/about-certificates)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必要條件

若要完成本指南，您必須具備下列資源。
* 一個金鑰保存庫。 您可以使用現有的金鑰保存庫，或依照下列其中一個快速入門中的步驟建立新的金鑰保存庫：
   - [使用 Azure CLI 建立金鑰保存庫](../secrets/quick-create-cli.md)
   - [使用 Azure PowerShell 建立金鑰保存庫](../secrets/quick-create-powershell.md)
   - [使用 Azure 入口網站建立金鑰保存庫](../secrets/quick-create-portal.md)。
*   您必須啟動 DigiCert CertCentral 帳戶。 [註冊](https://www.digicert.com/account/signup/)您的 CertCentral 帳戶。
*   帳戶中的管理員層級許可權。


### <a name="before-you-begin"></a>開始之前

確定您隨手可及來自 DigiCert CertCentral 帳戶的下列資訊：
-   CertCentral 帳戶識別碼
-   組織識別碼
-   API 金鑰

## <a name="adding-certificate-authority-in-key-vault"></a>在 Key Vault 中新增憑證授權單位 
從 DigiCert CertCentral 帳戶收集上述資訊之後，您現在可以將 DigiCert 新增至金鑰保存庫中的憑證授權單位清單。

### <a name="azure-portal"></a>Azure 入口網站

1.  若要新增 DigiCert 憑證授權單位，請瀏覽至您想要新增 DigiCert 的金鑰保存庫。 
2.  在 Key Vault 屬性頁面上，選取 [憑證]。
3.  選取 [憑證授權單位] 索引標籤。![憑證屬性](../media/certificates/how-to-integrate-certificate-authority/select-certificate-authorities.png)
4.  選取 [新增] 選項。
 ![憑證屬性](../media/certificates/how-to-integrate-certificate-authority/add-certificate-authority.png)
5.  在 [建立憑證授權單位] 畫面上，選擇下列值：
    -   **Name**：新增可識別的簽發者名稱。 範例 DigicertCA
    -   **提供者**：從功能表中選取 DigiCert。
    -   **帳戶識別碼**：輸入您的 DigiCert CertCentral 帳戶識別碼
    -   **帳戶密碼**：輸入您在 DigiCert CertCentral 帳戶中產生的 API 金鑰
    -   **組織識別碼**：輸入從 DigiCert CertCentral 帳戶收集的 OrgID 
    -   按一下 [建立]。
   
6.  您會看到 DigicertCA 現在已加入憑證授權單位清單中。


### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell 可供使用命令列或指令碼來建立和管理 Azure 資源。 Azure Cloud Shell 是一種互動式殼層環境，您可以在瀏覽器本身透過 Azure 入口網站使用此環境。

如果您選擇在本機安裝和使用 PowerShell，則在執行本教學課程時，您必須使用 Azure PowerShell 模組 1.0.0 版或更新版本。 執行 `$PSVersionTable.PSVersion` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Login-AzAccount` 以建立與 Azure 的連線。

```azurepowershell-interactive
Login-AzAccount
```

1.  建立**資源群組**

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 來建立 Azure 資源群組。 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

2. 建立 **Key Vault**

您必須針對金鑰保存庫使用唯一名稱。 這裡的 "Contoso-Vaultname" 是本指南中 Key Vault 的名稱。

- **保存庫名稱** Contoso-Vaultname。
- **資源群組名稱**：ContosoResourceGroup。
- **位置** EastUS。

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vaultname' -ResourceGroupName 'ContosoResourceGroup' -Location 'EastUS'
```

3. 針對從 DigiCert CertCentral 帳戶收集的資訊定義變數。

- 定義**帳戶識別碼**變數
- 定義**帳戶識別碼**變數
- 定義 **API 金鑰**變數
- 定義**簽發者名稱**變數

```azurepowershell-interactive
$accountId = "myDigiCertCertCentralAccountID"
$org = New-AzKeyVaultCertificateOrganizationDetails -Id OrganizationIDfromDigiCertAccount
$secureApiKey = ConvertTo-SecureString DigiCertCertCentralAPIKey -AsPlainText –Force
$issuerName = "DigiCertCA"
```

4. 設定**簽發者**。 這會將 Digicert 新增為金鑰保存庫中的憑證授權單位。
```azurepowershell-interactive
Set-AzureKeyVaultCertificateIssuer -VaultName $vaultName -IssuerName $issuerName -IssuerProvider DigiCert -AccountId $accountId -ApiKey $secureApiKey -OrganizationDetails $org
```

5. **設定憑證的原則，並直接在 Key Vault 內從 DigiCert 發出憑證**。

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName DigiCertCA -ValidityInMonths 12 -RenewAtNumberOfDaysBeforeExpiry 60
Add-AzKeyVaultCertificate -VaultName "Contoso-Vaultname" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

憑證現在已透過此整合，在指定 Key Vault 內由 Digicert CA 成功發出。

## <a name="troubleshoot"></a>疑難排解

如果在 Azure 入口網站中發出的憑證處於「已停用」狀態，請繼續檢視**憑證作業**，來檢閱該憑證的 DigiCert 錯誤訊息。

 ![憑證屬性](../media/certificates/how-to-integrate-certificate-authority/certificate-operation-select.png)

如需詳細資訊，請參閱 [Key Vault REST API 參考中的憑證作業](/rest/api/keyvault)。 如需建立權限的相關資訊，請參閱[保存庫 - 建立或更新](/rest/api/keyvault/vaults/createorupdate)和[保存庫 - 更新存取原則](/rest/api/keyvault/vaults/updateaccesspolicy)。

## <a name="frequently-asked-questions"></a>常見問題集

- 我可以透過 KeyVault 產生數位憑證的萬用字元憑證嗎？ 
   是。 這會根據您設定數位憑證帳戶的方式而定。
- 如果要建立 EV 憑證，應該如何指定？ 
   建立憑證時，請按一下 [進階原則組態]，然後指定憑證類型。 支援的值為：OV-SSL、EV-SSL
- 相較於直接透過數位憑證取得憑證，透過整合來建立數位憑證是否會有時間上的延遲？
   否。 憑證是一種驗證程序，可能需要一些時間，而且該驗證相依於 DigiCert 遵循的程序。


## <a name="next-steps"></a>後續步驟

- [驗證、要求和回應](../general/authentication-requests-and-responses.md)
- [Key Vault 開發人員指南](../general/developers-guide.md)
