---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 3d3f88ad5911a959d0550d68452a894e8bb1494a
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98703781"
---
|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure 資料箱作業應針對裝置上的待用資料啟用雙重加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |針對裝置上的待用資料啟用軟體型的第二層加密。 裝置已透過進階加密標準的 256 位加密來保護待用資料。 此選項會新增第二層資料加密。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|[Azure 資料箱作業應使用客戶自控金鑰加密裝置解除鎖定密碼](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86efb160-8de7-451d-bc08-5d475b0aadae) |使用客戶自控金鑰控制 Azure 資料箱的裝置解除鎖定密碼加密。 客戶自控金鑰也有助於管理資料箱服務對裝置解除鎖定密碼的存取權，以便準備裝置並自動複製資料。 裝置本身上的資料已使用進階加密標準 256 位加密進行待用加密，而裝置解除鎖定密碼預設會使用 Microsoft 受控金鑰加密。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_CMK_Audit.json) |
