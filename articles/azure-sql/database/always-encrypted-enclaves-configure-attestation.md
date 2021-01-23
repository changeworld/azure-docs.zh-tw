---
title: 為您的 Azure SQL 邏輯伺服器設定 Azure 證明
description: 在 Azure SQL Database 中使用安全記憶體保護區來設定 Always Encrypted 的 Azure 證明。
keywords: 加密資料、sql 加密、資料庫加密、機密資料、Always Encrypted、安全記憶體保護區、SGX、證明
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: 51431bf0da9145e1b61da708942b675e4c3eea78
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733807"
---
# <a name="configure-azure-attestation-for-your-azure-sql-logical-server"></a>為您的 Azure SQL 邏輯伺服器設定 Azure 證明

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted 與 Azure SQL Database 的安全記憶體保護區目前處於 **公開預覽** 狀態。

[Microsoft Azure 證明](../../attestation/overview.md) 是證明受信任的執行環境 (tee) 的解決方案，包括 Intel Software Guard Extension (intel SGX) 記憶體保護區。 

若要使用 Always Encrypted 適用于證明的 Azure 證明搭配 Azure SQL Database 的 [安全記憶體保護區](/sql/relational-databases/security/encryption/always-encrypted-enclaves) 使用的 Intel SGX 記憶體保護區，您需要：

1. 建立 [證明提供者](../../attestation/basic-concepts.md#attestation-provider) ，並使用建議的證明原則進行設定。

2. 將您的 Azure SQL 邏輯伺服器存取權授與您的證明提供者。

> [!NOTE]
> 設定證明是證明系統管理員的責任。 設定 [SGX 記憶體保護區和證明時，請參閱角色和責任](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation)。

## <a name="requirements"></a>需求

Azure SQL 邏輯伺服器和證明提供者必須屬於相同的 Azure Active Directory 租使用者。 不支援跨租使用者互動。 

Azure SQL 邏輯伺服器必須指派 Azure AD 身分識別。 證明系統管理員必須從該伺服器的 Azure SQL Database 系統管理員取得伺服器的 Azure AD 身分識別。 您將使用此身分識別來授與伺服器存取證明提供者的許可權。 

如需有關如何建立具有身分識別的伺服器，或使用 PowerShell 和 Azure CLI 將身分識別指派給現有伺服器的指示，請參閱 [將 Azure AD 身分識別指派給您的伺服器](transparent-data-encryption-byok-configure.md#assign-an-azure-active-directory-azure-ad-identity-to-your-server)。

## <a name="create-and-configure-an-attestation-provider"></a>建立並設定證明提供者

[證明提供者](../../attestation/basic-concepts.md#attestation-provider)是 Azure 證明中的資源，可根據[證明原則](../../attestation/basic-concepts.md#attestation-request)和簽發[證明權杖](../../attestation/basic-concepts.md#attestation-token)來評估[證明要求](../../attestation/basic-concepts.md#attestation-request)。 

證明原則是使用宣告 [規則文法](../../attestation/claim-rule-grammar.md)來指定。

Microsoft 建議您在 Azure SQL Database 中用於 Always Encrypted 的證明 Intel SGX 記憶體保護區的下列原則：

```output
version= 1.0;
authorizationrules 
{
       [ type=="x-ms-sgx-is-debuggable", value==false ]
        && [ type=="x-ms-sgx-product-id", value==4639 ]
        && [ type=="x-ms-sgx-svn", value>= 0 ]
        && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
    => permit();
};
```

上述原則會驗證：

- Azure SQL Database 內的記憶體保護區不支援 (的偵錯工具，這會降低記憶體保護區提供) 的保護層級。
- 記憶體保護區內程式庫的產品識別碼是指派給 Always Encrypted，並具有安全記憶體保護區 (4639) 的產品識別碼。
- 程式庫 (svn) 的版本識別碼大於0。
- 記憶體保護區中的程式庫已使用 Microsoft 簽署金鑰簽署 (mrsigner 宣告的值是簽署金鑰) 的雜湊。

> [!IMPORTANT]
> 證明提供者會使用 Intel SGX 記憶體保護區的預設原則建立，而不會驗證在記憶體保護區內執行的程式碼。 Microsoft 強烈建議您針對具有安全記憶體保護區的 Always Encrypted 設定上述建議原則，而不使用預設原則。

如需如何使用證明原則建立證明提供者和設定的指示，請使用：

- [快速入門：使用 Azure 入口網站設定 Azure 證明](../../attestation/quickstart-portal.md)
    > [!IMPORTANT]
    > 當您使用 Azure 入口網站設定證明原則時，請將證明類型設定為 `SGX-IntelSDK` 。
- [快速入門：使用 Azure PowerShell 設定 Azure 證明](../../attestation/quickstart-powershell.md)
    > [!IMPORTANT]
    > 當您使用 Azure PowerShell 設定證明原則時，請將 `Tee` 參數設定為 `SgxEnclave` 。
- [快速入門：使用 Azure CLI 設定 Azure 證明](../../attestation/quickstart-azure-cli.md)
    > [!IMPORTANT]
    > 當您使用 Azure CLI 設定證明原則時，請將 `attestation-type` 參數設定為 `SGX-IntelSDK` 。

## <a name="determine-the-attestation-url-for-your-attestation-policy"></a>判斷證明原則的證明 URL

設定證明原則之後，您需要共用證明 URL、參考原則、使用 Always Encrypted 的應用程式系統管理員，以 Azure SQL Database 中的安全記憶體保護區。 應用程式系統管理員或/和應用程式使用者將需要使用證明 URL 設定其應用程式，讓他們可以執行使用安全記憶體保護區的語句。

### <a name="use-powershell-to-determine-the-attestation-url"></a>使用 PowerShell 來判斷證明 URL

使用下列腳本來判斷您的證明 URL：

```powershell
$attestationProvider = Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName 
$attestationUrl = $attestationProvider.AttestUri + “/attest/SgxEnclave”
Write-Host "Your attestation URL is: " $attestationUrl 
```

### <a name="use-azure-portal-to-determine-the-attestation-url"></a>使用 Azure 入口網站來判斷證明 URL

1. 在您證明提供者的 [總覽] 窗格中，將 [證明 URI] 屬性的值複製到 [剪貼簿]。 證明 URI 看起來應該像這樣： `https://MyAttestationProvider.us.attest.azure.net` 。

2. 將下列內容附加至證明 URI： `/attest/SgxEnclave` 。 

產生的證明 URL 看起來應該像這樣： `https://MyAttestationProvider.us.attest.azure.net/attest/SgxEnclave`

## <a name="grant-your-azure-sql-logical-server-access-to-your-attestation-provider"></a>將您的 Azure SQL 邏輯伺服器存取權授與您的證明提供者

在證明工作流程中，包含您資料庫的 Azure SQL 邏輯伺服器會呼叫證明提供者來提交證明要求。 針對可提交證明要求的 Azure SQL 邏輯伺服器，伺服器必須具有 `Microsoft.Attestation/attestationProviders/attestation/read` 證明提供者之動作的許可權。 授與許可權的建議方式是，證明提供者的系統管理員將伺服器的 Azure AD 識別指派給證明提供者的證明讀者角色，或其包含的資源群組。

### <a name="use-azure-portal-to-assign-permission"></a>使用 Azure 入口網站指派許可權

若要將 Azure SQL server 的身分識別指派給證明提供者的證明讀者角色，請遵循 [使用 Azure 入口網站新增或移除 azure 角色指派](../../role-based-access-control/role-assignments-portal.md)中的一般指示。 當您在 [ **新增角色指派** ] 窗格時：

1. 在 [ **角色** ] 下拉式清單中，選取 [ **證明讀取** 者] 角色。
1. 在 [ **選取** ] 欄位中，輸入要搜尋的 Azure SQL 伺服器名稱。

如需範例，請參閱下列螢幕擷取畫面。

![證明讀者角色指派](./media/always-encrypted-enclaves/attestation-provider-role-assigment.png)

> [!NOTE]
> 若要在 [ **新增角色指派** ] 窗格中顯示伺服器，伺服器必須已指派 Azure AD 身分識別-請參閱 [需求](#requirements)。

### <a name="use-powershell-to-assign-permission"></a>使用 PowerShell 指派許可權

1. 尋找您的 Azure SQL 邏輯伺服器。

```powershell
$serverResourceGroupName = "<server resource group name>"
$serverName = "<server name>" 
$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName
```
 
2. 將伺服器指派給包含您證明提供者之資源群組的證明讀者角色。

```powershell
$attestationResourceGroupName = "<attestation provider resource group name>"
New-AzRoleAssignment -ObjectId $server.Identity.PrincipalId -RoleDefinitionName "Attestation Reader" -ResourceGroupName $attestationResourceGroupName
```

如需詳細資訊，請參閱 [使用 Azure PowerShell 新增或移除 Azure 角色指派](../../role-based-access-control/role-assignments-powershell.md#add-role-assignment-examples)。

## <a name="next-steps"></a>後續步驟

- [為具有安全記憶體保護區的 Always Encrypted 管理金鑰](/sql/relational-databases/security/encryption/always-encrypted-enclaves-manage-keys)

## <a name="see-also"></a>另請參閱

- [教學課程：在 Azure SQL Database 中使用安全記憶體保護區來開始使用 Always Encrypted](always-encrypted-enclaves-getting-started.md)
