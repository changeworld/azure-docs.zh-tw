---
title: Azure Key Vault 的安全性建議
description: Azure Key Vault 的安全性建議。 實行本指南可協助您遵循我們的共同責任模型中所述，滿足您的安全性義務
services: key-vault
author: barclayn
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: article
ms.date: 09/30/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: 9fd41ae9b61a9c74fd5d99dd71199cfde06f5cb2
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78192365"
---
# <a name="security-recommendations-for-azure-key-vault"></a>Azure Key Vault 的安全性建議

本文包含 Azure Key Vault 的安全性建議。 依照我們的共同責任模型所述，執行這些建議可協助您履行安全性義務。 如需 Microsoft 如何履行服務提供者責任的詳細資訊，請參閱[雲端運算的共同責任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)。

本文中包含的一些建議可由 Azure 資訊安全中心自動監視。 Azure 資訊安全中心是保護 Azure 中資源的第一道防線。 它會定期分析 Azure 資源的安全性狀態，以找出潛在的安全性弱點。 接著，它會為您提供如何解決這些問題的建議。

- 如需 Azure 資訊安全中心建議的詳細資訊，請參閱[Azure 資訊安全中心中的安全性建議](../security-center/security-center-recommendations.md)。
- 如需 Azure 資訊安全中心的詳細資訊，請參閱[什麼是 Azure 資訊安全中心？](../security-center/security-center-intro.md)

## <a name="data-protection"></a>資料保護

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
|啟用虛刪除 | 虛[刪除](key-vault-ovw-soft-delete.md)可讓您復原已刪除的保存庫和保存庫物件 |  - |
| 限制對保存庫資料的存取  | 遵循最低許可權的原則，並限制組織中有哪些成員可以存取保存庫資料 |  - |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
| 限制具有參與者存取權的使用者數目 | 如果使用者具有金鑰保存庫管理平面的參與者許可權，則使用者可以藉由設定 Key Vault 存取原則，授與自己對資料平面的存取權。 您應該嚴格控制誰擁有金鑰保存庫的「參與者」角色存取權。 請確定只有有權存取授權的人員才能存取和管理您的保存庫。 您可以讀取[金鑰保存庫的安全存取權](key-vault-secure-your-key-vault.md) | - |

## <a name="monitoring"></a>監視

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
 應該啟用 Key Vault 中的診斷記錄 | 啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。 | [是](../security-center/security-center-identity-access.md) |
| 限制誰可以存取您的 Azure 金鑰保存庫記錄 | [Key Vault 記錄](key-vault-logging.md)會儲存在您的保存庫上執行之活動的相關資訊，例如建立或刪除保存庫、金鑰、秘密，並可在調查期間使用。 |  - |

## <a name="networking"></a>網路功能

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
|限制網路暴露 | 網路存取應限制為需要保存庫存取之解決方案所使用的虛擬網路。 查看[Azure Key Vault 虛擬網路服務端點的](key-vault-overview-vnet-service-endpoints.md)相關資訊 | - |

## <a name="next-steps"></a>後續步驟

請洽詢您的應用程式提供者，以查看是否有額外的安全性需求。 如需有關開發安全應用程式的詳細資訊，請參閱[保護開發檔](../security/fundamentals/abstract-develop-secure-apps.md)。
