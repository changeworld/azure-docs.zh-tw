---
title: 遷移條件式存取原則 - Azure Active Directory
description: 本文示範在 Azure 入口網站中移轉要求多重要素驗證的傳統原則。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: d637ac464b689a25ce5d5a79cf47da0c85d38d0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74846011"
---
# <a name="migrate-a-classic-policy-in-the-azure-portal"></a>在 Azure 門戶中遷移經典策略

本文示範如何移轉要求雲端應用程式進行**多重要素驗證**的傳統原則。 雖然這不是必要條件，但我們建議您先閱讀[在 Azure 入口網站中移轉傳統原則](policy-migration.md)，再開始移轉傳統原則。

![需要 MfA 用於 Salesforce 應用的經典策略詳細資訊](./media/policy-migration/33.png)

移轉程序包含下列步驟：

1. [開啟傳統原則](#open-a-classic-policy)以取得組態設定。
1. 建立新的 Azure AD 條件式存取原則，以取代您的傳統原則。 
1. 停用傳統原則。

## <a name="open-a-classic-policy"></a>開啟傳統原則

1. 在[Azure 門戶](https://portal.azure.com)中，導航到**Azure 活動目錄** > **安全** > **條件訪問**。
1. 選擇，**經典策略**。

   ![經典策略視圖](./media/policy-migration-mfa/12.png)

1. 在經典策略清單中，選擇要遷移的策略。 記錄配置設置，以便可以使用新的條件訪問策略重新創建。

## <a name="create-a-new-conditional-access-policy"></a>建立新的條件式存取原則

1. 在[Azure 門戶](https://portal.azure.com)中，導航到**Azure 活動目錄** > **安全** > **條件訪問**。
1. 要創建新的條件訪問策略，請選擇 **"新策略**"。
1. 在 [新增]**** 頁面的 [名稱]**** 文字方塊中，鍵入您的原則名稱。
1. 在 [指派]**** 區段中，按一下 [使用者和群組]****。
   1. 如已選取傳統原則中的所有使用者，請按一下 [所有使用者]****。 
   1. 如已選取傳統原則中的群組，請按一下 [選取使用者和群組]****，然後選取所需的使用者和群組。
   1. 如已排除群組，請按一下 [排除]**** 索引標籤，然後選取所需的使用者和群組。 
   1. 選取 [完成] ****
1. 在 **"分配"** 部分中，按一下 **"雲應用"或"操作**"。
1. 在 **"雲應用"或"操作"** 頁上，執行以下步驟：
   1. 按一下 [選取應用程式]****。
   1. 按一下 **"選擇**"。
   1. 在 [選取]**** 頁面上，選取您的雲端應用程式，然後按一下 [選取]****。
   1. 在 **"雲應用"** 頁上，按一下"**完成**"。
1. 如已選取 [需要多重要素驗證]****：
   1. 在 [存取控制]**** 區段中，按一下 [授與]****。
   1. 在 [授與]**** 頁面上，按一下 [授與存取權]****，然後按一下 [需要多重要素驗證]****。
   1. 按一下 **"選擇**"。
1. 按一下 **"打開**"以啟用策略，然後選擇"**保存**"。

   ![條件訪問策略創建](./media/policy-migration-mfa/conditional-access-policy-migration.png)

## <a name="disable-the-classic-policy"></a>停用傳統原則

若要停用傳統原則，請在 [詳細資料]**** 檢視中按一下 [停用]****。

![禁用經典策略](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>後續步驟

- 如需傳統原則移轉的詳細資訊，請參閱[在 Azure 入口網站中移轉傳統原則](policy-migration.md)。
- [對條件訪問使用僅報告模式來確定新策略決策的影響。](concept-conditional-access-report-only.md)