---
title: Azure AD Connect 雲端布建新的代理程式設定
description: 本文說明如何安裝雲端布建。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b18629a776dd98950f49b1f607cbc876abcd9c
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91628876"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-based-provisioning"></a>建立 Azure AD Connect 雲端式布建的新設定

安裝代理程式之後，您必須登入 Azure 入口網站，並設定 Azure Active Directory (Azure AD) Connect 雲端布建。 請遵循下列步驟來啟用代理程式。

## <a name="configure-provisioning"></a>設定布建
若要設定布建，請遵循下列步驟。

 1. 在 [Azure 入口網站中，選取 **Azure Active Directory**
 2. 選取 [Azure AD Connect]****。
 3. 選取 [ **管理**布建]。

 ![管理布建](media/how-to-configure/manage1.png)
 
 4. 選取 [ **新增**設定]。
 5. 在 [設定] 畫面上，選取您的網域，以及是否啟用密碼雜湊同步處理。 按一下 [ **建立**]。  
 
 ![建立新組態](media/how-to-configure/configure1.png)


 6.  [編輯布建設定] 畫面隨即開啟。

   ![編輯設定](media/how-to-configure/configure2.png)

 7. 輸入 **通知電子郵件**。 當布建狀況不良時，將會通知這封電子郵件。
 8. 將選取器移至 [啟用]，然後選取 [儲存]。

## <a name="scope-provisioning-to-specific-users-and-groups"></a>範圍布建至特定使用者和群組
您可以使用內部部署 Active Directory 群組或組織單位，將代理程式的範圍設為同步處理特定的使用者和群組。 您無法在設定中設定群組和組織單位。 

 1.  在 [Azure 入口網站中，選取 [ **Azure Active Directory**]。
 2. 選取 [Azure AD Connect]****。
 3. 選取 [管理布建 ** (預覽]) **。
 4. 在 [設定] **底下，選取您的設定**。

 ![[設定] 區段](media/how-to-configure/scope1.png)
 
 5. 在 [ **設定**] 底下，選取 [ **編輯範圍篩選器** ] 來變更設定規則的範圍。
 ![編輯範圍](media/how-to-configure/scope3.png)
 7. 您可以在右側變更範圍。  按一下 [ **完成**  ]，然後在完成時 **儲存** 。
 8. 變更範圍之後，您應該 [重新開機](#restart-provisioning) 布建，以起始立即同步處理變更。

## <a name="attribute-mapping"></a>屬性對應
Azure AD Connect 雲端布建可讓您輕鬆地對應內部部署使用者/群組物件與 Azure AD 中物件之間的屬性。  您可以根據您的業務需求自訂預設的屬性對應。 因此，您可以變更或刪除現有的屬性對應，或建立新的屬性對應。  如需詳細資訊，請參閱 [屬性對應](how-to-attribute-mapping.md)。

## <a name="on-demand-provisioning"></a>隨選布建
Azure AD Connect 雲端布建可讓您藉由將這些變更套用至單一使用者或群組，來測試設定變更。  您可以使用此驗證並確認對設定所做的變更已正確套用，並已正確地同步處理至 Azure AD。  如需詳細資訊，請參閱 [隨選布建](how-to-on-demand-provision.md)。

## <a name="restart-provisioning"></a>重新開機布建 
如果您不想等候下一個排程執行，請使用 [ **重新開機** 布建] 按鈕來觸發布建執行。 
 1.  在 [Azure 入口網站中，選取 [ **Azure Active Directory**]。
 2. 選取 [Azure AD Connect]****。
 3.  選取 [管理布建 ** (預覽]) **。
 4. 在 [設定] **底下，選取您的設定**。

   ![重新開機布建的設定選項](media/how-to-configure/scope1.png)

 5. 在頂端，選取 [ **重新開機**布建]。

## <a name="remove-a-configuration"></a>移除設定
若要刪除設定，請遵循下列步驟。

 1.  在 [Azure 入口網站中，選取 [ **Azure Active Directory**]。
 2. 選取 [Azure AD Connect]****。
 3. 選取 [管理布建 ** (預覽]) **。
 4. 在 [設定] **底下，選取您的設定**。
   
   ![要移除設定的設定選項](media/how-to-configure/scope1.png)

 5. 在 [設定] 畫面的頂端，選取 [ **刪除**]。

>[!IMPORTANT]
>刪除設定之前沒有確認。 選取 [ **刪除**] 之前，請確定這是您想要採取的動作。


## <a name="next-steps"></a>後續步驟 

- [什麼是佈建？](what-is-provisioning.md)
- [什麼是 Azure AD Connect 雲端佈建？](what-is-cloud-provisioning.md)
