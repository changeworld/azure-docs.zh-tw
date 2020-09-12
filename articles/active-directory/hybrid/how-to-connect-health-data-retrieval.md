---
title: Azure AD Connect Health 指示資料抓取 |Microsoft Docs
description: 此頁面描述如何從 Azure AD Connect Health 取出資料。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/02/2020
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d147d2c094923e971e52e1dbfe3f7a19776d38c
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2020
ms.locfileid: "89463451"
---
# <a name="azure-ad-connect-health-instructions-for-data-retrieval"></a>Azure AD Connect Health 資料抓取的指示

本檔說明如何使用 Azure AD Connect 從 Azure AD Connect Health 取出資料。

[!INCLUDE [active-directory-app-provisioning.md](../../../includes/gdpr-intro-sentence.md)]

## <a name="retrieve-all-email-addresses-for-users-configured-for-health-alerts"></a>取得針對健康情況警示設定的使用者的所有電子郵件地址。

若要抓取 Azure AD Connect Health 中設定的所有使用者的電子郵件地址以接收警示，請使用下列步驟。

1.  從 Azure Active Directory Connect health] 分頁開始，然後從左側導覽列選取 [ **同步處理服務** ]。
 ![同步處理服務](./media/how-to-connect-health-data-retrieval/retrieve1.png)

2.  按一下 [ **警示** ] 磚。</br>
 ![警示](./media/how-to-connect-health-data-retrieval/retrieve3.png)

3.  按一下 [ **通知設定**]。
 ![通知](./media/how-to-connect-health-data-retrieval/retrieve4.png)

4.  在 [ **通知設定** ] 分頁中，您會找到已啟用為 [健康情況警示通知收件者] 的電子郵件地址清單。
 ![電子郵件](./media/how-to-connect-health-data-retrieval/retrieve5a.png)
 
## <a name="retrieve-accounts-that-were-flagged-with-ad-fs-bad-password-attempts"></a>取得標有 AD FS 錯誤密碼嘗試的帳戶

若要取得以 AD FS 錯誤密碼嘗試方式標示的帳戶，請使用下列步驟。

1.  從 Azure Active Directory 健康情況分頁開始，選取 [ **同步處理錯誤**]。
 ![同步錯誤](./media/how-to-connect-health-data-retrieval/retrieve6.png)

2.  在 [ **同步處理錯誤** ] 分頁中，按一下 [ **匯出**]。 這會匯出已記錄的同步處理錯誤清單。
 ![匯出](./media/how-to-connect-health-data-retrieval/retrieve7.png)

## <a name="next-steps"></a>後續步驟
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health 代理程式安裝](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health 操作](how-to-connect-health-operations.md)
* [Azure AD Connect Health 常見問題集](reference-connect-health-faq.md)
* [Azure AD Connect Health 版本歷程記錄](reference-connect-health-version-history.md)