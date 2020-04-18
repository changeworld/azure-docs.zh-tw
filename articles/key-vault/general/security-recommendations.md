---
title: Azure Key Vault 的安全性建議
description: Azure 密鑰保管庫的安全建議。 實施本指南將説明您履行我們共同責任模型中所述的安全義務
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: article
ms.date: 09/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 0da1a3019124f62aba6a959ce9104c85bd85d3fc
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616491"
---
# <a name="security-recommendations-for-azure-key-vault"></a>Azure Key Vault 的安全性建議

本文包含 Azure 密鑰保管庫的安全建議。 實施這些建議將説明您履行我們共同責任模型中所述的安全義務。 有關 Microsoft 如何履行服務提供者職責的詳細資訊,請閱讀[雲計算的共享責任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)。

本文中包含的某些建議可以由 Azure 安全中心自動監視。 Azure 安全中心是保護 Azure 中資源的第一道防線。 它定期分析 Azure 資源的安全狀態,以識別潛在的安全漏洞。 然後,它為您提供關於如何解決它們的建議。

- 有關 Azure 安全中心建議的詳細資訊,請參閱[Azure 安全中心中的安全建議](../../security-center/security-center-recommendations.md)。
- 有關 Azure 安全中心的資訊,請參閱[什麼是 Azure 安全中心?](../../security-center/security-center-intro.md)

## <a name="data-protection"></a>資料保護

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
|開啟軟刪除 | [軟刪除](overview-soft-delete.md))允許您回復已刪除的保存庫和保管庫物件 |  - |
| 限制對保管庫資料的存取  | 遵循最低特權原則,限制組織成員有權訪問保管庫數據 |  - |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
| 限制具有參與者存取權限的使用者數 | 如果使用者對密鑰保管庫管理層具有參與者許可權,則使用者可以通過設置密鑰保管庫訪問策略來授予自己對數據平面的訪問許可權。 您應該嚴格控制誰有權訪問您的密鑰保管庫。 確保只有需要訪問許可權的人員才能訪問和管理您的保管庫。 您可以讀取[對金鑰保存的安全性存取](secure-your-key-vault.md)) | - |

## <a name="monitoring"></a>監視

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
 應該在 Key Vault 中啟用診斷記錄 | 啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。 | [是](../../security-center/security-center-identity-access.md) |
| 限制可以存取 Azure 金鑰保管庫紀錄的人員 | [金鑰保存庫紀錄](logging.md)保存有關在保管庫中執行的活動的資訊,例如建立或刪除保管庫、密鑰、機密,並可能在調查期間使用 |  - |

## <a name="networking"></a>網路功能

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
|限制網路曝光 | 網路訪問應限於需要保管庫訪問的解決方案使用的虛擬網路。 檢視[Azure 金鑰保存式資訊](overview-vnet-service-endpoints.md) | - |

## <a name="next-steps"></a>後續步驟

請諮詢應用程式供應商,瞭解是否有其他安全要求。 有關開發安全應用程式的詳細資訊,請參閱[安全開發文件](../../security/fundamentals/abstract-develop-secure-apps.md)。
