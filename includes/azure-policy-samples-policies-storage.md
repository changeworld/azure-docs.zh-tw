---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 7aa6abb1a5fa9c969ca5e6d0730bed3b461fe81b
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170286"
---
|名稱 |描述 |效果 |版本 |
|---|---|---|---|
|[允許的儲存體帳戶 SKU](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/AllowedStorageSkus_Deny.json) |此原則可讓您指定一組組織可以部署的儲存體帳戶 SKU。 |拒絕 |1.0.0 |
|[稽核不受限制的儲存體帳戶網路存取](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |稽核儲存體帳戶防火牆設定中不受限制的網路存取。 請改為設定網路規則，只允許來自認可之網路的應用程式存取儲存體帳戶。 若要允許來自特定網際網路或內部部署用戶端的連線，可將存取權授與來自特定 Azure 虛擬網路的流量，或授與公用網際網路 IP 位址範圍 |Audit, Disabled |1.0.0 |
|[在儲存體帳戶部署進階威脅防護](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/StorageAdvancedThreatProtection_Deploy.json) |此原則可在儲存體帳戶上進行進階威脅防護。 |DeployIfNotExists, Disabled |1.0.0 |
|[應為儲存體帳戶啟用異地備援儲存體](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/GeoRedundant_StorageAccounts_Audit.json) |此原則會稽核所有未啟用異地備援儲存體的儲存體帳戶。 |Audit, Disabled |1.0.0 |
|[應啟用儲存體帳戶的安全傳輸](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |稽核儲存體帳戶中安全傳輸的需求。 安全傳輸這個選項會強制您的儲存體帳戶僅接受來自安全連線 (HTTPS) 的要求。 使用 HTTPS 可確保伺服器與服務之間的驗證，避免傳輸中的資料遭受網路層的攻擊，例如中間人攻擊、竊聽及工作階段劫持 |Audit, Deny, Disabled |1.0.0 |
|[儲存體帳戶應允許來自信任 Microsoft 服務的存取](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |有些與儲存體帳戶互動的 Microsoft 服務是從無法透過網路規則授與存取權的網路運作。 若要讓這類服務如預期方式運作，請允許受信任的 Microsoft 服務集合略過網路規則。 這些服務會使用增強式驗證存取儲存體帳戶。 |Audit, Deny, Disabled |1.0.0 |
|[儲存體帳戶應移轉至新的 Azure Resource Manager 資源](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Storage/Classic_AuditForClassicStorages_Audit.json) |在您的儲存體帳戶使用新的 Azure Resource Manager 以加強安全性，除了增強存取控制 (RBAC)、改善稽核、提供以 Azure Resource Manager 為基礎的部署及治理、受控身分識別的存取、金鑰保存庫的祕密存取、以 Azure AD 為基礎的驗證，還支援標記和資源群組，讓安全性管理更加輕鬆 |Audit, Deny, Disabled |1.0.0 |
