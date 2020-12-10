---
title: 使用已啟用 Arc 的伺服器對 Azure 資源進行驗證
description: 本文說明適用于已啟用 Arc 之伺服器的 Azure Instance Metadata Service 支援，以及如何使用秘密來對 Azure 資源和本機進行驗證。
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 49b70928ae972da8e0a0d14d711e4b6f246cca6a
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939101"
---
# <a name="authenticate-against-azure-resources-with-arc-enabled-servers"></a>使用已啟用 Arc 的伺服器對 Azure 資源進行驗證

直接在 Azure Arc 啟用的伺服器上執行的應用程式或處理常式，可以利用受控識別來存取其他支援 Azure Active Directory 型驗證的 Azure 資源。 應用程式可以取得代表其身分識別的 [存取權杖](../../active-directory/develop/developer-glossary.md#access-token) ，此權杖是系統指派給啟用 Arc 的伺服器，並使用它作為「持有人」權杖，以向另一個服務驗證其本身。

如需受控識別的詳細說明，以及系統指派和使用者指派的身分識別之間的差異，請參閱 [受控識別總覽](../../active-directory/managed-identities-azure-resources/overview.md) 檔。

在本文中，我們會示範伺服器如何使用系統指派的受控識別來存取 Azure [Key Vault](../../key-vault/general/overview.md)。 作為啟動程序，金鑰保存庫可讓您的用戶端應用程式，接著使用密碼存取未受 Azure Active Directory (AD) 保護的資源。 例如，您的 IIS web 伺服器所使用的 TLS/SSL 憑證可以儲存在 Azure Key Vault 中，並將憑證安全地部署到 Azure 外部的 Windows 或 Linux 伺服器。

## <a name="security-overview"></a>安全性概觀

當您將伺服器上架到 Azure Arc 啟用的伺服器時，會使用受控識別來執行幾個動作，類似于針對 Azure VM 執行的動作：

- Azure Resource Manager 收到在啟用 Arc 的伺服器上啟用系統指派的受控識別的要求。

- Azure Resource Manager 會在 Azure AD 中建立伺服器身分識別的服務主體。 服務主體會建立在此訂用帳戶信任的 Azure AD 租用戶中。

- Azure Resource Manager 使用服務主體用戶端識別碼和憑證來更新 [Windows](../../virtual-machines/windows/instance-metadata-service.md) 或 [Linux](../../virtual-machines/linux/instance-metadata-service.md) 的 Azure Instance Metadata Service (IMDS) 識別端點，以在伺服器上設定身分識別。 端點是只可使用已知且無法路由傳送的 IP 位址，從伺服器記憶體取的 REST 端點。 這項服務會提供已啟用 Arc 之伺服器的中繼資料資訊子集，以協助管理和設定。

受管理身分識別之伺服器的環境將會在啟用 Windows Arc 的伺服器上使用下列變數進行設定：

- **IMDS_ENDPOINT**：已啟用 Arc 之伺服器的 IMDS 端點 IP 位址 `http://localhost:40342` 。

- **IDENTITY_ENDPOINT**：對應至服務受控識別的 localhost 端點 `http://localhost:40342/metadata/identity/oauth2/token` 。

您在伺服器上執行的程式碼可以向 Azure Instance Metadata service 端點要求權杖，而且只能從伺服器內進行存取。

系統內容變數 **IDENTITY_ENDPOINT** 用來探索應用程式的身分識別端點。 應用程式應該嘗試取出 **IDENTITY_ENDPOINT** 並 **IMDS_ENDPOINT** 值並加以使用。 具有任何存取層級的應用程式，可以對端點提出要求。 中繼資料回應會正常處理，並提供給電腦上的任何進程。 不過，當提出的要求會公開權杖時，我們需要用戶端提供秘密，證明他們能夠存取僅供較高許可權使用者使用的資料。

## <a name="prerequisites"></a>Prerequisites

- 了解受控識別。
- 連接到已啟用 Arc 之伺服器的伺服器。
- 您是訂用帳戶或資源群組中 [擁有者群組](../../role-based-access-control/built-in-roles.md#owner)* * 的成員，以便執行必要的資源建立和角色管理步驟。
- 用來儲存和取得認證的 Azure Key Vault。 並將 Azure Arc 身分識別存取權指派給 KeyVault。

    - 如果您沒有建立 Key Vault，請參閱 [建立 Key Vault](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#create-a-key-vault-)。
    - 若要使用伺服器所使用的受控識別來設定存取權，請參閱 [授與 Linux 存取權](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#grant-access) 或 [授與 Windows 的存取](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#grant-access)權。 在步驟5中，您將輸入已啟用 Arc 之伺服器的名稱。 若要使用 PowerShell 來完成此程式，請參閱 [使用 Powershell 指派存取原則](../../key-vault/general/assign-access-policy-powershell.md)。

## <a name="acquiring-an-access-token-using-rest-api"></a>使用 REST API 取得存取權杖

取得並使用系統指派的受控識別來向 Azure 資源進行驗證的方法，與使用 Azure VM 進行驗證的方法類似。

針對已啟用 Arc 的 Windows server，使用 PowerShell，您會叫用 web 要求，以從特定埠中的本機主機取得權杖。 使用 IP 位址或環境變數 **IDENTITY_ENDPOINT** 來指定要求。

```powershell
$apiVersion = "2020-06-01"
$resource = "https://management.azure.com/"
$endpoint = "{0}?resource={1}&api-version={2}" -f $env:IDENTITY_ENDPOINT,$resource,$apiVersion
$secretFile = ""
try
{
    Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'} -UseBasicParsing
}
catch
{
    $wwwAuthHeader = $_.Exception.Response.Headers["WWW-Authenticate"]
    if ($wwwAuthHeader -match "Basic realm=.+")
    {
        $secretFile = ($wwwAuthHeader -split "Basic realm=")[1]
    }
}
Write-Host "Secret file path: " $secretFile`n
$secret = cat -Raw $secretFile
$response = Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'; Authorization="Basic $secret"} -UseBasicParsing
if ($response)
{
    $token = (ConvertFrom-Json -InputObject $response.Content).access_token
    Write-Host "Access token: " $token
}
```

下列回應是傳回的範例：

:::image type="content" source="media/managed-identity-authentication/powershell-token-output-example.png" alt-text="使用 PowerShell 成功取回存取權杖。":::

針對已啟用 Arc 的 Linux 伺服器，使用 Bash，您會叫用 web 要求，以從特定埠中的本機主機取得權杖。 使用 IP 位址或環境變數 **IDENTITY_ENDPOINT** 指定下列要求。 若要完成此步驟，您需要 SSH 用戶端。

```bash
ChallengeTokenPath=$(curl -s -D - -H Metadata:true "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com" | grep Www-Authenticate | cut -d "=" -f 2 | tr -d "[:cntrl:]")
ChallengeToken=$(cat $ChallengeTokenPath)
if [ $? -ne 0 ]; then
    echo "Could not retrieve challenge token, double check that this command is run with root privileges."
else
    curl -s -H Metadata:true -H "Authorization: Basic $ChallengeToken" "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com"
fi
```

下列回應是傳回的範例：

:::image type="content" source="media/managed-identity-authentication/bash-token-output-example.png" alt-text="使用 Bash 成功取回存取權杖。":::

回應包括存取 Azure 中任何資源所需的存取權杖。 若要完成設定以向 Azure Key Vault 進行驗證，請參閱 [使用 Windows 存取 Key Vault](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#access-data) 或 [使用 Linux 存取 Key Vault](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#access-data)。

## <a name="next-steps"></a>後續步驟

- 若要深入瞭解 Azure Key Vault，請參閱 [Key Vault 總覽](../../key-vault/general/overview.md)。

- 瞭解如何 [使用 PowerShell](../../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) 或使用 [Azure CLI](../../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)，將受控識別存取權指派給資源。