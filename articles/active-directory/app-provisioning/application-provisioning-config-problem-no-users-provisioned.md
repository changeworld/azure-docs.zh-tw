---
title: 未在應用程式中布建使用者
description: 如何對當您沒有看到 Azure AD 資源庫應用程式中顯示任何使用者時所面臨的常見問題進行疑難排解，而該應用程式是您已設定來搭配 Azure AD 進行使用者佈建
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 12/03/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 89a5838524daa1959ecf6b4fe3c17d6175ca8553
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96571771"
---
# <a name="no-users-are-being-provisioned"></a>未佈建使用者 
>[!NOTE]
>從04/16/2020 開始，我們已變更指派預設存取角色之使用者的行為。 請參閱下一節以取得詳細資料。 
>
為應用程式設定自動佈建 (包括驗證供給 Azure AD 以連接至應用程式的應用程式認證提是有效的) 之後，使用者及/或群組就會佈建至應用程式。 佈建是由下列事項決定：

-   已將哪些使用者和群組 **指派** 給應用程式。 請注意，不支援布建嵌套群組。 如需指派的詳細資訊，請參閱[在 Azure Active Directory 中將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)。
-   是否已啟用 **屬性對應**，並設定為將來自 Azure AD 的有效屬性同步處理至應用程式。 如需詳細資訊，請參閱[在 Azure Active Directory 中自訂 SaaS 應用程式的使用者佈建屬性對應](customize-application-attributes.md)。
-   是否存在 **範圍設定篩選**，這會根據根據特定的屬性值來篩選使用者。 如需範圍設定篩選條件的詳細資訊，請參閱[含範圍篩選器的屬性型應用程式佈建](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。
  
  
如果您發現未布建使用者，請參閱 Azure AD 中的布建 [記錄 (預覽) ](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 。 搜尋特定使用者的記錄項目。

您可以在 [活動] 區段中選取 [ **Azure Active Directory** &gt; **企業應用程式** 布建 &gt; **記錄] (預覽)** **Activity** ，以存取 Azure 入口網站中的布建記錄。 您可以根據使用者的名稱或來源系統或目標系統中的識別碼來搜尋布建資料。 如需詳細資訊，請參閱 [ (預覽版布建記錄) ](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)。 

布建記錄會記錄布建服務所執行的所有作業，包括查詢在布建範圍內的已指派使用者 Azure AD、查詢目標應用程式是否存在這些使用者，以及比較系統之間的使用者物件。 接著，根據比較，在目標系統中新增、更新或停用使用者帳戶。

## <a name="general-problem-areas-with-provisioning-to-consider"></a>關於佈建要考慮的一般問題領域
如果您已知道要從何處著手，則以下是您可以向下切入的一般問題領域清單。

- [佈建服務似乎未啟動](#provisioning-service-does-not-appear-to-start)
- [布建記錄檔表示略過和未布建使用者，即使已指派它們](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>佈建服務似乎未啟動
如果您在 Azure 入口網站的 [Azure Active Directory]&gt;[企業應用程式][&gt; \[應用程式名稱]\] &gt;[佈建] 區段中，將 [佈建狀態] 設為 [啟用]。 不過，在後續重載之後，該頁面上就不會顯示其他狀態詳細資料，但可能是服務正在執行，但尚未完成初始週期。 查看上述的布建 **記錄 (預覽)** 上面所述，以判斷服務正在執行的作業，以及是否有任何錯誤。

>[!NOTE]
>視 Azure AD 目錄的大小和布建範圍中的使用者數目而定，初始週期可能需要20分鐘到數小時的時間。 初始迴圈之後的後續同步處理會更快，因為布建服務會儲存浮水印，代表兩個系統在初始迴圈之後的狀態。 初始週期會改善後續同步處理的效能。
>


## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>布建記錄會指出即使已指派使用者，也會略過並未布建使用者

當使用者在布建記錄中顯示為「已略過」時，請務必檢查記錄檔的 [ **步驟** ] 索引標籤，以判斷原因。 下面是常見原因和解決方式：

- **已設定範圍設定篩選，** **這會根據屬性值篩選出使用者**。 如需有關範圍設定篩選的詳細資訊，請參閱[範圍篩選器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。
- **使用者「不具有效權限」。** 如果您看到此特定錯誤訊息，這是因為儲存在 Azure AD 中的使用者指派記錄發生問題。 若要修正此問題，請從應用程式解除指派使用者 (或群組)，然後重新指派一次。 如需有關指派的詳細資訊，請參閱[指派使用者或群組存取](../manage-apps/assign-user-or-group-access-portal.md)。
- **必要屬性已遺失或未針對使用者填入。** 設定佈建時必須考量的重點是，檢視和設定屬性 (Attribute) 對應，以及定義哪些使用者 (或群組) 屬性 (Property) 會從 Azure AD 流向應用程式的工作流程。 此組態包括設定「比對屬性」，此屬性可用於唯一識別並比對兩個系統之間的使用者/群組。 如需這個重要程序的詳細資訊，請參閱[在 Azure Active Directory 中自訂 SaaS 應用程式的使用者佈建屬性對應](customize-application-attributes.md)。
- **群組的屬性對應：** 除了成員，還有群組名稱和群組詳細資訊的佈建 (如果某些應用程式有支援)。 您可以啟用或停用 [布 **建] 索引** 標籤中顯示之群組物件的 **對應**，以啟用或停用這項功能。如果已啟用布建群組，請務必檢查屬性對應，以確保「相符識別碼」使用適當的欄位。 此比對識別碼可以是顯示名稱或電子郵件別名。 如果 Azure AD 中某個群組的比對屬性空白或未填入，則不會佈建群組及其成員。
## <a name="provisioning-users-assigned-to-the-default-access-role"></a>布建指派給預設存取角色的使用者
資源庫中的應用程式預設角色稱為「預設存取」角色。 在過去，指派給此角色的使用者不會布建，而且會在布建 [記錄](../reports-monitoring/concept-provisioning-logs.md) 中標示為略過，因為「沒有有效的權利」。 

**在04/16/2020 之後建立的布建設定行為：** 指派給預設存取角色的使用者將會被評估為與所有其他角色相同。 指派預設存取權的使用者將不會被略過「無法有效存取」。 

**在04/16/2020 之前建立的布建設定行為：** 在接下來的3個月內，行為將會繼續進行，如同今天一樣。 具有預設存取角色的使用者會被略過，而不是有效的許可權。 2020年7月之後，所有應用程式的行為都是一致的。 我們不會略過以預設存取角色布建使用者，因為「不是有效的權利」。 這項變更將由 Microsoft 進行，不需要客戶動作。 如果您想要確保即使在這項變更之後仍會略過這些使用者，請套用適當的範圍篩選準則，或從應用程式取消指派使用者，以確保這些使用者超出範圍。  

如有這些變更的相關問題，請聯繫 provisioningfeedback@microsoft.com
## <a name="next-steps"></a>後續步驟

[Azure AD Connect 同步處理：了解宣告式佈建](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
