---
title: 指派對 Azure 成本管理資料的存取權
description: 本文逐步引導您針對不同存取範圍，指派對 Azure 成本管理資料的權限。
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.reviewer: adwise
ms.custom: secdec18
ms.openlocfilehash: a11dcbf9aaa383fca1a0d99539b63ff9478176e9
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87290888"
---
# <a name="assign-access-to-cost-management-data"></a>指派成本管理資料的存取權

對於具有 Azure Enterprise 合約的使用者，在 Azure 入口網站和企業 (EA) 入口網站中授與的權限組合，定義了使用者對 Azure 成本管理資料的存取權層級。 對於其他 Azure 帳戶類型的使用者，定義使用者對成本管理資料的存取層級，會比使用 Azure 角色型存取控制簡單。 本文逐步引導您指派對 Azure 成本管理資料的存取權。 指派權限組合之後，使用者根據其存取權範圍和他們在 Azure 入口網站中選取的範圍，來檢視成本管理中的資料。

使用者選取的範圍使用於成本管理，以提供資料彙總及控制對成本資訊的存取權。 使用範圍時，使用者不能多重選取範圍。 相反地，他們是選取子範圍彙總至的較大範圍，然後再篩選出想要檢視的部分。 請務必了解資料彙總，因為有些人員不應存取子範圍所彙總至的父範圍。

觀看[成本管理控制存取](https://www.youtube.com/watch?v=_uQzQ9puPyM)影片，了解如何使用 Azure 角色型存取控制來指派檢視成本和費用的存取權。 若要觀看其他影片，請造訪[成本管理 YouTube 頻道](https://www.youtube.com/c/AzureCostManagement)。

>[!VIDEO https://www.youtube.com/embed/_uQzQ9puPyM]

## <a name="cost-management-scopes"></a>成本管理範圍

成本管理支援各種不同的 Azure 帳戶類型。 若要檢視所支援帳戶類型的完整清單，請參閱[了解成本管理資料](understand-cost-mgt-data.md)。 帳戶類型會決定可用範圍。

### <a name="azure-ea-subscription-scopes"></a>Azure EA 訂用帳戶的範圍

若要檢視 Azure EA 訂用帳戶的成本資料，使用者必須至少有一或多個下列範圍的讀取存取。

| **範圍** | **定義於** | **檢視資料所需的存取權** | **事先的 EA 設定** | **資料彙總至** |
| --- | --- | --- | --- | --- |
| 計費帳戶<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | 企業管理員 | None | Enterprise 合約中的所有訂用帳戶 |
| department | [https://ea.azure.com](https://ea.azure.com/) | 部門管理員 | 啟用 **DA 檢視費用** | 連結至部門的註冊帳戶所含的所有訂用帳戶 |
| 註冊帳戶<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | 帳戶擁有者 | 啟用 **AO 檢視費用** | 註冊帳戶中的所有訂用帳戶 |
| 管理群組 | [https://portal.azure.com](https://portal.azure.com/) | 成本管理讀者 (或讀者) | 啟用 **AO 檢視費用** | 管理群組下的所有訂用帳戶 |
| 訂用帳戶 | [https://portal.azure.com](https://portal.azure.com/) | 成本管理讀者 (或讀者) | 啟用 **AO 檢視費用** | 訂用帳戶中的所有資源/資源群組 |
| 資源群組 | [https://portal.azure.com](https://portal.azure.com/) | 成本管理讀者 (或讀者) | 啟用 **AO 檢視費用** | 資源群組中的所有資源 |

<sup>1</sup> 帳單帳戶也稱為「Enterprise 合約」或「註冊」。

<sup>2</sup> 註冊帳戶也稱為「帳戶擁有者」。


## <a name="other-azure-account-scopes"></a>其他 Azure 帳戶的範圍

若要檢視其他 Azure 訂用帳戶的成本資料，使用者必須至少有一或多個下列範圍的讀取存取：

- Azure 帳戶
- 管理群組
- 資源群組

在合作夥伴將客戶登錄至 Microsoft 客戶合約後，就會有多種範圍可供使用。 其後，CSP 客戶可在其 CSP 合作夥伴啟用成本管理功能後使用這些功能。 如需詳細資訊，請參閱 [適用於合作夥伴的 Azure 成本管理入門](get-started-partners.md)。

## <a name="enable-access-to-costs-in-the-azure-portal"></a>在 Azure 入口網站中啟用對成本的存取權

針對部門範圍，必須將 [部門管理員可檢視費用]  (DA 檢視費用) 選項設定為 [開啟]  。 請在 Azure 入口網站或 EA 入口網站中設定該選項。 對於其他所有範圍，都必須將 [帳戶擁有者可檢視費用]  (AO 檢視費用) 選項設定為 [開啟]  。

若要在 Azure 入口網站中啟用選項：

1. 使用企業系統管理員帳戶登入 Azure 入口網站 (https://portal.azure.com )。
1. 選取 [成本管理 + 計費]  功能表項目。
1. 選取 [計費範圍]  ，以檢視可用計費範圍和計費帳戶的清單。
1. 從可用的計費帳戶清單中，選取您的 [計費帳戶]  。
1. 在 [設定]  底下選取 [原則]  功能表項目，然後進行設定。  
    ![顯示檢視費用選項的計費範圍原則](./media/assign-access-acm-data/azure-portal-policies-view-charges.png)

啟用檢視費用選項之後，大部分的範圍在 Azure 入口網站中也都需要角色型存取控制 (RBAC) 權限設定。

## <a name="enable-access-to-costs-in-the-ea-portal"></a>在 EA 入口網站中啟用對成本的存取權

部門範圍需要 EA 入口網站中的 [DA 檢視費用]  選項為 [已啟用]  。 請在 Azure 入口網站或 EA 入口網站中設定該選項。 所有其他範圍都需要 EA 入口網站中的 [AO 檢視費用]  選項為 [已啟用]  。

若要在 EA 入口網站中啟用選項：

1. 使用企業系統管理員帳戶登入在 [https://ea.azure.com](https://ea.azure.com) 的 EA 入口網站。
2. 在左窗格中選取 [管理]  。
3. 針對您想要提供存取權的成本管理範圍，啟用 [DA 檢視費用]  和/或 [AO 檢視費用]  的費用選項。  
    ![顯示 DA 和 AO 檢視費用選項的 [註冊] 索引標籤](./media/assign-access-acm-data/ea-portal-enrollment-tab.png)

啟用檢視費用選項之後，大部分的範圍在 Azure 入口網站中也都需要角色型存取控制 (RBAC) 權限設定。

## <a name="enterprise-administrator-role"></a>Enterprise 系統管理員角色

根據預設，企業系統管理員可存取帳單帳戶 (Enterprise 合約/註冊)，以及所有其他範圍，而這些範圍是子範圍。 企業系統管理員會為其他使用者指派對範圍的存取權。 根據商務持續性的最佳做法，您應該一律有兩個使用者具備企業系統管理員存取權。 下列各節是企業系統管理員為其他使用者指派範圍存取權的逐步範例。

## <a name="assign-billing-account-scope-access"></a>指派帳單帳戶範圍存取權

對帳單帳戶範圍的存取權，在 EA 入口網站中需要企業系統管理員權限。 企業系統管理員可檢視整個 EA 註冊或多個註冊的成本。 對於帳單帳戶範圍，在 Azure 入口網站中不需要採取動作。

1. 使用企業系統管理員帳戶登入在 [https://ea.azure.com](https://ea.azure.com) 的 EA 入口網站。
2. 在左窗格中選取 [管理]  。
3. 在 [註冊]  索引標籤上，選取您想要管理的註冊。  
    ![在 EA 入口網站中選取您的註冊](./media/assign-access-acm-data/ea-portal.png)
4. 選取 [+ 新增系統管理員]  。
5. 在 [新增系統管理員] 方塊中，選取驗證類型，並輸入使用者的電子郵件地址。
6. 如果使用者應該要有對成本和使用量資料的唯讀存取權，請在 [唯讀]  底下選取 [是]  。  否則，請選取 [否]  。
7. 選取 [新增]  以建立帳戶。  
    ![顯示於 [新增系統管理員] 方塊中的範例資訊](./media/assign-access-acm-data/add-admin.png)

可能需要最多 30 分鐘，新使用者才能在成本管理中存取資料。

### <a name="assign-department-scope-access"></a>指派部門範圍存取權

對部門範圍的存取權，在 EA 入口網站中需要部門系統管理員 (DA 檢視收費) 存取權。 部門系統管理員可檢視與部門或多個部門相關聯的成本和使用量資料。 部門的資料包括屬於連結至部門之註冊帳戶的所有訂用帳戶。 在 Azure 入口網站中不需要採取動作。

1. 使用企業系統管理員帳戶登入在 [https://ea.azure.com](https://ea.azure.com) 的 EA 入口網站。
2. 在左窗格中選取 [管理]  。
3. 在 [註冊]  索引標籤上，選取您想要管理的註冊。
4. 選取 [部門]  索引標籤，然後選取 [新增系統管理員]  。
5. 在 [新增部門系統管理員] 方塊中，選取驗證類型，然後輸入使用者的電子郵件地址。
6. 如果使用者應該要有對成本和使用量資料的唯讀存取權，請在 [唯讀]  底下選取 [是]  。  否則，請選取 [否]  。
7. 選取您想要授與部門系統管理員權限的部門。
8. 選取 [新增]  以建立帳戶。  
    ![在 [新增部門系統管理員] 方塊中輸入必要的資訊](./media/assign-access-acm-data/add-depart-admin.png)

## <a name="assign-enrollment-account-scope-access"></a>指派註冊帳戶範圍存取權

對註冊帳戶範圍的存取權，在 EA 入口網站中需要帳戶擁有者 (AO 檢視費用) 存取權。 帳戶擁有者可以檢視與從該註冊帳戶建立之訂用帳戶相關聯的成本和使用量資料。 在 Azure 入口網站中不需要採取動作。

1. 使用企業系統管理員帳戶登入在 [https://ea.azure.com](https://ea.azure.com) 的 EA 入口網站。
2. 在左窗格中選取 [管理]  。
3. 在 [註冊]  索引標籤上，選取您想要管理的註冊。
4. 選取 [帳戶]  索引標籤，然後選取 [新增帳戶]  。
5. 在 [新增帳戶] 方塊中，選取 [部門]  以和帳戶產生關聯，或將它保留為未指派。
6. 選取驗證類型並輸入帳戶名稱。
7. 輸入使用者的電子郵件地址，然後選擇性地輸入成本中心。
8. 選取 [新增]  以建立帳戶。  
    ![在註冊帳戶的 [新增帳戶] 方塊中輸入必要的資訊](./media/assign-access-acm-data/add-account.png)

完成上述步驟之後，使用者帳戶會成為企業版入口網站中的註冊帳戶，並可建立訂用帳戶。 使用者可以存取所建立訂用帳戶的成本和使用量資料。

## <a name="assign-management-group-scope-access"></a>指派管理群組範圍存取權

至少須具備成本管理讀者 (或讀者) 權限，才有權檢視管理群組範圍。 您可以在 Azure 入口網站中設定對管理群組的權限。 您必須至少有管理群組的使用者存取系統管理員 (或擁有者) 權限，才能為其他人啟用存取權。 而且針對 Azure EA 帳戶，您也必須啟用 EA 入口網站中的 [AO 檢視費用]  設定。

1. 登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。
2. 在側邊欄中選取 [所有服務]  ，再搜尋「管理群組」  ，然後選取 [管理群組]  。
3. 您可以選取階層中的管理群組。
4. 在您的管理群組名稱旁，選取 [詳細資料]  。
5. 從左窗格中選取 [存取控制 (IAM)]  。
6. 選取 [新增]  。
7. 在 [角色]  底下，選取 [成本管理讀者]  。
8. 在 [存取權指派對象為]  底下，選取 [Azure AD 使用者、群組或應用程式]  。
9. 若要指派存取權，搜尋並選取使用者。
10. 選取 [儲存]  。  
    ![管理群組的 [新增權限] 方塊中的範例資訊](./media/assign-access-acm-data/add-permissions.png)

## <a name="assign-subscription-scope-access"></a>指派訂用帳戶範圍存取權

對訂用帳戶的存取權至少需要成本管理讀者 (或讀者) 權限。 您可以在 Azure 入口網站中設定對訂用帳戶的權限。 您必須至少有訂用帳戶的使用者存取系統管理員 (或擁有者) 權限，才能為其他人啟用存取權。 而且針對 Azure EA 帳戶，您也必須啟用 EA 入口網站中的 [AO 檢視費用]  設定。

1. 登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。
2. 在側邊欄中選取 [所有服務]  ，再搜尋「訂用帳戶」  ，然後選取 [訂用帳戶]  。
3. 選取您的訂用帳戶。
4. 從左窗格中選取 [存取控制 (IAM)]  。
5. 選取 [新增]  。
6. 在 [角色]  底下，選取 [成本管理讀者]  。
7. 在 [存取權指派對象為]  底下，選取 [Azure AD 使用者、群組或應用程式]  。
8. 若要指派存取權，搜尋並選取使用者。
9. 選取 [儲存]  。

## <a name="assign-resource-group-scope-access"></a>指派資源群組範圍存取權

對資源群組的存取權至少需要成本管理讀者 (或讀者) 權限。 您可以在 Azure 入口網站中設定對資源群組的權限。 您必須至少有資源群組的使用者存取系統管理員 (或擁有者) 權限，才能為其他人啟用存取權。 而且針對 Azure EA 帳戶，您也必須啟用 EA 入口網站中的 [AO 檢視費用]  設定。

1. 登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。
2. 在側邊欄中選取 [所有服務]  ，再搜尋「資源群組」  ，然後選取 [資源群組]  。
3. 選取您的資源群組。
4. 從左窗格中選取 [存取控制 (IAM)]  。
5. 選取 [新增]  。
6. 在 [角色]  底下，選取 [成本管理讀者]  。
7. 在 [存取權指派對象為]  底下，選取 [Azure AD 使用者、群組或應用程式]  。
8. 若要指派存取權，搜尋並選取使用者。
9. 選取 [儲存]  。

## <a name="cross-tenant-authentication-issues"></a>跨租用戶驗證問題

目前，Azure 成本管理對於跨租用戶驗證僅提供有限支援。 在某些情況下，當您嘗試進行跨租用戶驗證時，您可能會在成本分析中收到**拒絕存取**錯誤。 如果您對另一個租用戶的訂用帳戶設定了角色型存取控制 (RBAC)，然後嘗試檢視成本資料，就可能會發生此問題。

*若要解決此問題*：設定跨租用戶 RBAC 之後，請等候一小時。 然後，嘗試在成本分析中檢視成本，或將成本管理存取權授與這兩個租用戶中的使用者。  


## <a name="next-steps"></a>後續步驟

- 如果您尚未完成成本管理的第一個快速入門，請在[開始分析成本](quick-acm-cost-analysis.md)閱讀它。
