---
title: 使用 Azure Active Directory 條件式存取來管理外部存取
description: 如何使用 Azure Active Directory 條件式存取原則來保護對資源的外部存取。
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
ms.openlocfilehash: 64209a4d9ca200c69783a4ae317b38beef8ded89
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222304"
---
# <a name="manage-external-access-with-conditional-access-policies"></a>使用條件式存取原則管理外部存取 

[條件式存取](../conditional-access/overview.md) 是 Azure AD 用來結合信號、強制執行原則，以及判斷是否應該允許使用者存取資源的工具。 如需有關如何建立和使用條件式存取原則的詳細資訊)  (條件式存取原則，請參閱 [規劃條件式存取部署](../conditional-access/plan-conditional-access.md)。 

![條件式存取信號和決策的圖表](media/secure-external-access//7-conditional-access-signals.png)



本文討論如何將條件式存取原則套用至外部使用者，並假設您沒有 [權利管理](../governance/entitlement-management-overview.md) 功能的存取權。 條件式存取原則可以與權利管理一起使用。

稍早在本檔集中，您 [建立了一](3-secure-access-plan.md) 份概述的安全性方案：

* 應用程式和資源具有相同的安全性需求，而且可分組以進行存取。

* 外部使用者的登入需求。

您將使用該方案來建立外部存取的條件式存取原則。 

> [!IMPORTANT]
> 建立幾個外部使用者測試帳戶，讓您可以測試您所建立的原則，然後將它們套用至所有外部使用者。

## <a name="conditional-access-policies-for-external-access"></a>外部存取的條件式存取原則

以下是使用條件式存取原則來管理外部存取的相關最佳做法。

* 如果您無法在權利管理中使用已連線的組織，請為您使用的每個夥伴組織建立 Azure AD 安全性群組或 Microsoft 365 群組。 將該夥伴的所有使用者指派給群組。 然後，您可以在條件式存取原則中使用這些群組。

* 盡可能只建立條件式存取原則。 針對具有相同存取需求的應用程式，將它們全部新增至相同的原則。  
‎ 
   > [!NOTE]
   > 條件式存取原則最多可套用至250應用程式。 如果有超過250的應用程式具有相同的存取需求，請建立重複的原則。 原則 A 會套用至應用程式1-250，原則 B 會套用至應用程式251-500 等等。

* 使用命名慣例，清楚命名外部存取的特定原則。 其中一個命名慣例是 *ExternalAccess_actiontaken_AppGroup*。 例如 ExternalAccess_Block_FinanceApps。

## <a name="block-all-external-users-from-resources"></a>封鎖所有外部使用者的資源

您可以封鎖外部使用者使用條件式存取原則來存取特定資源集。 一旦您決定要封鎖存取的一組資源，請建立原則。

若要建立原則來封鎖外部使用者對一組應用程式的存取：

1. 存取 **Azure 入口網站**，選取 [ **Azure Active Directory**]，選取 [**安全性**]，然後選取 [ **條件式存取**]。

2. 選取 [ **新增原則**]，並輸入 **名稱**，例如 ExternalAccess_Block_FinanceApps

3. 選取 [ **使用者和群組**]。 在 [包含] 索引標籤上，選擇 [ **選取使用者和群組**]，然後選取 [ **所有來賓和外部使用者**]。 

4. 選取 [ **排除** ]，並輸入您的系統管理員群組 (s) 和任何緊急存取 (半透明) 帳戶。

5. 選取 [ **雲端應用程式] 或 [動作**]，選擇 [ **選取應用程式**]，選取您要封鎖外部存取的所有應用程式，然後選擇 [ **選取**]。

6. 選取 [ **條件**]，選取 [ **位置**]，在 [設定] 下選取 **[是]**，然後選取 **任何位置**。

7. 在 [存取控制] 底下，選取 **[授** 與]、變更切換為 [ **封鎖**]，然後選擇 [ **選取**]。

8. 確定 [啟用原則] 設定設為 [ **僅報告**]，然後選取 [ **建立**]。

## <a name="block-external-access-to-all-except-specific-external-users"></a>封鎖特定外部使用者以外的外部存取

有時您可能會想要封鎖外部使用者，但特定群組除外。 例如，您可能會想要封鎖所有外部使用者，但不包括從財務應用程式工作的財務小組。 若要這樣做：

1. 建立安全性群組，以保存應存取財務群組的外部使用者。

2. 遵循 [封鎖來自上述資源的外部存取] 中的步驟1-3。

3. 在步驟4中，新增您想要排除的安全性群組，使其不受財務應用程式封鎖。

4. 執行其餘的步驟。

## <a name="implement-conditional-access"></a>執行條件式存取

許多常見的條件式存取原則都有記載。 請參閱下列程式，您可以針對外部使用者進行調整。

* [所有使用者都需要 Multi-Factor Authentication](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)

* [使用者風險型條件式存取](../conditional-access/howto-conditional-access-policy-risk-user.md)

* [需要 Multi-Factor Authentication 才能從不受信任的網路進行存取](../conditional-access/untrusted-networks.md) 

* [需要使用規定](../conditional-access/terms-of-use.md)

## <a name="next-steps"></a>後續步驟

請參閱下列文章，以保護對資源的外部存取。 建議您依列出的循序執行動作。

1. [判斷您所需的外部存取安全性狀態](1-secure-access-posture.md)

2. [探索您目前的狀態](2-secure-access-current-state.md)

3. [建立治理計畫](3-secure-access-plan.md)

4. [使用群組進行安全性](4-secure-access-groups.md)

5. [轉換至 Azure AD B2B](5-secure-access-b2b.md)

6. [使用權利管理來保護存取權](6-secure-access-entitlement-managment.md)

7. [使用條件式存取原則來保護存取](7-secure-access-conditional-access.md) (您在這裡) 

8. [使用敏感度標籤保護存取](8-secure-access-sensitivity-labels.md)

9. [安全存取 Microsoft 小組、OneDrive 和 SharePoint](9-secure-access-teams-sharepoint.md)
