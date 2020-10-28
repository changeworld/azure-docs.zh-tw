---
title: 佇列儲存體的安全性建議
titleSuffix: Azure Storage
description: 深入瞭解佇列儲存體的安全性建議。 依照我們的共同責任模型中所述，實施本指南將有助於您履行安全性義務。
services: storage
author: tamram
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: tamram
ms.custom: security-recommendations
ms.openlocfilehash: 1b13bcbe17ac546030b177eea2b2bf8a290a6af6
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92781561"
---
# <a name="security-recommendations-for-queue-storage"></a>佇列儲存體的安全性建議

本文包含佇列儲存體的安全性建議。 依照我們的共同責任模型所述，執行這些建議有助於您履行安全性義務。 如需 Microsoft 如何履行服務提供者責任的詳細資訊，請參閱[雲端運算的共同責任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf)。

本文中包含的一些建議可由 Azure 資訊安全中心自動監視。 Azure 資訊安全中心是保護 Azure 中資源的第一道防線。 如需 Azure 資訊安全中心的詳細資訊，請參閱 [Azure 資訊安全中心是什麼？](../../security-center/security-center-introduction.md)。

Azure 資訊安全中心會定期分析 Azure 資源的安全性狀態，以找出潛在的安全性弱點。 接著，這會為您提供如何解決這些問題的建議。 如需 Azure 資訊安全中心建議的詳細資訊，請參閱 [Azure 資訊安全中心的安全性建議](../../security-center/security-center-recommendations.md)。

## <a name="data-protection"></a>資料保護

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
| 使用 Azure Resource Manager 部署模型 | 使用 Azure Resource Manager 部署模型來建立新的儲存體帳戶，以進行重要的安全性增強功能，包括絕佳的 Azure 角色型存取控制 (Azure RBAC) 和審核、Resource Manager 型部署和治理、受控識別的存取、秘密的 Azure Key Vault 存取，以及 Azure AD 資料和資源的存取 Azure 儲存體型驗證和授權。 可能的話，請將使用傳統部署模型的現有儲存體帳戶遷移為使用 Azure Resource Manager。 如需 Azure Resource Manager 的詳細資訊，請參閱 [Azure Resource Manager 總覽](../../azure-resource-manager/management/overview.md)。 | - |
| 為您所有的儲存體帳戶啟用先進的威脅防護 | Azure 儲存體的「先進威脅防護」提供額外一層的安全性情報，可偵測到不尋常且可能有害的存取或惡意探索儲存體帳戶的嘗試。 當活動中發生異常狀況，而且也透過電子郵件傳送給訂用帳戶管理員時，會在 Azure 資訊安全中心中觸發安全性警示，並提供可疑活動的詳細資料，以及如何調查和修復威脅的建議。 如需詳細資訊，請參閱 [Azure 儲存體的 Advanced 威脅防護](../common/azure-defender-storage-configure.md)。 | [是](../../security-center/security-center-remediate-recommendations.md) |
| 將共用存取簽章 (SAS) 權杖限制為僅限 HTTPS 連接 | 當用戶端使用 SAS 權杖存取佇列資料時，需要 HTTPS 有助於將竊聽的風險降至最低。 如需詳細資訊，請參閱 [使用共用存取簽章將有限存取權授與 Azure 儲存體資源 (SAS) ](../common/storage-sas-overview.md)。 | - |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
| 使用 Azure Active Directory (Azure AD) 來授權佇列資料的存取權 | Azure AD 透過共用金鑰提供更高的安全性和使用，以授權佇列儲存體的要求。 如需詳細資訊，請參閱 [使用 Azure Active Directory 授與 Azure blob 和佇列的存取權](../common/storage-auth-aad.md)。 | - |
| 透過 Azure RBAC 將許可權指派給 Azure AD 安全性主體時，請記住最低許可權的主體。 | 將角色指派給使用者、群組或應用程式時，只授與該安全性主體執行其工作所需的許可權。 限制對資源的存取有助於防止不慎和惡意誤用您的資料。 | - |
| 使用 Azure Key Vault 保護您的帳戶存取金鑰 | Microsoft 建議使用 Azure AD 來授權 Azure 儲存體的要求。 但是，如果您必須使用共用金鑰授權，請使用 Azure Key Vault 保護您的帳戶金鑰。 您可以在執行時間從金鑰保存庫中取出金鑰，而不是將它們儲存到您的應用程式。 | - |
| 定期重新產生您的帳戶金鑰 | 輪替帳戶金鑰會定期減少將資料公開給惡意執行者的風險。 | - |
| 將許可權指派給 SAS 時，請記住最低許可權的主體 | 建立 SAS 時，請只指定用戶端執行其功能所需的許可權。 限制對資源的存取有助於防止不慎和惡意誤用您的資料。 | - |
| 為您發出至用戶端的任何 SAS 備妥撤銷計畫 | 如果 SAS 遭到入侵，您會想要儘快撤銷 SAS。 若要撤銷使用者委派 SAS，請撤銷使用者委派金鑰，以快速使與該金鑰相關聯的所有簽章失效。 若要撤銷與預存存取原則相關聯的服務 SAS，您可以刪除儲存的存取原則、重新命名原則，或將其到期時間變更為過去的時間。 如需詳細資訊，請參閱 [使用共用存取簽章將有限存取權授與 Azure 儲存體資源 (SAS) ](../common/storage-sas-overview.md)。  | - |
| 如果服務 SAS 未與預存存取原則相關聯，請將到期時間設定為一小時或更少 | 無法撤銷未與儲存的存取原則相關聯的服務 SAS。 基於這個理由，建議將到期時間限制為一小時或更短的時間才會生效。 | - |

## <a name="networking"></a>網路功能

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
| 為儲存體帳戶設定傳輸層安全性 (TLS) 的最小必要版本。  | 要求用戶端使用更安全的 TLS 版本，藉由設定該帳戶的最小 TLS 版本來對 Azure 儲存體帳戶提出要求。 如需詳細資訊，請參閱為 [儲存體帳戶設定傳輸層安全性的最小必要版本 (TLS) ](../common/transport-layer-security-configure-minimum-version.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)| - |
| 在您所有的儲存體帳戶上啟用「 **需要安全傳輸** 」選項 | 當您啟用「 **需要安全傳輸** 」選項時，對儲存體帳戶提出的所有要求都必須透過安全連線進行。 透過 HTTP 提出的任何要求都會失敗。 如需詳細資訊，請參閱 [Azure 儲存體中的「需要安全傳輸](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)」。 | [是](../../security-center/security-center-remediate-recommendations.md) |
| 啟用防火牆規則 | 設定防火牆規則，以將您儲存體帳戶的存取限制為源自指定 IP 位址或範圍的要求，或從 Azure 虛擬網路中的子網清單 (VNet) 。 如需設定防火牆規則的詳細資訊，請參閱 [設定 Azure 儲存體防火牆和虛擬網路](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)。 | - |
| 允許信任的 Microsoft 服務存取儲存體帳戶 | 開啟儲存體帳戶的防火牆規則會預設封鎖傳入的資料要求，除非要求源自 Azure 虛擬網路 (VNet) 內運作的服務或源自允許的公用 IP位址。 封鎖的要求包括來自其他 Azure 服務、Azure 入口網站及記錄與計量服務等等的要求。 您可以藉由新增例外狀況來允許受信任的 Microsoft 服務存取儲存體帳戶，以允許來自其他 Azure 服務的要求。 如需為信任的 Microsoft 服務新增例外狀況的詳細資訊，請參閱 [設定 Azure 儲存體防火牆和虛擬網路](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)。| - |
| 使用私人端點 | 私人端點會從您的 Azure 虛擬網路將私人 IP 位址指派給儲存體帳戶 (VNet) 。 它會透過私人連結保護您的 VNet 與儲存體帳戶之間的所有流量。 如需私人端點的詳細資訊，請參閱 [使用 Azure 私人端點私下連接至儲存體帳戶](../../private-link/tutorial-private-endpoint-storage-portal.md)。 | - |
| 使用 VNet 服務標記 | 服務標籤代表來自指定 Azure 服務的一組 IP 位址前置詞。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。 如需 Azure 儲存體所支援服務標籤的詳細資訊，請參閱 [Azure 服務標記總覽](../../virtual-network/service-tags-overview.md)。 如需示範如何使用服務標記來建立輸出網路規則的教學課程，請參閱 [限制對 PaaS 資源的存取](../../virtual-network/tutorial-restrict-network-access-to-resources.md)。 | - |
| 限制對特定網路的網路存取 | 限制網路存取需要存取的用戶端需要存取，可減少您的資源暴露在網路攻擊的風險。 | [是](../../security-center/security-center-remediate-recommendations.md) |

## <a name="loggingmonitoring"></a>記錄/監視

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
| 追蹤要求的授權方式 | 啟用 Azure 儲存體記錄，以追蹤針對 Azure 儲存體所提出的每個要求如何獲得授權。 這些記錄會指出是以匿名方式、使用 OAuth 2.0 權杖、使用共用金鑰，或使用共用存取簽章 (SAS) 來進行要求。 如需詳細資訊，請參閱 [Azure 儲存體分析記錄](../common/storage-analytics-logging.md)。 | - |

## <a name="next-steps"></a>後續步驟

- [Azure 安全性文件](../../security/index.yml)
- [安全開發檔](../../security/develop/index.yml)。