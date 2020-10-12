---
title: 在 PIM 中檢查 Azure 資源角色的存取-Azure AD |Microsoft Docs
description: 瞭解如何在 Azure AD Privileged Identity Management (PIM) 中檢查 Azure 資源角色的存取權。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8091890d174ae68f012a6ec24685a0e705100f8b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84743672"
---
# <a name="review-access-to-azure-resource-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中審查 Azure 資源角色的存取權

Privileged Identity Management (PIM) 存取權審核可協助保護 Azure Active Directory (Azure AD) 中特殊許可權角色的存取。 本文將逐步說明如何在 Azure AD 存取權審核中完成您的特殊許可權角色指派。

如果您指派給系統管理角色，您可能必須完成系統管理員的存取權檢查，才能確認您的角色需求。 確認要求可能會收到包含連結的電子郵件，您也可以在 [Azure 入口網站](https://portal.azure.com)中確認。

如果您是對存取權檢閱感興趣的特殊權限角色管理員，請在 [如何開始存取權檢閱](pim-resource-roles-start-access-review.md)中取得更多詳細資訊。

## <a name="approve-or-deny-access"></a>核准或拒絕存取

您可以根據是否仍使用此角色來核准或拒絕存取。 如果您想要繼續擔任此角色，請選擇 [核准]****，如果您不再需要此存取權，則請選擇 [拒絕]****。 只有在審核者套用結果之後，您的狀態才會變更。

請依照下列步驟來尋找並完成存取權檢閱︰

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 選取 **Azure Active Directory** ，然後開啟 **Privileged Identity Management**。
1. 選取 [ **審核存取**]。

   ![已選取審核存取分頁的 Privileged Identity Management 應用程式螢幕擷取畫面](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

1. 選取您想要完成的檢閱。
1. 選擇 [ **核准** ] 或 [ **拒絕**]。 在 [ **提供原因**] 方塊中，視需要輸入您的決策的業務理由。

   ![檢閱詳細資料頁面的螢幕擷取畫面](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>接下來的步驟

- [在 Privileged Identity Management 中執行我 Azure AD 角色的存取權審核](pim-how-to-perform-security-review.md)
