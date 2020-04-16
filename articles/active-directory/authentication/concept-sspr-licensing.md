---
title: 授權自助式密碼重設 - Azure Active Directory
description: 瞭解 Azure 活動目錄自助密碼重置授權要求的差異
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
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393069"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Azure 活動目錄自助服務密碼重置的許可要求

為了減少使用者無法登錄到其設備或應用程式時的幫助台呼叫和工作效率損失,可以啟用 Azure 活動目錄 (Azure AD) 中的使用者帳戶進行自助服務密碼重置 (SSPR)。 構成 SSPR 的功能包括密碼更改、重置、解鎖和寫入本地目錄。 Office 365 和所有 Azure AD 使用者均可免費提供基本 SSPR 功能。

本文詳細介紹了自助服務密碼重置可以許可和使用的不同方式。 有關定價和計費的具體詳細資訊,請參閱[Azure AD 定價頁](https://azure.microsoft.com/pricing/details/active-directory/)。

## <a name="compare-editions-and-features"></a>比較版本和功能

SSPR 是每個使用者的許可。 為了保持合規性,組織需要為其使用者分配適當的許可證。

下表概述了密碼更改、重置或本地寫回的不同 SSPR 方案,以及哪些 SKU 提供了該功能。

| 功能 | Azure AD Free | Office 365 商務進階版 | Microsoft 365 商務版 | Azure AD Premium P1 或 P2 |
| --- |:---:|:---:|:---:|:---:|
| **只限雲的使用者密碼變更**<br />當 Azure AD 中的使用者知道其密碼並希望將其更改為新密碼時。 | ● | ● | ● | ● |
| **只限雲的使用者密碼重置**<br />當 Azure AD 中的使用者忘記了其密碼並需要重置密碼時。 | | ● | ● | ● |
| **混合使用者密碼變更或重置與上前寫**<br />當使用 Azure AD Connect 從本地目錄同步的 Azure AD 中的使用者想要更改或重置其密碼,並將新密碼寫回 on-prem 時。 | | | ● | ● |

> [!WARNING]
> 獨立 Office 365 許可計劃不支援 SSPR 與本地回寫。 這些 Office 365 許可計劃需要 Azure AD 高級 P1、高級 P2 或 Microsoft 365 業務才能正常工作。

有關其他許可資訊(包括成本),請參閱以下頁面:

* [Azure Active Directory 價格](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory 功能和功用](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [企業移動性與安全性](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 企業版](https://www.microsoft.com/microsoft-365/enterprise)
* [微軟 365 業務](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>啟用以群組或使用者為基礎的授權

Azure AD 支援基於組的許可。 系統管理員可以將大量授權指派給一群使用者，而不是一次指派一個授權。 如需詳細資訊，請參閱[指派、驗證授權及解決其問題](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)。

某些 Microsoft 服務並非在所有位置都可用。 在將許可證分配給使用者之前,管理員必須在使用者上指定 **「使用方式」位置**。 許可證分配可以在 Azure 門戶中的 **「使用者** > **設定檔** > **設定」** 部分下完成。 使用群組授權指派時，未指定使用位置的任何使用者在指派期間會繼承目錄的位置。**

## <a name="next-steps"></a>後續步驟

要開始使用 SSPR,請完成以下教學:

> [!div class="nextstepaction"]
> [教學:啟用自助服務密碼重置 (SSPR)](tutorial-enable-sspr.md)
