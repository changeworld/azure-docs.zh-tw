---
title: 創建&發佈應用程式的單一登入文檔
description: 獨立軟體廠商與 Azure 活動目錄集成指南
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb223ec8ab7b5c053136c78d3b4ca30ad4da4e18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232276"
---
# <a name="create-and-publish-single-sign-on-documentation-for-your-application"></a>為應用程式創建和發佈單一登入文檔   

## <a name="documentation-on-your-site"></a>網站上的文檔

易於採用是企業軟體決策的重要因素。 清晰的易於遵循的文檔可支援客戶的採用過程，並降低支援成本。 通過與成千上萬的軟體供應商合作，微軟已經看到了哪些是有效的。

我們建議您在網站上的文檔至少包含以下專案。

* SSO 功能簡介

  * 支援的通訊協定

  * 版本和 SKU

  * 支援的身份提供程式清單，包含文檔連結

* 應用程式的許可資訊

* 用於配置 SSO 的基於角色的存取控制

* SSO 配置步驟

  * SAML 的 UI 配置元素，具有來自提供程式的預期值

  * 要傳遞給標識提供程式的服務提供者資訊

* 如果 OIDC/OAuth

  * 具有業務理由同意所需的許可權清單

* 試驗使用者的測試步驟

* 故障排除資訊，包括錯誤代碼和消息

* 支援客戶機制

## <a name="documentation-on-the-microsoft-site"></a>微軟網站上的文檔

當您使用 Azure 活動目錄應用程式庫（在 Azure 應用商店中發佈應用程式）列出應用程式時，Microsoft 將為我們共同的客戶生成文檔，解釋分步過程。 你可以[在這裡](https://aka.ms/appstutorial)看到一個例子。 本文檔是根據您提交到庫而創建的，如果您使用 GitHub 帳戶對應用程式進行更改，則可以輕鬆地更新它。

## <a name="next-steps"></a>後續步驟

[在 Azure AD 應用程式庫中列出應用程式](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)