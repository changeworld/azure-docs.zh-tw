---
title: 包含檔案
description: 包含檔案
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 03/30/2020
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 7fd716be397d9ef6b9d6132cd4470f653f3cea0f
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655962"
---
## <a name="for-users-in-your-directory"></a>對目錄中的使用者

如果要允許目錄中的用戶能夠請求此訪問包,請按照以下步驟操作。 定義請求策略時,可以指定單個使用者,或者更常見的使用者組。 例如,您的組織可能已經具有一個組,如 **「所有員工**」。。  如果為可以請求訪問的使用者在策略中添加該組,則該組的任何成員都可以請求訪問。

1. 在 [可要求存取的使用者]**** 區段中，按一下 [您目錄中的使用者]****。

    選擇此選項時,將顯示新選項以進一步優化目錄中誰可以請求此訪問包。

    ![存取套件 -或您的資料夾的使用者](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. 選取下列其中一個選項：

    |  |  |
    | --- | --- |
    | **特定使用者與群組** | 如果僅希望目錄中的使用者和組能夠請求此訪問包,請選擇此選項。 |
    | **所有成員(不包括客人)** | 如果希望目錄中的所有成員用戶能夠請求此訪問包,請選擇此選項。 此選項不包括您可能邀請到目錄中的任何來賓使用者。 |
    | **所有使用者(包括客人)** | 如果希望目錄中的所有成員使用者和來賓用戶能夠請求此訪問包,請選擇此選項。 |

    來賓使用者是指已使用[Azure AD B2B](../articles/active-directory/b2b/what-is-b2b.md)邀請到目錄中的外部使用者。 有關成員使用者和來賓使用者之間的差異的詳細資訊,請參閱[Azure 活動目錄中的默認使用者許可權是什麼?](../articles/active-directory/fundamentals/users-default-permissions.md)

1. 如果選擇 **「特定使用者和組**」,請按下 **「添加使用者和群組**」 。。

1. 在「選擇使用者和組」窗格中,選擇要添加的使用者和組。

    ![存取套件 -請使用使用者與群組](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. 按下「**選擇」** 以添加使用者和組。

1. 向下跳到["審批"](#approval)部分。

## <a name="for-users-not-in-your-directory"></a>對於不在目錄中的使用者

 **不在目錄中的使用者**是指位於其他 Azure AD 目錄或域中的使用者。 這些使用者可能尚未被邀請到您的目錄中。 Azure AD 目錄必須配置為允許**協作限制**中的邀請。 有關詳細資訊,請參閱啟用[B2B 外部協作並管理誰可以邀請來賓](../articles/active-directory/b2b/delegate-invitations.md)。

> [!NOTE]
> 將為尚未在目錄中的請求獲得批准或自動批准的用戶創建來賓使用者帳戶。 客人將被邀請,但不會收到邀請電子郵件。 相反,當他們的訪問包分配被傳遞時,他們將收到一封電子郵件。 默認情況下,如果該來賓使用者由於上次分配已過期或已取消,因此以後該來賓使用者不再具有任何訪問包分配,該來賓使用者帳戶將被阻止登錄,然後被刪除。 如果希望來賓使用者無限期地保留在目錄中,即使他們沒有訪問包分配,也可以更改授權管理配置的設置。 有關來賓使用者物件的詳細資訊,請參閱 Azure[活動目錄 B2B 協作使用者的屬性](../articles/active-directory/b2b/user-properties.md)。

如果要允許目錄中未的使用者請求此存取套件,請按照以下步驟操作:

1. 在 **「可以請求存取的使用者」** 部分中,按下 **「對於不在目錄中的使用者**」 。

    選擇此選項時,將顯示新選項。

    ![存取套件 ─請求─對於不在目錄中的使用者](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. 選取下列其中一個選項：

    |  |  |
    | --- | --- |
    | **特定互聯組織** | 如果要從管理員以前添加的組織列表中選擇,請選擇此選項。 所選組織的所有使用者都可以請求此訪問包。 |
    | **所有互聯組織** | 如果所有連接組織的所有使用者都可以請求此訪問包,請選擇此選項。 |
    | **所有使用者 (所有連線的組織 + 任何新的外部使用者)** | 如果所有已連接組織的所有使用者都可以請求此訪問包,並且 B2B 允許或拒絕清單設置應優先於任何新的外部使用者,請選擇此選項。 |

    已連接的組織是您與其有關係的外部 Azure AD 目錄或域。

1. 如果選擇 **「特定已連接的組織**」,請按下「**添加目錄**」以從管理員以前添加的連接組織清單中選擇。

1. 鍵入名稱或功能變數名稱以搜索以前連接的組織。

    ![存取套件 -請存取目錄](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    如果要協作的組織不在清單中,可以要求管理員將其添加為已連接的組織。 有關詳細資訊,請參閱[新增已連接的組織](../articles/active-directory/governance/entitlement-management-organization.md)。

1. 選擇所有已連接的組織后,按一下「**選擇**」。。

    > [!NOTE]
    > 所選連接組織的所有用戶將能夠請求此訪問包。 這包括 Azure AD 中來自與組織關聯的所有子域的使用者,除非 Azure B2B 允許或拒絕清單阻止這些域。 如需詳細資訊，請參閱[允許或封鎖對特定組織的 B2B 使用者的邀請](../articles/active-directory/b2b/allow-deny-list.md)。

1. 向下跳到["審批"](#approval)部分。

## <a name="none-administrator-direct-assignments-only"></a>無(僅限管理員直接分配)

如果要繞過訪問請求並允許管理員直接將特定使用者分配給此訪問包,請按照以下步驟操作。 使用者不必請求訪問包。 您仍然可以設置生命週期設置,但沒有請求設置。

1. 在 **「可以請求存取的使用者**」部分中,按下 **「 無」(管理員直接分配僅**。

    ![存取套件 -請求 - 無管理員直接分配](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    創建訪問包后,可以直接將特定的內部和外部使用者分配給訪問包。 如果指定外部使用者,將在目錄中創建來賓使用者帳戶。 有關直接配置使用者的資訊,請參閱[檢視、新增和刪除存取套件的分配](../articles/active-directory/governance/entitlement-management-access-package-assignments.md)。

1. 向下跳到[啟用請求](#enable-requests)部分。

## <a name="approval"></a>核准

在「審批」部分中,您可以指定使用者請求此訪問包時是否需要批准。 核准設定的工作方式如下:

- 只有一個選定的審批人或回退審批者需要批准單階段審批請求。 
- 每個階段的選定審批者中只有一個需要批准兩階段審批請求。
- 審批人可以是經理、內部贊助者或外部贊助者,具體取決於政策管理訪問的人員。
- 單階段或兩階段審批不需要每個選定的審批人的批准。
- 批准決定基於先審核請求的審批人。

有關如何向請求策略添加審批人演示,請觀看以下視頻:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

有關如何向請求策略添加多階段審批的演示,請觀看以下視頻:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]

依以下步驟指定存取套件請求的審批設定:

1. 要要求從所選使用者請求批准,請將 **「需要審批**」切換為 **」是**"。 或者,要自動批准請求,請將切換設置為 **"否**"。

1. 要要求使用者提供請求訪問包的理由,請將 **「要求請求者理由**」切換為 **「是**」。。
    
1. 現在確定請求是否需要單階段或 2 階段批准。 將「**單個階段審批的多少個階段**切換為**1」,** 或將切換設置為**2**進行 2 階段審批。

    ![存取套件 -請求 - 批准設定](./media/active-directory-entitlement-management-request-policy/approval.png)

在選擇需要多少階段后,使用以下步驟添加審批者: 

### <a name="single-stage-approval"></a>單階段審批

1. 新增**第一個項目**:
    
    如果策略設定為控制目錄中使用者的存取權限,則可以選擇**Manager 作為核准者**。 或者,在選擇下拉菜單中選擇特定審批人後,按下 **「添加審批者**」來添加特定使用者。
    
    ![存取套件 -請求 - 對目錄中的使用者 - 第一個項目](./media/active-directory-entitlement-management-request-policy/approval-single-stage-first-approver-manager.png)

    如果此政策設定為管理不在目錄中的使用者的存取,則可以選擇**外部贊助商**或**內部贊助商**。 或者,通過單擊"選擇特定審批**人"下的"添加審批者**"或"組"來添加特定使用者。
    
    ![存取套件 -請求 - 對目錄外的使用者 - 第一個項目](./media/active-directory-entitlement-management-request-policy/out-directory-first-approver.png)
    
1. 如果選擇**Manager**作為第一個審批者,請按下「**添加回退**」以選擇目錄中的一個或多個使用者或組作為回退審批人。 如果授權管理找不到請求訪問的使用者的管理員,則回退審批者將收到請求。

    透過 Manager 屬性的授權管理找到**管理員**。 該屬性位於 Azure AD 中的使用者的配置檔中。 關於詳細資訊,請參閱使用[Azure 的目錄加入或更新使用者的設定檔資訊](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md)。

1. 如果選擇 **「選擇特定審批者**」,請按下「**添加審批者**」以選擇目錄中的一個或多個使用者或組作為審批者。

1. 在 **「決定」下的框中,必須在多少天內做出?,** 指定審批人必須審核此訪問包請求的天數。

    如果請求在此時間段內未獲得批准,則將自動拒絕。 用戶必須提交訪問包的另一個請求。

1. 要要求審批人為其決策提供理由,請將"要求批准人的理由"設置為 **"是**"。

    其他審批人和請求者可以看到理由。

### <a name="2-stage-approval"></a>2 階段批准

如果選擇了 2 階段批准,則需要添加第二個審批人。

1. 新增第**二個項目 :** 
    
    如果用戶位於目錄中,請單擊"選擇特定審批人"下的 **'添加審批者**",將特定使用者添加為第二個審批者。

    ![存取套件 -請求 - 對目錄中的使用者 - 第二個項目](./media/active-directory-entitlement-management-request-policy/in-directory-second-approver.png)

    如果使用者不在目錄中,請選擇 **「內部發起人**」或 **「外部發起人**」作為第二個審批者。 選擇審批人後,添加回退審批人。

    ![存取套件 -關於目錄外的使用者 - 第二個項目](./media/active-directory-entitlement-management-request-policy/out-directory-second-approver.png) 

1. 指定第二個審批人必須在"決定"框中批准請求的天數,**必須在多少天內做出。。** 

1. 將「需要審批人理由」切換為 **「或****」或 「否**」。

### <a name="alternate-approvers"></a>候補審批人

您可以指定備用審批人,類似於指定可以批准請求的第一個和第二個審批人。 擁有備用審批人有助於確保請求在過期(超時)之前獲得批准或拒絕。 您可以列出候補審批人、第一個審批人和第二個審批人,以便進行兩階段審批。 

通過指定備用審批人,如果第一個或第二個審批人無法批准或拒絕請求,則根據您在策略設置期間指定的轉發計劃,將掛起的請求轉發給備用審批人。 他們收到一封電子郵件以批准或拒絕掛起的請求。

將請求轉發給備用審批人後,第一個或第二個審批人仍然可以批准或拒絕請求。 備用審批人使用相同的「我的訪問」網站來批准或拒絕掛起的請求。

我們可以列出作為審批人和候補審批人的人或人組。 請確保列出不同的人員集,作為第一、第二和備用審批者。
例如,如果您將 Alice 和 Bob 列為第一審批人,則將卡羅爾和 Dave 列為候補審批人。 使用以下步驟將備用審批人新增到存取套件:

1. 在第一個審批者、第二個審批者或兩者下,單擊「**顯示高級請求設置**」。

    ![存取套件 - 策略 - 顯示進階設定設定](./media/active-directory-entitlement-management-request-policy/alternate-approvers-click-advanced-request.png)

1. 設置**如果沒有執行任何操作,則轉發給備用審批人?** 切換為 **"是**"。

1. 按下 **「添加備用審批人**」並從清單中選擇備用審批人。

    ![存取套件 - 政策 - 新增備用核准人](./media/active-directory-entitlement-management-request-policy/alternate-approvers-add.png)

1. 在 **"轉發到備用審批人"中,在多少天之後**框中,放入審批人必須批准或拒絕請求的天數。 如果在請求持續時間之前沒有審批或拒絕請求,則請求過期(超時),並且使用者必須提交訪問包的另一個請求。 

    請求只能在請求期限達到半衰期后一天轉發給備用審批人。 在此示例中,請求的持續時間為 14 天。 因此,請求持續時間在第 7 天達到半衰期。 因此,請求不能早於第 8 天轉發。 此外,請求無法在請求持續時間的最後一天轉發。 因此,在此示例中,可以轉發請求的最新請求是第 13 天。

## <a name="enable-requests"></a>開啟要求

1. 如果希望訪問包立即可供請求策略中的使用者使用,請將"啟用"切換移動到 **"是**"。

    在建立完存取包後,您以後始終可以啟用它。

    如果選擇了 **「無」(僅限管理員直接分配),** 並且將啟用設置為 **「否**」,則管理員無法直接分配此訪問包。

    ![存取套件 - 政策 - 開啟政策設定](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. 按 [下一步]  。
