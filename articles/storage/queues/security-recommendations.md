---
title: 佇列存儲的安全建議
titleSuffix: Azure Storage
description: 瞭解佇列存儲的安全建議。 實施本指南將説明您履行我們共同責任模型中所述的安全義務。
services: storage
author: tamram
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.custom: security-recommendations
ms.openlocfilehash: 8bb56db9eed962ac8f8202c61a7446527c15dfc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060905"
---
# <a name="security-recommendations-for-queue-storage"></a>佇列存儲的安全建議

本文包含佇列存儲的安全建議。 實施這些建議將説明您履行我們共同責任模型中所述的安全義務。 有關 Microsoft 如何履行服務提供者職責的詳細資訊，請閱讀[雲計算的共用責任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf)。

本文中包含的某些建議可以由 Azure 安全中心自動監視。 Azure 安全中心是保護 Azure 中資源的第一道防線。 有關 Azure 安全中心的資訊，請參閱[什麼是 Azure 安全中心？](../../security-center/security-center-intro.md)

Azure 安全中心定期分析 Azure 資源的安全狀態，以識別潛在的安全性漏洞。 然後，它為您提供關於如何解決它們的建議。 有關 Azure 安全中心建議的詳細資訊，請參閱[Azure 安全中心中的安全建議](../../security-center/security-center-recommendations.md)。

## <a name="data-protection"></a>資料保護

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
| 使用 Azure 資源管理器部署模型 | 使用 Azure 資源管理器部署模型創建新的存儲帳戶，以進行重要的安全增強，包括高級存取控制 （RBAC） 和審核、基於資源管理器的部署和治理、對託管標識的訪問、訪問用於 Azure 金鑰保存庫以獲取機密，以及基於 Azure AD 的身份驗證和訪問 Azure 存儲資料和資源的授權。 如果可能，遷移使用經典部署模型使用 Azure 資源管理器的現有存儲帳戶。 有關 Azure 資源管理器的詳細資訊，請參閱[Azure 資源管理器概述](/azure/azure-resource-manager/resource-group-overview)。 | - |
| 在所有存儲帳戶上啟用**安全轉移要求**選項 | 啟用 **"安全轉移"選項**時，針對存儲帳戶發出的所有請求都必須通過安全連線進行。 通過 HTTP 發出的任何請求都將失敗。 有關詳細資訊，請參閱在[Azure 存儲中要求安全傳輸](../common/storage-require-secure-transfer.md)。 | [是](../../security-center/security-center-sql-service-recommendations.md) |
| 為所有存儲帳戶啟用高級威脅防護 | Azure 存儲的高級威脅保護提供了額外的安全智慧層，用於檢測訪問或利用存儲帳戶的異常和潛在有害嘗試。 當活動異常發生時，在 Azure 安全中心中觸發安全警報，並且也會通過電子郵件發送給訂閱管理員，其中詳細說明可疑活動以及如何調查和修復威脅的建議。 有關詳細資訊，請參閱[Azure 存儲的高級威脅保護](../common/storage-advanced-threat-protection.md)。 | [是](../../security-center/security-center-sql-service-recommendations.md) |
| 僅將共用訪問簽名 （SAS） 權杖限制為 HTTPS 連接 | 當用戶端使用 SAS 權杖訪問佇列資料時，要求 HTTPS 有助於將竊聽風險降至最低。 有關詳細資訊，請參閱[使用共用訪問簽名 （SAS） 授予對 Azure 存儲資源的有限存取權限](../common/storage-sas-overview.md)。 | - |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
| 使用 Azure 活動目錄 （Azure AD） 授權訪問佇列資料 | Azure AD 比共用金鑰提供卓越的安全性和易用性，用於授權對佇列存儲的請求。 有關詳細資訊，請參閱使用[Azure 活動目錄 授權訪問 Azure blob 和佇列](../common/storage-auth-aad.md)。 | - |
| 通過 RBAC 將許可權分配給 Azure AD 安全主體時，請記住最低特權主體 | 將角色指派給使用者、組或應用程式時，僅授予該安全主體執行其任務所需的許可權。 限制對資源的訪問有助於防止意外和惡意濫用您的資料。 | - |
| 使用 Azure 金鑰保存庫保護帳戶訪問金鑰 | Microsoft 建議使用 Azure AD 授權對 Azure 存儲的請求。 但是，如果必須使用共用金鑰授權，請使用 Azure 金鑰保存庫保護帳戶金鑰。 您可以在運行時從金鑰保存庫檢索金鑰，而不是將金鑰與應用程式一起保存。 | - |
| 定期重新生成帳戶金鑰 | 定期輪換帳戶金鑰可降低將資料暴露給惡意參與者的風險。 | - |
| 在將許可權分配給 SAS 時，請記住最低特權的主體 | 創建 SAS 時，請僅指定用戶端執行其功能所需的許可權。 限制對資源的訪問有助於防止意外和惡意濫用您的資料。 | - |
| 針對向客戶發出的任何 SAS 制定吊銷計畫 | 如果 SAS 遭到破壞，您將希望儘快撤銷該 SAS。 要撤銷使用者委派 SAS，請撤銷使用者委派金鑰，以快速使與該金鑰關聯的所有簽名無效。 要撤銷與存儲的訪問策略關聯的服務 SAS，可以刪除存儲的訪問策略、重命名策略或將其過期時間更改為過去的時間。 有關詳細資訊，請參閱[使用共用訪問簽名 （SAS） 授予對 Azure 存儲資源的有限存取權限](../common/storage-sas-overview.md)。  | - |
| 如果服務 SAS 未與存儲的訪問策略關聯，則將過期時間設置為一小時或更少 | 不能吊銷未與存儲的訪問策略關聯的服務 SAS。 因此，建議限制過期時間，以便 SAS 的有效期為一小時或更短。 | - |

## <a name="networking"></a>網路

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
| 啟用防火牆規則 | 配置防火牆規則，將存儲帳戶的訪問限制為源自指定 IP 位址或範圍的請求，或來自 Azure 虛擬網路 （VNet） 中的子網清單的請求。 有關配置防火牆規則的詳細資訊，請參閱 Azure[檔同步代理和防火牆設置](../files/storage-sync-files-firewall-and-proxy.md)。 | - |
| 允許受信任的 Microsoft 服務訪問存儲帳戶 | 預設情況下，打開存儲帳戶的防火牆規則會阻止傳入的資料請求，除非請求來自在 Azure 虛擬網路 （VNet） 中運行的服務或來自允許的公共 IP 位址。 封鎖的要求包括來自其他 Azure 服務、Azure 入口網站及記錄與計量服務等等的要求。 可以通過添加異常來允許來自其他 Azure 服務的請求，以允許受信任的 Microsoft 服務訪問存儲帳戶。 有關為受信任的 Microsoft 服務添加異常的詳細資訊，請參閱[Azure 檔同步代理和防火牆設置](../files/storage-sync-files-firewall-and-proxy.md)。| - |
| 使用專用終結點 | 專用終結點從 Azure 虛擬網路 （VNet） 向存儲帳戶分配私人 IP 位址。 它通過專用鏈路保護 VNet 和存儲帳戶之間的所有流量。 有關專用終結點的詳細資訊，請參閱[使用 Azure 專用終結點 將私人連接到存儲帳戶](../../private-link/create-private-endpoint-storage-portal.md)。 | - |
| 限制對特定網路的網路訪問 | 限制對需要訪問的託管用戶端的網路的網路的訪問，可以減少資源受到網路攻擊的風險。 | [是](../../security-center/security-center-sql-service-recommendations.md) |

## <a name="loggingmonitoring"></a>日誌記錄/監視

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
| 跟蹤請求的授權方式 | 啟用 Azure 存儲日誌記錄，以跟蹤如何授權針對 Azure 存儲的每個請求。 日誌指示請求是匿名進行的，通過使用 OAuth 2.0 權杖、使用共用金鑰或使用共用訪問簽名 （SAS）。 有關詳細資訊，請參閱[Azure 存儲分析日誌記錄](../common/storage-analytics-logging.md)。 | - |

## <a name="next-steps"></a>後續步驟

- [Azure 安全文檔](https://docs.microsoft.com//azure/security/)
- [安全開發文檔](https://docs.microsoft.com/azure/security/develop/)。
