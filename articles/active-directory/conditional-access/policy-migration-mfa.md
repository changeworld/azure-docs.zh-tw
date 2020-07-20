---
title: 遷移條件式存取原則 - Azure Active Directory
description: 本文示範在 Azure 入口網站中移轉要求多重要素驗證的傳統原則。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1dc7b7227dbaee488726a75fe7120bd26614dbf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "83993723"
---
# <a name="migrate-a-classic-policy-in-the-azure-portal"></a>在 Azure 入口網站中遷移傳統原則

本文示範如何移轉要求雲端應用程式進行**多重要素驗證**的傳統原則。 雖然這不是必要條件，但我們建議您先閱讀[在 Azure 入口網站中移轉傳統原則](policy-migration.md)，再開始移轉傳統原則。

![針對 Salesforce 應用程式需要 MFA 的傳統原則詳細資料](./media/policy-migration/33.png)

移轉程序包含下列步驟：

1. [開啟傳統原則](#open-a-classic-policy)以取得組態設定。
1. 建立新的 Azure AD 條件式存取原則，以取代您的傳統原則。 
1. 停用傳統原則。

## <a name="open-a-classic-policy"></a>開啟傳統原則

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，流覽至 [ **Azure Active Directory**  >  **安全性**] [  >  **條件式存取**]。
1. 選取 [**傳統原則**]。

   ![傳統原則視圖](./media/policy-migration-mfa/12.png)

1. 在傳統原則清單中，選取您想要遷移的原則。 記錄設定，讓您可以使用新的條件式存取原則來重新建立。

## <a name="create-a-new-conditional-access-policy"></a>建立新的條件式存取原則

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，流覽至 [ **Azure Active Directory**  >  **安全性**] [  >  **條件式存取**]。
1. 若要建立新的條件式存取原則，請選取 [**新增原則**]。
1. 在 [新增]**** 頁面的 [名稱]**** 文字方塊中，鍵入您的原則名稱。
1. 在 [指派]**** 區段中，按一下 [使用者和群組]****。
   1. 如已選取傳統原則中的所有使用者，請按一下 [所有使用者]****。 
   1. 如已選取傳統原則中的群組，請按一下 [選取使用者和群組]****，然後選取所需的使用者和群組。
   1. 如已排除群組，請按一下 [排除]**** 索引標籤，然後選取所需的使用者和群組。 
   1. 選取 [完成] 
1. 在 [**指派**] 區段中，按一下 [**雲端應用程式] 或 [動作**]。
1. 在 [**雲端應用程式] 或 [動作**] 頁面上，執行下列步驟：
   1. 按一下 [選取應用程式]****。
   1. 按一下 [選取]。
   1. 在 [選取]**** 頁面上，選取您的雲端應用程式，然後按一下 [選取]****。
   1. 在 [**雲端應用程式**] 頁面上，按一下 [**完成**]。
1. 如已選取 [需要多重要素驗證]****：
   1. 在 [存取控制]**** 區段中，按一下 [授與]****。
   1. 在 [授與]**** 頁面上，按一下 [授與存取權]****，然後按一下 [需要多重要素驗證]****。
   1. 按一下 [選取]。
1. 按一下 [**開啟**] 以啟用您的原則，然後選取 [**儲存**]。

   ![條件式存取原則建立](./media/policy-migration-mfa/conditional-access-policy-migration.png)

## <a name="disable-the-classic-policy"></a>停用傳統原則

若要停用傳統原則，請在 [詳細資料]**** 檢視中按一下 [停用]****。

![停用傳統原則](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>後續步驟

- 如需傳統原則移轉的詳細資訊，請參閱[在 Azure 入口網站中移轉傳統原則](policy-migration.md)。
- [使用僅限報告模式來進行條件式存取，以判斷新原則決策的影響。](concept-conditional-access-report-only.md)