---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 389a7bf897b4b11b1b7e4d8e8b3f3a645ebd25aa
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98807623"
---
|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure API for FHIR 應該使用客戶自控金鑰 (CMK) 來加密待用資料](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |若需要符合法規或合規性需求，請使用客戶管理的金鑰來控制 Azure API for FHIR 中儲存的待用資料加密。 客戶管理的金鑰也會在使用服務管理的金鑰完成的預設加密以外新增第二層加密，來提供雙重加密。 |稽核, 已停用 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|[Azure API for FHIR 應該使用私人連結](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |Azure API for FHIR 應該至少有一個已核准的私人端點連結。 虛擬網路中的用戶端可以安全地存取透過私人連結而擁有私人端點連線的資源。 如需詳細資訊，請瀏覽：[https://aka.ms/fhir-privatelink](https://aka.ms/fhir-privatelink)。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[CORS 不應允許每個網域存取您的 API for FHIR](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |跨原始資源共用 (CORS) 不應允許所有網域存取適用於 FHIR 的 API。 若要保護您適用於 FHIR 的 API，請移除所有網域的存取權，並明確定義允許連線的網域。 |稽核, 已停用 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
