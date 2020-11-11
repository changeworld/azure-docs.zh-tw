---
title: Azure 受控 HSM 概觀 - Azure 受控 HSM | Microsoft Docs
description: Azure 受控 HSM 是一項雲端服務，可保護雲端應用程式的密碼編譯金鑰。
services: key-vault
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: overview
ms.custom: mvc
ms.date: 09/15/2020
ms.author: mbaldwin
author: msmbaldwin
ms.openlocfilehash: 95feeebf009cae22cf3952df80596c12e75cbe4b
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357567"
---
# <a name="what-is-azure-key-vault-managed-hsm-preview"></a>什麼是 Azure Key Vault 受控 HSM (預覽)？

Azure Key Vault 受控 HSM 是完全受控、高可用性、單一租用戶、符合標準的雲端服務，可讓您使用經過 **FIPS 140-2 層級 3** 驗證的 HSM，來保護雲端應用程式的密碼編譯金鑰。  

## <a name="why-use-managed-hsm"></a>為何要使用受控 HSM？

### <a name="fully-managed-highly-available-single-tenant-hsm-as-a-service"></a>完全受控、高可用性、單一租用戶 HSM 即服務

- **完全受控** ：HSM 佈建、設定、修補和維護由服務處理。 
- **高可用性和區域復原性** (支援可用性區域)：每個 HSM 叢集都包含至少跨兩個可用性區域的多個 HSM 分割區。 硬體故障時，HSM 叢集的成員分割區將自動遷移至狀況良好的節點。
- **單一租用戶** ：每個受控 HSM 執行個體分別供單一客戶專用，其中包含多個 HSM 分割區的叢集。 每個 HSM 叢集分別使用個別的客戶專屬安全性網域，此網域會透過密碼編譯隔離每個客戶的 HSM 叢集。


### <a name="access-control-enhanced-data-protection--compliance"></a>存取控制、增強型資料保護和合規性

- **集中管理金鑰** ：在單一位置管理整個組織的重要、高價值金鑰。 透過精細的個別金鑰權限，根據「最低權限存取」原則控制對每個金鑰的存取。
- **隔離的存取控制** ：受控 HSM 的「本機 RBAC」存取控制模型，可讓指定的 HSM 叢集管理員完全掌控連管理群組、訂用帳戶或資源群組管理員都無法覆寫的 HSM。
- **經過 FIPS 140-2 層級 3 驗證的 HSM** ：使用經過 FIPS (聯邦資訊保護標準) 140-2 層級 3 驗證的 HSM，來保護您的資料並達到合規性需求。 受控 HSM 會使用 Marvell LiquidSecurity HSM 介面卡。
- **監視和稽核** ：與 Azure 監視器完全整合。 透過 Azure 監視器取得所有活動的完整記錄。 使用 Azure Log Analytics 進行分析和發出警示。

### <a name="integrated-with-azure-and-microsoft-paassaas-services"></a>與 Azure 和 Microsoft PaaS/SaaS 服務整合 

- 產生 (或使用 [BYOK](hsm-protected-keys-byok.md) 匯入) 金鑰，並使用這些金鑰將 Azure 服務中的待用資料加密，例如 [Azure 儲存體](../../storage/common/customer-managed-keys-overview.md)、[Azure SQL](../../azure-sql/database/transparent-data-encryption-byok-overview.md) 和 [Azure 資訊保護](/azure/information-protection/byok-price-restrictions)等服務。

### <a name="uses-same-api-and-management-interfaces-as-key-vault"></a>使用與 Key Vault 相同的 API 和管理介面

- 輕鬆遷移使用保存庫 (多租用戶) 的現有應用程式，以使用受控 HSM。
- 無論使用何種金鑰管理解決方案，您所有的應用程式都能使用相同的應用程式開發和部署模式：多租用戶保存庫或單一租用戶受控 HSM

### <a name="import-keys-from-your-on-premise-hsms"></a>從您的內部部署 HSM 匯入金鑰

- 在您的內部部署 HSM 中產生受 HSM 保護的金鑰，並將其安全地匯入至受控 HSM

## <a name="next-steps"></a>下一步
- 請參閱[快速入門：使用 Azure CLI 佈建並啟動受控 HSM](quick-create-cli.md)，以建立並啟動受控 HSM
- 請參閱[使用 Azure Key Vault 受控 HSM 的最佳做法](best-practices.md)
