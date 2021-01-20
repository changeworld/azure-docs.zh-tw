---
title: Azure AD Connect 雲端同步意外刪除
description: 本主題說明如何使用「意外刪除」功能來防止刪除。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/19/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ef7b6d9495b1431e03808b830671e839b90d436
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613399"
---
# <a name="accidental-delete-prevention"></a>意外刪除預防措施

下列檔說明 Azure AD Connect cloud sync 的意外刪除功能。 意外刪除功能的設計目的是要防止您意外設定變更，以及對內部部署目錄的變更，而這些變更會影響許多使用者和群組。  此功能可讓您：

- 設定防止意外刪除的功能。 
- 設定 (閾值) 超過設定將會生效的物件數目 
- 設定通知電子郵件地址，讓他們可以在此案例中有問題的同步處理工作被放入隔離時，收到電子郵件通知 

若要使用此功能，您可以設定如果已刪除，應停止同步處理的物件數目閾值。  因此，如果達到這個數位，同步處理將會停止，而且會傳送通知給指定的電子郵件。  這可讓您調查發生的狀況。


## <a name="configure-accidental-delete-prevention"></a>設定意外刪除防止
若要使用新功能，請遵循下列步驟。


1.  在 Azure 入口網站中，選取 [Azure Active Directory]  。
2.  選取 [Azure AD Connect]。
3.  選取 [ **管理雲端同步**]。
4. 在 [設定] **底下，選取您的設定**。
5. 在 [ **設定** ] 下，填入下列內容：
    - **通知電子郵件** -用於通知的電子郵件
    - **防止意外刪除** -勾選此方塊可啟用此功能
    - **意外刪除閾值** -輸入多個物件來觸發同步處理停止和通知

![意外刪除](media/how-to-accidental-deletes/accident-1.png)

## <a name="next-steps"></a>後續步驟 

- [什麼是雲端同步 Azure AD Connect？](what-is-cloud-sync.md)
- [如何安裝 Azure AD Connect cloud sync](how-to-install.md)
 

