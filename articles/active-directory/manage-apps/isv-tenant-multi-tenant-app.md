---
title: 為多租戶應用程式創建 Azure 租戶
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
ms.openlocfilehash: 637adba89445e6974e83486f0641576225ccd268
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "70812622"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>為多租戶應用程式創建 Azure 租戶  

要提供對多租戶應用程式的存取權限，必須創建 Azure 活動目錄租戶來註冊應用程式並啟用客戶標識的聯合。 請參閱[為多租戶應用程式選擇正確的識別身分同盟協定](isv-choose-multi-tenant-federation.md)。 此租戶將允許您在類似于客戶 Azure AD 環境的環境中測試應用程式和聯合體。

## <a name="costs-of-hosting-a-multi-tenant-application"></a>託管多租戶應用程式的成本

Azure 活動目錄在多個版本中可用。 [請參閱詳細的特徵比較](https://azure.microsoft.com/pricing/details/active-directory/)。

您可以免費創建 Azure 訂閱和 Azure 活動目錄，並使用基本功能。

## <a name="create-your-tenant"></a>創建租戶

1. 創建租戶。 請參閱[設置開發環境](../develop/quickstart-create-new-tenant.md)。

2. 啟用和測試對應用程式的單一登入存取權限，

   a. **對於 OIDC 或 Oath 應用程式**，[將應用程式註冊](../develop/quickstart-register-app.md)為多租戶應用程式。 在"支援帳戶類型"中選擇任何組織目錄中的帳戶和個人 Microsoft 帳戶選項

   b. **對於基於 SAML 和 WS-Fed 的應用程式**，使用 Azure AD 中的通用 SAML 範本[配置基於 SAML 的單一登入](configure-single-sign-on-non-gallery-applications.md)應用程式。

如有必要，還可以[將單租戶應用程式轉換為多租戶](../develop/howto-convert-app-to-be-multi-tenant.md)。

## <a name="next-steps"></a>後續步驟

[將 SSO 集成到應用程式中](isv-sso-content.md)
