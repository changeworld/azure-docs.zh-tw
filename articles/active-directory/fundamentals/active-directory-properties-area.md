---
title: 新增組織的隱私權資訊 - Azure Active Directory | Microsoft Docs
description: 以下相關指示說明如何將組織的隱私權資訊新增至 Azure Active Directory 的 [屬性] 區域。
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: ajburnle
ms.reviewer: bpham
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95f1877ae630919ba7da5a851b5f8291ade2d165
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76897575"
---
# <a name="add-your-organizations-privacy-info-using-azure-active-directory"></a>使用 Azure Active Directory 新增組織的隱私權資訊
本文說明租用戶系統管理員如何透過 Azure 入口網站，將隱私權相關資訊新增至組織的 Azure Active Directory (Azure AD) 租用戶。

強烈建議您將全球隱私權連絡人和貴組織的隱私權聲明一併加入，讓內部員工和外部來賓均能檢閱您的原則。 由於隱私權聲明是為每個企業量身打造、專屬於該家企業，強烈建議您洽詢律師尋求協助。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>在 Azure AD 新增您的隱私權資訊
在 Azure AD 的 [屬性面板] 區域中，新增組織的隱私權資訊

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>若要存取 [屬性面板] 區域並新增隱私權資訊

1.  以租用戶系統管理員身分登入 Azure 入口網站。

2.  在左導覽列中，選取 [Azure Active Directory]，然後選取 [屬性面板]。

    [屬性面板]區域隨即出現。

    ![標明隱私權資訊區域的 [Azure AD 屬性面板] 區域](media/active-directory-properties-area/properties-area.png)

3.  為您的員工新增隱私權資訊：

    - **技術聯絡人。** 請輸入組織中技術支援聯絡人的電子郵件地址。
    
    - **全域隱私權聯絡人。** 請輸入負責處理個人資料隱私權問題的聯絡人電子郵件地址。 若發生資料外洩情事，Microsoft 也會連繫此人員。 如果此處沒有列出人員，Microsoft 會聯絡您的全域系統管理員。

    - **隱私權聲明 URL。** 請輸入貴組織用以說明內部及外部來賓資料隱私權處理方式的文件連結。

        >[!Important]
        >如果未加入自己的隱私權聲明或隱私權聯絡人，外部來賓會在 [檢閱使用權限]方塊中看到文字說明： **<_您的組織名稱_ > 並未提供可供檢閱的條款連結**。 例如，來賓使用者在受邀透過 B2B 共同作業存取組織時，便會看到此訊息。

        ![顯示該訊息的 [B2B 共同作業檢閱使用權限] 方塊](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4.  選取 [儲存]。

## <a name="next-steps"></a>後續步驟
- [Azure Active Directory B2B 共同作業邀請兌換](https://aka.ms/b2bredemption)
- [在 Azure Active Directory 中新增或變更使用者的設定檔資訊](active-directory-users-profile-azure-portal.md)
