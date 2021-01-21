---
title: Azure AD Connect cloud sync 新增代理程式設定
description: 本文說明如何安裝 cloud sync。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffdd27b67a122f82cc5fdb5568f11c85387955e8
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660791"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-sync"></a>建立 Azure AD Connect cloud sync 的新設定

安裝 Azure AD Connect 布建代理程式之後，您必須登入 Azure 入口網站並加以設定。 請遵循下列步驟來啟用代理程式。

## <a name="configure-provisioning"></a>設定布建
若要設定布建，請遵循下列步驟。

 1. 在 [Azure 入口網站中，選取 **Azure Active Directory**
 2. 選取 [Azure AD Connect]。
 3. 選取 [ **管理雲端同步**]。

 ![管理布建](media/how-to-install/install-6.png)
 
 4. 選取 [ **新增** 設定]。
 5. 在 [設定] 畫面上，選取您的網域，以及是否啟用密碼雜湊同步處理。 按一下 [ **建立**]。  
 
 ![建立新組態](media/how-to-configure/configure-1.png)


 6.  [編輯布建設定] 畫面隨即開啟。

   ![編輯設定](media/how-to-configure/con-1.png)

 7. 輸入 **通知電子郵件**。 當布建狀況不良時，將會通知這封電子郵件。  建議您 **避免意外刪除** 啟用，並且將 **意外刪除的閾值** 設為您想要收到通知的數位。  如需詳細資訊，請參閱下面的 [意外刪除](#accidental-deletions) 。
 8. 將選取器移至 [啟用]，然後選取 [儲存]。

## <a name="scope-provisioning-to-specific-users-and-groups"></a>範圍布建至特定使用者和群組
您可以使用內部部署 Active Directory 群組或組織單位，將代理程式的範圍設為同步處理特定的使用者和群組。 您無法在設定中設定群組和組織單位。 

 1.  在 Azure 入口網站中，選取 [Azure Active Directory]  。
 2. 選取 [Azure AD Connect]。
 3. 選取 [ **管理雲端同步**]。
 4. 在 [設定] **底下，選取您的設定**。

 ![[設定] 區段](media/how-to-configure/scope-1.png)
 
 5. 在 [ **設定**] 底下，選取 [ **編輯範圍篩選器** ] 來變更設定規則的範圍。
 ![編輯範圍](media/how-to-configure/scope-3.png)
 7. 您可以在右側變更範圍。  按一下 [ **完成**  ]，然後在完成時 **儲存** 。
 8. 變更範圍之後，您應該 [重新開機](#restart-provisioning) 布建，以起始立即同步處理變更。

## <a name="attribute-mapping"></a>屬性對應
Azure AD Connect cloud sync 可讓您輕鬆地對應內部部署使用者/群組物件與 Azure AD 中物件之間的屬性。  您可以根據您的業務需求自訂預設的屬性對應。 因此，您可以變更或刪除現有的屬性對應，或建立新的屬性對應。  如需詳細資訊，請參閱 [屬性對應](how-to-attribute-mapping.md)。

## <a name="on-demand-provisioning"></a>隨選佈建
Azure AD Connect cloud sync 可讓您藉由將這些變更套用至單一使用者或群組，來測試設定變更。  您可以使用此驗證並確認對設定所做的變更已正確套用，並已正確地同步處理至 Azure AD。  如需詳細資訊，請參閱 [隨選布建](how-to-on-demand-provision.md)。

## <a name="accidental-deletions"></a>意外刪除
意外刪除功能的設計目的是要防止您意外設定變更，以及對內部部署目錄的變更，而這些變更會影響許多使用者和群組。  此功能可讓您：

- 設定防止意外刪除的功能。 
- 設定 (閾值) 超過設定將會生效的物件數目 
- 設定通知電子郵件地址，讓他們可以在此案例中有問題的同步處理工作被放入隔離時，收到電子郵件通知 

如需詳細資訊，請參閱 [意外刪除](how-to-accidental-deletes.md)

## <a name="quarantines"></a>隔離
雲端同步會監視設定的健康情況，並將狀況不良的物件置於隔離狀態。 如果對目標系統進行的大部分或所有呼叫一直都因為錯誤而失敗（例如，不正確系統管理員認證），則同步處理作業會標示為隔離中。  如需詳細資訊，請參閱 [隔離](how-to-troubleshoot.md#provisioning-quarantined-problems)的疑難排解一節。

## <a name="restart-provisioning"></a>重新開機布建 
如果您不想等候下一個排程執行，請使用 [ **重新開機** 布建] 按鈕來觸發布建執行。 
 1.  在 Azure 入口網站中，選取 [Azure Active Directory]  。
 2. 選取 [Azure AD Connect]。
 3.  選取 [ **管理雲端同步**]。
 4. 在 [設定] **底下，選取您的設定**。

   ![重新開機布建的設定選項](media/how-to-configure/scope-1.png)

 5. 在頂端，選取 [ **重新開機** 布建]。

## <a name="remove-a-configuration"></a>移除設定
若要刪除設定，請遵循下列步驟。

 1.  在 Azure 入口網站中，選取 [Azure Active Directory]  。
 2. 選取 [Azure AD Connect]。
 3. 選取 [ **管理雲端同步**]。
 4. 在 [設定] **底下，選取您的設定**。
   
   ![要移除設定的設定選項](media/how-to-configure/scope-1.png)

 5. 在 [設定] 畫面的頂端，選取 [ **刪除**]。

>[!IMPORTANT]
>刪除設定之前沒有確認。 選取 [ **刪除**] 之前，請確定這是您想要採取的動作。


## <a name="next-steps"></a>後續步驟 

- [什麼是佈建？](what-is-provisioning.md)
- [什麼是雲端同步 Azure AD Connect？](what-is-cloud-sync.md)
