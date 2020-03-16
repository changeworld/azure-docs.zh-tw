---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: bd86457bccb417dc086355b856a5956b83f30fa8
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79381647"
---
|名稱 |描述 |效果 |版本 |GitHub |
|---|---|---|---|---|
|[應用程式設定應使用客戶管理金鑰](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F967a4b4b-2da9-43c1-b7d0-f98d0d74d0b1) |此原則會稽核任何未使用客戶管理金鑰的應用程式設定執行個體。 |Audit, Disabled |1.0.0 |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/CustomerManagedKey_Audit.json)
|[應用程式設定應使用私人連結](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |此原則會稽核任何未使用私人連結的應用程式設定執行個體。 |AuditIfNotExists, Disabled |1.0.0 |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json)
