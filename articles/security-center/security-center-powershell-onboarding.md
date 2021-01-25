---
title: 使用 PowerShell 上架至 Azure 資訊安全中心
description: 本檔將逐步引導您完成使用 PowerShell Cmdlet 來啟用 Azure 資訊安全中心的流程。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e400fcbf-f0a8-4e10-b571-5a0d0c3d0c67
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 4979ff0010c1f959e8f8fc16f56da61971faf1e9
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98757063"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>使用 PowerShell 自動化上架 Azure 資訊安全中心

您可以使用 Azure 資訊安全中心 PowerShell 模組，以程式設計的方式保護 Azure 工作負載。
使用 PowerShell 可讓您將工作自動化，並避免手動工作中固有的人為錯誤。 這特別適用於數十個具有成千上萬資源之訂用帳戶的大規模部署 – 這些全部都必須從一開始就保護它們。

使用 PowerShell 上架 Azure 資訊安全中心，可讓您以程式設計的方式自動化上架與管理您的 Azure 資源，並新增必要的安全性控制項。

此文章提供了一個範例 PowerShell 指令碼，可以在您的環境中對其進行修改並使用，以在您的訂用帳戶之間推出資訊安全中心。 

在此範例中，我們將在識別碼為 d07c0080-170c-4c24-861d-9c817742786c 的訂用帳戶上啟用資訊安全中心，並藉由啟用可提供進階威脅防護和偵測功能的 Azure Defender，來套用提供高層級保護的建議設定：

1. 啟用 [Azure Defender](azure-defender.md)。 
 
2. 設定 Log Analytics 工作區，以將 Log Analytics 代理程式在訂用帳戶相關聯 VM 上收集的資料傳送到其中 – 在此範例中，是現有的使用者定義工作區 (myWorkspace)。

3. 啟用資訊安全中心的自動代理程式佈建，這會[部署 Log Analytics 代理程式](security-center-enable-data-collection.md#auto-provision-mma)。

5. 將組織的 [CISO 設定為資訊安全中心警示與重大事件的安全性連絡人](security-center-provide-security-contact-details.md)。

6. 指派資訊安全中心的[預設安全性原則](tutorial-security-policy.md)。

## <a name="prerequisites"></a>必要條件

在執行資訊安全中心 Cmdlet 之前，應該執行這些步驟：

1. 以系統管理員身分執行 PowerShell。

1. 在 PowerShell 中執行下列命令：
      
    ```Set-ExecutionPolicy -ExecutionPolicy AllSigned```

    ```Install-Module -Name Az.Security -Force```

## <a name="onboard-security-center-using-powershell"></a>使用 PowerShell 上架資訊安全中心

1. 將您的訂用帳戶註冊到資訊安全中心資源提供者：

    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```

    ```Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security'```

1. 選用：設定訂用帳戶的涵蓋範圍層級 (開啟/關閉 Azure Defender)。 如果未定義，Defender 將會關閉：

    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```

    ```Set-AzSecurityPricing -Name "default" -PricingTier "Standard"```

1. 設定代理程式將向其回報的 Log Analytics 工作區。 您必須擁有已建立的 Log Analytics 工作區，訂用帳戶的 VM 將向其回報。 您可以定義多個訂用帳戶，向相同的工作區回報。 如果未定義，就會使用預設工作區。

    ```Set-AzSecurityWorkspaceSetting -Name "default" -Scope "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"```

1. 在您的 Azure VM 上自動佈建 Log Analytics 代理程式的安裝：
    
    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```
    
    ```Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision```

    > [!NOTE]
    > 建議您啟用自動佈建，以確定 Azure 資訊安全中心會自動保護您的 Azure 虛擬機器。
    >

1. 選擇性：強烈建議您定義上架之訂用帳戶的安全性連絡人詳細資料，這些訂用帳戶將作為資訊安全中心所產生之警示和通知的收件者：

    ```Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -Phone "2142754038" -AlertAdmin -NotifyOnAlert```

1. 指派預設資訊安全中心原則計畫：

    ```Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'```

    ```$Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ 'Azure Security Benchmark'} New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'```

您已成功使用 PowerShell 上架 Azure 資訊安全中心。

您現在可以將這些 PowerShell Cmdlet 與自動化指令碼一起使用，以程式設計方式逐一查看跨訂用帳戶和資源。 這可以節省時間，並減少人為錯誤的可能性。 您可以使用此[範例指令碼](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1)作為參考。




## <a name="see-also"></a>另請參閱
若要深入了解如何使用 PowerShell 來自動化上架到資訊安全中心的相關資訊，請參閱下列文章：

* [Az.Security](/powershell/module/az.security)

如要深入了解資訊安全中心，請參閱下列文章：

* [在 Azure 資訊安全中心設定安全性原則](tutorial-security-policy.md) -- 了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
* [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) -- 了解如何管理與回應安全性警示。