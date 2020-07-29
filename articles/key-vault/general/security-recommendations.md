---
title: Azure Key Vault 的安全性建議
description: Azure Key Vault 的安全性建議。 實作本指南有助於您遵循我們的共同責任模型中所述，滿足您的安全性義務
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: article
ms.date: 09/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 28b768e421ac72be363ab187312e52ba15d34970
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "83872998"
---
# <a name="security-recommendations-for-azure-key-vault"></a>Azure Key Vault 的安全性建議

此文章內有 Azure Key Vault 的安全性建議。 依照我們的共同責任模型所述，執行這些建議有助於您履行安全性義務。 如需 Microsoft 如何履行服務提供者責任的詳細資訊，請參閱[雲端運算的共同責任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)。

本文中包含的一些建議可由 Azure 資訊安全中心自動監視。 Azure 資訊安全中心是保護 Azure 中資源的第一道防線。 這會定期分析 Azure 資源的安全性狀態，以識別潛在的安全性弱點。 接著，這會為您提供如何解決這些問題的建議。

- 如需 Azure 資訊安全中心建議的詳細資訊，請參閱 [Azure 資訊安全中心的安全性建議](../../security-center/security-center-recommendations.md)。
- 如需 Azure 資訊安全中心的詳細資訊，請參閱[什麼是 Azure 資訊安全中心？](../../security-center/security-center-intro.md)

## <a name="data-protection"></a>資料保護

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
|啟用虛刪除 | [虛刪除](overview-soft-delete.md)可讓您復原已刪除的保存庫和保存庫物件 |  - |
| 限制對保存庫資料的存取  | 遵循最低權限的原則，並限制組織中有哪些成員可以存取保存庫資料 |  - |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
| 限制具有參與者存取權的使用者人數 | 如果使用者具有金鑰保存庫管理平面的參與者權限，他們可以透過設定金鑰保存庫存取原則，對本身授與資料平面的存取權。 您應該嚴格控制誰有金鑰保存庫的「參與者」存取權。 請確定只有有權存取授權的人員才能存取和管理您的保存庫。 您可以[參閱針對金鑰保存庫的存取進行保護](secure-your-key-vault.md)) | - |

## <a name="monitoring"></a>監視

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
 應啟用 Key Vault 中的診斷記錄 | 啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。 | [是](../../security-center/security-center-identity-access.md) |
| 限制哪些人可以存取您的 Azure 金鑰保存庫記錄 | [金鑰保存庫記錄](logging.md)儲存在保存庫上執行的活動有關的資訊，例如建立或刪除保存庫、金鑰、祕密，而且可在調查期間使用 |  - |

## <a name="networking"></a>網路功能

| 建議 | 註解 | 資訊安全中心 |
|-|----|--|
|限制網路曝光 | 網路存取應限制為需要保存庫存取的解決方案所使用的虛擬網路。 請參閱 [Azure Key Vault 的虛擬網路服務端點](overview-vnet-service-endpoints.md)的資訊) | - |

## <a name="next-steps"></a>後續步驟

請洽詢您的應用程式提供者，以查看是否有額外的安全性需求。 如需開發安全應用程式的詳細資訊，請參閱[安全開發文件](../../security/fundamentals/abstract-develop-secure-apps.md)。
