---
title: Azure AD Connect 雲端同步意外刪除
description: 本主題說明如何使用「意外刪除」功能來防止刪除。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/25/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0da54bd28c1d9ea933e88b6c86cf6092c10d036a
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785166"
---
# <a name="accidental-delete-prevention"></a>意外刪除預防措施

下列檔說明 Azure AD Connect cloud sync 的意外刪除功能。 意外刪除功能的設計目的是要防止您意外設定變更，以及對內部部署目錄的變更，而這些變更會影響許多使用者和群組。  此功能可讓您：

- 設定防止意外刪除的功能。 
- 設定 (閾值) 超過設定將會生效的物件數目 
- 設定通知電子郵件地址，讓他們可以在此案例中有問題的同步處理工作被放入隔離時收到電子郵件通知 

若要使用此功能，您可以設定如果已刪除，應停止同步處理的物件數目閾值。  因此，如果達到這個數位，同步處理將會停止，而且會傳送通知給指定的電子郵件。  此通知可讓您調查發生的狀況。


## <a name="configure-accidental-delete-prevention"></a>設定意外刪除防止
若要使用新功能，請遵循下列步驟。


1.  在 Azure 入口網站中，選取 [Azure Active Directory]  。
2.  選取 [Azure AD Connect]。
3.  選取 [ **管理雲端同步**]。
4. 在 [設定] **底下，選取您的設定**。
5. 在 [ **設定** ] 下，填入下列內容：
    - **通知電子郵件** -用於通知的電子郵件
    - **防止意外刪除** -勾選此方塊可啟用此功能
    - **意外刪除閾值** -輸入要停止同步處理的物件數目，並傳送通知

![意外刪除](media/how-to-accidental-deletes/accident-1.png)

## <a name="recovering-from-an-accidental-delete-instance"></a>從意外刪除的實例復原
如果您遇到意外刪除的情況，您會在布建代理程式設定的狀態看到此情況。  它會顯示 **超過刪除閾值**。
 
![意外刪除狀態](media/how-to-accidental-deletes/delete-1.png)

按一下 [ **超過刪除閾值**]，您就會看到同步處理狀態資訊。  這會提供其他詳細資料。 
 
 ![同步狀態](media/how-to-accidental-deletes/delete-2.png)

以滑鼠右鍵按一下省略號，即可取得下列選項：
 - 查看布建記錄
 - 檢視代理程式
 - 允許刪除

 ![按滑鼠右鍵](media/how-to-accidental-deletes/delete-3.png)

您可以使用 [View 布建 **記錄**] 來查看 **StagedDelete** 專案，並檢查已刪除的使用者所提供的資訊。
 
 ![佈建記錄](media/how-to-accidental-deletes/delete-7.png)

### <a name="allowing-deletes"></a>允許刪除

[ **允許刪除** ] 動作將會刪除觸發意外刪除臨界值的物件。  使用下列程式接受刪除。  

1. 以滑鼠右鍵按一下省略號，然後選取 [ **允許刪除**]。
2. 確認時按一下 [ **是]** 以允許刪除。
 
 ![確認時為 [是]](media/how-to-accidental-deletes/delete-4.png)

3. 您會看到已接受刪除的確認，而且狀態將會在下一個週期回復為狀況良好。 
 
 ![接受刪除](media/how-to-accidental-deletes/delete-8.png)

### <a name="rejecting-deletions"></a>拒絕刪除

如果您不想要允許刪除，您需要執行下列動作：
- 調查刪除的來源
- 修正問題 (範例中，不小心將 OU 移出範圍，而您現在已將其重新新增回範圍) 
- 在代理程式設定上執行 **重新開機同步** 處理

## <a name="next-steps"></a>後續步驟 

- [Azure AD Connect 雲端同步疑難排解？](how-to-troubleshoot.md)
- [Azure AD Connect 雲端同步錯誤代碼](reference-error-codes.md)
 

