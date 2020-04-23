---
title: Blob 儲存的安全建議
titleSuffix: Azure Storage
description: 瞭解 Blob 存儲的安全建議。 實施本指南將説明您履行我們共同責任模型中所述的安全義務。
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: tamram
ms.custom: security-recommendations
ms.openlocfilehash: 0b32f7e8fa2ec0d1d28f2fd42147e140d2d03341
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086108"
---
# <a name="security-recommendations-for-blob-storage"></a>Blob 儲存的安全建議

本文包含 Blob 存儲的安全建議。 實施這些建議將説明您履行我們共同責任模型中所述的安全義務。 有關 Microsoft 如何履行服務提供者職責的詳細資訊,請閱讀[雲計算的共享責任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf)。

本文中包含的某些建議可以由 Azure 安全中心自動監視。 Azure 安全中心是保護 Azure 中資源的第一道防線。 有關 Azure 安全中心的資訊,請參閱[什麼是 Azure 安全中心?](../../security-center/security-center-intro.md)

Azure 安全中心定期分析 Azure 資源的安全狀態,以識別潛在的安全漏洞。 然後,它為您提供關於如何解決它們的建議。 有關 Azure 安全中心建議的詳細資訊,請參閱[Azure 安全中心中的安全建議](../../security-center/security-center-recommendations.md)。

## <a name="data-protection"></a>資料保護

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
| 使用 Azure 資源管理員部署模型 | 使用 Azure 資源管理器部署模型創建新的儲存帳戶,以進行重要的安全增強,包括高級訪問控制 (RBAC) 和審核、基於資源管理器的部署和治理、對託管標識的訪問、對機密的 Azure 密鑰保管庫的訪問以及基於 Azure AD 的基於 Azure 的身份驗證和訪問 Azure 儲存數據和資源的授權。 如果可能,遷移使用經典部署模型使用 Azure 資源管理器的現有存儲帳戶。 有關 Azure 資源管理員的詳細資訊,請參閱[Azure 資源管理器概述](/azure/azure-resource-manager/resource-group-overview)。 | - |
| 在所有儲存帳戶上啟用**安全轉移要求**選項 | 啟用 **「安全轉移」選項**時,針對存儲帳戶發出的所有請求都必須通過安全連接進行。 通過 HTTP 發出的任何請求都將失敗。 有關詳細資訊,請參閱在[Azure 儲存中要求安全傳輸](../common/storage-require-secure-transfer.md)。 | [是](../../security-center/security-center-sql-service-recommendations.md) |
| 為所有存儲帳戶啟用高級威脅防護 | Azure 儲存的先進威脅保護提供額外的安全智慧式,用於偵測存取或利用儲存帳戶的異常和潛在有害嘗試。 當活動異常發生時,在 Azure 安全中心中觸發安全警報,並且也會通過電子郵件發送給訂閱管理員,其中詳細說明可疑活動以及如何調查和修復威脅的建議。 有關詳細資訊,請參閱[Azure 儲存的進階威脅保護](../common/storage-advanced-threat-protection.md)。 | [是](../../security-center/security-center-sql-service-recommendations.md) |
| 開啟 Blob 資料的軟刪除 | 軟刪除使您能夠在 Blob 資料被刪除後恢復資料。 有關軟刪除的詳細資訊,請參閱[Azure 儲存 blob 的軟刪除](storage-blob-soft-delete.md)。 | - |
| 將業務關鍵型資料儲存在不可變的 blob 中 | 配置法定保留和基於時間的保留策略,以將 Blob 資料儲存在 WORM(一次寫入、讀取多個)狀態。 可以不可變地存儲的 Blob,但在保留間隔期間無法修改或刪除。 有關詳細資訊,請參閱[使用不可變儲存儲存業務關鍵型 Blob 資料](storage-blob-immutable-storage.md)。 | - |
| 只將分享存取簽名 (SAS) 權限為 HTTPS 連線 | 當用戶端使用 SAS 令牌訪問 Blob 數據時,要求 HTTPS 有助於將竊聽風險降至最低。 有關詳細資訊,請參閱[使用共享存取簽章 (SAS) 授予對 Azure 資源的有限存取權限](../common/storage-sas-overview.md)。 | - |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
| 使用 Azure 活動目錄 (Azure AD) 授權存取 Blob 資料 | Azure AD 比共享金鑰提供卓越的安全性和易用性,用於授權對 Blob 儲存的請求。 有關詳細資訊,請參閱使用[Azure 的目錄 授權存取 Azure blob 和佇列](../common/storage-auth-aad.md)。 | - |
| 通過 RBAC 將許可權分配給 Azure AD 安全主體時,請記住最低特權主體 | 將角色分配給使用者、組或應用程式時,僅授予該安全主體執行其任務所需的許可權。 限制對資源的訪問有助於防止意外和惡意濫用您的數據。 | - |
| 使用使用者委派 SAS 向用戶端授予對 Blob 資料的有限存取權限 | 使用者委派 SAS 使用 Azure 活動目錄 (Azure AD) 認證以及為 SAS 指定的權限進行保護。 使用者委派 SAS 在範圍和功能方面類似於服務 SAS,但比服務 SAS 具有安全優勢。 有關詳細資訊,請參閱[使用共享存取簽章 (SAS) 授予對 Azure 資源的有限存取權限](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。 | - |
| 使用 Azure 金鑰保存庫保護帳號存取金鑰 | Microsoft 建議使用 Azure AD 授權對 Azure 儲存的請求。 但是,如果必須使用共享密鑰授權,請使用 Azure 密鑰保管庫保護帳戶金鑰。 您可以在執行時從金鑰保管庫檢索密鑰,而不是將密鑰與應用程式一起保存。 有關 Azure 金鑰保存庫的詳細資訊,請參閱[Azure 金鑰保管庫概述](../../key-vault/general/overview.md)。 | - |
| 定期重新產生帳戶金鑰 | 定期輪換帳戶金鑰可降低將數據暴露給惡意參與者的風險。 | - |
| 在將權限分配給 SAS 時,請記住最低特權的主體 | 創建 SAS 時,請僅指定用戶端執行其功能所需的許可權。 限制對資源的訪問有助於防止意外和惡意濫用您的數據。 | - |
| 針對向客戶發出的任何 SAS 制定撤銷計劃 | 如果 SAS 遭到破壞,您將希望儘快撤銷該 SAS。 要撤銷使用者委派 SAS,請撤消使用者委派密鑰,以快速使與該密鑰關聯的所有簽名無效。 要撤銷與儲存的存取策略關聯的服務 SAS,可以刪除儲存的存取策略、重命名策略或將其過期時間更改為過去的時間。 有關詳細資訊,請參閱[使用共享存取簽章 (SAS) 授予對 Azure 資源的有限存取權限](../common/storage-sas-overview.md)。  | - |
| 如果服務 SAS 未與儲存的存取策略關聯,則將過期時間設定為一小時或更少 | 不能撤銷未與儲存的存取策略關聯的服務 SAS。 因此,建議限制過期時間,以便 SAS 的有效期為一小時或更短。 | - |
| 限制對容器及 blob 的匿名公共讀取存取 | 對容器及其 blob 的匿名公共讀取訪問將這些資源的唯讀存取許可權授予任何用戶端。 除非方案需要,否則請避免啟用公共讀取訪問。 | - |

## <a name="networking"></a>網路功能

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
| 開啟防火牆規則 | 配置防火牆規則,將儲存帳戶的訪問限制為源自指定 IP 位址或範圍的請求,或來自 Azure 虛擬網路 (VNet) 中的子網清單的請求。 有關設定防火牆規則的詳細資訊,請參閱 Azure[檔同步代理和防火牆設定](../files/storage-sync-files-firewall-and-proxy.md)。 | - |
| 允許受信任的 Microsoft 服務存取儲存帳戶 | 預設情況下,打開儲存帳戶的防火牆規則會阻止傳入的數據請求,除非請求來自在 Azure 虛擬網路 (VNet) 中運行的服務或來自允許的公共 IP 位址。 封鎖的要求包括來自其他 Azure 服務、Azure 入口網站及記錄與計量服務等等的要求。 可以通過添加異常來允許來自其他 Azure 服務的請求,以允許受信任的 Microsoft 服務存取儲存帳戶。 有關為受信任的 Microsoft 服務添加例外的詳細資訊,請參閱[Azure 檔同步代理和防火牆設定](../files/storage-sync-files-firewall-and-proxy.md)。| - |
| 使用私人端點 | 專用終結點從 Azure 虛擬網路 (VNet) 向儲存帳戶分配專用 IP 位址。 它通過專用鏈路保護 VNet 和存儲帳戶之間的所有流量。 有關專用終結點的詳細資訊,請參閱[使用 Azure 專用終結點 將私人連接到儲存帳戶](../../private-link/create-private-endpoint-storage-portal.md)。 | - |
| 使用 VNet 服務標記 | 服務標記表示給定 Azure 服務的一組 IP 位址前綴。 Microsoft 管理服務標記包含的位址首碼,並在位址更改時自動更新服務標記。 有關 Azure 儲存支援的服務標記的詳細資訊,請參閱[Azure 服務標記概述](../../virtual-network/service-tags-overview.md)。 有關示範如何使用服務標記建立出站網路規則的教學,請參閱[限制對 PaaS 資源的存取](../../virtual-network/tutorial-restrict-network-access-to-resources.md)。 | - |
| 限制對特定網路的網路存取 | 限制對需要訪問的託管用戶端的網路的網路的訪問,可以減少資源受到網路攻擊的風險。 | [是](../../security-center/security-center-sql-service-recommendations.md) |

## <a name="loggingmonitoring"></a>記錄記錄/監視

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
| 追蹤要求的授權方式 | 啟用 Azure 儲存記錄,以追蹤如何授權針對 Azure 儲存的每個請求。 日誌指示請求是匿名進行的,通過使用 OAuth 2.0 權杖、使用共享密鑰或使用共享存取簽名 (SAS)。 有關詳細資訊,請參閱[Azure 儲存分析紀錄記錄](../common/storage-analytics-logging.md)。 | - |

## <a name="next-steps"></a>後續步驟

- [Azure 安全性文件](https://docs.microsoft.com//azure/security/)
- [安全開發文件](https://docs.microsoft.com/azure/security/develop/)。
