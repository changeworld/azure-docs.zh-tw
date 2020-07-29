---
title: 建立 & 發行應用程式的單一登入檔
description: 獨立軟體廠商與 Azure Active Directory 整合的指引
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: kenwith
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c758e90548dd22b5abfb731f674f83dfbde9819
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85955976"
---
# <a name="create-and-publish-single-sign-on-documentation-for-your-application"></a>建立及發佈應用程式的單一登入檔   

## <a name="documentation-on-your-site"></a>網站上的檔

簡化採用是企業軟體決策中的重要因素。 清楚易懂的檔可支援您的客戶採用旅程，並降低支援成本。 與數以千計的軟體廠商合作，Microsoft 看過哪些功能可以運作。

我們建議您的網站最少包含下列專案的檔。

* SSO 功能簡介

  * 支援的通訊協定

  * 版本和 SKU

  * 支援的身分識別提供者清單與檔連結

* 應用程式的授權資訊

* 以角色為基礎的存取控制，用於設定 SSO

* SSO 設定步驟

  * 具有提供者所需值之 SAML 的 UI 設定元素

  * 要傳遞給識別提供者的服務提供者資訊

* 如果 OIDC/OAuth

  * 同意商業理由所需的許可權清單

* 試驗使用者的測試步驟

* 疑難排解資訊，包括錯誤代碼和訊息

* 客戶的支援機制

## <a name="documentation-on-the-microsoft-site"></a>Microsoft 網站上的檔

當您使用 Azure Active Directory 應用程式庫列出您的應用程式時，這也會在 Azure Marketplace 中發佈您的應用程式，Microsoft 會為我們的相互客戶產生檔，以說明逐步流程。 您可以在[這裡](https://aka.ms/appstutorial)看到範例。 本檔是根據您提交至資源庫而建立的，如果您使用 GitHub 帳戶對應用程式進行變更，就可以輕鬆地更新。

## <a name="next-steps"></a>後續步驟

[在 Azure AD 應用程式庫中列出您的應用程式](https://docs.microsoft.com/Azure/active-directory/develop/howto-app-gallery-listing)
