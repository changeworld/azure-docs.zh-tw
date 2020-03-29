---
title: Azure AD Connect 同步處理：變更 AD DS 帳戶密碼 | Microsoft Docs
description: 本主題文件說明如何在變更 AD DS 帳戶的密碼後更新 Azure AD Connect。
services: active-directory
keywords: AD DS 帳戶, Active Directory 帳戶, 密碼
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35e04be046e20883f60c576745a29342add68a81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "60241606"
---
# <a name="changing-the-ad-ds-account-password"></a>變更 AD DS 帳戶密碼
AD DS 帳戶指的是 Azure AD Connect 用來與內部部署 Active Directory 進行通訊的使用者帳戶。 如果您變更 AD DS 帳戶的密碼，您必須以新密碼更新 Azure AD Connect 同步處理服務。 否則，同步處理服務就無法再正確地與內部部署 Active Directory 進行同步處理，而且您會遇到下列錯誤︰

* 在同步處理服務管理員中，使用內部部署 AD 進行的匯入或匯出作業會失敗，並出現 **no-start-credentials** 錯誤。

* 在 Windows 事件檢視器下，應用程式事件日誌包含事件**ID 6000**的錯誤，並且消息 **"管理代理"contoso.com"由於憑據無效而無法運行**。


## <a name="how-to-update-the-synchronization-service-with-new-password-for-ad-ds-account"></a>如何以新的 AD DS 帳戶密碼更新同步處理服務
若要以新密碼更新同步處理服務︰

1. 啟動同步處理服務管理員 ([開始] → [同步處理服務])。
</br>![Sync Service Manager](./media/how-to-connect-sync-change-addsacct-pass/startmenu.png)  

2. 移至 [連接器]**** 索引標籤。

3. 選取密碼已變更之 AD DS 帳戶所對應的 [AD 連接器]****。

4. 在 **"操作"** 下，選擇**屬性**。

5. 在快顯對話方塊中，選取 [連線至 Active Directory 樹系]****：

6. 在 [密碼]**** 文字方塊中輸入 AD DS 帳戶的新密碼。

7. 按一下 [確定]**** 以儲存新密碼，然後關閉快顯對話方塊。

8. 在 Windows 服務控制管理員底下重新啟動 Azure AD Connect 同步處理服務。 這可確保系統會從記憶體快取中移除舊密碼的任何參考。

## <a name="next-steps"></a>後續步驟
**概觀主題**

* [Azure AD Connect 同步處理：了解及自訂同步處理](how-to-connect-sync-whatis.md)

* [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)
