---
title: 使用 PowerShell 連線到 Azure 安全中心
description: 本文件將逐步引導您完成使用 PowerShell Cmdlet 上架 Azure 資訊安全中心的程序。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e400fcbf-f0a8-4e10-b571-5a0d0c3d0c67
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2018
ms.author: memildin
ms.openlocfilehash: b471fbb62862cd48ebbb239d65b563aa109ef629
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435485"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>使用 PowerShell 自動化上架 Azure 資訊安全中心

您可以使用 Azure 資訊安全中心 PowerShell 模組，以程式設計的方式保護 Azure 工作負載。
使用 PowerShell 可讓您將工作自動化，並避免手動工作中固有的人為錯誤。 這特別適用於數十個具有成千上萬資源之訂用帳戶的大規模部署 – 這些全部都必須從一開始就保護它們。

使用 PowerShell 上架 Azure 資訊安全中心，可讓您以程式設計的方式自動化上架與管理您的 Azure 資源，並新增必要的安全性控制項。

此文章提供了一個範例 PowerShell 指令碼，可以在您的環境中對其進行修改並使用，以在您的訂用帳戶之間推出資訊安全中心。 

在此範例中，我們將在識別碼為 d07c0080-170c-4c24-861d-9c817742786c 的訂用帳戶上啟用資訊安全中心，並藉由實作資訊安全中心的標準層提供進階威脅防護和偵測功能，來套用提供高層級保護的建議設定：

1. 設定[安全中心標準保護等級](https://azure.microsoft.com/pricing/details/security-center/)。 
 
2. 設置日誌分析工作區,日誌分析代理將在與訂閱關聯的 VM 上發送其收集的數據 - 在此示例中,現有使用者定義的工作區(my工作區)。

3. 啟動安全中心的自動代理預配,部署[紀錄分析代理](security-center-enable-data-collection.md#auto-provision-mma)程式 。

5. 將組織的[CISO 設定為安全中心警報與顯著事件的安全連絡人](security-center-provide-security-contact-details.md)。

6. 指派資訊安全中心的[預設安全性原則](tutorial-security-policy.md)。

## <a name="prerequisites"></a>Prerequisites

在執行資訊安全中心 Cmdlet 之前，應該執行這些步驟：

1.  以系統管理員身分執行 PowerShell。
2.  在 PowerShell 中執行下列命令：
      
        Set-ExecutionPolicy -ExecutionPolicy AllSigned
        Install-Module -Name Az.Security -Force

## <a name="onboard-security-center-using-powershell"></a>使用 PowerShell 上架資訊安全中心

1.  將您的訂用帳戶註冊到資訊安全中心資源提供者：

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security' 

2.  選擇性：設定訂用帳戶的涵蓋範圍等級 (定價層) (如果未定義，則定價層設定為「免費」)：

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Set-AzSecurityPricing -Name "default" -PricingTier "Standard"

3.  設定代理程式將向其回報的 Log Analytics 工作區。 您必須擁有已建立的 Log Analytics 工作區，訂用帳戶的 VM 將向其回報。 您可以定義多個訂用帳戶，向相同的工作區回報。 如果未定義，就會使用預設工作區。

        Set-AzSecurityWorkspaceSetting -Name "default" -Scope
        "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"

4.  在 Azure VM 上自動預配紀錄分析代理:
    
        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    
        Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision

    > [!NOTE]
    > 建議您啟用自動佈建，以確定 Azure 資訊安全中心會自動保護您的 Azure 虛擬機器。
    >

5.  選擇性：強烈建議您定義上架之訂用帳戶的安全性連絡人詳細資料，這些訂用帳戶將作為資訊安全中心所產生之警示和通知的收件者：

        Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -Phone "2142754038" -AlertAdmin -NotifyOnAlert 

6.  指派預設資訊安全中心原則計畫：

        Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
        $Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ '[Preview]: Enable Monitoring in Azure Security Center'}
        New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'

您現在已成功使用 PowerShell 上架 Azure 資訊安全中心！

您現在可以將這些 PowerShell Cmdlet 與自動化指令碼一起使用，以程式設計方式逐一查看跨訂用帳戶和資源。 這可以節省時間，並減少人為錯誤的可能性。 您可以使用此[範例指令碼](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1)作為參考。






## <a name="see-also"></a>另請參閱
若要深入了解如何使用 PowerShell 來自動化上架到資訊安全中心的相關資訊，請參閱下列文章：

* [Az.Security](https://docs.microsoft.com/powershell/module/az.security)。

如要深入了解資訊安全中心，請參閱下列文章：

* [在 Azure 安全中心設置安全原則](tutorial-security-policy.md)-- 瞭解如何為 Azure 訂閱和資源組配置安全原則。
* [管理和回應 Azure 安全中心中的安全警報](security-center-managing-and-responding-alerts.md)-- 瞭解如何管理和回應安全警報。