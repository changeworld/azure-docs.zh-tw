---
title: 故障排除授權管理 - Azure AD
description: 瞭解應檢查的一些專案，以説明您解決 Azure 活動目錄授權管理。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128465"
---
# <a name="troubleshoot-azure-ad-entitlement-management"></a>排除 Azure AD 授權管理

本文介紹了一些應檢查的專案，以説明您解決 Azure 活動目錄 （Azure AD） 授權管理。

## <a name="administration"></a>系統管理

* 如果在配置授權管理時收到拒絕訪問的消息，並且您是全域管理員，請確保目錄具有 Azure AD 高級[P2（或 EMS E5）許可證](entitlement-management-overview.md#license-requirements)。

* 如果在創建或查看訪問包時收到拒絕訪問的消息，並且您是目錄建立者組的成員，則必須在創建第一個訪問包之前[創建目錄](entitlement-management-catalog-create.md)。

## <a name="resources"></a>資源

* 應用程式的角色是由應用程式本身定義，並在 Azure AD 中進行管理。 如果應用程式沒有任何資源角色，則授權管理會將使用者分配給**預設訪問**角色。

    請注意，Azure 門戶還可以顯示無法選擇為應用程式提供服務的服務主體。  特別是，**連線交換**和**SharePoint Online**是服務，而不是目錄中具有資源角色的應用程式，因此它們不能包含在訪問包中。  相反地，請使用群組型授權，為需要存取這些服務的使用者建立適當的授權。

* 若要讓群組成為存取套件中的資源，該群組必須能夠在 Azure AD 中進行修改。  源自內部部署 Active Directory 的群組無法指派為資源，因為無法在 Azure AD 中變更其擁有者或成員屬性。   源自 Exchange Online 的群組，是因為也無法在 Azure AD 中修改通訊群組。 

* SharePoint Online 文件庫和個別文件無法新增為資源。  相反，創建[Azure AD 安全性群組](../fundamentals/active-directory-groups-create-azure-portal.md)，在訪問包中包括該組和網站角色，在 SharePoint Online 中使用該組來控制對文件庫或文檔的訪問。

* 如果已將使用者指派給您想透過存取套件管理的資源，請確定已將使用者指派給具有適當原則的存取套件。 例如，您可以在存取套件中納入群組中已經有使用者的群組。 如果群組中的使用者需要繼續保有存取權，則必須擁有適當的存取套件原則，才能繼續存取群組。 您可以要求使用者要求包含該資源的存取套件，或直接將其指派給存取套件，以指派存取套件。 有關詳細資訊，請參閱[更改訪問包的請求和審批設置](entitlement-management-access-package-request-policy.md)。

* 移除小組成員時，也會從 Office 365 群組中將其移除。 移除小組的交談功能方面可能會延遲。 有關詳細資訊，請參閱[組成員身份](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership)。

* 請確定您的目錄未設定為多地理位置。 權利管理目前不支援 SharePoint Online 的多地理位置。 SharePoint Online 網站必須位於預設地理位置，才能與權利管理一同控管。 有關詳細資訊，請參閱[OneDrive 中的多地理功能和連線共用點](https://docs.microsoft.com/Office365/Enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-office-365)。

## <a name="access-packages"></a>存取套件

* 如果您嘗試刪除訪問包或策略，並看到一條錯誤訊息，指出存在活動分配，如果您沒有看到任何具有分配的使用者，請檢查最近刪除的任何使用者是否仍有分配。 在使用者刪除後的 30 天時段內，可以還原使用者帳戶。   

## <a name="external-users"></a>外部使用者

* 當外部使用者想要請求訪問包時，請確保他們使用訪問包的 **"我的訪問"門戶連結**。 有關詳細資訊，請參閱[共用連結以請求訪問包](entitlement-management-access-package-settings.md)。 如果外部使用者只是訪問**myaccess.microsoft.com**並且不使用完整的"我的訪問"門戶連結，那麼他們將在自己的組織中看到可供他們訪問的套裝程式，而不是您的組織中。

* 如果外部使用者無法請求訪問包或無法訪問資源，請確保[檢查外部使用者的設置](entitlement-management-external-users.md#settings-for-external-users)。

* 如果新的外部使用者先前尚未登入您的目錄，但收到包含 SharePoint Online 網站的存取套件，則這些外部使用者的存取套件會顯示為在 SharePoint Online 中佈建帳戶之前不會完整傳遞的訊息。 有關共用設置的詳細資訊，請參閱查看[SharePoint 連線外部共用設置](entitlement-management-external-users.md#review-your-sharepoint-online-external-sharing-settings)。

## <a name="requests"></a>Requests

* 當使用者要請求訪問包時，請確保他們使用訪問包的 **"我的訪問"門戶連結**。 有關詳細資訊，請參閱[共用連結以請求訪問包](entitlement-management-access-package-settings.md)。

* 如果您在設為私人或無痕模式的瀏覽器中開啟「我的存取權」入口網站，可能會與登入行為相互衝突。 造訪「我的存取權」入口網站時，建議您不要使用設為私人或無痕模式的瀏覽器。

* 當不在您目錄中的使用者登入「我的存取權」入口網站並要求存取套件時，請務必使用其組織帳戶進行驗證。 組織帳戶可以是資源目錄中的帳戶，或包含在存取套件其中一個原則中的目錄。 如果使用者的帳戶不是組織帳戶，或驗證所在的目錄未包含在原則中，則使用者將看不到存取套件。 有關詳細資訊，請參閱[請求訪問包](entitlement-management-request-access.md)。

* 如果使用者遭到封鎖而無法登入資源目錄，則無法在「我的存取權」入口網站中要求存取權。 您必須先從使用者的設定檔中移除登入區塊，才能要求存取權。 要刪除登錄塊，請在 Azure 門戶中按一下**Azure 活動目錄**，按一下 **"使用者**"，按一下"使用者"，然後按一下 **"設定檔**"。 編輯 **"設置"** 部分並將 **"阻止登錄"** 更改為 **"否**"。 有關詳細資訊，請參閱使用[Azure 活動目錄添加或更新使用者的設定檔資訊](../fundamentals/active-directory-users-profile-azure-portal.md)。  您還可以檢查使用者是否由於[身份保護原則](../identity-protection/howto-unblock-user.md)而被阻止。

* 在"我的訪問"門戶中，如果使用者既是要求者和核准者，他們將不會在 **"審批"** 頁上看到他們的訪問包請求。 這是刻意的行為，因為使用者無法核准自己的要求。 請確定使用者要求的存取套件已在原則上設定其他核准者。 有關詳細資訊，請參閱[更改訪問包的請求和審批設置](entitlement-management-access-package-request-policy.md)。

### <a name="view-a-requests-delivery-errors"></a>查看請求的傳遞錯誤

**先決條件角色：** 全域管理員、使用者管理員、目錄擁有者或訪問包管理器

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左側功能表中，按一下 **"訪問包**"，然後打開訪問包。

1. 按一下**請求**。

1. 選擇要查看的請求。

    如果請求有任何傳遞錯誤，則請求狀態將**取消傳遞**或**部分傳遞**。

    如果存在任何傳遞錯誤，請在請求的詳細資訊窗格中，將存在傳遞錯誤計數。

1. 按一下計數以查看請求的所有傳遞錯誤。

### <a name="reprocess-a-request"></a>重新處理請求

如果請求遇到錯誤，您可以重新處理請求以重試它。 您只能重新處理狀態**為"交付失敗**"或**部分送達**且完成日期少於一周的請求。

**先決條件角色：** 全域管理員、使用者管理員、目錄擁有者或訪問包管理器

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左側功能表中，按一下 **"訪問包**"，然後打開訪問包。

1. 按一下**請求**。

1. 按一下要重新處理的請求。

1. 在請求詳細資訊窗格中，按一下"**重新處理請求**"。

    ![重新處理失敗的請求](./media/entitlement-management-troubleshoot/reprocess-request.png)

### <a name="cancel-a-pending-request"></a>取消擱置要求

您只能取消尚未送達或其傳遞失敗的請求。

**先決條件角色：** 全域管理員、使用者管理員、目錄擁有者或訪問包管理器

1. 在 Azure 入口網站中按一下 [Azure Active Directory]****，然後按一下 [身分識別治理]****。

1. 在左側功能表中，按一下 **"訪問包**"，然後打開訪問包。

1. 按一下**請求**。

1. 按一下要取消的請求。

1. 在請求詳細資訊窗格中，按一下 **"取消請求**"。

## <a name="multiple-policies"></a>多個策略

* 授權管理遵循最低特權最佳做法。 當使用者請求訪問具有多個應用策略的訪問包時，授權管理包括邏輯，以説明確保對泛型策略設置更嚴格或更具體的策略優先順序。 如果策略是通用的，則授權管理可能不會向要求者顯示策略，或者可能自動選擇更嚴格的策略。

* 例如，考慮一個訪問包，其中兩個策略都適用于要求者，其中兩個策略都適用于要求者。 第一個策略適用于包含要求者的特定使用者。 第二個策略適用于要求者是該目錄的所有使用者。 在這種情況下，自動為要求者選擇第一個策略，因為它更加嚴格。 要求者沒有選擇第二個策略的選項。

* 應用多個策略時，自動選擇的策略或向要求者顯示的策略基於以下優先順序邏輯：

    | 政策優先順序 | 影響範圍 |
    | --- | --- |
    | P1 | 目錄中的特定使用者和組或特定連接的組織 |
    | P2 | 目錄中的所有成員（不包括客人） |
    | P3 | 目錄中的所有使用者（包括來賓）或特定連接的組織 |
    | P4 | 所有連接的組織或所有使用者（所有連接的組織 + 任何新的外部使用者） |
    
    如果任何策略屬於較高優先順序類別，則忽略優先順序較低的類別。 有關如何向要求者顯示具有相同優先順序的多個策略的示例，請參閱[選擇策略](entitlement-management-request-access.md#select-a-policy)。

## <a name="next-steps"></a>後續步驟

- [管控外部使用者的存取](entitlement-management-external-users.md)
- [查看使用者如何在授權管理中獲得存取權限的報告](entitlement-management-reports.md)
