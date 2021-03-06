---
title: 使用存取權審核來管理使用者存取-Azure AD
description: 了解如何透過 Azure Active Directory 存取權檢閱，以群組成員資格或指派給應用程式的方式管理使用者存取權
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc12b4cb7e97a0808405baebc64ca83cdb742bf1
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696943"
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>使用 Azure AD 存取權檢閱來管理使用者存取權

透過 Azure Active Directory (Azure AD)，您可以輕易地確認使用者是否有適當的存取權。 您可藉由要求使用者本身或決策者參與存取權檢閱，並重新證實 (或「證明」) 使用者的存取權。 檢閱者可以根據 Azure AD 的建議，對每位使用者的持續存取需求給予其意見。 存取權檢閱完成時，您可接著進行變更並為使用者移除不再需要的存取權。

> [!NOTE]
> 如果您只想檢閱來賓使用者的存取權，而不要檢閱各類使用者的存取權，請參閱[透過存取權檢閱管理來賓使用者存取權](manage-guest-access-with-access-reviews.md)。 若您想要檢閱使用者的系統管理角色 (例如全域系統管理員) 成員資格，請參閱[在 Azure AD Privileged Identity Management 中開始存取權檢閱](../privileged-identity-management/pim-how-to-start-security-review.md)。

## <a name="prerequisites"></a>先決條件

- Azure AD Premium P2

如需詳細資訊，請參閱[授權需求](access-reviews-overview.md#license-requirements)。

## <a name="create-and-perform-an-access-review"></a>建立和執行存取權檢閱

您可以讓一或多個使用者作為存取權檢閱中的檢閱者。  

1. 在 Azure AD 中選取具有一個或多個成員的群組。 或選取連線到 Azure AD 的應用程式，該應用程式上有一或多個指派至此的使用者。 

2. 決定是否要讓每個使用者檢閱自己的存取權，或讓一個或多個使用者檢閱每個人的存取權。

3. 下列其中一個角色：全域管理員、使用者系統管理員或 (預覽版) 要檢查之群組的 M365 或 AAD 安全性群組擁有者，請移至 [身分 [識別管理] 頁面](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)。

4. 建立存取權檢閱。 如需詳細資訊，請參閱 [建立群組或應用程式的存取權審核](create-access-review.md)。

5. 存取權檢閱開始時，要求檢閱者提供輸入。 根據預設，每個使用者都會收到來自 Azure AD 的電子郵件，其中包含存取面板的連結，可在其中 [查看群組或應用程式的存取權](perform-access-review.md)。

6. 如果檢閱者有沒有指定的輸入，則您可以要求 Azure AD 將提醒傳送給他們。 依預設，Azure AD 會在結束日期過半時自動將提醒傳送給尚未回應的檢閱者。

7. 在檢閱者提供輸入後，停止存取權檢閱並套用變更。 如需詳細資訊，請參閱 [完成群組或應用程式的存取權檢查](complete-access-review.md)。


## <a name="next-steps"></a>後續步驟

[建立群組或應用程式的存取權檢閱](create-access-review.md)




