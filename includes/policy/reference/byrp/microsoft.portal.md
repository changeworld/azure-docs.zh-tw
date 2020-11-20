---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c3c9e698aeddbe4818e7b77596eed81e60cb38e5
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "95002140"
---
|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[共用的儀表板不應具有內嵌內容的 markdown 圖格](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |不允許建立在 markdown 圖格中具有內嵌內容的共用儀表板，並強制內容應儲存為線上裝載的 markdown 檔案。 如果您使用 [markdown] 磚中的內嵌內容，則無法管理內容的加密。 藉由設定您自己的儲存體，您可以加密、雙重加密，甚至攜帶您自己的金鑰。 啟用此原則會限制使用者使用 2020-09-01-preview 或更高版本的共用儀表板 REST API。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
