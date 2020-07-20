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
ms.openlocfilehash: 50f02ea42bb792320da6e2523b733f09afd412a0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85360957"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-based-provisioning"></a>建立 Azure AD Connect 雲端式布建的新設定

安裝代理程式之後，您必須登入 Azure 入口網站並設定 Azure Active Directory （Azure AD） [連線雲端布建]。 請遵循下列步驟來啟用代理程式。

## <a name="configure-provisioning"></a>設定布建
若要設定布建，請遵循下列步驟。

1.  在 [Azure 入口網站中，選取 [ **Azure Active Directory**]。
1.  選取 [Azure AD Connect]****。
1.  選取 **[管理布建（預覽）**]。

    ![管理布建（預覽）](media/how-to-configure/manage1.png)

1.  選取 [**新增**設定]。
1.  在 [設定] 畫面上，已預先填入內部部署網域。
1.  輸入**通知電子郵件**。 當布建狀況不良時，將會通知這封電子郵件。
1.  將選取器移至 [**啟用**]，然後選取 [**儲存**]。

    ![Azure AD 布建（預覽）](media/tutorial-single-forest/configure2.png)

## <a name="scope-provisioning-to-specific-users-and-groups"></a>將範圍布建到特定使用者和群組
您可以使用內部部署 Active Directory 群組或組織單位，將代理程式設為範圍，以同步處理特定的使用者和群組。 您無法在設定內設定群組和組織單位。 

1.  在 [Azure 入口網站中，選取 [ **Azure Active Directory**]。
1.  選取 [Azure AD Connect]****。
1.  選取 **[管理布建（預覽）**]。
1.  在 [設定]**下，選取您的設定**。

    ![[設定] 區段](media/how-to-configure/scope1.png)

1.  在 [設定] 底下選取 [所有使用者]，以變更組態規則的範圍。

    ![[所有使用者] 選項](media/how-to-configure/scope2.png)

1. 在右側，您可以將範圍變更為僅包含安全性群組。 輸入群組的辨別名稱，然後選取 [**新增**]。

    ![選取的安全性群組選項](media/how-to-configure/scope3.png)

1.  或者，您可以將範圍變更為僅包含特定的組織單位。 選取 [**完成**] 和 [**儲存**]。  
2.  變更範圍之後，您應該[重新開機](#restart-provisioning)布建以起始變更的立即同步處理。

    ![選取的組織單位選項](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>重新開機布建 
如果您不想等候下一個排程的執行，請使用 [**重新開機**布建] 按鈕來觸發布建執行。 
1.  在 [Azure 入口網站中，選取 [ **Azure Active Directory**]。
1.  選取 [Azure AD Connect]****。
1.  選取 **[管理布建（預覽）**]。
1.  在 [設定]**下，選取您的設定**。

    ![設定選項以重新開機布建](media/how-to-configure/scope1.png)

1.  在頂端，選取 [**重新開機**布建]。

## <a name="remove-a-configuration"></a>移除設定
若要刪除設定，請遵循下列步驟。

1.  在 [Azure 入口網站中，選取 [ **Azure Active Directory**]。
1.  選取 [Azure AD Connect]****。
1.  選取 **[管理布建（預覽）**]。
1.  在 [設定]**下，選取您的設定**。

    ![要移除設定的設定選項](media/how-to-configure/scope1.png)

1.  在 [設定] 畫面頂端，選取 [**刪除**]。

    ![刪除按鈕](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>在刪除設定之前，不會確認。 請確定這是您想要採取的動作，然後再選取 [**刪除**]。


## <a name="next-steps"></a>後續步驟 

- [什麼是佈建？](what-is-provisioning.md)
- [什麼是 Azure AD Connect 雲端佈建？](what-is-cloud-provisioning.md)
