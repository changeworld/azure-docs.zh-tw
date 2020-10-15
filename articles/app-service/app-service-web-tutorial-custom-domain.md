---
title: 教學課程：對應現有的自訂 DNS 名稱
description: 學習如何將現有的自訂 DNS 網域名稱 (虛名網域) 新增至 Azure App Service 中的 Web 應用程式、行動裝置應用程式後端或 API 應用程式。
keywords: 應用程式服務, Azure 應用程式服務, 網域對應, 網域名稱, 現有的網域, 主機名稱
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18
ms.openlocfilehash: 6a74f105525ec8ce28559b47ed4fc9624f518a06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89488332"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>教學課程：將現有的自訂 DNS 名稱對應至 Azure App Service

[Azure App Service](overview.md) 可提供可高度擴充、自我修復的 Web 主控服務。 本教學課程說明如何將現有的自訂網域名稱系統 (DNS) 名稱對應至 App Service。

![顯示 Azure 入口網站導覽至 Azure 應用程式的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 使用 CNAME 記錄來對應子網域 (例如，`www.contoso.com`)。
> * 使用 A 記錄來對應根網域 (例如，`contoso.com`)。
> * 使用 CNAME 記錄來對應萬用字元網域 (例如，`*.contoso.com`)。
> * 將預設 URL 重新導向至自訂目錄。
> * 使用指令碼來自動對應網域。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程：

* [建立 App Service 應用程式](./index.yml)，或使用您針對另一個教學課程建立的應用程式。
* 購買網域名稱，並確定您擁有網域提供者 (例如 GoDaddy) 之 DNS 登錄的存取權。

  例如，若要為 `contoso.com` 和 `www.contoso.com` 新增 DNS 項目，您必須有權設定 `contoso.com` 根網域的 DNS 設定。

  > [!NOTE]
  > 如果您沒有現有的網域名稱，請考慮[使用 Azure 入口網站購買網域](manage-custom-dns-buy-domain.md)。

## <a name="prepare-the-app"></a>準備應用程式

若要將自訂 DNS 名稱對應至 Web 應用程式，Web 應用程式的 [App Service 方案](https://azure.microsoft.com/pricing/details/app-service/)必須是付費層 (若為 Azure Functions，則為 [共用]、[基本]、[標準]、[進階] 或 [取用])。 在此步驟中，您要確定 App Service 應用程式位於支援的定價層。

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="sign-in-to-azure"></a>登入 Azure

開啟 [Azure 入口網站](https://portal.azure.com)並使用您的 Azure 帳戶登入。

### <a name="select-the-app-in-the-azure-portal"></a>在 Azure 入口網站中選取應用程式

1. 搜尋並選取 [應用程式服務]。

   ![顯示選取 App Service 的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. 在 [應用程式服務] 頁面上，選取您的 Azure 應用程式名稱。

   ![顯示入口網站導覽至 Azure 應用程式的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/select-app.png)

您看到 App Service 應用程式的管理分頁。

<a name="checkpricing" aria-hidden="true"></a>

### <a name="check-the-pricing-tier"></a>檢查定價層

1. 在應用程式分頁的左側窗格中，捲動到 [設定] 區段，然後選取 [擴大 (App Service 方案)]。

   ![顯示 [擴大 (App Service 方案)] 功能表的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. 會以藍色框線醒目顯示應用程式目前的層。 請檢查以確定您的應用程式不是位於 **F1** 層。 **F1** 層不支援自訂 DNS。

   ![顯示建議定價層的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. 如果 App Service 方案不是位於 **F1** 層，請關閉 [擴大] 分頁，然後跳至 [對應 CNAME 記錄](#map-a-cname-record)。

<a name="scaleup" aria-hidden="true"></a>

### <a name="scale-up-the-app-service-plan"></a>擴大 App Service 方案

1. 選取任何非免費層 (**D1**、**B1**、**B2**、**B3** 或「生產」類別中的任何一層)。 如需其他選項，請選取 [查看其他選項]。

1. 選取 [套用]。

   ![顯示檢查定價層的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   當您看見下列通知時，表示擴充作業已完成。

   ![顯示擴充作業確認的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname" aria-hidden="true"></a>

## <a name="get-a-domain-verification-id"></a>取得網域驗證識別碼

若要將自訂網域新增至您的應用程式，您必須將驗證識別碼新增為包含網域提供者的 TXT 記錄，以驗證網域的擁有權。 在應用程式頁面的左側窗格中，選取 [自訂網域]。 在下一個步驟的 [自訂網域] 頁面中，複製 [自訂網域驗證識別碼] 方快中的識別碼。

![在 [自訂網域驗證識別碼] 方塊中顯示識別碼的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

> [!WARNING]
> 將網域驗證識別碼新增到您的自訂網域，可以防止無關聯的 DNS 項目並有助於避免子網域接管。 如需有關此常見高嚴重性威脅的詳細資訊，請參閱[子網域接管](../security/fundamentals/subdomain-takeover.md)。

## <a name="map-your-domain"></a>對應您的網域

您可以使用 CNAME 記錄或 A 記錄將自訂 DNS 名稱對應至 App Service。 請依照個別步驟操作：

- [對應 CNAME 記錄](#map-a-cname-record)
- [對應 A 記錄](#map-an-a-record)
- [對應萬用字元網域 (使用 CNAME 記錄)](#map-a-wildcard-domain)

> [!NOTE]
> 您應對所有自訂 DNS 名稱使用 CNAME 記錄，但根網域除外 (例如 `contoso.com`)。 對於根網域，請使用 A 記錄。

### <a name="map-a-cname-record"></a>對應 CNAME 記錄

在教學課程範例中，您新增 `www` 子網域 (例如，`www.contoso.com`) 的 CNAME 記錄。

如果您有 `www` 以外的子網域，請以您的子網域取代 `www` (例如，如果您的自訂網域為 `sub.constoso.com`，請使用 `sub`)。

#### <a name="access-dns-records-with-a-domain-provider"></a>存取網域提供者中的 DNS 記錄

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>建立 CNAME 記錄

將子網域對應到應用程式的預設網域名稱 (`<app-name>.azurewebsites.net`，其中 `<app-name>` 是您的應用程式的名稱)。 若要建立 `www` 子網域的 CNAME 對應，請建立兩個記錄：

| 記錄類型 | Host | 值 | 註解 |
| - | - | - |
| CNAME | `www` | `<app-name>.azurewebsites.net` | 對應本身的網域。 |
| TXT | `asuid.www` | [您稍早所獲得的驗證識別碼](#get-a-domain-verification-id) | App Service 會存取 `asuid.<subdomain>` TXT 記錄，以驗證自訂網域的擁有權。 |

新增 CNAME 和 TXT 記錄之後，DNS 記錄分頁看起來如下列範例所示：

![顯示入口網站導覽至 Azure 應用程式的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/cname-record.png)

#### <a name="enable-the-cname-record-mapping-in-azure"></a>在 Azure 中啟用 CNAME 記錄對應

1. 在 Azure 入口網站之應用程式分頁的左側窗格中，選取 [自訂網域]。

    ![顯示 [自訂網域] 功能表的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. 在應用程式的 [自訂網域] 頁面上，於清單中新增自訂的完整 DNS 名稱 (`www.contoso.com`)。

1. 選取 [新增自訂網域]。

    ![顯示 [新增主機名稱] 項目的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. 輸入您新增 CNAME 記錄的完整網域名稱，例如 `www.contoso.com`。

1. 選取 [驗證]。 [新增自訂網域] 頁面隨即出現。

1. 確定 [主機名稱記錄類型] 已設定為 [CNAME (www\.example.com 或任何子網域)]。 選取 [新增自訂網域]。

    ![顯示 [新增自訂網域] 按鈕的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    可能需要一些時間，新的自訂網域才會反映在應用程式的 [自訂網域] 頁面中。 重新整理瀏覽器以更新資料。

    ![顯示新增 CNAME 記錄的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > 自訂網域的警告標籤表示其尚未繫結至 TLS/SSL 憑證。 任何從瀏覽器到自訂網域的 HTTPS 要求都會收到錯誤或警告 (視瀏覽器而定)。 若要新增 TLS 繫結，請參閱[在 Azure App Service 中使用 TLS/SSL 繫結保護自訂 DNS 名稱](configure-ssl-bindings.md)。

    如果稍早錯過某個步驟或在某處打錯字，頁面底部會出現驗證錯誤。

    ![顯示驗證錯誤的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

### <a name="map-an-a-record"></a>對應 A 記錄

在教學課程範例中，您新增根網域 (例如，`contoso.com`) 的 A 記錄。

<a name="info"></a>

#### <a name="copy-the-apps-ip-address"></a>複製應用程式的 IP 位址

若要對應 A 記錄，您需要應用程式的外部 IP 位址。 您可以在 Azure 入口網站中應用程式的 [自訂網域] 頁面上找到這個 IP 位址。

1. 在 Azure 入口網站之應用程式分頁的左側窗格中，選取 [自訂網域]。

   ![顯示 [自訂網域] 功能表的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. 在 [自訂網域] 頁面上，複製應用程式的 IP 位址。

   ![顯示入口網站導覽至 Azure 應用程式的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

#### <a name="access-dns-records-with-a-domain-provider"></a>存取網域提供者中的 DNS 記錄

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-a-record"></a>建立 A 記錄

若要將 A 記錄對應至應用程式 (通常是根網域)，請建立兩筆記錄：

| 記錄類型 | Host | 值 | 註解 |
| - | - | - |
| A | `@` | 來自[複製應用程式的 IP 位址](#info)的 IP 位址 | 對應本身的網域 (`@` 通常代表根網域)。 |
| TXT | `asuid` | [您稍早所獲得的驗證識別碼](#get-a-domain-verification-id) | App Service 會存取 `asuid.<subdomain>` TXT 記錄，以驗證自訂網域的擁有權。 若為根網域，請使用 `asuid`。 |

> [!NOTE]
> 若要使用 A 記錄而非建議的 [CNAME 記錄](#map-a-cname-record)來新增子網域 (像是 `www.contoso.com`)，您的 A 記錄和 TXT 記錄看起來應該如下表所示：
>
> | 記錄類型 | Host | 值 |
> | - | - | - |
> | A | `www` | 來自[複製應用程式的 IP 位址](#info)的 IP 位址 |
> | TXT | `asuid.www` | `<app-name>.azurewebsites.net` |
>

新增記錄之後，DNS 記錄分頁看起來如下列範例所示：

![顯示 DNS 記錄頁面的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a" aria-hidden="true"></a>

#### <a name="enable-the-a-record-mapping-in-the-app"></a>在應用程式中啟用 A 記錄對應

回到 Azure 入口網站中的應用程式 [自訂網域] 分頁，在清單中新增自訂的完整 DNS 名稱 (例如，`contoso.com`)。

1. 選取 [新增自訂網域]。

    ![顯示新增主機名稱的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. 輸入您設定 A 記錄的完整網域名稱，例如 `contoso.com`。 

1. 選取 [驗證]。 [新增自訂網域] 頁面隨即顯示。

1. 請確定 [主機名稱記錄類型] 設為 [A 記錄 (example.com)]。 選取 [新增自訂網域]。

    ![顯示將 DNS 名稱新增至應用程式的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    可能需要一些時間，新的自訂網域才會反映在應用程式的 [自訂網域] 頁面中。 重新整理瀏覽器以更新資料。

    ![顯示新增 A 記錄的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    > [!NOTE]
    > 自訂網域的警告標籤表示其尚未繫結至 TLS/SSL 憑證。 任何從瀏覽器到自訂網域的 HTTPS 要求都會收到錯誤或警告 (視瀏覽器而定)。 若要新增 TLS 繫結，請參閱[在 Azure App Service 中使用 TLS/SSL 繫結保護自訂 DNS 名稱](configure-ssl-bindings.md)。
    
    如果稍早錯過某個步驟或在某處打錯字，頁面底部會出現驗證錯誤。
    
    ![顯示驗證錯誤的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

### <a name="map-a-wildcard-domain"></a>對應萬用字元網域

在教學課程範例中，您藉由新增 CNAME 記錄，將[萬用字元 DNS 名稱](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (例如，`*.contoso.com`) 對應至 App Service 應用程式。

#### <a name="access-dns-records-with-a-domain-provider"></a>存取網域提供者中的 DNS 記錄

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>建立 CNAME 記錄

將萬用字元名稱 `*` 對應到應用程式的預設網域名稱 (`<app-name>.azurewebsites.net`，其中 `<app-name>` 是您的應用程式的名稱)。 若要對應萬用字元名稱，請建立兩筆記錄：

| 記錄類型 | Host | 值 | 註解 |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | 對應本身的網域。 |
| TXT | `asuid` | [您稍早所獲得的驗證識別碼](#get-a-domain-verification-id) | App Service 會存取 `asuid` TXT 記錄，以驗證自訂網域的擁有權。 |

針對 `*.contoso.com` 網域範例，CNAME 記錄會將名稱 `*` 對應至 `<app-name>.azurewebsites.net`。

新增 CNAME 時，DNS 記錄分頁看起來如下列範例所示：

![顯示導覽至 Azure 應用程式的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

#### <a name="enable-the-cname-record-mapping-in-the-app"></a>在應用程式中啟用 CNAME 記錄對應

您現在可以將符合萬用字元名稱的任何子網域新增至應用程式 (例如，`sub1.contoso.com`、`sub2.contoso.com` 和 `*.contoso.com` 均符合 `*.contoso.com`)。

1. 在 Azure 入口網站之應用程式分頁的左側窗格中，選取 [自訂網域]。

    ![顯示 [自訂網域] 功能表的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. 選取 [新增自訂網域]。

    ![顯示新增主機名稱的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. 輸入符合萬用字元網域的完整網域名稱 (例如，`sub1.contoso.com`)，然後選取 [驗證]。

    [新增自訂網域] 按鈕會啟用。

1. 確定 [主機名稱記錄類型] 已設定為 [CNAME 記錄 (www\.example.com 或任何子網域)]。 選取 [新增自訂網域]。

    ![顯示將 DNS 名稱新增至應用程式的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    可能需要一些時間，新的自訂網域才會反映在應用程式的 [自訂網域] 頁面中。 重新整理瀏覽器以更新資料。

1. 再次選取 **+** 圖示，以新增另一個與萬用字元網域相符的自訂網域。 例如，新增 `sub2.contoso.com`。

    ![顯示新增 CNAME 記錄的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard-2.png)

    > [!NOTE]
    > 自訂網域的警告標籤表示其尚未繫結至 TLS/SSL 憑證。 任何從瀏覽器到自訂網域的 HTTPS 要求都會收到錯誤或警告 (視瀏覽器而定)。 若要新增 TLS 繫結，請參閱[在 Azure App Service 中使用 TLS/SSL 繫結保護自訂 DNS 名稱](configure-ssl-bindings.md)。
    
## <a name="test-in-a-browser"></a>在瀏覽器中測試

瀏覽至您稍早設定的 DNS 名稱 (例如 `contoso.com`、`www.contoso.com`、`sub1.contoso.com` 和 `sub2.contoso.com`)。

![顯示導覽至 Azure 應用程式的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-not-found"></a>解析 404「找不到」

如果您在瀏覽自訂網域 URL 時收到 HTTP 404 (找不到) 錯誤，請使用 <a href="https://www.whatsmydns.net/" target="_blank">WhatsmyDNS.net</a> 確認網域是否能解析成應用程式的 IP 位址。 如果不能，則可能會因為下列其中一個原因而發生：

- 設定的自訂網域遺漏 A 記錄或 CNAME 記錄。
- 瀏覽器用戶端已將網域的舊 IP 位址加入快取。 請清除快取，然後再次測試 DNS 解析。 在 Windows 電腦上，您可以使用 `ipconfig /flushdns` 清除快取。

<a name="virtualdir" aria-hidden="true"></a>

## <a name="migrate-an-active-domain"></a>移轉作用中的網域

若要將即時網站及其 DNS 網域名稱遷移至 App Service 而不要停機，請參閱[將作用中的 DNS 名稱遷移至 Azure App Service](manage-custom-dns-migrate-domain.md)。

## <a name="redirect-to-a-custom-directory"></a>重新導向至自訂目錄

根據預設，App Service 會將 Web 要求導向應用程式程式碼的根目錄。 但是某些 Web 架構並非從根目錄開始。 例如，[Laravel](https://laravel.com/) 從`public` 子目錄開始。 若要繼續 `contoso.com` DNS 範例，這類應用程式可在 `http://contoso.com/public` 上存取，但您會想要將 `http://contoso.com` 改為導向 `public` 目錄。 這個步驟與 DNS 解析無關，反而與自訂虛擬目錄有關。

若要自訂虛擬目錄，請在 Web 應用程式頁面的左窗格中選取 [應用程式設定]。

在頁面底部，根虛擬目錄 `/` 預設指向 `site\wwwroot`，這是應用程式程式碼的根目錄。 例如，請將其變更為指向 `site\wwwroot\public`，然後再儲存變更。

![顯示自訂虛擬目錄的螢幕擷取畫面。](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

在作業完成後，應用程式應該會傳回位於根路徑 (如 `http://contoso.com`) 的正確頁面。

## <a name="automate-with-scripts"></a>使用指令碼進行自動化

您可以使用 [Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/)，透過指令碼將自訂網域的管理作業自動化。

### <a name="azure-cli"></a>Azure CLI

下列命令會在 App Service 應用程式中新增所設定的自訂 DNS 名稱。

```bash 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

如需詳細資訊，請參閱[將自訂網域對應至 Web 應用程式](scripts/cli-configure-custom-domain.md)。

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

下列命令會在 App Service 應用程式中新增所設定的自訂 DNS 名稱。

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

如需詳細資訊，請參閱[將自訂網域指派給 Web 應用程式](scripts/powershell-configure-custom-domain.md)。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 使用 CNAME 記錄來對應子網域。
> * 使用 A 記錄來對應根網域。
> * 使用 CNAME 記錄來對應萬用字元網域。
> * 將預設 URL 重新導向至自訂目錄。
> * 使用指令碼來自動對應網域。

繼續進行下一個教學課程，以了解如何將自訂 TLS/SSL 憑證繫結至 Web 應用程式。

> [!div class="nextstepaction"]
> [在 Azure App Service 中使用 TLS/SSL 繫結保護自訂 DNS 名稱](configure-ssl-bindings.md)
