---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/13/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 6e551f8aa1a32fde62bb97bfe0424e6213771037
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650557"
---
|名稱 |描述 |效果 |版本 |GitHub |
|---|---|---|---|---|
|[將串流分析的診斷設定部署至事件中樞](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fedf3780c-3d70-40fe-b17e-ab72013dafca) |針對串流分析部署診斷設定，以在任何缺少此診斷設定的串流分析建立或更新時串流至區域事件中樞。 |DeployIfNotExists, Disabled |2.0.0 |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/StreamAnalytics_DeployDiagnosticLog_Deploy_EventHub.json) |
|[將串流分析的診斷設定部署至 Log Analytics 工作區](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F237e0f7e-b0e8-4ec4-ad46-8c12cb66d673) |針對串流分析部署診斷設定，以在任何缺少此診斷設定的串流分析建立或更新時串流至區域 Log Analytics 工作區。 |DeployIfNotExists, Disabled |1.0.0 |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/StreamAnalytics_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[應啟用 Azure 串流分析中的診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |稽核診斷記錄的啟用情形。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索以供調查之用 |AuditIfNotExists, Disabled |2.0.0 |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
