---
title: Azure 多重身份驗證概述
description: 瞭解 Azure 多重身份驗證如何幫助保護對數據和應用程式的訪問,同時滿足使用者對簡單登錄過程的需求。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c50232abd12c8c0390409bd7bf72833b4f153e02
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667352"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>運作方式：Azure Multi-Factor Authentication

多重要素驗證是使用者在登入過程中經提示而提供其他形式的識別 (例如，在其行動電話上輸入代碼或提供指紋掃描) 的程序。

如果僅使用密碼來驗證使用者，將會有不安全的媒介可能遭到攻擊。 如果密碼不嚴謹或已於他處公開，那麼以使用者名稱和密碼登入的究竟是使用者，還是攻擊者？ 當您要求第二種形式的驗證時，安全性將會增加，因為這項額外的因素並不容易讓攻擊者取得或複製。

![不同多重要素驗證形式的概念影像](./media/concept-mfa-howitworks/methods.png)

Azure Multi-Factor Authentication 的運作需要下列二種或更多的驗證方法：

* 您知道的某些資訊 (通常是密碼)。
* 您擁有的某些資訊，例如不容易複製的信任裝置 (如電話或硬體金鑰)。
* 代表您個人身分的某些資訊 - 指紋或臉部掃描之類的生物識別特徵。

使用者可以在單一步驟中將其身分註冊至自助式密碼重設和 Azure Multi-Factor Authentication，以簡化上線體驗。 系統管理員可定義可以使用的次要驗證形式。 在使用者執行自助式密碼重設以進一步保護該程序時，也可以要求 Azure Multi-Factor Authentication。

![在 [登入] 畫面使用中的驗證方法](media/concept-authentication-methods/overview-login.png)

Azure 多重身份驗證有助於保護對數據和應用程式的訪問,同時保持使用者的簡單性。 它藉由要求第二種形式的驗證來提供額外的安全性，並透過一系列易於使用的[驗證方法](concept-authentication-methods.md)來提供增強式驗證。 因管理員所做的設定決定不同，使用者可能必須也可能無須通過 MFA。

應用程式或服務無需進行任何更改,即使用 Azure 多重身份驗證。 驗證提示是 Azure AD 登入事件的一部分,該事件在需要時自動請求和處理 MFA 質詢。

## <a name="available-verification-methods"></a>可用的驗證方法

當使用者登錄到應用程式或服務並收到 MFA 提示時,他們可以選擇其註冊的其他驗證形式之一。 管理員可能需要註冊這些 Azure 多重身份驗證驗證方法,或者使用者可以存取自己的[「我的設定檔」](https://myprofile.microsoft.com)來編輯或添加驗證方法。

以下其他驗證形式可用於 Azure 多重身份驗證:

* Microsoft Authenticator 應用程式
* OATH 硬體權杖
* sms
* 語音通話

## <a name="how-to-enable-and-use-azure-multi-factor-authentication"></a>如何開啟與使用 Azure 多重身份驗證

可以為 Azure 多重身份驗證啟用使用者和組,以在登入事件期間提示進行其他驗證。 所有 Azure AD 租戶都可以使用[安全預設值](../fundamentals/concept-fundamentals-security-defaults.md),以便快速啟用所有使用者使用 Microsoft 身份驗證器應用。

對於更精細的控制項,[條件存取](../conditional-access/overview.md)策略可用於定義需要 MFA 的事件或應用程式。 當用戶位於公司網路或已註冊的設備上時,這些策略可以允許定期登錄事件,但在遠端或個人設備上提示其他驗證因素。

![條件式存取如何運作以保護登入程序的概觀圖表](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

## <a name="next-steps"></a>後續步驟

要瞭解許可,請參閱[Azure 多重身份驗證的功能和授權](concept-mfa-licensing.md)。

要查看 MFA 的執行情況,請在以下教學中為一組測試使用者啟用 Azure 多重身份驗證:

> [!div class="nextstepaction"]
> [啟用 Azure Multi-Factor Authentication](tutorial-mfa-applications.md)
