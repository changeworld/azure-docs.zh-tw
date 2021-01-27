---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c925eb4e9cbd9fa5df472844d7e3bcae278c2102
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98859879"
---
## <a name="azure-security-benchmark"></a>Azure 安全性效能評定

[Azure 安全性基準](../../../../articles/security/benchmarks/overview.md)提供如何在 Azure 上保護雲端解決方案的建議。 若要查看此服務如何完全對應到 Azure 安全性基準，請參閱 [Azure 安全性基準對應檔案](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

若要檢閱適用於所有 Azure 服務的可用 Azure 原則內建項目對應到此合規性標準的方法，請參閱 [Azure 原則法規合規性 - Azure 安全性基準](../../../../articles/governance/policy/samples/azure-security-benchmark.md)。

|網域 |控制識別碼 |控制標題 |原則<br /><sub>(Azure 入口網站)</sub> |原則版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|網路安全性 |NS-2 |將私人網路連接在一起 |[Azure Cache for Redis 應位於虛擬網路內](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|資料保護 |DP-4 |加密傳輸中的機密資訊 |[只允許對您 Azure Cache for Redis 的安全連線](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="azure-security-benchmark-v1"></a>Azure 安全性效能評定 v1

[Azure 安全性基準](../../../../articles/security/benchmarks/overview.md)提供如何在 Azure 上保護雲端解決方案的建議。 若要查看此服務如何完全對應到 Azure 安全性基準，請參閱 [Azure 安全性基準對應檔案](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

若要檢閱適用於所有 Azure 服務的可用 Azure 原則內建項目對應到此合規性標準的方法，請參閱 [Azure 原則法規合規性 - Azure 安全性基準](../../../../articles/governance/policy/samples/azure-security-benchmark.md)。

|網域 |控制識別碼 |控制標題 |原則<br /><sub>(Azure 入口網站)</sub> |原則版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|資料保護 |4.4 |加密傳輸中的所有敏感性資訊 |[只允許對您 Azure Cache for Redis 的安全連線](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="cmmc-level-3"></a>CMMC 層級3

若要查看所有 Azure 服務的可用 Azure 原則內建如何對應到此合規性標準，請參閱 [Azure 原則法規合規性-CMMC 層級 3](../../../../articles/governance/policy/samples/cmmc-l3.md)。
如需此合規性標準的詳細資訊，請參閱 [網路安全性成熟度模型認證 (CMMC) ](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf)。

|網域 |控制識別碼 |控制標題 |原則<br /><sub>(Azure 入口網站)</sub> |原則版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|存取控制 |AC. 1.002 |將資訊系統存取許可權制為允許授權使用者執行的交易和函式類型。 |[只允許對您 Azure Cache for Redis 的安全連線](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|系統與通訊保護 |SC. 1.175 |監視、控制和保護通訊 (也就是組織系統所傳輸或接收的資訊)，其範圍是組織系統的外部界限和關鍵內部界限。 |[只允許對您 Azure Cache for Redis 的安全連線](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|系統與通訊保護 |SC. 3.185 |實作密碼編譯機制，以防止在傳輸期間未經授權洩漏 CUI，除非受到其他實體保護。 |[只允許對您 Azure Cache for Redis 的安全連線](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

若要檢閱適用於所有 Azure 服務的可用 Azure 原則內建項目對應到此合規性標準的方法，請參閱 [Azure 原則法規合規性 - HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md)。
如需此合規性標準的詳細資訊，請參閱 [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)。

|網域 |控制識別碼 |控制標題 |原則<br /><sub>(Azure 入口網站)</sub> |原則版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|網路連線控制 |0809.01n2Organizational.1234 - 01.n |網路流量會根據組織的存取控制原則，透過每個網路存取點的防火牆和其他網路相關限制，或外部電信服務的受控介面來加以控制。 |[只允許對您 Azure Cache for Redis 的安全連線](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|網路連線控制 |0810.01n2Organizational.5 - 01.n |資訊會在透過開放的公用網路傳輸時受到保護及進行最低程度的加密。 |[只允許對您 Azure Cache for Redis 的安全連線](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|網路連線控制 |0811.01n2Organizational.6 - 01.n |流量原則的例外狀況中會記載支援的任務/商務需求及例外狀況的持續時間，並至少每年檢查一次；當明確的任務/商務需求不再支援流量原則例外狀況時，則會將例外狀況移除。 |[只允許對您 Azure Cache for Redis 的安全連線](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|網路連線控制 |0812.01n2Organizational.8 - 01.n |建立非遠端連線的遠端裝置不得與外部 (遠端) 資源進行通訊。 |[只允許對您 Azure Cache for Redis 的安全連線](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|網路連線控制 |0814.01n1Organizational.12 - 01.n |根據存取控制原則和臨床和商務應用程式的需求，在受控介面上使用預設拒絕和允許例外的原則來限制使用者連線到內部網路的能力。 |[只允許對您 Azure Cache for Redis 的安全連線](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|識別與外部合作對象相關的風險 |1451.05iCSPOrganizational.2 - 05.i |雲端服務提供者會藉由對供應鏈中所有人員制定適當的責任區分、角色型存取及最低權限存取，來設計和實作控制措施，以減輕和遏制資料安全性風險。 |[只允許對您 Azure Cache for Redis 的安全連線](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|線上交易 |0946.09y2Organizational.14 - 09.y |組織需要在交易中牽涉到的每一方之間使用加密，以及每一方都需使用電子簽章。 |[只允許對您 Azure Cache for Redis 的安全連線](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="nist-sp-800-171-r2"></a>NIST SP 800-171 R2

若要檢閱適用於所有 Azure 服務的可用 Azure 原則內建項目對應到此合規性標準的方法，請參閱 [Azure 原則法規合規性 - NIST SP 800-171 R2](../../../../articles/governance/policy/samples/nist-sp-800-171-r2.md)。
如需此合規性標準的詳細資訊，請參閱 [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final)。

|網域 |控制識別碼 |控制標題 |原則<br /><sub>(Azure 入口網站)</sub> |原則版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|系統與通訊保護 |3.13.1 |監視、控制和保護通訊 (也就是組織系統所傳輸或接收的資訊)，其範圍是組織系統的外部界限和關鍵內部界限。 |[只允許對您 Azure Cache for Redis 的安全連線](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|系統與通訊保護 |3.13.8 |實作密碼編譯機制，以防止在傳輸期間未經授權洩漏 CUI，除非受到其他實體保護。 |[只允許對您 Azure Cache for Redis 的安全連線](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="nist-sp-800-53-r4"></a>NIST SP 800-53 R4

若要檢閱適用於所有 Azure 服務的可用 Azure 原則內建項目對應到此合規性標準的方法，請參閱 [Azure 原則法規合規性 - NIST SP 800-53 R4](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md)。
如需此合規性標準的詳細資訊，請參閱 [NIST SP 800-53 R4](https://nvd.nist.gov/800-53)。

|網域 |控制識別碼 |控制標題 |原則<br /><sub>(Azure 入口網站)</sub> |原則版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|系統與通訊保護 |SC-8 (1) |傳輸機密性和完整性 \| 密碼編譯或替代實體保護 |[只允許對您 Azure Cache for Redis 的安全連線](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

