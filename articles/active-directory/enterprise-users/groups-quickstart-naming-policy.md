---
title: 群組命名原則快速入門 - Azure Active Directory | Microsoft Docs
description: 說明如何在 Azure Active Directory 中新增使用者或刪除現有的使用者
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c31ff1fdc3eaff00d3fa4533d4642f980ddb1dcf
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95488771"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>快速入門：Azure Active Directory 中的群組命名原則

在本快速入門中，您將在 Azure Active Directory (Azure AD) 組織中為使用者建立的 Microsoft 365 群組設定命名原則，以利排序和搜尋組織的群組。 例如，您可以使用命名原則來：

* 傳達群組的功能、成員資格、地理區域或群組的建立者。
* 將通訊錄中的群組分類。
* 防止在群組名稱和別名中使用特定字組。

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="configure-the-group-naming-policy-in-the-azure-portal"></a>在 Azure 入口網站中設定群組命名原則

1. 使用使用者管理員帳戶登入 [Azure AD 系統管理中心](https://aad.portal.azure.com)。
1. 選取 [群組]，然後選取 [命名原則] 以開啟 [命名原則] 頁面。

    ![在管理中心開啟 [命名原則] 頁面](./media/groups-quickstart-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>檢視或編輯前置詞後置詞命名原則

1. 在 [命名原則] 頁面上，選取 [群組命名原則]。
1. 您可以選取您想要在命名原則時強制執行的屬性或字串，以個別檢視或編輯目前的前置詞或後置詞命名原則。
1. 若要從清單中移除前置詞或後置詞，請選取該前置詞或後置詞，然後選取 [刪除]。 您同時可以刪除多個項目。
1. 選取 [儲存] 可讓您對原則所做的變更生效。

### <a name="view-or-edit-the-custom-blocked-words"></a>檢視或編輯自訂封鎖字組

1. 在 [命名原則] 頁面上，選取 [封鎖的字組]。

    ![編輯和上傳命名原則的封鎖字組清單](./media/groups-quickstart-naming-policy/blockedwords.png)

1. 選取 [下載] 可檢視或編輯目前的自訂封鎖字組清單。
1. 選取 [檔案] 圖示可上傳新的自訂封鎖字組清單。
1. 選取 [儲存] 可讓您對原則所做的變更生效。

就這麼簡單。 您已設定命名原則並新增自訂封鎖字組。

## <a name="clean-up-resources"></a>清除資源

### <a name="remove-the-naming-policy-using-azure-portal"></a>使用 Azure 入口網站移除命名原則

1. 在 [命名原則] 頁面上，選取 [刪除原則]。
1. 確認刪除之後，命名原則就會遭到移除，包括所有前置詞後置詞命名原則以及任何自訂封鎖字組。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已經了解如何透過 Azure 入口網站設定 Azure AD 組織的命名原則。

如需詳細資訊，包括命名原則的 PowerShell Cmdlet、技術限制、新增自訂封鎖字組清單，以及 Microsoft 365 應用程式的使用者體驗，請繼續參考下一篇文章。
> [!div class="nextstepaction"]
> [命名原則 PowerShell](groups-naming-policy.md)