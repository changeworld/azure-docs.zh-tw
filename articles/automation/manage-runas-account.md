---
title: 管理 Azure 自動化執行身分帳戶
description: 本文說明如何使用 PowerShell 或從 Azure 入口網站管理您的執行身分帳戶。
services: automation
ms.subservice: shared-capabilities
ms.date: 09/28/2020
ms.topic: conceptual
ms.openlocfilehash: 3357cb40ff476a3cc0bce259930068aeccf2c10c
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767406"
---
# <a name="manage-an-azure-automation-run-as-account"></a>管理 Azure 自動化執行身分帳戶

Azure 自動化中的執行身份帳戶提供使用自動化 runbook 和其他自動化功能來管理 Azure Resource Manager 或 Azure 傳統部署模型上資源的驗證。 本文提供如何管理執行身份或傳統執行帳戶的指引。

若要深入瞭解與程式自動化案例相關的 Azure 自動化帳戶驗證和指引，請參閱 [自動化帳戶驗證總覽](automation-security-overview.md)。

## <a name="run-as-account-permissions"></a><a name="permissions"></a>執行身份帳戶許可權

本節將定義一般執行身分帳戶和傳統執行身分帳戶的權限。

若要建立或更新執行身分帳戶，您必須擁有特定的權限和使用權限。 Azure Active Directory 中的應用程式系統管理員和訂用帳戶中的擁有者可以完成所有工作。 針對有劃分職責的情況，下表顯示工作、對等的 Cmdlet 及所需權限的清單：

|Task|Cmdlet  |最低權限  |設定權限的位置|
|---|---------|---------|---|
|建立 Azure AD 應用程式|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | 應用程式開發人員角色<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>首頁 > Azure AD > 應用程式註冊 |
|將認證新增至應用程式。|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | 應用程式系統管理員或全域管理員<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>首頁 > Azure AD > 應用程式註冊|
|建立並取得 Azure AD 服務主體|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | 應用程式系統管理員或全域管理員<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>首頁 > Azure AD > 應用程式註冊|
|為指定的主體指派或取得 Azure 角色|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | 使用者存取系統管理員或擁有者具有下列權限：</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [訂用帳戶](../role-based-access-control/role-assignments-portal.md)</br>Home > 訂用帳戶 > \<subscription name\> -存取控制 (IAM) |
|建立或移除自動化憑證|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | 資源群組的參與者         |自動化帳戶資源群組|
|建立或移除自動化連線|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|資源群組的參與者 |自動化帳戶資源群組|

<sup>1</sup> 如果 Azure AD 租用戶在 [使用者設定] 頁面中的 [使用者可以註冊應用程式] 選項設定為 [是]，Azure AD 租用戶中的非管理使用者就可以[註冊 AD 應用程式](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)。 如果應用程式註冊設定 [否]，則執行此動作的使用者必須如此表中所定義。

如果您在被新增至訂用帳戶的全域管理員角色之前，並非訂用帳戶 Active Directory 執行個體的成員，系統就會將您新增為來賓。 在此情況下，您會在 `You do not have permissions to create…` [ **新增自動化帳戶** ] 頁面上收到警告。

如果您是指派全域管理員角色之訂用帳戶 Active Directory 實例的成員，您也可以在 `You do not have permissions to create…` [ **新增自動化帳戶** ] 頁面上收到警告。 在此情況下，您可以要求從訂用帳戶的 Active Directory 執行個體中移除，然後要求重新新增，以便成為 Active Directory 的完整使用者。

若要確認產生錯誤訊息的情況已修正：

1. 在 Azure 入口網站的 [Azure Active Directory] 窗格中，選取 [使用者和群組]。
2. 選取 [所有使用者]。
3. 選擇您的名稱，然後選取 [設定檔]。
4. 確定您的使用者設定檔下的 [使用者類型] 屬性值未設定為 [來賓]。

### <a name="permissions-required-to-create-or-manage-classic-run-as-accounts"></a><a name="permissions-classic"></a>建立或管理傳統執行身份帳戶所需的許可權

若要設定或更新傳統執行身分帳戶，您必須具有訂用帳戶層級的共同管理員角色。 若要深入了解傳統訂用帳戶權限，請參閱 [Azure 傳統訂用帳戶管理員](../role-based-access-control/classic-administrators.md#add-a-co-administrator)。

## <a name="create-a-run-as-account-in-azure-portal"></a>在 Azure 入口網站中建立執行身分帳戶

執行下列步驟以在 Azure 入口網站更新 Azure 自動化帳戶。 個別建立「執行身分帳戶」和「傳統執行身分帳戶」。 如果您不需要管理傳統資源，則可以只建立 Azure 執行身分帳戶。

1. 您必須以訂用帳戶管理員角色成員和訂用帳戶共同管理員的帳戶登入 Azure 入口網站。

2. 搜尋並選取 [自動化帳戶]。

3. 在 [自動化帳戶] 頁面上，從清單中選取您的自動化帳戶。

4. 在左窗格中，選取 [**帳戶設定**] 區段中的 [**執行身份帳戶**]。

    :::image type="content" source="media/manage-runas-account/automation-account-properties-pane.png" alt-text="選取 [執行身份帳戶] 選項。":::

5. 根據您所需的帳戶，使用 [ **+ Azure 執行帳戶** ] 或 [ **+ Azure 傳統執行帳戶** ] 窗格。 在檢閱概觀資訊後，按一下 [建立]。

    :::image type="content" source="media/manage-runas-account/automation-account-create-runas.png" alt-text="選取 [執行身份帳戶] 選項。":::

6. 在 Azure 建立執行身分帳戶時，您可以在功能表的 [通知]  底下追蹤進度。 橫幅也會顯示說明帳戶正在建立。 此程序需要數分鐘的時間完成。

## <a name="create-a-run-as-account-using-powershell"></a>使用 PowerShell 建立執行身分帳戶

下列清單提供在 PowerShell 中使用提供的腳本建立執行身份帳戶的需求。 這些需求適用於這兩種類型的執行身分帳戶。

* 具有 Azure Resource Manager 模組 3.4.1 和更新版本的 Windows 10 或 Windows Server 2016。 PowerShell 指令碼不支援舊版 Windows。
* Azure PowerShell PowerShell 6.2.4 或更新版本。 如需詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/install-az-ps)。
* 自動化帳戶，系統會將其參照為 `AutomationAccountName` 和 `ApplicationDisplayName` 參數的值。
* 權限相當於[設定執行身分帳戶的必要權限](#permissions)中所列的權限。

若要取得 `AutomationAccountName` 、 `SubscriptionId` 和 `ResourceGroupName` （這些是 PowerShell 腳本所需的參數）的值，請完成下列步驟。

1. 在 Azure 入口網站中，選取 [自動化帳戶]。

1. 在 [自動化帳戶] 頁面上，選取 [自動化帳戶]。

1. 在 [帳戶設定] 區段中，選取 [屬性]。

1. 請注意 [**屬性**] 頁面上的 [**名稱**]、[訂用帳戶**識別碼**] 和 [**資源群組**] 的值。

   ![自動化帳戶的屬性頁面](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>用來建立執行身分帳戶的 PowerShell 指令碼

PowerShell 腳本包含數個設定的支援。

* 使用自我簽署憑證建立執行身分帳戶。
* 使用自我簽署憑證建立執行身分帳戶和傳統執行身分帳戶。
* 使用企業憑證授權單位 (CA) 所核發的憑證，來建立執行身分帳戶和傳統執行身分帳戶。
* 在 Azure Government 雲端中，使用自我簽署憑證建立執行身分帳戶和傳統執行身分帳戶。

1. 使用下列命令，下載並將腳本儲存至本機資料夾。

    ```powershell
    wget https://raw.githubusercontent.com/azureautomation/runbooks/master/Utility/AzRunAs/Create-RunAsAccount.ps1 -outfile Create-RunAsAccount.ps1
    ```

2. 使用提升的使用者權限啟動 PowerShell，並流覽至包含腳本的資料夾。

3. 執行下列其中一個命令，根據您的需求建立執行身份和（或）傳統執行身份帳戶。

    * 使用自我簽署憑證建立執行帳戶。

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
        ```

    * 使用自我簽署憑證建立執行身分帳戶和傳統執行身分帳戶。

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
        ```

    * 使用企業憑證建立執行身分帳戶和傳統執行身分帳戶。

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
        ```

        如果您使用企業公開憑證 ( .cer 檔案) 建立了傳統執行身分帳戶，請使用此憑證。 腳本會在您 `%USERPROFILE%\AppData\Local\Temp` 用來執行 PowerShell 會話的使用者設定檔下，建立並將它儲存到您電腦上的 [暫存檔案] 資料夾中。 請參閱[將管理 API 憑證上傳至 Azure 入口網站](../cloud-services/cloud-services-configure-ssl-certificate-portal.md)。

    * 在 Azure Government 雲端中，使用自我簽署憑證建立執行身分帳戶和傳統執行身分帳戶

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnvironmentName AzureUSGovernment
        ```

4. 指令碼執行之後，系統會提您向 Azure 進行驗證。 使用屬於訂用帳戶系統管理員角色成員的帳戶登入。 如果您要建立傳統執行身分帳戶，您的帳戶必須是訂用帳戶的共同管理員。

## <a name="delete-a-run-as-or-classic-run-as-account"></a>刪除執行身分或傳統執行身分帳戶

本節說明如何刪除執行身分或傳統執行身分帳戶。 當您執行此動作時，系統不會保留自動化帳戶。 刪除執行身份帳戶之後，您可以在 Azure 入口網站中或使用所提供的 PowerShell 腳本來重新建立它。

1. 在 Azure 入口網站中，開啟自動化帳戶。

2. 在左窗格的 [帳戶設定] 區段中，選取 [執行身分帳戶]。

3. 在 [執行身分帳戶] 屬性頁面中，選取您想要刪除的執行身分帳戶或傳統執行身分帳戶。

4. 在所選帳戶的 [屬性] 窗格上，按一下 [刪除]。

   ![刪除執行身分帳戶](media/manage-runas-account/automation-account-delete-runas.png)

5. 刪除帳戶時，您可以在功能表的 [通知] 底下追蹤進度。

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>更新自我簽署憑證

您為執行身分帳戶建立的自我簽署憑證，會在建立日起算一年後到期。 有時您必須在執行身分帳戶到期前更新憑證。 您可以在該憑證到期前隨時更新憑證。

當您更新自我簽署憑證時，系統會保留目前的有效憑證，以確保已排入佇列或正在執行以及使用該「執行身分」帳戶進行驗證的所有 Runbook，都不會受到負面影響。 憑證在到期日之前會保持有效。

>[!NOTE]
>如果您認為執行身分帳戶遭到盜用，您可以刪除自我簽署憑證並重新加以建立。

>[!NOTE]
>如果您已將執行身分帳戶設定成使用您的企業憑證授權單位所核發的憑證，且您使用此選項來更新自我簽署憑證選項，則該企業憑證會由自我簽署憑證所取代。

使用下列步驟來更新自我簽署憑證。

1. 在 Azure 入口網站中，開啟自動化帳戶。

1. 在 [帳戶設定] 區段中，選取 [執行身分帳戶]。

    ![自動化帳戶的屬性窗格](media/manage-runas-account/automation-account-properties-pane.png)

1. 在 [執行身分帳戶] 屬性頁面中，選取要更新憑證的執行身分帳戶或傳統執行身分帳戶。

1. 在所選帳戶的 [屬性] 窗格上，按一下 [更新憑證]。

    ![更新執行身分帳戶的憑證](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. 更新憑證時，您可以在功能表的 [通知] 底下追蹤進度。

## <a name="limit-run-as-account-permissions"></a>限制執行身分帳戶權限

若要對 Azure 中的資源控制自動化的目標，您可以執行 [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) 指令碼。 此指令碼會變更您現有的執行身分帳戶服務主體，以建立及使用自訂角色定義。 此角色具有所有資源的權限 ([Key Vault](../key-vault/index.yml) 除外)。

>[!IMPORTANT]
>執行 **Update-AutomationRunAsAccountRoleAssignments.ps1** 指令碼之後，使用執行身分帳戶存取 Key Vault 的 Runbook 即無法再運作。 執行指令碼之前，您應先檢閱帳戶中用來呼叫 Azure Key Vault 的 Runbook。 若要啟用 Azure 自動化 Runbook 對 Key Vault 的存取，您必須[將執行身分帳戶新增至 Key Vault 的權限](#add-permissions-to-key-vault)。

如果您需要限制執行身分服務主體可執行的進一步動作，您可以將其他資源類型新增至自訂角色定義的 `NotActions` 元素。 下列範例會限制對 `Microsoft.Compute/*` 的存取。 如果您將此資源類型新增至角色定義的 `NotActions`，該角色即無法存取任何計算資源。 若要深入了解角色定義，請參閱[了解 Azure 資源的角色定義](../role-based-access-control/role-definitions.md)。

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

您可以確認您的執行身分帳戶所使用的服務主體是在參與者角色定義還是自訂角色中。

1. 移至您的自動化帳戶，然後選取 [帳戶設定] 區段中的 [執行身分帳戶]。
2. 選取 [Azure 執行身分帳戶]。
3. 選取 [角色]，以找出正在使用的角色定義。

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="選取 [執行身份帳戶] 選項。" lightbox="media/manage-runas-account/verify-role-expanded.png":::

您也可以針對多個訂用帳戶或自動化帳戶，確認執行身分帳戶所使用的角色定義。 此動作可使用 PowerShell 資源庫中的 [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) 指令碼來完成。

### <a name="add-permissions-to-key-vault"></a>將權限新增至 Key Vault

您可以允許 Azure 自動化確認 Key Vault 和您的執行身分帳戶服務主體是否使用自訂角色定義。 您必須：

* 將權限授與 Key Vault。
* 設定存取原則。

您可以使用 PowerShell 資源庫中的 [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) 指令碼，將您的執行身分帳戶權限授與 Key Vault。 如需有關在 Key Vault 上設定許可權的詳細資訊，請參閱 [指派 Key Vault 存取原則](/azure/key-vault/general/assign-access-policy-powershell) 。

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>解決執行身分帳戶設定錯誤的問題

在初始設定期間，可能會不當刪除或建立要讓執行身分或傳統執行身分帳戶正常運作所需的某些設定項目。 可能的設定錯誤實例包括：

* 憑證資產
* 連線資產
* 從參與者角色中移除了執行身分帳戶
* Azure AD 中的服務主體或應用程式

在這類設定錯誤實例中，自動化帳戶會偵測到變更，並在帳戶的 [執行身分帳戶] 屬性頁面上顯示 [不完整] 狀態。

![不完整的執行身分帳戶設定狀態](media/manage-runas-account/automation-account-runas-config-incomplete.png)

當您選取執行身分帳戶時，帳戶的 [屬性] 窗格會顯示下列錯誤訊息：

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

您可以藉由刪除並重新建立執行身份帳戶，快速解決這些執行帳戶的問題。

## <a name="next-steps"></a>後續步驟

* [應用程式物件和服務主體物件](../active-directory/develop/app-objects-and-service-principals.md)。
* [Azure 雲端服務的憑證概觀](../cloud-services/cloud-services-certs-create.md)。
