---
title: 為多租用戶應用程式啟用 SSO
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
ms.openlocfilehash: 4c89a83ade6305579e700afb86f0b9e3aca2695e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67795172"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>為多租戶應用程式啟用單一登入  

當您通過購買或訂閱提供應用程式供其他公司使用時，可以將應用程式提供給其自己的 Azure 租戶中的客戶。 這稱為創建多租戶應用程式。 有關此概念的概述，請參閱[Azure 中的多租戶應用程式](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications)和[Azure 活動目錄中的租戶](../develop/single-and-multi-tenant-apps.md)。

## <a name="what-is-single-sign-on"></a>什麼是單一登入

當使用者使用 Azure 活動目錄和其他標識登錄到應用程式時，單一登入 （SSO） 增加了安全性和便利性。 啟用 SSO 時，使用者無需輸入單獨的憑據即可訪問該應用程式。 有關單一登入的完整說明。 [請參閱 Azure 活動目錄中的應用程式單一登入](what-is-single-sign-on.md)。

## <a name="why-enable-single-sign-on-in-your-application"></a>為什麼在應用程式中啟用單一登入？

在多租戶應用程式中啟用 SSO 有許多優點。 當您為應用程式啟用 SSO 時：

* 您的應用程式可以列在 Azure 應用商店中，其中數百萬使用 Azure 活動目錄的組織可以發現你的應用。
  * 使客戶能夠使用 Azure AD 快速配置應用程式。

* 您的應用程式可以在 Office 365 應用庫、Office 365 應用啟動器和 Microsoft 搜索中Office.com

* 您的應用程式可以使用 Microsoft 圖形 REST API 訪問可從 Microsoft 圖形獲取的資料，從而提高使用者工作效率。

* 通過讓客戶更容易地降低支援成本。
  * 與 Azure AD 團隊為我們共同客戶共同製作的特定于應用程式的文檔，可簡化採用。
  * 如果啟用了一鍵式 SSO，則客戶的 IT 管理員不必瞭解如何配置應用程式以便在其組織中使用。

* 您可以為客戶提供完全管理其員工和來賓身份的身份驗證和授權的能力。

  * 將所有客戶管理和合規責任交給這些身份的客戶擁有者。

  * 為特定標識供應商、組或使用者啟用或禁用 SSO 以滿足其業務需求。

* 提高您的可上市性和可採用性。 許多大型組織要求（或渴望）其員工在所有應用程式中擁有無縫的 SSO 體驗。 使 SSO 變得簡單非常重要。

* 您可以減少最終使用者的摩擦，這可能會增加最終使用者的使用並增加您的收入。

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>如何在已發佈的應用程式中啟用單一登入

1. [為多租戶應用程式選擇正確的識別身分同盟協定](isv-choose-multi-tenant-federation.md)。
1. 在應用程式中實現 SSO
   - 有關[身份驗證模式的指導](../develop/v2-app-types.md)
   - 有關 OIDC 和 OAuth 協定的[Azure 活動目錄代碼示例](../develop/sample-v2-code.md)
1. [創建 Azure 租戶](isv-tenant-multi-tenant-app.md)並測試應用程式
1. [在您的網站上創建和發佈 SSO 文檔](isv-create-sso-documentation.md)。
1. [提交您的應用程式清單](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)，並與 Microsoft 合作，在 Microsoft 網站上創建文檔。
1. [加入微軟合作夥伴網路（免費），並創建您的市場計畫](https://partner.microsoft.com/en-us/explore/commercial#gtm)。
