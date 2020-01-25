---
title: 如何設定 Azure AD 資源庫應用程式的使用者布建
description: 如何為已經列於 Azure AD 應用程式庫中的應用程式快速設定豐富的使用者帳戶佈建和解除佈建
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: 87818066a656aa4bcd6f45f1835ca1512a674a9e
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712315"
---
# <a name="how-to-configure-user-provisioning-to-an-azure-ad-gallery-application"></a>如何設定 Azure AD 資源庫應用程式的使用者佈建

*使用者帳戶佈建*是指在應用程式的本機使用者設定檔儲存中建立、更新及/或停用使用者帳戶記錄的動作。 大部分的雲端和 SaaS 應用程式會將使用者角色和許可權儲存在使用者自己的本機使用者設定檔存放區中，而使用者本機存放區中的這類使用者記錄*必須*存在，才能讓單一登入和存取工作。

在 Azure 入口網站中，位於企業應用程式左側瀏覽窗格中的 [佈建] 索引標籤會顯示該應用程式支援哪些佈建模式。 這可能是下列其中一個值：

## <a name="configuring-an-application-for-manual-provisioning"></a>設定應用程式以進行手動佈建

*手動*布建表示必須使用應用程式所提供的方法來手動建立使用者帳戶。 這可能表示登入該應用程式的系統管理員入口網站，然後使用 Web 架構使用者介面新增使用者。 或者，它可能會使用該應用程式所提供的機制，來上傳含有使用者帳戶詳細資料的試算表。 請參閱應用程式所提供的檔，或洽詢應用程式開發人員以判斷有哪些機制可供使用。

如果 [*手動*] 是針對特定應用程式所顯示的唯一模式，則表示應用程式尚未自動 Azure AD 布建連接器。 或者，這表示應用程式不支援 Microsoft 的使用者管理 API 的必要條件，這是用來建立自動布建連接器。

如果您想要求支援自動佈建指定的應用程式，可以使用 [Azure Active Directory 應用程式要求](https://aka.ms/aadapprequest)來填寫要求。

## <a name="configuring-an-application-for-automatic-provisioning"></a>設定應用程式以進行自動佈建

「自動」表示已經為此應用程式開發 Azure AD 佈建連接器。 如需 Azure AD 佈建服務及其運作方式的詳細資訊，請參閱[自動化使用 Azure Active Directory 對於 SaaS 應用程式的使用者佈建和解除佈建](user-provisioning.md)。

如需如何將特定使用者和群組佈建到應用程式的相關詳細資訊，請參閱[管理企業應用程式的使用者帳戶佈建](configure-automatic-user-provisioning-portal.md)。

啟用和設定自動布建所需的實際步驟會因應用程式而異。

> [!NOTE]
> 您一開始應該先尋找設定您應用程式佈建專用的設定教學課程，然後依照這些步驟來設定應用程式和 Azure AD 以建立佈建連接。

您可以在[如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](../saas-apps/tutorial-list.md)中找到應用程式教學課程。

設定佈建時必須考量的重點是，檢視和設定屬性 (Attribute) 對應，以及定義哪些使用者 (或群組) 屬性 (Property) 會從 Azure AD 流向應用程式的工作流程。 這包括設定「比對屬性」，用來唯一識別並比對兩個系統之間的使用者/群組。 如需屬性對應的詳細資訊，請參閱*後續步驟*中的連結。

## <a name="next-steps"></a>後續步驟
[在 Azure Active Directory 中自訂 SaaS 應用程式的使用者佈建屬性對應](customize-application-attributes.md)

