---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/13/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 22fc625b6da2e9c6320920274153a9962e0d910f
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83656269"
---
|名稱 |描述 |效果 |版本 |GitHub |
|---|---|---|---|---|
|[將 Data Lake Storage Gen1 的診斷設定部署至事件中樞](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8d096bc-85de-4c5f-8cfb-857bd1b9d62d) |針對 Data Lake Storage Gen1 部署診斷設定，以在任何缺少此診斷設定的 Data Lake Storage Gen1 建立或更新時串流至區域事件中樞。 |DeployIfNotExists, Disabled |2.0.0 |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DataLakeStorage_DeployDiagnosticLog_Deploy_EventHub.json) |
|[將 Data Lake Storage Gen1 的診斷設定部署至 Log Analytics 工作區](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F25763a0a-5783-4f14-969e-79d4933eb74b) |針對 Data Lake Storage Gen1 部署診斷設定，以在任何缺少此診斷設定的 Data Lake Storage Gen1 建立或更新時串流至區域 Log Analytics 工作區。 |DeployIfNotExists, Disabled |1.0.0 |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DataLakeStorage_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[應在 Azure Data Lake Store 中啟用診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |稽核診斷記錄的啟用情形。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索以供調查之用 |AuditIfNotExists, Disabled |2.0.0 |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[Data Lake Store 帳戶需要加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7ff3161-0087-490a-9ad9-ad6217f4f43a) |此原則可確保所有 Data Lake Store 帳戶上均已啟用加密 |deny |1.0.0 |[連結](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStoreEncryption_Deny.json) |
