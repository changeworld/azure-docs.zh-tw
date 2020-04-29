---
title: 使用者未布建在我的應用程式中
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
ms.date: 04/20/2020
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: ac6d4f24d3b6c21828ccb11785005df736c6b070
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680342"
---
# <a name="no-users-are-being-provisioned"></a>未佈建使用者 
>[!NOTE]
>從04/16/2020 開始，我們已針對指派預設存取角色的使用者變更其行為。 如需詳細資訊，請參閱下面的一節。 
>
為應用程式設定自動佈建 (包括驗證供給 Azure AD 以連接至應用程式的應用程式認證提是有效的) 之後，使用者及/或群組就會佈建至應用程式。 佈建是由下列事項決定：

-   已將哪些使用者和群組**指派**給應用程式。 請注意，不支援布建嵌套群組或 Office 365 群組。 如需指派的詳細資訊，請參閱[在 Azure Active Directory 中將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)。
-   是否已啟用**屬性對應**，並設定為將來自 Azure AD 的有效屬性同步處理至應用程式。 如需詳細資訊，請參閱[在 Azure Active Directory 中自訂 SaaS 應用程式的使用者佈建屬性對應](customize-application-attributes.md)。
-   是否存在**範圍設定篩選**，這會根據根據特定的屬性值來篩選使用者。 如需範圍設定篩選條件的詳細資訊，請參閱[含範圍篩選器的屬性型應用程式佈建](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。
  
  
如果您發現使用者未布建，請參閱 Azure AD 中的布建[記錄（預覽）](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 。 搜尋特定使用者的記錄項目。

您可以在 [**活動**] 區段中選取 [ **Azure Active Directory** &gt; **企業應用程式** &gt;布建**記錄（預覽）** ]，以存取 Azure 入口網站中的布建記錄。 您可以根據使用者名稱或來源系統或目標系統中的識別碼來搜尋布建資料。 如需詳細資訊，請參閱布建[記錄（預覽）](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)。 

布建記錄會記錄布建服務所執行的所有作業，包括針對位於布建範圍內的已指派使用者查詢 Azure AD，並查詢目標應用程式是否存在那些使用者，並比較系統之間的使用者物件。 接著，根據比較，在目標系統中新增、更新或停用使用者帳戶。

## <a name="general-problem-areas-with-provisioning-to-consider"></a>關於佈建要考慮的一般問題領域
如果您已知道要從何處著手，則以下是您可以向下切入的一般問題領域清單。

- [佈建服務似乎未啟動](#provisioning-service-does-not-appear-to-start)
- [布建記錄表示會略過並不布建使用者，即使已指派它們](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>佈建服務似乎未啟動
如果您在 Azure 入口網站的 [Azure Active Directory]&gt;[企業應用程式][&gt; \[應用程式名稱]\] &gt;[佈建]**** 區段中，將 [佈建狀態]**** 設為 [啟用]****。 不過，在後續重載之後，該頁面上不會顯示其他狀態詳細資料，可能是服務正在執行，但尚未完成初始迴圈。 檢查上面所述的布建**記錄（預覽）** ，以判斷服務正在執行哪些作業，以及是否有任何錯誤。

>[!NOTE]
>視 Azure AD 目錄大小和布建範圍中的使用者數目而定，初始週期可能需要20分鐘到數小時的時間。 在初始週期後的後續同步處理會更快，因為布建服務會在初始迴圈後儲存表示兩個系統狀態的浮水印。 初始週期可改善後續同步處理的效能。
>


## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>布建記錄表示會略過使用者，但即使已指派

當使用者在布建記錄檔中顯示為「已略過」時，請務必查看記錄檔的 [**步驟**] 索引標籤，以判斷原因。 下面是常見原因和解決方式：

- **已設定範圍設定篩選，** **這會根據屬性值篩選出使用者**。 如需有關範圍設定篩選的詳細資訊，請參閱[範圍篩選器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。
- **使用者「不具有效權限」。** 如果您看到此特定錯誤訊息，這是因為儲存在 Azure AD 中的使用者指派記錄發生問題。 若要修正此問題，請從應用程式解除指派使用者 (或群組)，然後重新指派一次。 如需有關指派的詳細資訊，請參閱[指派使用者或群組存取](../manage-apps/assign-user-or-group-access-portal.md)。
- **必要屬性已遺失或未針對使用者填入。** 設定佈建時必須考量的重點是，檢視和設定屬性 (Attribute) 對應，以及定義哪些使用者 (或群組) 屬性 (Property) 會從 Azure AD 流向應用程式的工作流程。 此組態包括設定「比對屬性」，此屬性可用於唯一識別並比對兩個系統之間的使用者/群組。 如需這個重要程序的詳細資訊，請參閱[在 Azure Active Directory 中自訂 SaaS 應用程式的使用者佈建屬性對應](customize-application-attributes.md)。
- **群組的屬性對應：** 除了成員，還有群組名稱和群組詳細資訊的佈建 (如果某些應用程式有支援)。 您可以啟用或停用 [布**建] 索引**標籤中所顯示之群組物件的**對應**，來啟用或停用這項功能。如果已啟用布建群組，請務必檢查屬性對應，以確保有適當的欄位用於「相符識別碼」。 此比對識別碼可以是顯示名稱或電子郵件別名。 如果 Azure AD 中某個群組的比對屬性空白或未填入，則不會佈建群組及其成員。
## <a name="provisioning-users-assigned-to-the-default-access-role"></a>布建指派給預設存取角色的使用者
來自資源庫之應用程式的預設角色稱為「預設存取」角色。 在過去，指派給此角色的使用者不會布建，而且會在布建[記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)中標示為已略過，因為「沒有有效的許可權」。 

**04/16/2020 之後所建立之布建設定的行為：** 指派給預設存取角色的使用者將會評估為與其他所有角色相同。 指派預設存取權的使用者將不會被略過為「無法有效地」。 

**在04/16/2020 之前建立之布建設定的行為：** 在接下來的3個月內，行為將會如同今天一樣繼續進行。 具有預設存取角色的使用者將會被略過，而不是有效的。 2020年7月之後，所有應用程式的行為都是統一的。 我們不會略過布建具有預設存取角色的使用者，因為「不是有效的」。 這項變更將由 Microsoft 進行，不需要客戶動作。 如果您想要確保這些使用者會繼續略過，即使在這項變更之後，請套用適當的範圍篩選器，或從應用程式取消指派使用者，以確保它們不在範圍內。  

如有這些變更的問題，請聯繫provisioningfeedback@microsoft.com
## <a name="next-steps"></a>後續步驟

[Azure AD Connect 同步處理：了解宣告式佈建](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
