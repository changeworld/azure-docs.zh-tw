---
title: 在 Azure 活動目錄門戶中下載使用者清單（預覽 ） |微軟文檔
description: 在 Azure 活動目錄中的 Azure 管理中心批量下載使用者記錄。
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 02/06/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4716ff9547f64dc6551b4d4adb0a8578da9fa83e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063827"
---
# <a name="download-a-list-of-users-preview-in-azure-active-directory-portal"></a>在 Azure 活動目錄門戶中下載使用者清單（預覽）

Azure 活動目錄 （Azure AD） 支援批量使用者導入（創建）操作。

## <a name="required-permissions"></a>所需的權限

要從 Azure AD 管理中心下載使用者清單，必須與在 Azure AD 中分配給一個或多個組織級管理員角色的使用者登錄。 來賓邀請者和應用程式開發人員不被視為管理員角色。

## <a name="to-download-a-list-of-users"></a>下載使用者清單

1. 使用組織中的使用者管理員帳戶[登錄到 Azure AD 組織](https://aad.portal.azure.com)。
2. 導航到 Azure 活動目錄>使用者。 然後，通過勾選每個使用者旁邊的左列中的框，選擇要包含在下載中的使用者。 注意：此時，無法選擇要匯出的所有使用者。 必須單獨選擇每個選項。
3. 在 Azure AD 中，選擇 **"使用者** > **下載使用者**"。
4. 在 **"下載使用者"** 頁上，選擇 **"開始"** 以接收列出使用者設定檔屬性的 CSV 檔。 如果存在錯誤，則可以在"批量操作結果"頁上下載並查看結果檔。 該檔包含每個錯誤的原因。

   ![選擇要下載的使用者清單的位置](./media/users-bulk-download/bulk-download.png)

   下載檔案將包含篩選的使用者清單。

   包括以下使用者屬性：

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
   - 身份驗證電話號
   - 身份驗證替代電話號碼
   - 身份驗證電子郵件
   - 備用電子郵件地址
   - 年齡群組
   - 同意為次要提供
   - 法律年齡組分類

## <a name="check-status"></a>檢查狀態

您可以在**批量操作結果（預覽）** 頁中查看掛起的批量請求的狀態。

   ![在"批量操作結果"頁中檢查上載狀態](./media/users-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>批量下載服務限制

創建使用者清單的每個批量活動最多可運行一小時。 這支援創建和下載至少 500，000 個使用者的清單。

## <a name="next-steps"></a>後續步驟

- [[大量新增使用者](users-bulk-add.md)
- [大量刪除使用者](users-bulk-delete.md)
- [批量還原使用者](users-bulk-restore.md)
