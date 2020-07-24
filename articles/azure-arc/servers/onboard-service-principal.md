---
title: 大規模將混合式機器連線至 Azure
description: 在本文中，您將了解如何使用服務主體，使用適用於伺服器的 Azure Arc (預覽) 將機器連線至 Azure。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 07/23/2020
ms.topic: conceptual
ms.openlocfilehash: 7ac04b29853ce0d4f6ac4004bdfad4effd283170
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87132988"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>大規模將混合式機器連線至 Azure

您可以根據您的需求，使用數個彈性選項，針對您環境中的多部 Windows 或 Linux 機器，啟用適用於伺服器的 Azure Arc (預覽)。 使用我們提供的範本指令碼，您可以讓安裝的每個步驟自動化，包括建立與 Azure Arc 的連線。不過，您必須與目標機器和 Azure 中具有提升權限的帳戶，以互動方式執行此指令碼。 若要將電腦連線到 Azure Arc 伺服器，您可以使用 Azure Active Directory [服務主體](../../active-directory/develop/app-objects-and-service-principals.md)，而不是使用您的特殊權限身分識別[以互動方式連線機器](onboard-portal.md)。 服務主體是特殊的有限管理身分識別，僅可獲得使用 `azcmagent` 命令將機器連線至 Azure 所需的最小權限。 這是比使用較高權限帳戶 (例如租用戶系統管理員) 更安全的做法，並且遵循我們的存取控制安全性最佳做法。 服務主體只會在上線期間使用，不會用於任何其他用途。  

用來安裝和設定 Connected Machine 代理程式的安裝方法，需要您使用的自動化方法具有機器的系統管理員權限。 在 Linux 上請使用根帳戶，在 Windows 上則必須是本機系統管理員群組的成員。

開始之前，請務必檢閱[必要條件](agent-overview.md#prerequisites)並確認訂用帳戶和資源已符合需求。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

在此程序結束時，您已成功將混合式機器連線到適用於伺服器的 Azure Arc。

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>建立服務主體以進行大規模上線

您可以使用 [Azure PowerShell](/powershell/azure/install-az-ps)，透過 [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal) Cmdlet 建立服務主體。 或者您可以遵循[使用 Azure 入口網站來建立服務主體](../../active-directory/develop/howto-create-service-principal-portal.md)之下所列的步驟，來完成此工作。

> [!NOTE]
> 在建立服務主體時，您的帳戶在要用於上線的訂用帳戶上必須是擁有者或使用者存取系統管理員。 如果您沒有足夠的權限可建立角色指派，服務主體仍可建立，但將無法使機器上線。
>

若要使用 PowerShell 建立服務主體，請執行下列命令。

1. 執行下列命令。 您必須在變數中儲存 [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) Cmdlet 的輸出，否則將無法擷取稍後步驟中所需的密碼。

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

2. 若要擷取儲存在 `$sp` 變數中的密碼，請執行下列命令：

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. 在輸出中，尋找 [密碼] 欄位下的密碼值，並且進行複製。 另外請在 [ApplicationId] 欄位下尋找值，並且進行複製。 儲存在安全的地方以供稍後使用。 如果忘記或遺失服務主體密碼，您可使用 [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) Cmdlet 加以重設。

下列屬性的值會與傳遞給 `azcmagent` 的參數搭配使用：

* **ApplicationId** 屬性的值是用於 `--service-principal-id` 參數值
* **password** 屬性的值是用於用來連線代理程式的 `--service-principal-secret` 參數。

> [!NOTE]
> 請務必使用服務主體的 **ApplicationId** 屬性，而非使用 **Id** 屬性。
>

**Azure Connected Machine 上線**角色只會包含讓機器上線所需的權限。 您可以指派服務主體權限，以允許其範圍包含資源群組或訂用帳戶。 若要新增角色指派，請參閱[使用 Azure RBAC 和 Azure 入口網站來新增或移除角色指派](../../role-based-access-control/role-assignments-portal.md)或[使用 Azure RBAC 和 Azure CLI 來新增或移除角色指派](../../role-based-access-control/role-assignments-cli.md)。

## <a name="install-the-agent-and-connect-to-azure"></a>安裝代理程式並連線至 Azure

下列步驟會藉由使用指令碼範本，在您的混合式機器上安裝及設定 Connected Machine 代理程式，該指令碼範本會執行如同[從 Azure 入口網站將混合式機器連線至 Azure](onboard-portal.md)一文所述的類似步驟。 其差異在於最後一個步驟，您使用服務主題，透過 `azcmagent` 命令建立與 Azure Arc 的連線。

以下是您設定 `azcmagent` 命令以用於服務主體的設定。

* `tenant-id`：唯一識別碼 (GUID)，表示 Azure AD 的專用執行個體。
* `subscription-id`：您想要讓機器在其中的 Azure 訂用帳戶訂用帳戶識別碼 (GUID)。
* `resource-group`：您想要連線機器隸屬的資源群組名稱。
* `location`：請參閱[支援的 Azure 區域](overview.md#supported-regions)。 此位置可與資源群組的位置相同或不同。
* `resource-name`：(*選擇性*) 用於內部部署機器的 Azure 資源表示法。 如果您未指定此值，將會使用電腦主機名稱。

您可以藉由檢閱 [Azcmagent 參考](./manage-agent.md)，深入了解 `azcmagent` 命令列工具。

### <a name="windows-installation-script"></a>Windows 安裝指令碼

以下是適用於 Windows 的 Connected Machine 代理程式安裝指令碼範例，該指令碼已修改為使用服務主體，以支援完全自動化、非互動式的代理程式安裝。

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

>[!NOTE]
>此腳本只支援從64位版本的 Windows PowerShell 執行。
>

### <a name="linux-installation-script"></a>Linux 安裝指令碼

以下是適用於 Linux 的 Connected Machine 代理程式安裝指令碼範例，該指令碼已修改為使用服務主體，以支援完全自動化、非互動式的代理程式安裝。

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

>[!NOTE]
>您必須具備 Linux 電腦的*根*存取權限，才能執行**azcmagent**。

在安裝好代理程式並將其設定為連線至適用於伺服器的 Azure Arc (預覽) 之後，請移至 Azure 入口網站以確認伺服器已成功連線。 在 [Azure 入口網站](https://aka.ms/hybridmachineportal)中檢視您的機器。

![成功的伺服器連線](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>後續步驟

- 了解如何使用 [Azure 原則](../../governance/policy/overview.md)，針對例如 VM [來賓設定](../../governance/policy/concepts/guest-configuration.md)、確認機器回報至預期的 Log Analytics 工作區、使用 [Azure 監視器與 VM](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md) 啟用監視等等項目，管理您的機器。

- 深入了解 [Log Analytics 代理程式](../../azure-monitor/platform/log-analytics-agent.md)。 您需要適用於 Windows 和 Linux 的 Log Analytics 代理程式來主動監視機器上執行的作業系統和工作負載、使用自動化 Runbook 或解決方案 (例如更新管理) 來管理機器，或使用其他 Azure 服務 (例如 [Azure 資訊安全中心](../../security-center/security-center-intro.md))。
