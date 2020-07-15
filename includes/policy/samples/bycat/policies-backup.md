---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 345cc8d5c1bc362973c8fae0e1c5605faf6e6e2d
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277121"
---
|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應該為虛擬機器啟用 Azure 備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |此原則有助於在所有虛擬機器都啟用 Azure 備份服務時進行稽核。 Azure 備份是符合成本效益的一鍵備份解決方案，能夠簡化資料復原，且較其他雲端備份服務易於啟用。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[設定將某位置的 VM 備份到相同位置中現有的中央保存庫](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |此原則會將指定位置中 VM 的 Azure 備份保護設定到相同位置中的現有中央保存庫。 此原則僅適用於尚未設定備份的 VM。 建議將此原則指派給不超過 200 個 VM。 如果將此原則指派給超過 200 個 VM，可能會導致備份在定義的排程過後數小時才觸發。 此原則將會增強，以支援更多的 VM 映像。 |deployIfNotExists、auditIfNotExists、disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json) |
