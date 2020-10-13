---
title: 在 Azure Active Directory 入口網站中標示為有風險的使用者 |Microsoft Docs
description: 了解 Azure Active Directory 入口網站中標幟為有風險的使用者安全性報告
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08305b6aec7dcd5906f4316767c4c512d3a0535e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90600495"
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>補救 Azure Active Directory 入口網站中標幟為有風險的使用者

利用 Azure Active Directory (Azure AD) 中的安全性報告，您可以評估環境中使用者帳戶被盜用的可能性。 標幟為有風險的使用者表示可能被盜用的使用者帳戶。

Microsoft 致力於協助保護您的環境。 為此承諾，Microsoft 會持續監視異常或與已知攻擊模式相符的活動。 

如果偵測到可能表示未經授權存取某些使用者帳戶的異常活動，您就會收到可讓您採取行動的通知。 這並非表示 Microsoft 的自有系統受到危害。

## <a name="access-the-users-flagged-for-risk-report"></a>存取標幟為有風險的使用者報告

您可以透過 Azure 入口網站中的[有風險的使用者報告](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RiskyUsers)，檢閱標幟為有風險的使用者。 如果您沒有 Azure AD，可以在免費註冊 [https://aka.ms/AccessAAD](https://aka.ms/AccessAAD) 。 

從標幟為有風險的使用者報告，您可以為每位使用者採取下列動作：

- 產生臨時密碼
- 要求使用者在下次登入時安全地重設其密碼
- 解除使用者風險，但不採取任何補救動作。

如需詳細資訊，請參閱[標幟為有風險的使用者安全性報告](../identity-protection/overview-identity-protection.md)。

### <a name="azure-ad-subscription-for-microsoft-365-customers"></a>Azure AD Microsoft 365 客戶的訂用帳戶

您也可以使用 Microsoft 365 認證來存取 Azure 系統 **管理中心**。 在您啟動 Azure AD 存取後，您就會重新導向至 Azure AD 入口網站。 在基本訂閱等級，報告中提供的詳細資料數量有限。 其他資料和分析資料則適用於 Azure 進階訂閱者。

若要存取 Microsoft 365 系統管理中心中 **標示為有風險** 報告的使用者：

1.  從左側的導覽功能表中，選取 [系統管理中心]****。 
2.  選取 [Azure AD]****。
3.  登入 [Azure Active Directory 系統管理中心]****。
4.  如果頁面頂端顯示的橫幅表示「查看新的入口網站」****，請選取此連結。
4.  在左側導覽功能表中，選取 [Azure Active Directory]****。 
5.  在導覽窗格中，從 [安全性]**** 區段選取 [標幟為有風險的使用者]****。

## <a name="remediation-actions"></a>補救動作

採取下列動作來協助改正受影響的帳戶以及保護您的環境：

1.  [驗證正確的資訊](https://aka.ms/MFAValid)，以進行多重要素驗證和自助式密碼重設。 
2.  對所有使用者[啟用多重要素驗證](https://aka.ms/MFAuth)。 
3.  對每個受影響的帳戶使用此[補救指令碼](https://aka.ms/remediate)，以自動執行下列步驟： 

    a. 重設密碼以保護帳戶及刪除作用中的工作階段。

    b. 移除信箱委派。

    c. 停用外部網域的郵件轉寄規則。

    d. 移除信箱上的全域郵件轉寄屬性。

    e. 在使用者帳戶上啟用 MFA。

    f. 將帳戶的密碼複雜性設定為很高。

    g. 啟用信箱稽核。

    h. 產生稽核記錄，以供系統管理員檢閱。

4. 調查您的 Microsoft 365 租使用者和其他 IT 基礎結構，包括審核所有租使用者設定、使用者帳戶，以及每個使用者的設定，以進行可能的修改。 檢查持續性方法的指標，以及入侵者可能已利用初始據點來取得 VPN 認證，或存取其他組織資源的指標。 

5.  在調查過程中，請考慮是否應該通知政府機關，包括執法機關。

此外，您應該：

- 讀取及實作這個[關於處理異常活動的指引](https://aka.ms/fixaccount)。 
- [啟用稽核管線](https://aka.ms/improvesecurity)可協助您分析租用戶中的活動。 完成後，您的稽核存放區會開始填入活動記錄。 此時，您也能夠運用[安全性和規範中心的搜尋與調查資源](https://aka.ms/sccsearch)。 
- 檢閱您所有信箱的委派權限和郵件轉寄規則。 您可以使用此 [PowerShell 指令碼](https://aka.ms/delegateforwardrules)來執行此工作。 

## <a name="next-steps"></a>後續步驟

* [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md)
* [標示有風險的使用者](../identity-protection/overview-identity-protection.md)