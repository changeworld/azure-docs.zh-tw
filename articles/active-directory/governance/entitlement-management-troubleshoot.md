---
title: 針對權利管理進行疑難排解-Azure AD
description: 瞭解您應該檢查的一些專案，以協助您針對 Azure Active Directory 權利管理進行疑難排解。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c38e1a61827da547bb39a699a0e92043e63466c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80128465"
---
# <a name="troubleshoot-azure-ad-entitlement-management"></a>針對 Azure AD 權利管理進行疑難排解

本文說明您應該檢查的一些專案，以協助您進行 Azure Active Directory （Azure AD）權利管理的疑難排解。

## <a name="administration"></a>系統管理

* 如果您在設定權利管理時收到拒絕存取的訊息，而且您是全域管理員，請確定您的目錄具有[Azure AD Premium P2 （或 EMS E5）授權](entitlement-management-overview.md#license-requirements)。

* 如果您在建立或查看存取套件時收到拒絕存取的訊息，而且您是目錄建立者群組的成員，則必須在建立第一個存取套件之前先[建立目錄](entitlement-management-catalog-create.md)。

## <a name="resources"></a>資源

* 應用程式的角色是由應用程式本身定義，並在 Azure AD 中進行管理。 如果應用程式沒有任何資源角色，權利管理會將使用者指派給**預設存取**角色。

    請注意，Azure 入口網站可能也會顯示無法選取為應用程式之服務的服務主體。  特別是， **Exchange online**和**SharePoint online**是服務，而不是在目錄中具有資源角色的應用程式，因此不能包含在存取套件中。  相反地，請使用群組型授權，為需要存取這些服務的使用者建立適當的授權。

* 若要讓群組成為存取套件中的資源，該群組必須能夠在 Azure AD 中進行修改。  源自內部部署 Active Directory 的群組無法指派為資源，因為無法在 Azure AD 中變更其擁有者或成員屬性。   源自 Exchange Online 的群組，是因為也無法在 Azure AD 中修改通訊群組。 

* SharePoint Online 文件庫和個別文件無法新增為資源。  相反地，請建立[Azure AD 安全性群組](../fundamentals/active-directory-groups-create-azure-portal.md)、在存取套件中包含該群組和網站角色，然後在 SharePoint Online 中使用該群組來控制文件庫或檔的存取權。

* 如果已將使用者指派給您想透過存取套件管理的資源，請確定已將使用者指派給具有適當原則的存取套件。 例如，您可以在存取套件中納入群組中已經有使用者的群組。 如果群組中的使用者需要繼續保有存取權，則必須擁有適當的存取套件原則，才能繼續存取群組。 您可以要求使用者要求包含該資源的存取套件，或直接將其指派給存取套件，以指派存取套件。 如需詳細資訊，請參閱[變更存取套件的要求和核准設定](entitlement-management-access-package-request-policy.md)。

* 移除小組成員時，也會從 Office 365 群組中將其移除。 移除小組的交談功能方面可能會延遲。 如需詳細資訊，請參閱[群組成員資格](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership)。

* 請確定您的目錄未設定為多地理位置。 權利管理目前不支援 SharePoint Online 的多地理位置。 SharePoint Online 網站必須位於預設地理位置，才能與權利管理一同控管。 如需詳細資訊，請參閱[OneDrive 和 SharePoint Online 中的多地理位置功能](https://docs.microsoft.com/Office365/Enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-office-365)。

## <a name="access-packages"></a>存取套件

* 如果您嘗試刪除存取封裝或原則，並看到錯誤訊息，指出有作用中的指派，如果您沒有看到任何具有指派的使用者，請查看是否有任何最近刪除的使用者仍有指派。 刪除使用者之後的30天期間，可以還原使用者帳戶。   

## <a name="external-users"></a>外部使用者

* 當外部使用者想要要求存取套件的存取權時，請確定他們使用的是存取套件的**我的存取權入口網站連結**。 如需詳細資訊，請參閱[共用連結以要求存取封裝](entitlement-management-access-package-settings.md)。 如果外部使用者只是造訪**myaccess.microsoft.com** ，而未使用完整的我的存取權入口網站連結，則他們會在自己的組織中看到其適用的存取套件，而不是您的組織。

* 如果外部使用者無法要求存取封裝或無法存取資源，請務必檢查[外部使用者的設定](entitlement-management-external-users.md#settings-for-external-users)。

* 如果新的外部使用者先前尚未登入您的目錄，但收到包含 SharePoint Online 網站的存取套件，則這些外部使用者的存取套件會顯示為在 SharePoint Online 中佈建帳戶之前不會完整傳遞的訊息。 如需共用設定的詳細資訊，請參閱[審查 SharePoint Online 外部共用設定](entitlement-management-external-users.md#review-your-sharepoint-online-external-sharing-settings)。

## <a name="requests"></a>Requests

* 當使用者想要要求存取套件的存取權時，請確定他們使用的是存取套件的**我的存取權入口網站連結**。 如需詳細資訊，請參閱[共用連結以要求存取封裝](entitlement-management-access-package-settings.md)。

* 如果您在設為私人或無痕模式的瀏覽器中開啟「我的存取權」入口網站，可能會與登入行為相互衝突。 造訪「我的存取權」入口網站時，建議您不要使用設為私人或無痕模式的瀏覽器。

* 當不在您目錄中的使用者登入「我的存取權」入口網站並要求存取套件時，請務必使用其組織帳戶進行驗證。 組織帳戶可以是資源目錄中的帳戶，或包含在存取套件其中一個原則中的目錄。 如果使用者的帳戶不是組織帳戶，或驗證所在的目錄未包含在原則中，則使用者將看不到存取套件。 如需詳細資訊，請參閱[要求存取封裝的存取權](entitlement-management-request-access.md)。

* 如果使用者遭到封鎖而無法登入資源目錄，則無法在「我的存取權」入口網站中要求存取權。 您必須先從使用者的設定檔中移除登入區塊，才能要求存取權。 若要移除登入區塊，請在 [Azure 入口網站中，按一下 [ **Azure Active Directory**，按一下 [**使用者**]，按一下使用者，然後按一下 [**設定檔**]。 編輯 [**設定**] 區段，並將 [**封鎖登入**] 變更為 [**否**]。 如需詳細資訊，請參閱[使用 Azure Active Directory 新增或更新使用者的設定檔資訊](../fundamentals/active-directory-users-profile-azure-portal.md)。  您也可以檢查使用者是否因身分[識別保護原則](../identity-protection/howto-unblock-user.md)而遭到封鎖。

* 在我的存取權入口網站中，如果使用者同時是要求者和核准者，他們將不會在 [**核准**] 頁面上看到其存取套件的要求。 這是刻意的行為，因為使用者無法核准自己的要求。 請確定使用者要求的存取套件已在原則上設定其他核准者。 如需詳細資訊，請參閱[變更存取套件的要求和核准設定](entitlement-management-access-package-request-policy.md)。

### <a name="view-a-requests-delivery-errors"></a>查看要求的傳遞錯誤

**必要角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [**存取套件**]，然後開啟存取封裝。

1. 按一下 [**要求**]。

1. 選取您想要查看的要求。

    如果要求有任何傳遞錯誤，則要求狀態將會是 [**未傳遞] 或 [** **部分傳遞**]。

    如果有任何傳遞錯誤，在要求的詳細資料窗格中，將會有傳遞錯誤的計數。

1. 按一下 [計數]，以查看所有要求的傳遞錯誤。

### <a name="reprocess-a-request"></a>重新處理要求

如果要求遇到錯誤，您可以重新處理要求以再次嘗試。 您只能重新處理狀態為 [**傳遞失敗**] 或 [**部分傳遞**] 且完成日期不到一周的要求。

**必要角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [**存取套件**]，然後開啟存取封裝。

1. 按一下 [**要求**]。

1. 按一下您要重新處理的要求。

1. 在 [要求詳細資料] 窗格中，按一下 [重新處理**要求**]。

    ![重新處理失敗的要求](./media/entitlement-management-troubleshoot/reprocess-request.png)

### <a name="cancel-a-pending-request"></a>取消擱置要求

您只能取消尚未傳遞或其傳遞失敗的擱置要求。

**必要角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]  ，然後按一下 [身分識別治理]  。

1. 在左側功能表中，按一下 [**存取套件**]，然後開啟存取封裝。

1. 按一下 [**要求**]。

1. 按一下您要取消的要求。

1. 在 [要求詳細資料] 窗格中，按一下 [**取消要求**]。

## <a name="multiple-policies"></a>多個原則

* 權利管理遵循最低許可權的最佳作法。 當使用者要求存取已套用多項原則的存取套件時，權利管理包含的邏輯可協助確保更嚴格或更明確的原則優先于一般原則。 如果原則是通用的，權利管理可能不會向要求者顯示原則，或可能會自動選取更嚴格的原則。

* 例如，假設有一個具有兩個原則的存取封裝，適用于這兩個原則會套用至要求者的內部員工。 第一個原則適用于包含要求者的特定使用者。 第二個原則適用于要求者為其成員之目錄中的所有使用者。 在此案例中，系統會針對要求者自動選取第一個原則，因為它較嚴格。 要求者未提供選取第二個原則的選項。

* 套用多個原則時，自動選取的原則或向要求者顯示的原則會以下列優先權邏輯為基礎：

    | 原則優先順序 | 影響範圍 |
    | --- | --- |
    | P1 | 您目錄中的特定使用者和群組，或特定的已連線組織 |
    | P2 | 目錄中的所有成員（不包括來賓） |
    | P3 | 您目錄中的所有使用者（包括來賓）或特定的已連線組織 |
    | P4 | 所有已連線的組織或所有使用者（所有已連線的組織 + 任何新的外部使用者） |
    
    如果有任何原則的優先順序較高，則會忽略較低優先順序的類別。 如需如何向要求者顯示多個具有相同優先順序的原則的範例，請參閱[選取原則](entitlement-management-request-access.md#select-a-policy)。

## <a name="next-steps"></a>後續步驟

- [管控外部使用者的存取](entitlement-management-external-users.md)
- [查看使用者在權利管理中取得存取權的報告](entitlement-management-reports.md)
