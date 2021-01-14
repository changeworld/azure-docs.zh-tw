---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 272a547d56fcfab6e016a6aafbc12f8c664abde8
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98047172"
---
|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[共用儀表板的 Markdown 組件不應出現內嵌內容](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |不允許在共用儀表板中建立具有內嵌內容的 Markdown 組件，也不允許強制將內容儲存為線上託管的 Markdown 檔案。 如果您在 Markdown 組件中使用內嵌內容，就無法管理內容的加密。 藉由設定自己的儲存體，您可以加密、雙重加密，甚至攜帶您自己的金鑰。 啟用此原則會限制使用者使用 2020-09-01-preview 或更新版本的共用儀表板 REST API。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
