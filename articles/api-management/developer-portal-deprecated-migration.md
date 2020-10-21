---
title: 從舊版開發人員入口網站遷移至新的開發人員入口網站
titleSuffix: Azure API Management
description: 瞭解如何從舊版開發人員入口網站遷移至 API 管理中的新開發人員入口網站。
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/15/2020
ms.author: apimpm
ms.openlocfilehash: f5105c685de4b3ccdffe69eec8ee8eeb32976c1f
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92325975"
---
# <a name="migrate-to-the-new-developer-portal"></a>遷移至新的開發人員入口網站

本文說明從已淘汰的舊版入口網站遷移至 API 管理中的新開發人員入口網站時，所需採取的步驟。

> [!IMPORTANT]
> 舊版開發人員入口網站現已淘汰，而且只會收到安全性更新。 您可以繼續使用它，就像平常一樣，直到2023年10月淘汰為止（當它從所有 API 管理服務中移除）。

![API Management developer portal](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="improvements-in-new-developer-portal"></a>新開發人員入口網站的改進

新的開發人員入口網站解決了已淘汰的入口網站的許多限制。 它具備 [視覺效果拖放編輯器，可供編輯內容](api-management-howto-developer-portal-customize.md) 和設計工具的專用面板來為網站設計樣式。 頁面、自訂和設定會以 Azure Resource Manager 資源的形式儲存在您的 API 管理服務中，可讓您 [自動化入口網站部署](api-management-howto-developer-portal.md#automate)。 最後，入口網站的程式碼基底是開放原始碼，因此 [您可以使用自訂功能來擴充它](api-management-howto-developer-portal.md#managed-vs-self-hosted)。

## <a name="how-to-migrate-to-new-developer-portal"></a>如何遷移至新的開發人員入口網站

新的開發人員入口網站與已淘汰的入口網站不相容，因此無法進行自動化遷移。 您必須手動重新建立 (頁面、文字、媒體檔案) 的內容，並自訂新入口網站的外觀。 確切的步驟會根據您的入口網站的自訂和複雜度而有所不同。 如需指引，請參閱 [開發人員入口網站教學](api-management-howto-developer-portal-customize.md) 課程。 其餘的設定，就像 Api、產品、使用者、身分識別提供者的清單，會在這兩個入口網站中自動共用。

> [!IMPORTANT]
> 如果您之前已經啟動新的開發人員入口網站，但尚未進行任何變更，請 [重設預設內容](api-management-howto-developer-portal.md#preview-to-ga) 以將其更新為最新版本。

當您從已淘汰的入口網站進行遷移時，請記住下列變更：

- 如果您透過自訂網域公開開發人員入口網站，請 [將網域指派](configure-custom-domain.md) 給新的開發人員入口網站。 使用 Azure 入口網站的下拉式清單中的 [ **開發人員入口網站** ] 選項。
- 在您的 Api 上套用[CORS 原則](api-management-howto-developer-portal.md#cors)，以啟用互動式測試主控台。
- 如果您插入自訂 CSS 以設定入口網站的樣式，則需要 [使用內建的設計面板](api-management-howto-developer-portal-customize.md)來複寫樣式。 新的入口網站不允許 CSS 插入。
- 您只能在 [新入口網站的自我裝載版本](api-management-howto-developer-portal.md#managed-vs-self-hosted)中插入自訂 JavaScript。
- 如果您的 API 管理位於虛擬網路中，並透過應用程式閘道向網際網路公開，請 [參閱這篇檔文章](api-management-howto-integrate-internal-vnet-appgateway.md) ，以取得精確的設定步驟。 您必須：

    - 啟用 API 管理管理端點的連線能力。
    - 啟用連接至新的入口網站端點。
    - 停用選取的 Web 應用程式防火牆規則。

- 如果您變更預設電子郵件通知範本以包含明確定義的已淘汰入口網站 URL，請將其變更為使用入口網站 URL 參數，或指向新的入口網站 URL。 如果範本改為使用內建的入口網站 URL 參數，則不需要進行任何變更。
- 新的開發人員入口網站不支援*問題*和*應用程式*。
- 在新的開發人員入口網站中，不支援與 Facebook、Microsoft、Twitter 和 Google 的直接整合，作為身分識別提供者。 您可以透過 Azure AD B2C 與這些提供者整合。
- 如果您使用委派，請變更應用程式中的傳回 URL，並使用 [*取得共用存取權杖* API 端點](/rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken) ，而不是 *產生 SSO URL* 端點。
- 如果您使用 Azure AD 作為身分識別提供者：

    - 變更應用程式中的傳回 URL，使其指向新的開發人員入口網站網域。
    - 將應用程式中傳回 URL 的尾碼從修改 `/signin-aad` 為 `/signin` 。

- 如果您使用 Azure AD B2C 作為身分識別提供者：

    - 變更應用程式中的傳回 URL，使其指向新的開發人員入口網站網域。
    - 將應用程式中傳回 URL 的尾碼從修改 `/signin-aad` 為 `/signin` 。
    - 在應用程式宣告中包含 *指定的名稱*、 *姓氏*和 *使用者的物件識別碼* 。

- 如果您在互動式測試主控台中使用 OAuth 2.0，請將應用程式中的傳回 URL 變更為指向新的開發人員入口網站網域，並修改尾碼：

    - 從 `/docs/services/[serverName]/console/oauth2/authorizationcode/callback` 到， `/signin-oauth/code/callback/[serverName]` 授權碼授與流程。
    - `/docs/services/[serverName]/console/oauth2/implicit/callback`若 `/signin-oauth/implicit/callback` 為，則為隱含授與流程。
- 如果您在互動式測試主控台中使用 OpenID Connect，請將應用程式中的傳回 URL 變更為指向新的開發人員入口網站網域，並修改尾碼：

    - 從 `/docs/services/[serverName]/console/openidconnect/authorizationcode/callback` 到， `/signin-oauth/code/callback/[serverName]` 授權碼授與流程。
    - `/docs/services/[serverName]/console/openidconnect/implicit/callback`若 `/signin-oauth/implicit/callback` 為，則為隱含授與流程。

## <a name="next-steps"></a>後續步驟

深入瞭解開發人員入口網站：

- [Azure API 管理開發人員入口網站概觀](api-management-howto-developer-portal.md)
- [存取和自訂開發人員入口網站](api-management-howto-developer-portal-customize.md)