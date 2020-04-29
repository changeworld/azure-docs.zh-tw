---
title: 大規模將混合式機器連線至 Azure
description: 在本文中，您將瞭解如何使用服務主體，將機器連接到 Azure 的 Azure Arc （預覽）。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: 3a19dc019d2566ddddb2c0ba7988b342d30a45d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77192268"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>大規模將混合式機器連線至 Azure

您可以根據您的需求，為您環境中的多部 Windows 或 Linux 電腦啟用 Azure Arc （預覽）。 使用我們提供的範本腳本，您可以將安裝的每個步驟自動化，包括建立與 Azure Arc 的連線。不過，您必須使用在目的電腦和 Azure 中具有較高許可權的帳戶，以互動方式執行此腳本。 若要將電腦連線到 Azure Arc 以進行伺服器，您可以使用 Azure Active Directory[服務主體](../../active-directory/develop/app-objects-and-service-principals.md)，而不是使用您的特殊許可權身分識別，以[互動方式連接電腦](onboard-portal.md)。 服務主體是特殊的有限管理身分識別，僅授與使用`azcmagent`命令將機器連線至 Azure 所需的最低許可權。 這比使用較高許可權的帳戶（例如租用戶系統管理員）更安全，並遵循我們的存取控制安全性最佳作法。 服務主體只會在上線期間使用，不會用於任何其他用途。  

安裝和設定連線的電腦代理程式的安裝方法，需要您使用的自動化方法具有電腦的系統管理員許可權。 在 Linux 上，使用根帳號和 Windows，做為本機系統管理員群組的成員。

開始之前，請務必檢閱[必要條件](overview.md#prerequisites)並確認訂用帳戶和資源已符合需求。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

在此程式結束時，您已成功將混合式機器連線到適用于伺服器的 Azure Arc。

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>建立服務主體以進行大規模上架

您可以使用[Azure PowerShell](/powershell/azure/install-az-ps) ，利用[new-azadserviceprincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal) Cmdlet 來建立服務主體。 或者，您可以遵循[使用 Azure 入口網站 [建立服務主體](../../active-directory/develop/howto-create-service-principal-portal.md)] 底下所列的步驟來完成這項工作。

> [!NOTE]
> 當您建立服務主體時，您的帳戶必須是您想要用於上架之訂用帳戶中的擁有者或使用者存取系統管理員。 如果您沒有足夠的權限可建立角色指派，服務主體仍可建立，但將無法使機器上線。
>

若要使用 PowerShell 建立服務主體，請執行下列動作。

1. 執行下列命令。 您必須將[`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) Cmdlet 的輸出儲存在變數中，否則您將無法在稍後的步驟中取得所需的密碼。

    ```azurepowershell-interactive
    $sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
    $sp
    ```

    ```output
    Secret                : System.Security.SecureString
    ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
    ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
    ObjectType            : ServicePrincipal
    DisplayName           : Hybrid-RP
    Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
    Type                  :
    ```

2. 若要取得儲存在`$sp`變數中的密碼，請執行下列命令：

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. 在輸出中，尋找 [**密碼**] 欄位底下的 [密碼] 值，然後將它複製。 同時在 [ **ApplicationId** ] 欄位底下尋找值，並複製它。 儲存以供稍後在安全的地方使用。 如果您忘記或遺失服務主體的密碼，您可以使用[`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) Cmdlet 來重設它。

下列屬性的值會與傳遞給的`azcmagent`參數搭配使用：

* **ApplicationId**屬性的值會用於`--service-principal-id`參數值
* **Password**屬性的值是用於連接代理程式的`--service-principal-secret`參數。

> [!NOTE]
> 請務必使用服務主體**ApplicationId**屬性，而不是**Id**屬性。
>

**Azure 連線的電腦**上線角色僅包含將電腦上架所需的許可權。 您可以指派服務主體許可權，以允許其範圍包含資源群組或訂用帳戶。 若要新增角色指派，請參閱[使用 AZURE rbac 來新增或移除角色指派和 Azure 入口網站](../../role-based-access-control/role-assignments-portal.md)，或[使用 azure Rbac 和 Azure CLI 新增或移除角色指派](../../role-based-access-control/role-assignments-cli.md)。

## <a name="install-the-agent-and-connect-to-azure"></a>安裝代理程式並連接到 Azure

下列步驟會使用腳本範本，在您的混合式電腦上安裝及設定已連線的電腦代理程式，其會執行將[混合式機器從 Azure 入口網站一文連接到 Azure](onboard-portal.md)中所述的類似步驟。 其差異在於最後一個步驟中，您會使用此`azcmagent`命令來建立與 Azure Arc 的連線。 

下列是您設定`azcmagent`命令以用於服務主體的設定。

* `tenant-id`：唯一識別碼（GUID），代表 Azure AD 的專用實例。
* `subscription-id`：您要讓機器在其中的 Azure 訂用帳戶的訂用帳戶識別碼（GUID）。
* `resource-group`：您要讓連線電腦所屬的資源組名。
* `location`：請參閱[支援的 Azure 區域](overview.md#supported-regions)。 這個位置可以與資源群組的位置相同或不同。
* `resource-name`：（*選擇性*）用於內部部署機器的 Azure 資源標記法。 如果您未指定此值，則會使用電腦主機名稱。

您可以藉由查看`azcmagent` [Azcmagent 參考](azcmagent-reference.md)，深入瞭解命令列工具。

### <a name="windows-installation-script"></a>Windows 安裝腳本

以下是已修改為使用服務主體來支援代理程式之完全自動化、非互動式安裝的已連線電腦代理程式 for Windows 安裝腳本範例。

```
 # Download the package
function download() {$ProgressPreference="SilentlyContinue"; Invoke-WebRequest -Uri https://aka.ms/AzureConnectedMachineAgent -OutFile AzureConnectedMachineAgent.msi}
download

# Install the package
msiexec /i AzureConnectedMachineAgent.msi /l*v installationlog.txt /qn | Out-String

# Run connect command
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
```

### <a name="linux-installation-script"></a>Linux 安裝腳本

以下是連線的 Linux 安裝腳本範例，其已修改為使用服務主體來支援代理程式的完全自動化、非互動式安裝。

```
# Download the installation package
wget https://aka.ms/azcmagent -O ~/install_linux_azcmagent.sh

# Install the hybrid agent
bash ~/install_linux_azcmagent.sh

# Run connect command
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
```

在安裝好代理程式並將其設定為連線至適用於伺服器的 Azure Arc (預覽) 之後，請移至 Azure 入口網站以確認伺服器已成功連線。 在 [Azure 入口網站](https://aka.ms/hybridmachineportal)中檢視您的機器。

![成功的伺服器連線](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>後續步驟

- 瞭解如何使用[Azure 原則](../../governance/policy/overview.md)來管理您的機器，例如 VM[來賓](../../governance/policy/concepts/guest-configuration.md)設定、確認機器回報至預期的 Log Analytics 工作區、使用[vm 的 Azure 監視器](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)來啟用監視等功能。

- 深入瞭解[Log Analytics 代理程式](../../azure-monitor/platform/log-analytics-agent.md)。 您需要適用於 Windows 和 Linux 的 Log Analytics 代理程式來主動監視機器上執行的作業系統和工作負載、使用自動化 Runbook 或解決方案 (例如更新管理) 來管理機器，或使用其他 Azure 服務 (例如 [Azure 資訊安全中心](../../security-center/security-center-intro.md))。
