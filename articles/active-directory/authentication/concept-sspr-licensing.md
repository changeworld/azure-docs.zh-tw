---
title: 授權自助式密碼重設 - Azure Active Directory
description: 瞭解 Azure Active Directory 自助密碼重設授權需求的差異
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45ca11af061e37cf4f804ce2d7ceed72a9448294
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393069"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Azure Active Directory 自助式密碼重設的授權需求

當使用者無法登入其裝置或應用程式時，若要減少服務台電話並喪失生產力，可以啟用 Azure Active Directory （Azure AD）中的使用者帳戶，以進行自助式密碼重設（SSPR）。 組成 SSPR 的功能包括密碼變更、重設、解除鎖定，以及回寫至內部部署目錄。 Office 365 和所有 Azure AD 使用者都可以免費使用基本的 SSPR 功能。

本文詳述自助式密碼重設的授權與使用方式。 如需定價和計費的特定詳細資料，請參閱[Azure AD 定價頁面](https://azure.microsoft.com/pricing/details/active-directory/)。

## <a name="compare-editions-and-features"></a>比較版本和功能

SSPR 是依每位使用者授權。 為了維持合規性，組織必須將適當的授權指派給他們的使用者。

下表概述密碼變更、重設或內部部署回寫的不同 SSPR 案例，以及哪些 Sku 提供此功能。

| 功能 | Azure AD Free | Office 365 商務進階版 | Microsoft 365 商務版 | Azure AD Premium P1 或 P2 |
| --- |:---:|:---:|:---:|:---:|
| **僅限雲端使用者密碼變更**<br />當 Azure AD 中的使用者知道他們的密碼，而且想要將它變更為新專案時。 | ● | ● | ● | ● |
| **僅限雲端使用者密碼重設**<br />當 Azure AD 中的使用者忘記其密碼，而且需要重設它時。 | | ● | ● | ● |
| **使用內部內部部署回寫進行混合式使用者密碼變更或重設**<br />當 Azure AD 中的使用者使用 Azure AD Connect 從內部部署目錄同步處理時，想要變更或重設其密碼，同時也將新密碼寫回內部內部部署。 | | | ● | ● |

> [!WARNING]
> 獨立 Office 365 授權方案不支援使用內部部署回寫進行 SSPR。 這些 Office 365 授權方案需要 Azure AD Premium P1、Premium P2 或 Microsoft 365 商務版，此功能才能運作。

如需其他授權資訊（包括成本），請參閱下列頁面：

* [Azure Active Directory 價格](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory 功能和功用](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 企業版](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 商務版](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>啟用以群組或使用者為基礎的授權

Azure AD 支援以群組為基礎的授權。 系統管理員可以將大量授權指派給一群使用者，而不是一次指派一個授權。 如需詳細資訊，請參閱[指派、驗證授權及解決其問題](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)。

某些 Microsoft 服務無法在所有位置使用。 在授權可以指派給使用者之前，系統管理員必須指定使用者的 [**使用位置**] 屬性。 您可以在 Azure 入口網站的 [**使用者** > **設定檔** > **設定**] 區段下，完成授權指派。 使用群組授權指派時，未指定使用位置的任何使用者在指派期間會繼承目錄的位置。**

## <a name="next-steps"></a>後續步驟

若要開始使用 SSPR，請完成下列教學課程：

> [!div class="nextstepaction"]
> [教學課程：啟用自助式密碼重設（SSPR）](tutorial-enable-sspr.md)
