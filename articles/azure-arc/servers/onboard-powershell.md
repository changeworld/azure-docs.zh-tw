---
title: 使用 PowerShell 將混合式機器連線至 Azure
description: 在本文中，您將瞭解如何使用已啟用 Azure Arc 的伺服器，使用 PowerShell 來安裝代理程式並將機器連線至 Azure。
ms.date: 10/21/2020
ms.topic: conceptual
ms.openlocfilehash: d36fd174606b49b28b1d8343bff6ccc1f62e5194
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92374759"
---
# <a name="connect-hybrid-machines-to-azure-using-powershell"></a>使用 PowerShell 將混合式機器連線至 Azure

您可以藉由手動執行一組步驟，為環境中的一或少數 Windows 或 Linux 電腦啟用 Azure Arc 啟用的伺服器。 或者，您可以使用 PowerShell Cmdlet [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine)。 此 Cmdlet 會執行下列動作：

- 將主機電腦設定為從 Microsoft 下載中心下載 Windows 代理程式，並從 packages.microsoft.com 設定 Linux 代理程式套件。
- 安裝連線的機器代理程式。
- 使用 Azure Arc 註冊電腦

若要使用這種方法，您必須擁有機器上的系統管理員權限，才能安裝和設定代理程式。 在 Linux 上請使用根帳號，在 Windows 上則必須是本機系統管理員群組的成員。

開始之前，請務必檢閱[必要條件](agent-overview.md#prerequisites)並確認訂用帳戶和資源已符合需求。 如需支援的區域和其他相關考慮的詳細資訊，請參閱 [支援的 Azure 區域](overview.md#supported-regions)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必要條件

- 具備 Azure PowerShell 的電腦。 如需指示，請參閱 [安裝並設定 Azure PowerShell](/powershell/azure/)。

在使用 Azure PowerShell 管理已啟用 Arc 之伺服器所管理的混合式伺服器上的 VM 擴充功能之前，您必須安裝該 `Az.ConnectedMachine` 模組。 在啟用 Arc 的伺服器上執行下列命令：

```powershell
Install-Module -Name Az.ConnectedMachine
```

當安裝完成時，會傳回下列訊息：

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-and-validate-the-agent-on-windows"></a>在 Windows 上安裝和驗證代理程式

1. 以系統管理員身分開啟 PowerShell 主控台。

2. 執行命令以登入 Azure `Connect-AzAccount` 。

3. 若要安裝已連線的機器代理程式，請使用搭配 `Connect-AzConnectedMachine` `-Name` 、 `-ResourceGroupName` 和 `-Location` 參數。 使用 `-SubscriptionId` 參數來覆寫預設訂用帳戶，因為在登入之後建立的 Azure 內容會產生此結果。

    若要在可以直接與 Azure 通訊的目的電腦上安裝已連線的機器代理程式，請執行下列命令：

    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e
    ```
    
    如果目標電腦透過 Proxy 伺服器進行通訊，請執行下列命令：
    
    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e -proxy http://<proxyURL>:<proxyport>
    ```

    如果此代理程式在安裝完成之後無法啟動，請檢查記錄以取得詳細的錯誤資訊。 在 Windows 上的 *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log*，以及 Linux 上的 */var/opt/azcmagent/log/himds.log*。

## <a name="verify-the-connection-with-azure-arc"></a>驗證與 Azure Arc 的連線

在安裝好代理程式並將其設定為連線至已啟用 Azure Arc 的伺服器之後，請移至 Azure 入口網站以確認伺服器已成功連線。 在 [Azure 入口網站](https://portal.azure.com)中檢視您的機器。

![成功的伺服器連線](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>後續步驟

* 疑難排解資訊可在「連線的 [機器代理程式疑難排解指南](troubleshoot-agent-onboard.md)」中找到。

* 了解如何使用 [Azure 原則](../../governance/policy/overview.md)，針對例如 VM [來賓設定](../../governance/policy/concepts/guest-configuration.md)、確認機器回報至預期的 Log Analytics 工作區、使用 [Azure 監視器與 VM](../../azure-monitor/insights/vminsights-enable-policy.md) 啟用監視等等項目，管理您的機器。

* 深入了解 [Log Analytics 代理程式](../../azure-monitor/platform/log-analytics-agent.md)。 當您想要收集作業系統和工作負載監視資料、使用自動化 runbook 或功能（例如更新管理）進行管理時，或使用其他 Azure 服務（例如 [Azure 資訊安全中心](../../security-center/security-center-introduction.md)）時，需要適用于 Windows 和 Linux 的 Log Analytics 代理程式。