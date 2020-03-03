---
title: 使用範本部署指令碼 | Microsoft Docs
description: 了解如何使用 Azure Resource Manager 範本中的部署指令碼。
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: carmonm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/24/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 21725e64bb359b2f11086baceb186605f010b796
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561454"
---
# <a name="tutorial-use-deployment-scripts-to-create-a-self-signed-certificate-preview"></a>教學課程：使用部署指令碼建立自我簽署憑證 (預覽)

了解如何使用 Azure Resource Manager 範本中的部署指令碼。 部署指令碼可用來執行無法由 Resource Manager 範本完成的自訂步驟。 例如，建立自我簽署憑證。  在本教學課程中，您將建立用來部署 Azure 金鑰保存庫的範本，然後在相同的範本中使用 `Microsoft.Resources/deploymentScripts` 資源建立憑證，再將憑證新增至金鑰保存庫。 若要深入了解部署指令碼，請參閱[使用 Azure Resource Manager 範本中的部署指令碼](./deployment-script-template.md)。

> [!NOTE]
> 部署指令碼目前為預覽狀態。 若要加以使用，您必須[註冊預覽版](https://aka.ms/armtemplatepreviews)。

> [!IMPORTANT]
> 系統會在相同的資源群組中建立兩個部署指令碼資源 (儲存體帳戶和容器執行個體)，用以執行指令碼和疑難排解。 當指令碼執行處於終止狀態時，指令碼服務通常會刪除這些資源。 在資源刪除之前，您需支付資源費用。 若要深入了解，請參閱[清除部署指令碼資源](./deployment-script-template.md#clean-up-deployment-script-resources)。

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 開啟快速入門範本
> * 編輯範本
> * 部署範本
> * 對失敗的指令碼進行偵錯
> * 清除資源

## <a name="prerequisites"></a>Prerequisites

若要完成本文，您需要：

* **[Visual Studio Cod](https://code.visualstudio.com/) 搭配 Resource Manager Tools 擴充功能**。 請參閱[使用 Visual Studio Code 建立 Azure Resource Manager 範本](./use-vs-code-to-create-template.md)。

* **使用者指派的受控識別，且在訂用帳戶層級上具有參與者角色**。 此身分識別會用來執行部署指令碼。 若要建立身分識別，請參閱[使用者指派的受控識別](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity)。 您在部署範本時將需要身分識別的識別碼。 此身分識別的格式為：

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  使用下列 PowerShell 指令碼，藉由提供資源組名和識別名稱來取得識別碼。

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  $id = (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name idName).Id
  ```

## <a name="open-a-quickstart-template"></a>開啟快速入門範本

您可以從 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/)開啟範本，而無須從頭建立範本。 Azure 快速入門範本是 Resource Manager 範本的存放庫。

本快速入門中使用的範本名為[建立 Azure Key Vault 和秘密](https://azure.microsoft.com/resources/templates/101-key-vault-create/)。 此範本會建立金鑰保存庫，然後將秘密新增至金鑰保存庫。

1. 在 Visual Studio Code 中，選取 [檔案]  >[開啟檔案]  。
2. 在 [檔案名稱]  中，貼上下列 URL：

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-key-vault-create/azuredeploy.json
    ```

3. 選取 [開啟]  以開啟檔案。
4. 選取 [檔案]  >[另存新檔]  ，在您的本機電腦上將檔案另存為 **azuredeploy.json**。

## <a name="edit-the-template"></a>編輯範本

對範本進行下列變更：

### <a name="clean-up-the-template-optional"></a>清除範本 (選擇性)

原始範本會將秘密新增至金鑰保存庫。  若要簡化教學課程，請移除下列資源：

* **Microsoft.KeyVault/vaults/secrets**

移除以下兩個參數定義：

* **secretName**
* **secretValue**

如果您選擇不移除這些定義，則必須在部署期間指定參數值。

### <a name="configure-the-key-vault-access-policies"></a>設定金鑰保存庫存取原則

部署指令碼會將憑證新增至金鑰保存庫。 請設定金鑰保存庫存取原則，為受控識別授與權限：

1. 新增參數以取得受控識別的識別碼：

    ```json
    "identityId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the ID of the user-assigned managed identity."
      }
    },
    ```

    > [!NOTE]
    > Visual Studio Code 的 Resource Manager 範本擴充功能尚無法格式化部署指令碼。 請勿使用 [SHIFT]+[ALT]+F 來格式化 deploymentScripts 資源，如下所示。

1. 新增參數以設定金鑰保存庫存取原則，讓受控識別可以將憑證新增至金鑰保存庫。

    ```json
    "certificatesPermissions": {
      "type": "array",
      "defaultValue": [
        "get",
        "list",
        "update",
        "create"
      ],
      "metadata": {
      "description": "Specifies the permissions to certificates in the vault. Valid values are: all, get, list, update, create, import, delete, recover, backup, restore, manage contacts, manage certificate authorities, get certificate authorities, list certificate authorities, set certificate authorities, delete certificate authorities."
      }
    }
    ```

1. 將現有的金鑰保存庫存取原則更新為：

    ```json
    "accessPolicies": [
      {
        "objectId": "[parameters('objectId')]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      },
      {
        "objectId": "[reference(parameters('identityId'), '2018-11-30').principalId]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      }
    ],
    ```

    系統定義了兩個原則，一個用於登入的使用者，另一個用於受控識別。  已登入的使用者只需要*清單*權限來驗證部署。  為了簡化本教學課程，我們會為受控識別和已登入的使用者指派相同的憑證。

### <a name="add-the-deployment-script"></a>新增部署指令碼

1. 新增部署指令碼所使用的三個參數。

    ```json
    "certificateName": {
      "type": "string",
      "defaultValue": "DeploymentScripts2019"
    },
    "subjectName": {
      "type": "string",
      "defaultValue": "CN=contoso.com"
    },
    "utcValue": {
      "type": "string",
      "defaultValue": "[utcNow()]"
    }
    ```

1. 新增 deploymentScripts 資源：

    > [!NOTE]
    > 因為內嵌部署指令碼會以雙引號括住，所以部署指令碼內的字串必須以單引號括住。 PowerShell 的逸出字元是 **&#92;** 。

    ```json
    {
      "type": "Microsoft.Resources/deploymentScripts",
      "apiVersion": "2019-10-01-preview",
      "name": "createAddCertificate",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
      ],
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[parameters('identityId')]": {
          }
        }
      },
      "kind": "AzurePowerShell",
      "properties": {
        "forceUpdateTag": "[parameters('utcValue')]",
        "azPowerShellVersion": "3.0",
        "timeout": "PT30M",
        "arguments": "[format(' -vaultName {0} -certificateName {1} -subjectName {2}', parameters('keyVaultName'), parameters('certificateName'), parameters('subjectName'))]", // can pass an argument string, double quotes must be escaped
        "scriptContent": "
          param(
            [string] [Parameter(Mandatory=$true)] $vaultName,
            [string] [Parameter(Mandatory=$true)] $certificateName,
            [string] [Parameter(Mandatory=$true)] $subjectName
          )

          $ErrorActionPreference = 'Stop'
          $DeploymentScriptOutputs = @{}

          $existingCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

          if ($existingCert -and $existingCert.Certificate.Subject -eq $subjectName) {

            Write-Host 'Certificate $certificateName in vault $vaultName is already present.'

            $DeploymentScriptOutputs['certThumbprint'] = $existingCert.Thumbprint
            $existingCert | Out-String
          }
          else {
            $policy = New-AzKeyVaultCertificatePolicy -SubjectName $subjectName -IssuerName Self -ValidityInMonths 12 -Verbose

            # private key is added as a secret that can be retrieved in the Resource Manager template
            Add-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName -CertificatePolicy $policy -Verbose

            $newCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

            # it takes a few seconds for KeyVault to finish
            $tries = 0
            do {
              Write-Host 'Waiting for certificate creation completion...'
              Start-Sleep -Seconds 10
              $operation = Get-AzKeyVaultCertificateOperation -VaultName $vaultName -Name $certificateName
              $tries++

              if ($operation.Status -eq 'failed')
              {
                throw 'Creating certificate $certificateName in vault $vaultName failed with error $($operation.ErrorMessage)'
              }

              if ($tries -gt 120)
              {
                throw 'Timed out waiting for creation of certificate $certificateName in vault $vaultName'
              }
            } while ($operation.Status -ne 'completed')

            $DeploymentScriptOutputs['certThumbprint'] = $newCert.Thumbprint
            $newCert | Out-String
          }
        ",
        "cleanupPreference": "OnSuccess",
        "retentionInterval": "P1D"
      }
    }
    ```

    `deploymentScripts` 資源取決於金鑰保存庫資源和角色指派資源。  其屬性包括：

    * **identity**：部署指令碼會使用使用者指派的受控識別來執行指令碼。
    * **kind**：指定指令碼的類型。 目前僅支援 PowerShell 指令碼。
    * **forceUpdateTag**：判斷即使在指令碼來源並未變更的情況下是否仍要執行部署指令碼。 可以是目前的時間戳記或 GUID。 若要深入了解，請參閱[執行指令碼多次](./deployment-script-template.md#run-script-more-than-once)。
    * **azPowerShellVersion**：指定要使用的 Azure PowerShell 模組版本。 部署指令碼目前支援 2.7.0、2.8.0 和3.0.0 版。
    * **timeout**：指定以 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601)指定的指令碼執行時間允許上限。 預設值為 **P1D**。
    * **arguments**：指定參數值。 多個值應以空格分隔。
    * **scriptContent**：指定指令碼內容。 若要執行外部指令碼，請改用 **primaryScriptURI**。 如需詳細資訊，請參閱[使用外部指令碼](./deployment-script-template.md#use-external-scripts)。
        只有在本機電腦上測試指令碼時，才需要宣告 **$DeploymentScriptOutputs**。 宣告此變數可讓指令碼直接在本機電腦和 deploymentScript 資源中執行，而無須進行變更。 指派給 $DeploymentScriptOutputs 的值可作為部署中的輸出。 如需詳細資訊，請參閱[使用 PowerShell 部署腳本的輸出](./deployment-script-template.md#work-with-outputs-from-powershell-script)或[使用 CLI 部署腳本的輸出](./deployment-script-template.md#work-with-outputs-from-cli-script)。
    * **cleanupPreference**：指定何時要刪除部署指令碼資源的喜好設定。  預設值為 [一律]  ，這表示即使處於終止狀態 (成功、失敗、已取消)，仍會刪除部署指令碼資源。 本教學課程中會使用 **OnSuccess**，以讓您有機會檢視指令碼執行結果。
    * **retentionInterval**：指定服務在指令碼資源到達結束狀態後保留該資源的間隔。 此持續時間到期後，即會刪除資源。 持續時間以 ISO 8601 模式為基礎。 本教學課程使用 P1D，這表示一天。  當 **cleanupPreference** 設定為 **OnExpiration** 時，就會使用此屬性。 目前並未啟用此屬性。

    部署指令碼會採用三個參數：金鑰保存庫名稱、憑證名稱和主體名稱。  指令碼會建立憑證，然後將憑證新增至金鑰保存庫。

    **$DeploymentScriptOutputs** 會用來儲存輸出值。  如需深入了解，請參閱[使用 PowerShell 部署腳本的輸出](./deployment-script-template.md#work-with-outputs-from-powershell-script)或[使用 CLI 部署腳本的輸出](./deployment-script-template.md#work-with-outputs-from-cli-script)。

    您可以在[這裡](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)找到完成的範本。

1. 若要查看偵錯程序，請將以下這一行新增至部署指令碼，以程式碼中放入錯誤：

    ```powershell
    Write-Output1 $keyVaultName
    ```

    正確的命令是 **Write-Output**，而不是 **Write-Output1**。

1. 選取 [檔案]  >[儲存]  ，以儲存檔案。

## <a name="deploy-the-template"></a>部署範本

請參閱 Visual Studio Code 快速入門中的[部署範本](./quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template)一節，以開啟 Cloud Shell，並將範本檔案上傳至殼層。 接著，請執行下列 PowerShell 指令碼：

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
$identityId = Read-Host -Prompt "Enter the user-assigned managed identity ID"

$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$resourceGroupName = "${projectName}rg"
$keyVaultName = "${projectName}kv"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -identityId $identityId -keyVaultName $keyVaultName -objectId $adUserId

Write-Host "Press [ENTER] to continue ..."
```

部署指令碼服務必須建立其他部署指令碼資源，以執行指令碼。 除了實際的指令碼執行時間以外，準備和清除程序最多可能需要一分鐘才能完成。

部署失敗，因為在指令碼中使用的命令 **Output1** 無效。 您應該會收到錯誤，指出：

```error
The term 'Write-Output1' is not recognized as the name of a cmdlet, function, script file, or operable
program.\nCheck the spelling of the name, or if a path was included, verify that the path is correct and try again.\n
```

部署指令碼的執行結果會儲存在部署指令碼資源中，以供疑難排解之用。

## <a name="debug-the-failed-script"></a>對失敗的指令碼進行偵錯

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 開啟資源群組。 這是附加 **rg** 的專案名稱。 您應該會在資源群組中看到兩個額外的資源。 這些資源稱為*部署指令碼資源*。

    ![Resource Manager 範本部署指令碼資源](./media/template-tutorial-deployment-script/resource-manager-template-deployment-script-resources.png)

    這兩個檔案都具有 **azscripts** 尾碼。 其中一個是儲存體帳戶，另一個則是容器執行個體。

    請選取 [顯示隱藏的類型]  ，以列出 deploymentScripts 資源。

1. 選取具有 **azscripts** 尾碼的儲存體帳戶。
1. 選取 [檔案共用]  磚。 您應該會看到 **azscripts** 資料夾。  此資料夾包含部署指令碼執行檔案。
1. 選取 [azscripts]  。 您應該會看到兩個資料夾：**azscriptinput** 和 **azscriptoutput**。  輸入資料夾包含系統 PowerShell 指令檔和使用者部署指令檔。 輸出檔案夾包含 **executionresult.json** 和指令碼輸出檔案。 您可以在 **executionresult.json** 中查看錯誤訊息。 輸出檔案不存在，因為執行失敗。

移除 **Write-Output1** 行，並重新部署範本。

當第二個部署執行成功時，指令碼服務應該會移除部署指令碼資源，因為 **cleanupPreference** 屬性設定為 **OnSuccess**。

## <a name="clean-up-resources"></a>清除資源

不再需要 Azure 資源時，可藉由刪除資源群組來清除您所部署的資源。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]  。
2. 在 [依名稱篩選]  欄位中輸入資源群組名稱。
3. 選取資源群組名稱。  您在資源群組中應該會看到共計六個資源。
4. 從頂端功能表中選取 [刪除資源群組]  。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用 Azure Resource Manager 範本中的部署指令碼。 若要深入了解如何根據條件部署 Azure 資源，請參閱：

> [!div class="nextstepaction"]
> [使用條件](./template-tutorial-use-conditions.md)
