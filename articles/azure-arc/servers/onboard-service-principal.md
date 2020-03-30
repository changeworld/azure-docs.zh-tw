---
title: 大規模將混合電腦連接到 Azure
description: 在本文中，您將瞭解如何使用 Azure Arc 將電腦連接到 Azure 的伺服器（預覽版）。使用服務主體。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: 3a19dc019d2566ddddb2c0ba7988b342d30a45d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77192268"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>大規模將混合電腦連接到 Azure

您可以為環境中的多個 Windows 或 Linux 電腦啟用 Azure Arc 伺服器（預覽），並根據您的要求提供多個靈活的選項。 使用我們提供的範本腳本，您可以自動執行安裝的每一步，包括建立與 Azure Arc 的連接。但是，您需要使用在目的電腦和 Azure 中具有提升許可權的帳戶以對話模式執行此腳本。 要將電腦連接到伺服器的 Azure Arc，可以使用 Azure 活動目錄[服務主體](../../active-directory/develop/app-objects-and-service-principals.md)，而不是使用特權標識[以交互連接電腦](onboard-portal.md)。 服務主體是一種特殊的有限管理標識，僅授予使用`azcmagent`命令將電腦連接到 Azure 所需的最低許可權。 這比使用租戶管理員等更高特權帳戶更安全，並且遵循我們的存取控制安全最佳實踐。 服務主體僅在載入期間使用，不用於任何其他目的。  

安裝和配置連接電腦代理的安裝方法要求您使用的自動方法對電腦具有管理員許可權。 在 Linux 上，通過使用根帳號和 Windows 作為本地管理員組的成員。

開始之前，請務必檢閱[必要條件](overview.md#prerequisites)並確認訂用帳戶和資源已符合需求。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

此過程結束時，您將成功地將混合電腦連接到用於伺服器的 Azure Arc。

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>創建大規模載入的服務主體

您可以使用[Azure PowerShell](/powershell/azure/install-az-ps)使用[New-AzADService 主要](/powershell/module/Az.Resources/New-AzADServicePrincipal)Cmdlet 創建服務主體。 或者，您可以[按照使用 Azure 門戶創建服務主體](../../active-directory/develop/howto-create-service-principal-portal.md)下列出的步驟來完成此任務。

> [!NOTE]
> 創建服務主體時，您的帳戶必須是要用於載入的訂閱中的擁有者或使用者訪問管理員。 如果您沒有足夠的權限可建立角色指派，服務主體仍可建立，但將無法使機器上線。
>

要使用 PowerShell 創建服務主體，請執行以下操作。

1. 執行下列命令。 您必須將[`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal)Cmdlet 的輸出存儲在變數中，否則將無法檢索後續步驟中所需的密碼。

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

2. 要檢索存儲在變數中的`$sp`密碼，請運行以下命令：

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. 在輸出中，查找欄位**密碼**下的密碼值並複製它。 還會在"**應用程式 Id"** 欄位下查找該值，並複製它。 將它們保存在安全的地方，以便以後使用。 如果您忘記或丟失了服務主體密碼，則可以使用[`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential)Cmdlet 重置它。

以下屬性中的值與傳遞給 的`azcmagent`參數一起使用：

* **來自應用程式 Id**屬性的值用於`--service-principal-id`參數值
* **密碼**屬性中的值用於用於連接代理的`--service-principal-secret`參數。

> [!NOTE]
> 請確保使用服務主體**應用程式 Id**屬性，而不是**Id**屬性。
>

**Azure 連接的電腦載入**角色僅包含載入電腦所需的許可權。 您可以分配服務主體許可權，以允許其範圍包括資源組或訂閱。 要添加角色指派，請參閱[使用 Azure RBAC 和 Azure 門戶添加或刪除角色指派](../../role-based-access-control/role-assignments-portal.md)，或使用[Azure RBAC 和 Azure CLI 添加或刪除角色指派](../../role-based-access-control/role-assignments-cli.md)。

## <a name="install-the-agent-and-connect-to-azure"></a>安裝代理並連接到 Azure

以下步驟通過使用腳本範本在混合電腦上安裝和配置已連接的電腦代理，該範本執行 Azure 門戶文章中在["將混合電腦連接到 Azure"](onboard-portal.md)中描述的類似步驟。 區別在於使用服務主體的命令建立與 Azure Arc`azcmagent`的連接的最後一步。 

以下是將`azcmagent`命令配置為用於服務主體的設置。

* `tenant-id`：表示 Azure AD 專用實例的唯一識別碼 （GUID）。
* `subscription-id`：要在 中的電腦的 Azure 訂閱的訂閱 ID （GUID）。
* `resource-group`：您希望連接的電腦所屬的資源組名稱。
* `location`：請參閱[支援的 Azure 區域](overview.md#supported-regions)。 此位置可以與資源組的位置相同或不同。
* `resource-name`： （*可選*） 用於本地電腦的 Azure 資源表示形式。 如果不指定此值，則使用電腦主機名稱。

您可以通過查看`azcmagent`[Azcmagent 參考](azcmagent-reference.md)來瞭解有關命令列工具的更多資訊。

### <a name="windows-installation-script"></a>視窗安裝腳本

下面是 Windows 安裝腳本的已連接電腦代理的示例，該代理已被修改為使用服務主體來支援代理的完全自動化非互動式安裝。

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

下面是 Linux 安裝腳本的已連接電腦代理的示例，該代理已被修改為使用服務主體來支援代理的完全自動化、非互動式安裝。

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

- 瞭解如何使用[Azure 策略](../../governance/policy/overview.md)管理電腦，用於 VM[來賓配置](../../governance/policy/concepts/guest-configuration.md)、驗證電腦是否報告到預期的日誌分析工作區、使用 VM 啟用 Azure[監視器](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)等操作。

- 瞭解有關[日誌分析代理](../../azure-monitor/platform/log-analytics-agent.md)的更多資訊。 您需要適用於 Windows 和 Linux 的 Log Analytics 代理程式來主動監視機器上執行的作業系統和工作負載、使用自動化 Runbook 或解決方案 (例如更新管理) 來管理機器，或使用其他 Azure 服務 (例如 [Azure 資訊安全中心](../../security-center/security-center-intro.md))。
