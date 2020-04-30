---
title: 佇列儲存體的安全性建議
titleSuffix: Azure Storage
description: 瞭解佇列儲存體的安全性建議。 依照我們的共同責任模型所述，執行本指南可協助您滿足您的安全性義務。
services: storage
author: tamram
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: tamram
ms.custom: security-recommendations
ms.openlocfilehash: 11e16453cc2a6044c4b153bd1556d85545ff9625
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82086605"
---
# <a name="security-recommendations-for-queue-storage"></a>佇列儲存體的安全性建議

本文包含佇列儲存體的安全性建議。 依照我們的共同責任模型所述，執行這些建議可協助您履行安全性義務。 如需 Microsoft 如何履行服務提供者責任的詳細資訊，請參閱[雲端運算的共同責任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf)。

本文中包含的一些建議可由 Azure 資訊安全中心自動監視。 Azure 資訊安全中心是保護 Azure 中資源的第一道防線。 如需 Azure 資訊安全中心的詳細資訊，請參閱[什麼是 Azure 資訊安全中心？](../../security-center/security-center-intro.md)。

Azure 資訊安全中心會定期分析 Azure 資源的安全性狀態，以找出潛在的安全性弱點。 接著，它會為您提供如何解決這些問題的建議。 如需 Azure 資訊安全中心建議的詳細資訊，請參閱[Azure 資訊安全中心中的安全性建議](../../security-center/security-center-recommendations.md)。

## <a name="data-protection"></a>資料保護

| 建議 | 評價 | 資訊安全中心 |
|-|----|--|
| 使用 Azure Resource Manager 部署模型 | 使用 Azure Resource Manager 部署模型建立新的儲存體帳戶，以進行重要的安全性增強功能，包括高階存取控制（RBAC）和審核、以 Resource Manager 為基礎的部署和治理、受控識別的存取、密碼 Azure Key Vault 的存取權，以及存取 Azure 儲存體資料和資源的 Azure AD 型驗證和授權。 可能的話，請將使用傳統部署模型的現有儲存體帳戶遷移到 Azure Resource Manager。 如需 Azure Resource Manager 的詳細資訊，請參閱[Azure Resource Manager 總覽](/azure/azure-resource-manager/resource-group-overview)。 | - |
| 在您的所有儲存體帳戶上啟用 [**需要安全傳輸**] 選項 | 當您啟用 [**需要安全傳輸**] 選項時，對儲存體帳戶提出的所有要求都必須透過安全連線進行。 透過 HTTP 提出的任何要求都會失敗。 如需詳細資訊，請參閱[Azure 儲存體中需要安全傳輸](../common/storage-require-secure-transfer.md)。 | [是](../../security-center/security-center-sql-service-recommendations.md) |
| 為您所有的儲存體帳戶啟用先進的威脅防護 | Azure 儲存體的先進威脅防護提供一層額外的安全性情報，可偵測不尋常且可能有害的存取或惡意探索儲存體帳戶的嘗試。 當活動發生異常且也透過電子郵件傳送給訂用帳戶管理員時，會在 Azure 資訊安全中心中觸發安全性警示，並提供可疑活動的詳細資料，以及如何調查和修復威脅的建議。 如需詳細資訊，請參閱[Azure 儲存體的 Advanced 威脅防護](../common/storage-advanced-threat-protection.md)。 | [是](../../security-center/security-center-sql-service-recommendations.md) |
| 將共用存取簽章（SAS）權杖限制為僅限 HTTPS 連線 | 當用戶端使用 SAS 權杖來存取佇列資料時需要 HTTPS，有助於將竊聽的風險降到最低。 如需詳細資訊，請參閱[使用共用存取簽章（SAS）授與 Azure 儲存體資源的有限存取權](../common/storage-sas-overview.md)。 | - |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 建議 | 評價 | 資訊安全中心 |
|-|----|--|
| 使用 Azure Active Directory （Azure AD）來授權佇列資料的存取權 | Azure AD 透過共用金鑰提供更高的安全性和易用性，以將要求授與佇列儲存體。 如需詳細資訊，請參閱[使用 Azure Active Directory 授權存取 Azure blob 和佇列](../common/storage-auth-aad.md)。 | - |
| 透過 RBAC 將許可權指派給 Azure AD 的安全性主體時，請記住最低許可權的主體 | 將角色指派給使用者、群組或應用程式時，請僅授與該安全性主體執行其工作所需的許可權。 限制對資源的存取有助於避免不慎和惡意誤用資料。 | - |
| 使用 Azure Key Vault 保護您的帳戶存取金鑰 | Microsoft 建議使用 Azure AD 來授權 Azure 儲存體的要求。 不過，如果您必須使用共用金鑰授權，請使用 Azure Key Vault 來保護您的帳戶金鑰。 您可以在執行時間從金鑰保存庫中取出金鑰，而不是使用您的應用程式加以儲存。 | - |
| 定期重新產生您的帳戶金鑰 | 定期輪替帳戶金鑰可降低將資料公開給惡意執行者的風險。 | - |
| 將許可權指派給 SAS 時，請記住最低許可權的主體 | 建立 SAS 時，請只指定用戶端執行其功能所需的許可權。 限制對資源的存取有助於避免不慎和惡意誤用資料。 | - |
| 針對您向用戶端發出的任何 SAS 備妥撤銷計畫 | 如果 SAS 遭到入侵，您會想要儘快撤銷該 SAS。 若要撤銷使用者委派 SAS，請撤銷使用者委派金鑰，以快速使與該金鑰相關聯的所有簽章失效。 若要撤銷與預存存取原則相關聯的服務 SAS，您可以刪除儲存的存取原則、重新命名原則，或將其到期時間變更為過去的時間。 如需詳細資訊，請參閱[使用共用存取簽章（SAS）授與 Azure 儲存體資源的有限存取權](../common/storage-sas-overview.md)。  | - |
| 如果服務 SAS 未與預存存取原則相關聯，請將到期時間設定為一小時或更少 | 無法撤銷與預存存取原則無關的服務 SAS。 基於這個理由，建議您限制到期時間，讓 SAS 的有效期為一小時或更少。 | - |

## <a name="networking"></a>網路功能

| 建議 | 評價 | 資訊安全中心 |
|-|----|--|
| 啟用防火牆規則 | 設定防火牆規則，以限制來自指定 IP 位址或範圍的要求，或從 Azure 虛擬網路（VNet）中的子網清單存取您的儲存體帳戶。 如需設定防火牆規則的詳細資訊，請參閱[Azure 檔案同步 proxy 和防火牆設定](../files/storage-sync-files-firewall-and-proxy.md)。 | - |
| 允許受信任的 Microsoft 服務存取儲存體帳戶 | 開啟儲存體帳戶的防火牆規則時，預設會封鎖資料的傳入要求，除非要求來自于 Azure 虛擬網路（VNet）內的服務，或來自允許的公用 IP 位址。 封鎖的要求包括來自其他 Azure 服務、Azure 入口網站及記錄與計量服務等等的要求。 您可以藉由新增例外狀況，允許受信任的 Microsoft 服務存取儲存體帳戶，以允許來自其他 Azure 服務的要求。 如需為受信任的 Microsoft 服務新增例外狀況的詳細資訊，請參閱[Azure 檔案同步 proxy 和防火牆設定](../files/storage-sync-files-firewall-and-proxy.md)。| - |
| 使用私人端點 | 私人端點會將 Azure 虛擬網路（VNet）中的私人 IP 位址指派給儲存體帳戶。 它會透過私人連結來保護 VNet 與儲存體帳戶之間的所有流量。 如需私人端點的詳細資訊，請參閱[使用 Azure 私人端點私下連接到儲存體帳戶](../../private-link/create-private-endpoint-storage-portal.md)。 | - |
| 使用 VNet 服務標記 | 服務標記代表來自指定 Azure 服務的一組 IP 位址首碼。 Microsoft 會管理服務標籤所包含的位址前置詞，並隨著位址變更自動更新服務標記。 如需 Azure 儲存體支援之服務標籤的詳細資訊，請參閱[Azure 服務標記總覽](../../virtual-network/service-tags-overview.md)。 如需說明如何使用服務標籤來建立輸出網路規則的教學課程，請參閱[限制對 PaaS 資源的存取](../../virtual-network/tutorial-restrict-network-access-to-resources.md)。 | - |
| 限制對特定網路的網路存取 | 將網路存取限制為裝載需要存取之用戶端的網路，可減少將資源暴露在網路攻擊的風險。 | [是](../../security-center/security-center-sql-service-recommendations.md) |

## <a name="loggingmonitoring"></a>記錄/監視

| 建議 | 評價 | 資訊安全中心 |
|-|----|--|
| 追蹤要求的授權方式 | 啟用 Azure 儲存體記錄，以追蹤每個對 Azure 儲存體所提出的要求是否已獲得授權。 這些記錄會指出是否以匿名方式進行要求、使用 OAuth 2.0 權杖、使用共用金鑰，或使用共用存取簽章（SAS）。 如需詳細資訊，請參閱[Azure 儲存體分析記錄](../common/storage-analytics-logging.md)。 | - |

## <a name="next-steps"></a>後續步驟

- [Azure 安全性檔案](https://docs.microsoft.com//azure/security/)
- [安全開發檔](https://docs.microsoft.com/azure/security/develop/)。
