---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e491c3a1f0364626a1efb5b81d4680e83c027a4a
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992247"
---
|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應該為虛擬機器啟用 Azure 備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |藉由啟用 Azure 備份，確保您的 Azure 虛擬機器受到保護。 Azure 備份是適用於 Azure 安全且符合成本效益的資料保護解決方案。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[使用預設原則將具有指定標記的 Vm 上的備份設定至新的復原服務保存庫](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83644c87-93dd-49fe-bf9f-6aff8fd0834e) |藉由在與虛擬機器相同的位置和資源群組中部署復原服務保存庫，來強制執行所有虛擬機器的備份。 當組織中不同的應用程式小組配置了不同的資源群組，而且需要管理自己的備份和還原時，這樣做會很有用。 您可以選擇性地包含包含指定標記的虛擬機器，以控制指派的範圍。 請參閱 [https://aka.ms/AzureVMAppCentricBackupIncludeTag](https://aka.ms/AzureVMAppCentricBackupIncludeTag) |deployIfNotExists |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineApplicationCentricBackup_Backup_Deploy_WithTag.json) |
|[使用指定標記將 Vm 上的備份設定至相同位置中的現有復原服務保存庫](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F345fa903-145c-4fe1-8bcd-93ec2adccde8) |在與虛擬機器相同的位置和訂用帳戶中，將所有虛擬機器備份至現有的中央復原服務保存庫，以強制執行所有虛擬機器的備份。 當您組織中的中央小組管理訂用帳戶中所有資源的備份時，這樣做會很有用。 您可以選擇性地包含包含指定標記的虛擬機器，以控制指派的範圍。 請參閱 [https://aka.ms/AzureVMCentralBackupIncludeTag](https://aka.ms/AzureVMCentralBackupIncludeTag) |deployIfNotExists、auditIfNotExists、disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineWithTag_Backup_Deploy.json) |
|[使用預設原則在沒有指定標記的 Vm 上設定備份至新的復原服務保存庫](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98d0b9f8-fd90-49c9-88e2-d3baf3b0dd86) |藉由在與虛擬機器相同的位置和資源群組中部署復原服務保存庫，來強制執行所有虛擬機器的備份。 當組織中不同的應用程式小組配置了不同的資源群組，而且需要管理自己的備份和還原時，這樣做會很有用。 您可以選擇性地排除包含指定標記的虛擬機器，以控制指派的範圍。 請參閱 [https://aka.ms/AzureVMAppCentricBackupExcludeTag](https://aka.ms/AzureVMAppCentricBackupExcludeTag) |deployIfNotExists |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineApplicationCentricBackup_Backup_Deploy_WithOutTag.json) |
|[在沒有指定標記的 Vm 上設定備份到相同位置中的現有復原服務保存庫](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |在與虛擬機器相同的位置和訂用帳戶中，將所有虛擬機器備份至現有的中央復原服務保存庫，以強制執行所有虛擬機器的備份。 當您組織中的中央小組管理訂用帳戶中所有資源的備份時，這樣做會很有用。 您可以選擇性地排除包含指定標記的虛擬機器，以控制指派的範圍。 請參閱 [https://aka.ms/AzureVMCentralBackupExcludeTag](https://aka.ms/AzureVMCentralBackupExcludeTag) |deployIfNotExists、auditIfNotExists、disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json) |
|[為復原服務保存庫將診斷設定部署到 Log Analytics 工作區，以納入資源專屬類別。](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc717fb0c-d118-4c43-ab3d-ece30ac81fb3) |為復原服務保存庫將診斷設定部署到 Log Analytics 工作區，以串流至資源專屬類別。 如果沒有啟用任何資源專屬類別，則會建立新的診斷設定。 |deployIfNotExists |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/EnableRecoveryServiceVaultDiagnosticSetting_Backup_DeployIfNotExist.json) |
