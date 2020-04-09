---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/03/2020
ms.author: dacoulte
ms.openlocfilehash: 156f253e9559f493ba6cccde4de4744068a32fc4
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758473"
---
|名稱 |描述 |效果 |版本 |GitHub |
|---|---|---|---|---|
|[應該為虛擬機器啟用 Azure 備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |此原則有助於在所有虛擬機器都啟用 Azure 備份服務時進行稽核。 Azure 備份是符合成本效益的一鍵備份解決方案，能夠簡化資料復原，且較其他雲端備份服務易於啟用。 |AuditIfNotExists, Disabled |1.0.0 |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json)
|[設定將某位置的 VM 備份到相同位置中現有的中央保存庫](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |此原則會將指定位置中 VM 的 Azure 備份保護設定到相同位置中的現有中央保存庫。 此原則僅適用於尚未設定備份的 VM。 建議將此原則指派給不超過 200 個 VM。 如果將此原則指派給超過 200 個 VM，可能會導致備份在定義的排程過後數小時才觸發。 此原則將會增強，以支援更多的 VM 映像。 |deployIfNotExists、auditIfNotExists、disabled |1.0.0 |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json)
