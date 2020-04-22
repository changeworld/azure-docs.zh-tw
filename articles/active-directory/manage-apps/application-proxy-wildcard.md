---
title: Azure AD 應用程式代理中的通配子應用程式
description: 了解如何在 Azure Active Directory 應用程式 Proxy 中使用萬用字元應用程式。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2018
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e5861e802f39adecb5661bc17c22b432f137d59
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770291"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Azure Active Directory 應用程式 Proxy 中的萬用字元應用程式

在 Azure Active Directory (Azure AD) 中，設定大量內部部署應用程式很快會變得難以管理，而且如果其中有多個需要相同設定，則會引入設定錯誤的不必要風險。 您可以使用 [Azure AD 應用程式 Proxy](application-proxy.md)，藉由使用發行的萬用字元應用程式來一次發行及管理許多應用程式，以解決這個問題。 這是解決方案，可讓您：

- 簡化系統管理負荷
- 減少潛在設定錯誤數目
- 讓使用者安全地存取更多資源

本文章提供您設定在您的環境中發行之萬用字元應用程式的必要資訊。

## <a name="create-a-wildcard-application"></a>建立萬用字元應用程式

如果您有一組具有相同設定的應用程式，您可以建立萬用字元 (*) 應用程式。 萬用字元應用程式的潛在候選項目是共用下列設定的應用程式：

- 具有其存取權的使用者群組
- SSO 方法
- 存取通訊協定 (http、https)

如果內部和外部 URL 都是以下格式，您可以發行具有萬用字元的應用程式：

> http(s)://*.\<domain\>

例如： `http(s)://*.adventure-works.com` 。

雖然內部和外部 URL 可以使用不同網域，讓它們使用相同網域是最佳做法。 發行應用程式時，如果其中一個 URL 沒有萬用字元，您會看到錯誤。

建立萬用字元應用程式是根據可用於所有其他應用程式的相同[應用程式發行流程](application-proxy-add-on-premises-application.md)。 唯一的差別是您在 URL (也可能在 SSO 設定) 中包含萬用字元。

## <a name="prerequisites"></a>Prerequisites

要開始,請確保您已滿足這些要求。

### <a name="custom-domains"></a>自訂網域

雖然[自訂網域](application-proxy-configure-custom-domain.md)對於所有其他應用程式是選擇性的，它們是萬用字元應用程式的必要條件。 建立自訂網域需要您：

1. 在 Azure 中創建已驗證的域。
1. 以 PFX 格式將 TLS/SSL 證書上傳到應用程式代理。

您應該考慮使用萬用字元憑證，以符合您打算建立的應用程式。 或者，您也可以使用僅列出特定應用程式的憑證。 在此情況下，只有憑證中列出的應用程式可透過此萬用字元應用程式存取。

基於安全性理由，這是硬性需求，而且我們不會對無法使用自訂網域作為外部 URL 的應用程式支援萬用字元。

### <a name="dns-updates"></a>DNS 更新

當使用自訂網域時，您需要為外部 URL 建立具有 CNAME 記錄的 DNS 項目 (例如，`*.adventure-works.com`) 指向應用程式 Proxy 端點的外部 URL。針對萬用字元應用程式，CNAME 記錄必須指向相關的外部 URL：

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

若要確認您已正確設定您的 CNAME，您可以在其中一個目標端點上使用 [nslookup](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup)，例如 `expenses.adventure-works.com`。  您的回應應該包含已經提到的別名 (`<yourAADTenantId>.tenant.runtime.msappproxy.net`)。

## <a name="considerations"></a>考量

以下是通配符應用程式的一些注意事項。

### <a name="accepted-formats"></a>接受的格式

針對萬用字元應用程式，**內部 URL** 必須格式化為 `http(s)://*.<domain>`。

![對於內部 URL,請使用格式 HTTP(s)://*。\<域>](./media/application-proxy-wildcard/22.png)

在設定**外部 URL** 時，您必須使用下列格式：`https://*.<custom domain>`

![對於外部 URL,請使用HTTPs://*格式。\<自訂網域>](./media/application-proxy-wildcard/21.png)

其他位置的萬用字元、多個萬用字元或其他 regex 字串不受支援，而且會導致錯誤。

### <a name="excluding-applications-from-the-wildcard"></a>從萬用字元排除應用程式

您可以從萬用字元應用程式排除應用程式，方法是

- 將例外應用程式發行為一般應用程式
- 只針對特定應用程式透過您的 DNS 設定啟用萬用字元

將應用程式發行為一般應用程式是從萬用字元排除應用程式的慣用方法。 您應該在萬用字元應用程式之前發行已排除的應用程式，以確保您的例外從一開始就強制執行。 最特定的應用程式一律有最高的優先順序 – 發行為 `budgets.finance.adventure-works.com` 的應用程式優先順序高於應用程式 `*.finance.adventure-works.com`，後者優先順序則再高於應用程式 `*.adventure-works.com`。

您也可以限制萬用字元透過您的 DNS 管理僅適用於特定應用程式。 最佳做法是您應該建立 CNAME 項目，其中包含萬用字元並且符合您已設定之外部 URL 的格式。 不過，您可以改為將特定應用程式 URL 指向萬用字元。 例如，並非 `*.adventure-works.com`，而是將 `hr.adventure-works.com`、`expenses.adventure-works.com` 和 `travel.adventure-works.com individually` 指向 `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`。

如果您使用此選項，您也需要另一個 CNAME 項目值 `AppId.domain`，例如 `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`，也指向相同的位置。 您可以在萬用字元應用程式的應用程式屬性分頁上找到 **AppId**：

![在應用程式的屬性頁上尋找應用程式識別碼](./media/application-proxy-wildcard/01.png)

### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>設定 MyApps 面板的首頁 URL

萬用字元應用程式在 [MyApps 面板](https://myapps.microsoft.com)中僅以一個圖格表示。 此圖格預設會隱藏。 若要顯示圖格並讓使用者登陸在特定分頁上：

1. 請遵循[設定首頁 URL](application-proxy-configure-custom-home-page.md) 的指導方針。
1. 在應用程式屬性分頁上將 [顯示應用程式]**** 設為 **true**。

### <a name="kerberos-constrained-delegation"></a>Kerberos 限制委派

對於使用 [kerberos 限制委派 (KCD) 作為 SSO 方法](application-proxy-configure-single-sign-on-with-kcd.md)的應用程式，針對 SSO 方法列出的 SPN 可能也需要萬用字元。 例如，SPN 可能是：`HTTP/*.adventure-works.com`。 您仍然需要在後端伺服器上設定個別 SPN (例如，`HTTP/expenses.adventure-works.com and HTTP/travel.adventure-works.com`)。

## <a name="scenario-1-general-wildcard-application"></a>案例 1：一般萬用字元應用程式

在此案例中，您有三個想要發行的不同應用程式：

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

三個應用程式全部：

- 由您的所有使用者使用
- 使用整合式 Windows 驗證**
- 具有相同的屬性

您可以使用[使用 Azure AD 應用程式 Proxy 發行應用程式](application-proxy-add-on-premises-application.md)中概述的步驟發行萬用字元應用程式。 此案例假設：

- 租用戶具有下列 ID：`000aa000-11b1-2ccc-d333-4444eee4444e`
- 已設定名為 `adventure-works.com` 的已驗證網域。
- 已建立將 `*.adventure-works.com` 指向 `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` 的 **CNAME** 項目。

遵循[記載步驟](application-proxy-add-on-premises-application.md)，您會在租用戶中建立新的應用程式 Proxy 應用程式。 在此範例中，萬用字元是在下列欄位：

- 內部 URL：

    ![範例:內部網址的通配子](./media/application-proxy-wildcard/42.png)

- 外部 URL：

    ![範例:外部網址的通配子](./media/application-proxy-wildcard/43.png)

- 內部應用程式 SPN：

    ![範例:SPN 設定中的通配子](./media/application-proxy-wildcard/44.png)

藉由發行萬用字元應用程式，您現在可以透過瀏覽至您使用的 URL 來存取三個應用程式 (例如，`travel.adventure-works.com`)。

設定會實作下列結構：

![顯示範例設定的結構](./media/application-proxy-wildcard/05.png)

| Color | 描述 |
| ---   | ---         |
| 藍色  | 應用程式在 Azure 門戶中顯式發佈和可見。 |
| 灰色  | 可以透過父代應用程式存取的應用程式。 |

## <a name="scenario-2-general-wildcard-application-with-exception"></a>案例 2：一般萬用字元應用程式具有例外狀況

在此案例中，您在三個一般應用程式以外還有另一個應用程式，`finance.adventure-works.com`，而它只能讓財務部門存取。 使用目前的應用程式結構，財務應用程式可透過萬用字元應用程式讓所有員工存取。 若要變更這種情況，您從萬用字元排除應用程式，方法是將「財務」設為具有更嚴格權限的個別應用程式。

您必須確定 CNAME 記錄存在且將 `finance.adventure-works.com` 指向應用程式特定端點，在應用程式的應用程式 Proxy 分頁上指定。 對於此案例，將 `finance.adventure-works.com` 指向 `https://finance-awcycles.msappproxy.net/`。

遵循[記載步驟](application-proxy-add-on-premises-application.md)，此案例需要下列設定：

- 在 [內部 URL]**** 中，您設定 **finance** 而不是萬用字元。

    ![範例:在內部網址中設定財務而不是通配子](./media/application-proxy-wildcard/52.png)

- 在 [外部 URL]**** 中，您設定 **finance** 而不是萬用字元。

    ![範例:在外部網址中設定財務而不是通配子](./media/application-proxy-wildcard/53.png)

- 在內部應用程式 SPN中，您設定 **finance** 而不是萬用字元。

    ![範例:在 SPN 設定中設定財務而不是通配子](./media/application-proxy-wildcard/54.png)

設定會實作下列案例：

![顯示範例方案實現的設定](./media/application-proxy-wildcard/09.png)

因為 `finance.adventure-works.com` 是比 `*.adventure-works.com` 更特定的 URL，所以優先順序較高。 瀏覽至 `finance.adventure-works.com` 的使用者具有在「財務資源」應用程式中指定的體驗。 在此情況下，只有財務員工可以存取 `finance.adventure-works.com`。

如果您已針對財務發行多個應用程式，且您有 `finance.adventure-works.com` 作為已驗證網域，您可以發行另一個萬用字元應用程式 `*.finance.adventure-works.com`。 因為它比一般 `*.adventure-works.com` 更特定，如果使用者在財務網域中存取應用程式，它的優先順序較高。

## <a name="next-steps"></a>後續步驟

- 要瞭解有關**自訂網域**的更多資訊,請參閱[在 Azure AD 應用程式代理中使用自訂網域](application-proxy-configure-custom-domain.md)。
- 要瞭解有關**發佈應用程式**的更多資訊,請參閱[使用 Azure AD 應用程式代理發佈應用程式](application-proxy-add-on-premises-application.md)
