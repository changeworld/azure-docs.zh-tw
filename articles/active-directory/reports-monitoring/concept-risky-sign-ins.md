---
title: 門戶中的危險登錄報告 |微軟文檔
description: 了解 Azure Active Directory 入口網站中有風險的登入報告
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: b77486064139895799ac5a48327377154f75da6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273829"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Azure Active Directory 入口網站中有風險的登入報告

Azure Active Directory (Azure AD) 會偵測使用者帳戶相關的可疑動作。 對於每個檢測到的操作，都會創建一個稱為**風險檢測**的記錄。 有關詳細資訊，請參閱[Azure AD 風險檢測](concept-risk-events.md)。 

您可以從 [Azure 入口網站](https://portal.azure.com)中，藉由選取 [Azure Active Directory]**** 刀鋒視窗，然後瀏覽至 [安全性]**** 區段，來存取安全性報告。 

根據風險檢測計算了兩種不同的安全報告：

- **有風險的登入** - 有風險的登入表示非使用者帳戶合法擁有者的某人嘗試登入。

- **標幟為有風險的使用者** - 有風險的使用者表示可能被盜用的使用者帳戶。 

![有風險的登入](./media/concept-risky-sign-ins/10.png)

要瞭解如何配置觸發這些風險檢測的策略，請參閱[如何配置使用者風險策略](../identity-protection/howto-user-risk-policy.md)。  

## <a name="who-can-access-the-risky-sign-ins-report"></a>誰可以存取風險登入報表？

以下角色的使用者可以檢視風險登入報表：

- 安全性系統管理員
- 全域管理員
- 安全性讀取者

若要了解如何將系統管理角色指派給 Azure Active Directory 中的使用者，請參閱[在 Azure Active Directory 中檢視和指派系統管理員角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal)。

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>您需要哪項 Azure AD 授權才能存取安全性報告？  

所有 Azure AD 版本都會為您提供有風險的登入報告。 不過，報告細微性層級因版本而異： 

- 在**Azure 活動目錄免費版本中**，您將獲得風險登錄的清單。 

- 此外 **，Azure 活動目錄高級 1**版允許您檢查已檢測到每個報表的某些基本風險檢測。 

- **Azure Active Directory 高級版**為您提供了有關所有潛在風險檢測的最詳細資訊，還使您能夠配置自動回應配置的風險級別的安全性原則。

## <a name="risky-sign-ins-report-for-azure-ad-free-edition"></a>Azure AD 免費版的有風險登錄報告

Azure AD 免費版為您提供為使用者檢測到的危險登錄的清單。 每個記錄都包含下列屬性：

- **使用者**- 登錄操作期間使用的使用者的名稱。
- **IP** - 用於連接到 Azure 活動目錄的設備的 IP 位址。
- **位置**- 用於連接到 Azure 活動目錄的位置。 這是根據追蹤、登錄資料、反向查詢及其他資訊，儘可能取得的近似值。
- **登入時間** - 執行登入的時間
- **狀態** - 登入狀態

![有風險的登入](./media/concept-risky-sign-ins/01.png)

在調查有風險的登入後，您可以採取下列動作，以向 Azure AD 提供意見反應：

- 解決
- 標記為誤判
- 略過
- 重新啟動

![有風險的登入](./media/concept-risky-sign-ins/21.png)

此報告也會提供選項以便：

- 搜尋資源
- 下載報告資料

![有風險的登入](./media/concept-risky-sign-ins/93.png)

## <a name="risky-sign-ins-report-for-azure-ad-premium-editions"></a>Azure AD 進階版本的有風險的登入報告

Azure AD 進階版本中有風險的登入報告可為您提供：

- 有關已檢測到[的風險檢測類型的](concept-risk-events.md)聚合資訊。 使用**Azure AD 高級 P1 版本**，許可證未涵蓋的檢測顯示為風險檢測**登錄，並檢測到其他風險**。 使用**Azure AD 高級 P2 版本**，您可以獲取有關所有基礎檢測的最詳細資訊。

- 下載報告的選項

![有風險的登入](./media/concept-risky-sign-ins/456.png)

選擇風險檢測時，您將獲得此風險檢測的詳細報表檢視，使您能夠：

- 選擇設定[使用者風險補救原則](../identity-protection/howto-user-risk-policy.md)  

- 查看風險檢測的檢測時間表  

- 查看檢測到此風險檢測的使用者清單

- 手動關閉風險檢測。 

![有風險的登入](./media/concept-risky-sign-ins/457.png)

> [!IMPORTANT]
> 有時，您可能會在[登錄報告中](concept-sign-ins.md)發現沒有相應登錄條目的風險檢測。 這是因為 Identity Protection 會同時評估**互動式**和**非互動式**登入的風險，而登入報告則只會顯示互動式登入的部分。

當您選取使用者時，即會取得這位使用者的詳細報告檢視，讓您能夠：

- 開啟 [所有登入] 檢視

- 重設使用者的密碼

- 關閉所有事件

- 調查使用者報告的風險檢測。 

![有風險的登入](./media/concept-risky-sign-ins/324.png)

要調查風險檢測，請從清單中選擇一個。  
這將打開此風險檢測**的詳細資訊**邊欄選項卡。 在 **"詳細資訊"** 邊欄選項卡上，您可以選擇手動關閉風險檢測或重新啟動手動關閉的風險檢測。 

![有風險的登入](./media/concept-risky-sign-ins/325.png)

## <a name="next-steps"></a>後續步驟

- [如何配置使用者風險策略](../identity-protection/howto-user-risk-policy.md)
- [如何設定風險補救原則](../identity-protection/howto-user-risk-policy.md)
- [風險檢測類型](concept-risk-events.md)
