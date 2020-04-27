---
title: 從 Azure 自動化 Runbook傳送電子郵件
description: 了解如何使用 SendGrid 從 Runbook 內傳送電子郵件。
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: tutorial
ms.openlocfilehash: d4b35458c76da82b33dfcb530cfdc71ee3da3bb6
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604774"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>教學課程：從 Azure 自動化 Runbook傳送電子郵件

您可以使用 PowerShell 和 [SendGrid](https://sendgrid.com/solutions) 從 Runbook 傳送電子郵件。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 建立 Azure 金鑰保存庫。
> * 將您的 `SendGrid` API 金鑰儲存在金鑰保存庫中。
> * 建立可重複使用的 Runbook 來擷取您的 API 金鑰，並使用儲存在 [Azure Key Vault](/azure/key-vault/) 中的 API 金鑰來傳送電子郵件。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需有關混合式 Runbook 背景工作角色的 Az 模組安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="prerequisites"></a>Prerequisites

若要完成此教學課程，需要有下列項目：

* Azure 訂用帳戶：如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [建立 SendGrid 帳戶](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account)。
* 具有**Az** 模組的[自動化帳戶](automation-offering-get-started.md)和[執行身分連線](automation-create-runas-account.md)，用以儲存及執行 Runbook。

## <a name="create-an-azure-key-vault"></a>建立 Azure Key Vault

您可以使用下列 PowerShell 指令碼來建立 Azure Key Vault。 將變數值取代為您的環境特有的值。 請透過程式碼區塊右上角的**立即試用**按鈕，使用內嵌的 Azure Cloud Shell。 如果您已在本機電腦上安裝 [Azure PowerShell 模組](/powershell/azure/install-az-ps)，您也可以在本機複製並執行程式碼。

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

## <a name="import-required-modules-to-your-automation-account"></a>將必要的模組匯入至您的自動化帳戶

若要在 Runbook 中使用 Azure Key Vault，您的自動化帳戶將需要下列模組：

* [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile)
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

在 [Azure 自動化] 索引標籤的 [安裝選項]  下方，按一下 [部署至 Azure 自動化]  。 此動作會開啟 Azure 入口網站。 在 [匯入] 頁面上選取您的自動化帳戶，然後按一下 [確定]  。

如需新增必要模組的其他方法，請參閱[匯入模組](/azure/automation/shared-resources/modules#importing-modules)。

## <a name="create-the-runbook-to-send-an-email"></a>建立 Runbook 以傳送電子郵件

建立金鑰保存庫並儲存您的 `SendGrid` API 金鑰之後，即可建立會擷取 API 金鑰並傳送電子郵件的 Runbook。

此 Runbook 會使用 `AzureRunAsConnection` 作為[執行身分帳戶](automation-create-runas-account.md)向 Azure 進行驗證，以從 Azure Key Vault 擷取祕密。

請使用此範例來建立名為 **Send-GridMailMessage** 的 Runbook。 您可以修改 PowerShell 指令碼，並將其重複用於不同的案例。

1. 移至您的「Azure 自動化」帳戶。
2. 在 [程序自動化]  下方，選取 [Runbook]  。
3. 在 Runbook 清單頂端，選取 [+ 建立 Runbook]  。
4. 在 [新增 Runbook]  頁面上，輸入 **Send-GridMailMessage** 作為 Runbook 名稱。 針對 Runbook 類型，選取 [PowerShell]  。 然後，選取 [Create]  \(建立\)。
   ![建立 Runbook](./media/automation-send-email/automation-send-email-runbook.png)
5. 隨即建立 Runbook，並隨即開啟 [編輯 PowerShell Runbook]  頁面。
   ![編輯 Runbook](./media/automation-send-email/automation-send-email-edit.png)
6. 將下列 PowerShell 範例複製到 [編輯]  頁面中。 請確定 `$VaultName` 是您建立金鑰保存庫時所指定的名稱。

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

7. 選取 [發佈]  來儲存並發佈 Runbook。

若要確認 Runbook 是否順利執行，您可以依照[測試 Runbook](manage-runbooks.md#testing-a-runbook) 或[啟動 Runbook](start-runbooks.md) 下的步驟操作。
如果您一開始未看到測試電子郵件，請查看  **垃圾郵件**資料夾。

## <a name="clean-up"></a>清除

若不再需要，請刪除 Runbook。 若要這樣做，選取 Runbook 清單中的 Runbook，然後按一下 [刪除]  。

使用 [Remove-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/remove-azkeyvault?view=azps-3.7.0) Cmdlet 刪除金鑰保存庫。

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>後續步驟

* 如有建立或啟動 Runbook 的問題，請參閱[對 Runbook 的錯誤進行疑難排解](./troubleshoot/runbooks.md)。
* 若要更新自動化帳戶中的模組，請參閱[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)。
* 若要監視 Runbook 執行情形，請參閱[從「自動化」將作業狀態和作業資料流轉送到 Azure 監視器記錄](automation-manage-send-joblogs-log-analytics.md)。
* 若要使用警示來觸發 Runbook，請參閱[使用警示來觸發 Azure 自動化 Runbook](automation-create-alert-triggered-runbook.md)。
