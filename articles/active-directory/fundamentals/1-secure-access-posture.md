---
title: 使用 Azure Active Directory 判斷外部共同作業的安全性狀態
description: 在您可以執行外部存取安全性計畫之前，您必須先判斷您嘗試達成的目標。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63d6c37f6cd32985e540164ef8b308652a5e7414
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/23/2020
ms.locfileid: "97743936"
---
# <a name="determine-your-security-posture-for-external-access"></a>判斷外部存取的安全性狀態 

當您考慮管理外部存取時，您必須在每個案例中，評估組織的安全性和協同作業需求。 在組織層級，請考慮您需要 IT 小組進行日常共同作業的控制量。 受管制產業的組織可能需要更多 IT 控制。 例如，可能必須有一道防線，才能確認並記載每個外部使用者、其存取權，以及移除存取權。 這項需求可能是在所有存取，或是在特定案例或工作負載上。 在該頻譜的另一端，顧問公司通常可能會允許使用者在特定 IT 防護滑軌內，判斷需要共同作業的外部使用者。 

![IT 與使用者共同作業的控制權](media/secure-external-access/1-overall-control.png)

> [!NOTE]
> 過度掌控共同作業可能會導致更高的 IT 預算、更低的生產力，以及延遲的業務成果。 當官方共同作業管道的感覺太過繁重時，終端使用者傾向于提供系統來完成工作，例如電子郵件不安全的檔。

## <a name="think-in-terms-of-scenarios"></a>以案例為考慮

在許多情況下，它可以委派夥伴存取權（至少在某些案例中），同時為安全性提供防護 rails。 IT 防護滑軌可協助確保智慧財產保持安全，同時讓員工與合作夥伴共同作業來完成工作。

當您考慮組織內的案例時，請評估員工與商務夥伴對資源的存取需求。 銀行可能會有符合需求，可將特定資源（例如使用者帳戶資訊）的存取限制為一小組內部員工。 相反地，相同的銀行可能會針對處理行銷活動的夥伴啟用委派存取權。

![每個案例的治理連續性](media\secure-external-access\1-scenarios.png)

在每個案例中，請考慮 

* 有風險的資訊敏感度

* 您是否需要限制合作夥伴可以看到的其他使用者

* 缺口的成本與集中式控制和使用者缺口的加權

 您也可以從集中管理的控制項開始，以符合合規性目標，並在一段時間後將控制權委派給終端使用者。 所有存取管理模型可能會同時並存于組織內。 

使用 [合作夥伴管理的認證](../external-identities/what-is-b2b.md) ，可讓您的組織在外部使用者失去其公司資源的存取權時，終止存取您資源的基本信號。

## <a name="goals-of-securing-external-access"></a>保護外部存取的目標

IT 控管和委派存取的目標不同。

**IT 控管存取的主要目標是：**

* 符合 (GRC) 目標的治理、法規和合規性。 

* 嚴格控制合作夥伴存取權，以及合作夥伴可以看到的成員使用者、群組和其他合作夥伴。

**委派存取權的主要目標是：**

* 讓商務擁有者可以在 IT 條件約束內管理共同作業的人員。

* 讓商務夥伴根據企業擁有者所定義的規則來要求存取權。

無論您是針對組織和案例而定，您都需要： 

* **控制應用程式、資料和內容的存取權**。 這可以透過各種不同的方法來完成，視您的 [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) 版本和 [Microsoft 365](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans)而定。 

* **減少受攻擊面**。 特殊[許可權身分識別管理](../privileged-identity-management/pim-configure.md)、[資料遺失防止 (DLP) ](https://docs.microsoft.com/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention)和[加密功能](https://docs.microsoft.com/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention)會減少受攻擊面。

* **定期審核活動和審核記錄，以確認合規性**。 它可以透過權利管理將存取決策委派給企業擁有者，而存取評論則提供定期確認持續存取的方式。 具有敏感度標籤的自動化資料分類可協助自動化機密內容的加密，讓員工的使用者能輕鬆地遵守。

## <a name="next-steps"></a>後續步驟 

請參閱下列文章，以保護對資源的外部存取。 建議您依列出的循序執行動作。

1. [判斷您的外部存取 (的安全性](1-secure-access-posture.md) 狀態。 ) 

2. [探索您目前的狀態](2-secure-access-current-state.md)

3. [建立治理計畫](3-secure-access-plan.md)

4. [使用群組進行安全性](4-secure-access-groups.md)

5. [轉換至 Azure AD B2B](5-secure-access-b2b.md)

6. [使用權利管理來保護存取權](6-secure-access-entitlement-managment.md)

7. [使用條件式存取原則保護存取](7-secure-access-conditional-access.md)

8. [使用敏感度標籤保護存取](8-secure-access-sensitivity-labels.md)

9. [安全存取 Microsoft 小組、OneDrive 和 SharePoint](9-secure-access-teams-sharepoint.md)
 

 
