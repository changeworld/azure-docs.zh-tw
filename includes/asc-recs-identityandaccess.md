---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 01-26-2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 7487262faa8af74af26ac2123686a8e202254e76
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797180"
---
此類別中有 **21** 個建議。

|建議 |描述 |嚴重性 |
|---|---|---|
|應針對您的訂用帳戶指定最多 3 位擁有者 |若要降低擁有者帳戶遭到洩露而造成缺口的潛在風險，建議您將擁有者帳戶數目限制為最多 3 個<br />(相關原則：[您應為訂用帳戶指定最多 3 位擁有者](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4f11b553-d42e-4e3a-89be-32ca364cad4c)) |高 |
|應啟用適用於 Key Vault 的 Azure Defender |Azure 資訊安全中心包含適用於 Key Vault 的 Azure Defender，可提供一層額外的安全性情報。<br>適用於 Key Vault 的 Azure Defender 會偵測是否有人試圖以不尋常且可能有害的方式存取或惡意探索 Key Vault 帳戶。<br><br>重要：補救此建議會產生保護您保存庫的費用。 如果您在此訂用帳戶中沒有任何保存庫，則不會產生任何費用。<br>如果您未來會在此訂用帳戶上建立任何保存庫，則這些保存庫會自動受到保護，並從該時間開始收費。<br> <a href='https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault'>深入了解適用於 Key Vault 的 Azure Defender</a>。<br />(相關原則：[應啟用適用於 Key Vault 的 Azure Defender](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fmicrosoft.authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)) |高 |
|已取代帳戶應該從您的訂用帳戶中移除 |已遭封鎖而無法登入的使用者帳戶，應從您的訂用帳戶中移除。<br>這些帳戶可能是攻擊者的目標，攻擊者可藉此找到存取資料的方式，而不被注意到。<br />(相關原則：[已取代的帳戶應該從您訂用帳戶中移除](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6b1cbf55-e8b6-442f-ba4c-7246b6381474)) |高 |
|具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除 |已遭封鎖而無法登入的使用者帳戶，應從您的訂用帳戶中移除。<br>這些帳戶可能是攻擊者的目標，攻擊者可藉此找到存取資料的方式，而不被注意到。<br />(相關原則：[具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2febb62a0c-3560-49e1-89ed-27e074e9f8ad)) |高 |
|應啟用 Key Vault 中的診斷記錄 |啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。<br />(相關原則：[應啟用 Key Vault 中的診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcf820ca0-f99e-4f3e-84fb-66e913812d21)) |低度 |
|具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除 |具有不同網域名稱 (外部帳戶) 且具有擁有者權限的帳戶，應從您的訂用帳戶中移除。 這可防止未受監視的存取動作。 這些帳戶可能是攻擊者的目標，攻擊者可藉此找到存取資料的方式，而不被注意到。<br />(相關原則：[具有擁有者權限的外部帳戶應該從您的訂閱中移除](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff8456c1c-aa66-4dfb-861a-25d127b775c9)) |高 |
|具有讀取權限的外部帳戶應該從您的訂用帳戶中移除 |具有不同網域名稱 (外部帳戶) 且具有讀取權限的帳戶，應從您的訂用帳戶中移除。 這可防止未受監視的存取動作。 這些帳戶可能是攻擊者的目標，攻擊者可藉此找到存取資料的方式，而不被注意到。<br />(相關原則：[應從您的訂用帳戶中移除具備讀取權限的外部帳戶](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f5f76cf89-fbf2-47fd-a3f4-b891fa780b60)) |高 |
|應從訂用帳戶移除具有寫入權限的外部帳戶 |具有不同網域名稱 (外部帳戶) 且具有擁有者權限的帳戶，應從您的訂用帳戶中移除。 這可防止未受監視的存取動作。 這些帳戶可能是攻擊者的目標，攻擊者可藉此找到存取資料的方式，而不被注意到。<br />(相關原則：[應從訂用帳戶中移除具有寫入權限的外部帳戶](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f5c607a2e-c700-4744-8254-d77e7c9eb5e4)) |高 |
|應該在 Key Vault 上啟用防火牆 |金鑰保存庫防火牆會防止未經授權的流量到達您的金鑰保存庫，並為您的祕密提供額外的保護層級。 啟用防火牆，以確保只有來自允許網路的流量可以存取您的金鑰保存庫。<br /> (相關原則： [Key Vault 上應啟用防火牆](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f55615ac9-af46-4a59-874e-391cc3dfb490))  |中 |
|Key Vault 金鑰應具有到期日 |密碼編譯金鑰應具有已定義的到期日，而不是永久有效。 永久有效的金鑰會讓潛在攻擊者有更多的時間來危害金鑰。 建議的安全性做法是在密碼編譯金鑰上設定到期日。<br /> (相關原則： [Key Vault 金鑰應具有到期日](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f152b15f7-8e1f-4c1f-ab71-8c010ba5dbc0))  |高 |
|Key Vault 祕密應設定到期日 |祕密應具有已定義的到期日，而不是永久有效。 永久有效的祕密會讓潛在攻擊者有更多的時間來危害祕密。 建議的安全性做法是在祕密上設定到期日。<br /> (相關原則： [Key Vault 秘密應具有到期日](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f98728c90-32c7-4049-8429-847dc0f4fe37))  |高 |
|金鑰保存庫應啟用清除保護 |惡意刪除金鑰保存庫可能會導致永久的資料遺失。 您組織中可能有惡意的內部人員能夠刪除和清除金鑰保存庫。 清除保護可對虛刪除的金鑰保存庫強制執行必要的保留期間，以保護您免於遭受內部攻擊。 您的組織內部人員或 Microsoft 在虛刪除保留期間內都無法清除您的金鑰保存庫。<br /> (相關原則： [金鑰保存庫應啟用清除保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0b60c0b2-2dc2-4e1c-b5c9-abbed971de53))  |中 |
|金鑰保存庫應已啟用虛刪除 |刪除未啟用虛刪除的金鑰保存庫時，將會永久刪除儲存在金鑰保存庫中的所有秘密、金鑰和憑證。 意外刪除金鑰保存庫可能會導致永久的資料遺失。 虛刪除可讓您復原不小心刪除的金鑰保存庫，以進行可設定的保留期限。<br /> (相關原則： [金鑰保存庫應已啟用虛刪除](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f1e66c121-a66a-4b1f-9b83-0fd99bf0fc2d))  |高 |
|應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA |具備擁有者權限的所有訂用帳戶，均應啟用多重要素驗證 (MFA)，以避免發生帳戶或資源資料外洩。<br />(相關原則：[應為您訂用帳戶中具有擁有者權限的帳戶啟用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2faa633080-8b72-40c4-a2d7-d00c03e80bed)) |高 |
|應在您訂用帳戶上具有讀取權限的帳戶上啟用 MFA |具備讀取權限的所有訂用帳戶，均應啟用多重要素驗證 (MFA)，以避免發生帳戶或資源資料外洩。<br />(相關原則：[應為您訂用帳戶中具有讀取權限的帳戶啟用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fe3576e28-8b17-4677-84c3-db2990658d64)) |高 |
|應在您訂用帳戶上具有寫入權限的帳戶上啟用 MFA |具備寫入權限的所有訂用帳戶，均應啟用多重要素驗證 (MFA)，以避免發生帳戶或資源資料外洩。<br />(相關原則：[應為您訂用帳戶中具有寫入權限的帳戶啟用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f9297c21d-2ed6-4474-b48f-163f75654ce3)) |高 |
|應設定 Key Vault 的私人端點 |私人連結提供不需透過公用網際網路傳送流量即可將 Key Vault 連線至 Azure 資源的方法。 私人連結提供深層防禦保護，以防止資料外流。<br /> (相關原則： [私人端點應設定 Key Vault](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f5f0bc445-3935-4915-9981-011aa2b46147))  |中 |
|應使用服務主體來保護您的訂用帳戶，而不是管理憑證 |管理憑證可讓使用這些憑證進行驗證的任何人，來管理與其相關聯的訂用帳戶。 若要更安全地管理訂用帳戶，建議將服務主體與 Resource Manager搭配使用，以限制憑證洩露時的影響。 其也會自動化資源管理。 <br />(相關原則：[應使用服務主體來保護您的訂用帳戶，而不是管理憑證](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6646a0bd-e110-40ca-bb97-84fcee63c414)) |中 |
|應禁止儲存體帳戶公用存取 |以匿名的公開讀取權限來存取 Azure 儲存體中的容器和 Blob，是共用資料的便利方式，但也可能會帶來安全性風險。 為了防止不想要的匿名存取所造成的資料缺口，Microsoft 建議除非您的案例需要，否則避免公開存取儲存體帳戶。<br />(相關原則：[應禁止儲存體帳戶公用存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fmicrosoft.authorization%2fpolicyDefinitions%2f4fa4b6c0-31ca-4c0d-b10d-24b96f62a751)) |中 |
|應將一個以上的擁有者指派給您的訂用帳戶 |指定多位訂用帳戶擁有者，以擁有系統管理員存取備援。<br />(相關原則：[應指派一個以上的擁有者給您的訂用帳戶](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f09024ccc-0c5f-475e-9457-b7c0d9ed487b)) |高 |
|憑證儲存在 Azure Key Vault 中的有效期間不應超過 12 個月 |請確定您憑證的有效期間沒有超過 12 個月。<br />(相關原則：[憑證應具有指定的有效期間上限](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0a075868-4c26-42ef-914c-5bc007359560)) |中 |
|||
