---
title: 在 [權利管理] 中查看報表 & 記錄-Azure AD
description: 瞭解如何在 Azure Active Directory 權利管理中查看使用者指派報告和審核記錄。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 514f8e86d6bd28cc5212e0f0058f00e270f43e35
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80128424"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management"></a>在 Azure AD 權利管理中查看報告和記錄

Azure AD 權利管理報告和 Azure AD audit 記錄檔會提供使用者可存取之資源的其他詳細資料。 身為系統管理員，您可以查看使用者的存取套件和資源指派，並查看要求記錄以進行審核，或判斷使用者要求的狀態。 本文說明如何使用權利管理報告，並 Azure AD audit 記錄。

觀看下列影片，以瞭解如何在 [權利管理] 中查看使用者可存取的資源：

>[!VIDEO https://www.youtube.com/embed/omtNJ7ySjS0]

## <a name="view-access-packages-for-a-user"></a>查看使用者的存取套件

這份報告可讓您列出使用者可以要求的所有存取封裝，以及目前指派給使用者的存取封裝。

**必要角色：** 全域管理員或使用者管理員

1. 按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [**報表**]。

1. 按一下 [**存取使用者的套件**]。

1. 按一下 [**選取使用者**] 以開啟 [選取使用者] 窗格。

1. 在清單中尋找使用者，然後按一下 [**選取**]。

    [**可要求**] 索引標籤會顯示使用者可以要求的存取封裝清單。 這份清單是由針對存取套件定義的[要求原則](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)所決定。 

    ![存取使用者的套件](./media/entitlement-management-reports/access-packages-report.png)

1. 如果有一個以上的資源角色或存取封裝的原則，請按一下 [資源角色] 或 [原則] 專案，以查看選取詳細資料。

1. 按一下 [**指派**] 索引標籤，以查看目前指派給使用者的存取套件清單。 當存取套件指派給使用者時，這表示使用者可以存取存取套件中的所有資源角色。

## <a name="view-resource-assignments-for-a-user"></a>查看使用者的資源指派

此報告可讓您列出目前在 [權利管理] 中指派給使用者的資源。 請注意，這份報告適用于使用 [權利管理] 管理的資源。 使用者可能可以存取您目錄中權利管理以外的其他資源。

**必要角色：** 全域管理員或使用者管理員

1. 按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [**報表**]。

1. 按一下 [**使用者的資源指派**]。

1. 按一下 [**選取使用者**] 以開啟 [選取使用者] 窗格。

1. 在清單中尋找使用者，然後按一下 [**選取**]。

    隨即會顯示目前指派給使用者的資源清單。 此清單也會顯示其取得資源角色的存取套件和原則，以及存取的開始和結束日期。
    
    如果使用者在兩個或多個套件中取得相同資源的存取權，您可以按一下箭號來查看每個套件和原則。

    ![使用者的資源指派](./media/entitlement-management-reports/resource-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>判斷使用者要求的狀態

若要取得使用者如何要求和存取存取套件的其他詳細資料，您可以使用 Azure AD audit 記錄檔。 特別是，您可以使用`EntitlementManagement`和`UserManagement`類別中的記錄檔記錄，取得每個要求之處理步驟的其他詳細資料。  

1. 按一下 [ **Azure Active Directory** ，然後按一下 [ **Audit logs**]。

1. 在頂端，根據您要尋找的 audit `EntitlementManagement`記錄`UserManagement`，將**類別**變更為或。  

1. 按一下 [套用]  。

1. 若要下載記錄檔，請按一下 [**下載**]。

當 Azure AD 收到新的要求時，它會寫入一個 audit 記錄，其中**Category**的分類`EntitlementManagement`為，而**活動**通常`User requests access package assignment`為。  在 Azure 入口網站中建立直接指派的情況下，audit 記錄的 [**活動**] 欄位是`Administrator directly assigns user to access package`，而執行指派的使用者則是由**ActorUserPrincipalName**所識別。

Azure AD 會在要求進行時寫入其他的審核記錄，包括：

| 類別 | 活動 | 要求狀態 |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | 要求不需要核准 |
| `UserManagement` | `Create request approval` | 要求需要核准 |
| `UserManagement` | `Add approver to request approval` | 要求需要核准 |
| `EntitlementManagement` | `Approve access package assignment request` | 要求已核准 |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |要求已核准，或不需要核准 |

將存取權指派給使用者時，Azure AD 會寫入具有**活動** `Fulfill access package assignment`之`EntitlementManagement`類別目錄的 audit 記錄。  收到存取權的使用者是以**ActorUserPrincipalName**欄位識別。

如果未指派存取權，則 Azure AD 會在核准者拒絕要求`EntitlementManagement`時， **Activity**以活動`Deny access package assignment request`寫入類別目錄的審核記錄，或者`Access package assignment request timed out (no approver action taken)`，如果要求在核准者核准之前就超時。

當使用者的存取套件指派過期時，使用者會取消，或由系統管理員移除，然後 Azure AD 寫入具有`EntitlementManagement` **活動**之`Remove access package assignment`類別目錄的 audit 記錄。

## <a name="next-steps"></a>後續步驟

- [封存報表和記錄](entitlement-management-logs-and-reporting.md)
- [針對 Azure AD 權利管理進行疑難排解](entitlement-management-troubleshoot.md)
- [常見案例](entitlement-management-scenarios.md)
