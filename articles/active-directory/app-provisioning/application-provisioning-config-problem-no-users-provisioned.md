---
title: 未將任何使用者佈建至 Azure AD 資源庫應用程式
description: 如何對當您沒有看到 Azure AD 資源庫應用程式中顯示任何使用者時所面臨的常見問題進行疑難排解，而該應用程式是您已設定來搭配 Azure AD 進行使用者佈建
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b60261d63e1bcb75aea9d2e8a6b74902520f391
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522912"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>未將任何使用者佈建至 Azure AD 資源庫應用程式
為應用程式設定自動佈建 (包括驗證供給 Azure AD 以連接至應用程式的應用程式認證提是有效的) 之後，使用者及/或群組就會佈建至應用程式。 佈建是由下列事項決定：

-   已將哪些使用者和群組**指派**給應用程式。 請注意，不支援預配嵌套組或 Office 365 組。 如需指派的詳細資訊，請參閱[在 Azure Active Directory 中將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)。
-   是否已啟用**屬性對應**，並設定為將來自 Azure AD 的有效屬性同步處理至應用程式。 如需詳細資訊，請參閱[在 Azure Active Directory 中自訂 SaaS 應用程式的使用者佈建屬性對應](customize-application-attributes.md)。
-   是否存在**範圍設定篩選**，這會根據根據特定的屬性值來篩選使用者。 如需範圍設定篩選條件的詳細資訊，請參閱[含範圍篩選器的屬性型應用程式佈建](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。
  
如果觀察到使用者未預配，請參閱 Azure AD 中的[預配日誌（預覽）。](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 搜尋特定使用者的記錄項目。

通過在 **"活動"** 部分中選擇**Azure 活動目錄**&gt;**企業應用**&gt;**預配日誌（預覽），** 可以在 Azure 門戶中訪問預配日誌。 您可以根據使用者名稱或源系統或目標系統中的識別碼搜索預配資料。 有關詳細資訊，請參閱[預配日誌（預覽）。](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 

預配日誌記錄預配服務執行的所有操作，包括為預配範圍內的分配使用者查詢 Azure AD、查詢目標應用以瞭解這些使用者的存在、比較使用者物件。系統。 接著，根據比較，在目標系統中新增、更新或停用使用者帳戶。

## <a name="general-problem-areas-with-provisioning-to-consider"></a>關於佈建要考慮的一般問題領域
如果您已知道要從何處著手，則以下是您可以向下切入的一般問題領域清單。

- [佈建服務似乎未啟動](#provisioning-service-does-not-appear-to-start)
- [預配日誌表示，使用者被跳過，並且未預配，即使已分配使用者](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>佈建服務似乎未啟動
如果您在 Azure 入口網站的 [Azure Active Directory]&gt;[企業應用程式][&gt; \[應用程式名稱]\] &gt;[佈建]**** 區段中，將 [佈建狀態]**** 設為 [啟用]****。 但是，在後續重新載入後，該頁上不會顯示其他狀態詳細資訊，則服務可能正在運行，但尚未完成初始週期。 檢查上面描述的**預配日誌（預覽），** 以確定服務正在執行哪些操作，以及是否有任何錯誤。

>[!NOTE]
>初始週期可能需要 20 分鐘到幾個小時，具體取決於 Azure AD 目錄的大小和預配範圍內的使用者數。 初始週期之後的後續同步速度更快，因為預配服務存儲的浮水印，表示初始週期後兩個系統的狀態。 初始週期可提高後續同步的性能。
>


## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>預配日誌表示，即使使用者已分配，也不會預配使用者

當使用者在預配日誌中顯示為"跳過"時，請務必查看日誌的 **"步驟**"選項卡以確定原因。 下面是常見原因和解決方式：

- **已設定範圍設定篩選，** **這會根據屬性值篩選出使用者**。 如需有關範圍設定篩選的詳細資訊，請參閱[範圍篩選器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。
- **使用者「不具有效權限」。** 如果您看到此特定錯誤訊息，這是因為儲存在 Azure AD 中的使用者指派記錄發生問題。 若要修正此問題，請從應用程式解除指派使用者 (或群組)，然後重新指派一次。 如需有關指派的詳細資訊，請參閱[指派使用者或群組存取](../manage-apps/assign-user-or-group-access-portal.md)。
- **必要屬性已遺失或未針對使用者填入。** 設定佈建時必須考量的重點是，檢視和設定屬性 (Attribute) 對應，以及定義哪些使用者 (或群組) 屬性 (Property) 會從 Azure AD 流向應用程式的工作流程。 此組態包括設定「比對屬性」，此屬性可用於唯一識別並比對兩個系統之間的使用者/群組。 如需這個重要程序的詳細資訊，請參閱[在 Azure Active Directory 中自訂 SaaS 應用程式的使用者佈建屬性對應](customize-application-attributes.md)。
- **群組的屬性對應：** 除了成員，還有群組名稱和群組詳細資訊的佈建 (如果某些應用程式有支援)。 您可以通過啟用或禁用**預配**選項卡中顯示的組物件的**映射**來啟用或禁用此功能。如果啟用了預配組，請確保查看屬性對應，以確保為"匹配 ID"使用適當的欄位。 此比對識別碼可以是顯示名稱或電子郵件別名。 如果 Azure AD 中某個群組的比對屬性空白或未填入，則不會佈建群組及其成員。

## <a name="next-steps"></a>後續步驟

[Azure AD Connect 同步處理：了解宣告式佈建](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
