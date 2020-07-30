---
title: 在 Azure Active Directory 入口網站中下載使用者清單 |Microsoft Docs
description: 在 Azure Active Directory 的 Azure 系統管理中心內大量下載使用者記錄。
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 04/16/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb119ef0ffd4da4dc524c9d3c0a88b94e2251142
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87423553"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>在 Azure Active Directory 入口網站中下載使用者清單

Azure Active Directory （Azure AD）支援大量使用者匯入（建立）作業。

## <a name="required-permissions"></a>必要權限

若要從 Azure AD 系統管理中心下載使用者清單，您必須使用在 Azure AD 中指派給一或多個組織層級系統管理員角色的使用者登入（使用者管理員是所需的最低角色）。 來賓邀請者和應用程式開發人員不會被視為系統管理員角色。

## <a name="to-download-a-list-of-users"></a>下載使用者清單

1. 使用組織中的使用者系統管理員帳戶登[入您的 Azure AD 組織](https://aad.portal.azure.com)。
2. 流覽至 Azure Active Directory > 使用者。 然後，在每個使用者旁邊的左側欄中勾選方塊，以選取您想要包含在下載中的使用者。 注意：目前沒有任何方法可選取要匯出的所有使用者。 每一個都必須個別選取。
3. 在 Azure AD 中，選取 [**使用者**] [  >  **下載使用者**]。
4. 在 [**下載使用者**] 頁面上，選取 [**啟動**] 以接收列出使用者設定檔內容的 CSV 檔案。 如果發生錯誤，您可以在 [大量作業結果] 頁面上，下載並檢視結果檔案。 此檔案包含每個錯誤的原因。

   ![選取您想要下載之使用者清單的位置](./media/users-bulk-download/bulk-download.png)

   下載檔案將包含篩選過的使用者清單。

   其中包含下列使用者屬性：

   - userPrincipalName
   - displayName
   - surname
   - mail
   - givenName
   - objectId
   - userType
   - jobTitle
   - department
   - accountEnabled
   - usageLocation
   - streetAddress
   - state
   - country
   - physicalDeliveryOfficeName
   - city
   - postalCode
   - telephoneNumber
   - mobile
   - authenticationPhoneNumber
   - authenticationAlternativePhoneNumber
   - authenticationEmail
   - alternateEmailAddress
   - ageGroup
   - consentProvidedForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>檢查狀態

您可以在 [**大量作業結果**] 頁面中查看暫止大量要求的狀態。

[![檢查 [大量作業結果] 頁面中的狀態。](media/users-bulk-download/bulk-center.png)](media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>大量下載服務限制

用來建立使用者清單的每個大量活動，最多可執行一小時。 這可讓您建立及下載至少500000個使用者的清單。

## <a name="next-steps"></a>後續步驟

- [[大量新增使用者](users-bulk-add.md)
- [大量刪除使用者](users-bulk-delete.md)
- [大量還原使用者](users-bulk-restore.md)
