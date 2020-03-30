---
title: 頁面上的連結對 Application Proxy 應用程式沒有作用
description: 如何為已與 Azure AD 整合之 Application Proxy 應用程式上的中斷連結問題疑難排解
services: active-directory
author: msmimart
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca782b5a190704911472d70c414d12afa7e558f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430234"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>頁面上的連結對 Application Proxy 應用程式沒有作用

這篇文章可協助您為 Azure Active Directory Application Proxy 應用程式上的連結無法正常運作疑難排解。

## <a name="overview"></a>總覽 
發佈 Application Proxy 應用程式後，只有預設在該應用程式中運作的連結才是已發佈根目錄 URL 內所包含目的地的連結。 該應用程式內的連結未運作，應用程式的內部 URL 或許未包含應用程式內連結的所有目的地。

**為何發生這種情況？** 當按一下應用程式中的連結時，Application Proxy 會嘗試將 URL 解析為相同應用程式內的內部 URL，或解析為外部可用的 URL。 如果連結指向的內部 URL 不在相同應用程式內，則該連結不屬於這些貯體，並會造成此找不到錯誤。

## <a name="ways-you-can-resolve-broken-links"></a>中斷連結的解決方式

有三種方法可以解決此問題。 以下依複雜度遞增方式列出解決方法。

1.  請確定內部 URL 是包含該應用程式所有相關連結的根目錄。 這可讓所有的連結解析為在相同應用程式內發佈的內容。

    如果您變更內部 URL，但不想要變更使用者的登陸頁面，請將首頁 URL 變更為先前發佈的內部 URL。 這可以通過去"Azure 活動目錄" -&gt;應用註冊 -&gt;選擇應用程式 -&gt;品牌。 在品牌部分，您可以看到欄位"主頁 URL"，您可以將其調整為所需的著陸頁。 如果您仍在使用舊版應用註冊體驗，則屬性選項卡將顯示"主頁 URL"詳細資訊。 
    
    > [!IMPORTANT]
    > 為了進行上述更改，您需要許可權來修改 Azure AD 中的應用程式物件。需要為使用者分配[應用程式管理員](../users-groups-roles/roles-delegate-app-roles.md#assign-built-in-application-admin-roles)角色，該角色將 Azure AD 中的應用程式 modificaion 許可權授予使用者。
    >

2.  如果您的應用程式使用完整的網域名稱 (FQDN)，請使用[自訂網域](application-proxy-configure-custom-domain.md)發佈您的應用程式。 這項功能可讓內部與外部使用相同的 URL。

    此選項可確保外部可透過 Application Proxy 存取您應用程式中的連結，因為外部也能辨識應用程式內對內部 URL 的連結。 所有的連結仍然必須屬於已發佈的應用程式。 不過有了此選項，連結不必屬於相同應用程式，而且可以屬於多個應用程式。

3.  如果這些選項都不適用，還有多個選項可讓您啟用內嵌連結轉譯。 這些選項包括使用 Intune Managed Browser 的、「我的應用程式」擴充功能，或在您的應用程式上使用連結轉譯設定。 若要深入了解其中每個選項及其啟用方式，請參閱[重新導向使用 Azure AD Application Proxy 發佈之應用程式的硬式編碼連結](application-proxy-configure-hard-coded-link-translation.md)。

## <a name="next-steps"></a>後續步驟
[使用現有的內部部署 Proxy 伺服器](application-proxy-configure-connectors-with-proxy-servers.md)

