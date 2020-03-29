---
title: 授權自助式密碼重設 - Azure Active Directory
description: Azure AD 自助式密碼重設授權需求
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 598f3bd8500a59cd41cc4126915e6cccbd4fb2f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848556"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Azure AD 自助式密碼重設的授權需求

Azure 活動目錄 （Azure AD） 有多種版本：免費版、高級 P1 版和高級 P2。 有幾個構成自助式密碼重設的不同功能 (包括變更、重設、解除鎖定和回寫)，可在不同的 Azure AD 版本中提供。 本文嘗試說明差異。 如需每個 Azure AD 版本中包含的功能詳細資料，請參閱 [Azure Active Directory 定價頁面](https://azure.microsoft.com/pricing/details/active-directory/)。

## <a name="compare-editions-and-features"></a>比較版本和功能

Azure AD 自助式密碼重設會依使用者授權來維持合規性，而組織必須將適當的授權指派給其使用者。

* 雲端使用者的自助式密碼變更
   * 我是**僅限雲端的使用者**而且知道我的密碼。
      * 我想要將我的密碼**變更**為新密碼。
   * 這項功能包含在所有的 Azure AD 版本中。

* 雲端使用者的自助式密碼重設
   * 我是**僅限雲端的使用者**而且忘了我的密碼。
      * 我想要將我的密碼**重設**為我所知道的密碼。
   * 此功能包含在 Azure AD 高級 P1 或 P2、Microsoft 365 業務或 Office 365 中。

* 自助式密碼重設/變更/**使用內部部署回寫**來解鎖
   * 我是**混合式使用者**，我的內部部署 Active Directory 使用者帳戶已使用 Azure AD Connect 與我的 Azure AD 帳戶同步處理。 我想要變更我的密碼、忘了我的密碼，或已鎖定。
      * 我想要變更我的密碼或將它重設為我知道的，或將我的帳戶解除鎖定，**以及**將該變更同步處理回到內部部署 Active Directory。
   * 此功能包含在 Azure AD Premium P1 或 P2，或 Microsoft 365 商務版中。

> [!WARNING]
> 獨立的 Office 365 授權方案不支援「自助式密碼重設/變更/使用內部部署回寫來解鎖」**，而且需要包含 Azure AD Premium P1、Premium P2 或 Microsoft 365 商務版的方案，這項功能才能運作。
>

在下列分頁可以找到額外的授權資訊 (包括成本)：

* [Azure Active Directory 價格網站](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory 功能和功用](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 企業版](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Business 服務描述](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>啟用以群組或使用者為基礎的授權

Azure AD 現在支援以群組為基礎的授權。 系統管理員可以將大量授權指派給一群使用者，而不是一次指派一個授權。 如需詳細資訊，請參閱[指派、驗證授權及解決其問題](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)。

並非所有位置都可使用某些 Microsoft 服務。 在將許可證分配給使用者之前，管理員必須在使用者上指定 **"使用方式"位置**屬性。 許可證分配可以在 Azure 門戶中的 **"使用者** > **設定檔** > **設置"** 部分下完成。 使用群組授權指派時，未指定使用位置的任何使用者在指派期間會繼承目錄的位置。**

## <a name="next-steps"></a>後續步驟

* [如何完成 SSPR 成功首度發行？](howto-sspr-deployment.md)
* [重置或更改密碼](../user-help/active-directory-passwords-update-your-own-password.md)
* [註冊進行自助服務密碼重設](../user-help/active-directory-passwords-reset-register.md)
* [SSPR 使用哪些資料，以及您應該為使用者填入哪些資料？](howto-sspr-authenticationdata.md)
* [哪些驗證方法可供使用者使用？](concept-sspr-howitworks.md#authentication-methods)
* [使用 SSPR 的原則選項有哪些？](concept-sspr-policy.md)
* [什麼是密碼回寫，且為什麼我需要了解它？](howto-sspr-writeback.md)
* [如何回報 SSPR 中的活動？](howto-sspr-reporting.md)
* [SSPR 中的所有選項有哪些，以及它們有何意義？](concept-sspr-howitworks.md)
* [我覺得有些東西壞了。如何對 SSPR 進行故障排除？](active-directory-passwords-troubleshoot.md)
* [在其他某處並未涵蓋我的問題](active-directory-passwords-faq.md)
