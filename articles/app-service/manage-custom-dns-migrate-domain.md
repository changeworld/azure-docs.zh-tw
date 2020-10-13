---
title: 遷移有效的 DNS 名稱
description: 了解如何在完全不停機的情況下，將已指派給即時網站的自訂 DNS 網域名稱移轉至 Azure App Service。
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.topic: article
ms.date: 08/25/2020
ms.custom: seodec18
ms.openlocfilehash: e1b50675bef0f883ff617b3098a742d3491b3c13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89484288"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>將作用中的 DNS 名稱移轉至 Azure App Service

本文示範如何在完全不停機的情況下，將作用中的 DNS 名稱移轉至 [Azure App Service](../app-service/overview.md)。

當您將即時網站及其 DNS 網域名稱移轉至 App Service 時，該 DNS 名稱已對即時流量提供服務。 您可以先將作用中的 DNS 名稱繫結至 App Service 應用程式，以避免 DNS 解析在移轉期間發生停機。

如果您不擔心 DNS 解析發生停機，請參閱[將現有的自訂 DNS 名稱對應至 Azure App Service](app-service-web-tutorial-custom-domain.md)。

## <a name="prerequisites"></a>必要條件

若要完成本操作說明：

- [確定您的 App Service 應用程式不是位於免費層](app-service-web-tutorial-custom-domain.md#checkpricing)。

## <a name="bind-the-domain-name-preemptively"></a>事先繫結網域名稱

當您系結自訂網域事先時，您可以在對現有 DNS 記錄進行任何變更之前完成下列兩項：

- 確認網域擁有權
- 為您的應用程式啟用網域名稱

當您最後將自訂 DNS 名稱從舊網站移轉至 App Service 應用程式時，DNS 解析不會發生停機。

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="get-domain-verification-id"></a>取得網域驗證識別碼

遵循 [取得網域驗證識別碼](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id)中的步驟，取得您應用程式的網域驗證識別碼。

### <a name="create-domain-verification-record"></a>建立網域驗證記錄

若要確認網域擁有權，請新增 TXT 記錄以進行網域驗證。 TXT 記錄的主機名稱取決於您要對應的 DNS 記錄類型類型。 請參閱下表 (`@` 通常代表根域) ：

| DNS 記錄範例 | TXT 主機 | TXT 值 |
| - | - | - |
| \@ (root) | _asuid_ | [應用程式的網域驗證識別碼](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |
| www (子網域) | _asuid www_ | [應用程式的網域驗證識別碼](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |
| \* (萬用字元) | _asuid_ | [應用程式的網域驗證識別碼](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |

在您的 DNS 記錄頁面中，記下您要移轉之 DNS 名稱的記錄類型。 App Service 支援 CNAME 與 A 記錄之間的對應。

> [!NOTE]
> 萬用字元 `*` 記錄不會使用現有 CNAME 記錄驗證子網域。 您可能需要為每個子網域明確地建立 TXT 記錄。

### <a name="enable-the-domain-for-your-app"></a>為您的應用程式啟用網域

1. 在 [Azure 入口網站](https://portal.azure.com)之應用程式頁面的左側導覽中，選取 [自訂網域]****。 

    ![[自訂網域] 功能表](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. 在 [ **自訂網域** ] 頁面中，選取 [ **新增自訂網域**]。

    ![新增主機名稱](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. 輸入您想要遷移的完整功能變數名稱，其對應至您所建立的 TXT 記錄，例如 `contoso.com` 、 `www.contoso.com` 或 `*.contoso.com` 。 選取 [驗證]。

    [新增自訂網域] 按鈕會啟用。 

1. 確定將 [主機名稱記錄類型]**** 設定為您要移轉的 DNS 記錄類型。 選取 [新增主機名稱]  。

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

在 [自訂網域] 頁面中，複製應用程式的 IP 位址。

![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>更新 DNS 記錄

回到您網域提供者的 DNS 記錄頁面，選取要重新對應的 DNS 記錄。

在 `contoso.com` 根網域範例中，重新對應 A 或 CNAME 記錄，如下表中的範例所示： 

| FQDN 範例 | 記錄類型 | Host | 值 |
| - | - | - | - |
| contoso.com (根網域) | A | `@` | 來自[複製應用程式的 IP 位址](#info)的 IP 位址 |
| www \. contoso.com (子)  | CNAME | `www` | _&lt;appname>. azurewebsites.net_ |
| \*.contoso.com (萬用字元) | CNAME | _\*_ | _&lt;appname>. azurewebsites.net_ |

儲存您的設定。

DNS 查詢應該會在 DNS 散佈發生後立即開始解析為 App Service 應用程式。

## <a name="migrate-domain-from-another-app"></a>從另一個應用程式遷移網域

您可以在 Azure、訂用帳戶之間或相同訂用帳戶內遷移使用中的自訂網域。 不過，這類不需要停機的遷移需要來源應用程式和目標應用程式在特定時間被指派給相同的自訂網域。 因此，您必須確定這兩個應用程式不會部署到相同的部署單位 (在內部稱為網路空間) 。 功能變數名稱只能指派給每個部署單位中的一個應用程式。

您可以查看 FTP/S URL 的功能變數名稱，找到應用程式的部署單位 `<deployment-unit>.ftp.azurewebsites.windows.net` 。 請檢查並確定來源應用程式和目標應用程式之間的部署單位不同。 應用程式的部署單位取決於它所在的 [App Service 方案](overview-hosting-plans.md) 。 當您建立方案時，Azure 會隨機選取它，而且無法變更。 Azure 只會在您于 [相同的資源群組 *和* 相同區域中建立](app-service-plan-manage.md#create-an-app-service-plan)兩個方案時，確保兩個方案都位於相同的部署單位，但它沒有任何邏輯可確保方案處於不同的部署單位。 在不同的部署單位中建立方案的唯一方式，是在您取得不同的部署單位之前，繼續在新的資源群組或區域中建立方案。

## <a name="next-steps"></a>後續步驟

瞭解如何將自訂的 TLS/SSL 憑證系結至 App Service。

> [!div class="nextstepaction"]
> [使用 Azure App Service 中的 TLS 系結來保護自訂 DNS 名稱](configure-ssl-bindings.md)
