---
title: 移轉活動 DNS 名稱
description: 了解如何在完全不停機的情況下，將已指派給即時網站的自訂 DNS 網域名稱移轉至 Azure App Service。
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.topic: article
ms.date: 10/21/2019
ms.custom: seodec18
ms.openlocfilehash: 5c1760c746aca439e19ab5727e5be02f6dbad3cb
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535684"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>將作用中的 DNS 名稱移轉至 Azure App Service

本文示範如何在完全不停機的情況下，將作用中的 DNS 名稱移轉至 [Azure App Service](../app-service/overview.md)。

當您將即時網站及其 DNS 網域名稱移轉至 App Service 時，該 DNS 名稱已對即時流量提供服務。 您可以先將作用中的 DNS 名稱繫結至 App Service 應用程式，以避免 DNS 解析在移轉期間發生停機。

如果您不擔心 DNS 解析發生停機，請參閱[將現有的自訂 DNS 名稱對應至 Azure App Service](app-service-web-tutorial-custom-domain.md)。

## <a name="prerequisites"></a>Prerequisites

若要完成本操作說明：

- [確定您的 App Service 應用程式不是位於免費層](app-service-web-tutorial-custom-domain.md#checkpricing)。

## <a name="bind-the-domain-name-preemptively"></a>事先繫結網域名稱

當您事先繫結自訂網域時，要完成下列兩項之後才能對 DNS 記錄進行任何變更︰

- 確認網域擁有權
- 為您的應用程式啟用網域名稱

當您最後將自訂 DNS 名稱從舊網站移轉至 App Service 應用程式時，DNS 解析不會發生停機。

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>建立網域驗證記錄

若要確認網域擁有權，請新增 TXT 記錄。 TXT 記錄會從 _awverify.&lt;子網域>_ 對應至 _&lt;應用程式名稱>.azurewebsites.net_。 

您需要的 TXT 記錄取決於您要移轉的 DNS 記錄。 如需範例，請參閱下表 (`@` 通常代表根網域)：

| DNS 記錄範例 | TXT 主機 | TXT 值 |
| - | - | - |
| \@ (root) | _驗證_ | _&lt;應用程式名稱>.azure 網站.net_ |
| www (子網域) | _awverify.www_ | _&lt;應用程式名稱>.azure 網站.net_ |
| \* (萬用字元) | _awverify.\*_ | _&lt;應用程式名稱>.azure 網站.net_ |

在您的 DNS 記錄頁面中，記下您要移轉之 DNS 名稱的記錄類型。 App Service 支援 CNAME 與 A 記錄之間的對應。

> [!NOTE]
> 對於某些提供者，例如 CloudFlare，`awverify.*` 不是有效的記錄。 改為只用 `*`。

> [!NOTE]
> 萬用字元 `*` 記錄不會使用現有 CNAME 記錄驗證子網域。 您可能需要為每個子網域明確地建立 TXT 記錄。


### <a name="enable-the-domain-for-your-app"></a>為您的應用程式啟用網域

在 [Azure 入口網站](https://portal.azure.com)之應用程式頁面的左側導覽中，選取 [自訂網域]****。 

![[自訂網域] 功能表](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

在 [自訂網域]**** 頁面中，選取 [新增主機名稱]**** 旁的 **+** 圖示。

![新增主機名稱](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

鍵入您新增 TXT 記錄的完整網域名稱，例如 `www.contoso.com`。 針對萬用字元網域 (例如 \*.contoso.com)，您可以使用符合萬用字元網域的任何 DNS 名稱。 

選取 [驗證]  。

[新增主機名稱]**** 按鈕會啟用。 

確定將 [主機名稱記錄類型]**** 設定為您要移轉的 DNS 記錄類型。

選取 [新增主機名稱]****。

![將 DNS 名稱新增至應用程式](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

可能需要一些時間，新的主機名稱才會反映在應用程式的 [自訂網域]**** 分頁中。 嘗試重新整理瀏覽器以更新資料。

![CNAME 記錄已新增](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

您的 DNS 名稱現已在您的 Azure 應用程式中啟用。 

## <a name="remap-the-active-dns-name"></a>重新對應作用中的 DNS 名稱

唯一剩下的工作是重新對應作用中的 DNS 記錄，以指向 App Service。 目前，它仍指向舊網站。

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>複製應用程式的 IP 位址 (僅限 A 記錄)

如果您重新對應的是 CNAME 記錄，請略過本節。 

若要重新對應 A 記錄，您需要 App Service 應用程式的外部 IP 位址，如 [自訂網域]**** 頁面中所示。

選取右上角的 **X** 關閉 [新增主機名稱]**** 頁面。 

在 [自訂網域]  頁面中，複製應用程式的 IP 位址。

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>更新 DNS 記錄

回到您網域提供者的 DNS 記錄頁面，選取要重新對應的 DNS 記錄。

在 `contoso.com` 根網域範例中，重新對應 A 或 CNAME 記錄，如下表中的範例所示： 

| FQDN 範例 | 記錄類型 | Host | 值 |
| - | - | - | - |
| contoso.com (根網域) | A | `@` | 來自[複製應用程式的 IP 位址](#info)的 IP 位址 |
| www\.contoso.com(子) | CNAME | `www` | _&lt;應用程式名稱>.azure 網站.net_ |
| \*.contoso.com (萬用字元) | CNAME | _\*_ | _&lt;應用程式名稱>.azure 網站.net_ |

儲存您的設定。

DNS 查詢應該會在 DNS 散佈發生後立即開始解析為 App Service 應用程式。

## <a name="active-domain-in-azure"></a>Azure 的活動域

您可以在 Azure 中、訂閱之間或同一訂閱中遷移活動自定義域。 但是,這種遷移而不停機需要源應用,並且目標應用在特定時間分配相同的自定義域。 因此,您需要確保兩個應用未部署到同一部署單元(內部稱為 Web 空間)。 每個部署單元中只能將功能變數名稱分配給一個應用。

您可以通過查看 FTP/S URL`<deployment-unit>.ftp.azurewebsites.windows.net`的功能變數名稱來查找應用的部署單元。 檢查並確保部署單元在源應用和目標應用之間不同。 應用的部署單元由應用[服務計劃](overview-hosting-plans.md)確定。 創建計劃時,Azure 會隨機選擇它,無法更改。 Azure 僅在[同一資源組*和*同一區域中創建](app-service-plan-manage.md#create-an-app-service-plan)兩個計劃時,才確保兩個計劃位於同一部署單元中,但它沒有任何邏輯可確保計劃位於不同的部署單元中。 在不同的部署單元中創建計劃的唯一方法是在新資源組或區域中繼續創建計劃,直到獲得不同的部署單元。

## <a name="next-steps"></a>後續步驟

瞭解如何將自定義 TLS/SSL 證書綁定到應用服務。

> [!div class="nextstepaction"]
> [使用 Azure 應用服務中的 TLS 繫結保護自訂 DNS 名稱](configure-ssl-bindings.md)
