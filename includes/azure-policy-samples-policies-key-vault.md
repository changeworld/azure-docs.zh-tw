---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 53494bc9642a3df91492d2353a6aa3c6875c1fff
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170126"
---
|名稱 |描述 |效果 |版本 |
|---|---|---|---|
|[將 Key Vault 的診斷設定部署至事件中樞](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_DiagnosticLog_Deploy.json) |針對 Key Vault 部署診斷設定，以在任何缺少此診斷設定的 Key Vault 建立或更新時串流至區域事件中樞。 |deployIfNotExists |1.0.0 |
|[應啟用 Key Vault 中的診斷記錄](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |稽核診斷記錄的啟用情形。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索以供調查之用 |AuditIfNotExists, Disabled |1.0.0 |
|[Key Vault 物件應該要可復原](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |此原則會稽核金鑰保存庫物件是否無法復原。 虛刪除功能可在即使已完成刪除作業後，仍有效地保留資源達指定保留期間 (90天)，並同時提供已刪除物件的外觀。 「清除保護」開啟時，必須等到 90 天的保留期間過後，才能清除處於已刪除狀態的保存庫或物件。 這些保存庫和物件仍可復原，使客戶確信系統會遵循保留原則。 |Audit, Disabled |1.0.0 |
