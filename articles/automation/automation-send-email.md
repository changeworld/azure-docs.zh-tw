---
title: 從 Azure 自動化 Runbook傳送電子郵件
description: 本文章帶您了解如何從 Runbook 內傳送電子郵件。
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: conceptual
ms.openlocfilehash: a92f65bd88a5aec79a179a6e2d53de15c274add4
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834555"
---
# <a name="send-an-email-from-a-runbook"></a>從 Runbook 傳送電子郵件

您可以使用 PowerShell 和 [SendGrid](https://sendgrid.com/solutions) 從 Runbook 傳送電子郵件。 

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或[註冊免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [SendGrid 帳戶](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account)。
* [自動化帳戶](automation-offering-get-started.md)與 **Az** 模組。
* [執行身分帳戶](automation-create-runas-account.md)以儲存及執行 Runbook。

## <a name="create-an-azure-key-vault"></a>建立 Azure Key Vault

您可以使用下列 PowerShell 指令碼來建立 Azure Key Vault。 將變數值取代為您的環境特有的值。 請透過程式碼區塊右上角的**立即試用**按鈕，使用內嵌的 Azure Cloud Shell。 如果您已在本機電腦上安裝 [Az 模組](/powershell/azure/install-az-ps)，也可以在本機複製並執行程式碼。

> [!NOTE]
> 若要擷取您的 API 金鑰，請使用[尋找您的 SendGrid API 金鑰](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key)中所列的步驟。

```azurepowershell-interactive
$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
# If you omit the SubscriptionId parameter, the default subscription is selected.
Connect-AzAccount -SubscriptionId $SubscriptionId

# Use Get-AzLocation to see your available locations.
$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "<Enter a universally unique vault name>"
$SendGridAPIKey = "<SendGrid API key>"
$AutomationAccountName = "testaa"

# Create new Resource Group, or omit this step if you already have a resource group.
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region
$resourceId = $newKeyVault.ResourceId

# Convert the SendGrid API key into a SecureString
$Secret = ConvertTo-SecureString -String $SendGridAPIKey -AsPlainText -Force
Set-AzKeyVaultSecret -VaultName $VaultName -Name 'SendGridAPIKey' -SecretValue $Secret

# Grant access to the Key Vault to the Automation Run As account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

如需建立 Azure Key Vault 和儲存秘密的其他方式，請參閱 [Key Vault 快速入門](/azure/key-vault/)。

## <a name="import-required-modules-into-your-automation-account"></a>將必要的模組匯入至您的自動化帳戶

若要在 Runbook 中使用 Azure Key Vault，您必須將下列模組匯入自動化帳戶：

    * [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile)
    * [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

如需指示，請參閱[匯入 Az 模組](shared-resources/modules.md#import-az-modules)。

## <a name="create-the-runbook-to-send-an-email"></a>建立 Runbook 以傳送電子郵件

建立 Key Vault 並儲存您的 `SendGrid` API 金鑰之後，即可建立會擷取 API 金鑰並傳送電子郵件的 Runbook。 請以會使用 `AzureRunAsConnection` 作為[執行身分帳戶](automation-create-runas-account.md)的 Runbook 向 Azure 進行驗證，以從 Azure Key Vault 擷取祕密。 我們會呼叫 Runbook **Send-GridMailMessage**。 您可以修改範例中的 PowerShell 指令碼，並將其重複用於不同的案例。

1. 移至您的「Azure 自動化」帳戶。
2. 在 [程序自動化] 下方，選取 [Runbook]。
3. 在 Runbook 清單頂端，選取 [+ 建立 Runbook]。
4. 在新增 Runbook 頁面上，輸入 **Send-GridMailMessage** 作為 Runbook 名稱。 針對 Runbook 類型，選取 [PowerShell]。 然後，選取 [Create] \(建立\)。
   ![建立 Runbook](./media/automation-send-email/automation-send-email-runbook.png)
5. 隨即建立 Runbook，並隨即開啟 [編輯 PowerShell Runbook] 頁面。
   ![編輯 Runbook](./media/automation-send-email/automation-send-email-edit.png)
6. 將下列 PowerShell 範例複製到編輯頁面中。 請確定 `VaultName` 指定您為 Key Vault 選擇的名稱。

    ```powershell-interactive
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content
    )

    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Out-Null
    $VaultName = "<Enter your vault name>"
    $SENDGRID_API_KEY = (Get-AzKeyVaultSecret -VaultName $VaultName -Name "SendGridAPIKey").SecretValueText
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer " + $SENDGRID_API_KEY)
    $headers.Add("Content-Type", "application/json")

    $body = @{
    personalizations = @(
        @{
            to = @(
                    @{
                        email = $destEmailAddress
                    }
            )
        }
    )
    from = @{
        email = $fromEmailAddress
    }
    subject = $subject
    content = @(
        @{
            type = "text/plain"
            value = $content
        }
    )
    }

    $bodyJson = $body | ConvertTo-Json -Depth 4

    $response = Invoke-RestMethod -Uri https://api.sendgrid.com/v3/mail/send -Method Post -Headers $headers -Body $bodyJson
    ```

7. 選取 [發佈] 來儲存並發佈 Runbook。

若要確認 Runbook 是否順利執行，您可以依照[測試 Runbook](manage-runbooks.md#test-a-runbook) 或[啟動 Runbook](start-runbooks.md) 下的步驟操作。

如果您一開始未看到測試電子郵件，請查看 **垃圾郵件**資料夾。

## <a name="clean-up-resources-after-the-email-operation"></a>在電子郵件作業後清除資源

1. 當不再需要 Runbook 時，請在 Runbook 清單中加以選取，然後按一下 [刪除]。

2. 使用 [Remove-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/remove-azkeyvault?view=azps-3.7.0) Cmdlet 刪除 Key Vault。

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>後續步驟

* 若要將 Runbook 作業資料傳送至您的 Log Analytics 工作區，請參閱[將 Azure 自動化作業資料轉送至 Azure 監視器記錄](automation-manage-send-joblogs-log-analytics.md)。
* 若要監視基本層級計量和記錄，請參閱[使用警示來觸發 Azure 自動化 Runbook](automation-create-alert-triggered-runbook.md)。
* 若要更正 Runbook 作業期間所發生的問題，請參閱 [Runbook 問題疑難排解](./troubleshoot/runbooks.md)。