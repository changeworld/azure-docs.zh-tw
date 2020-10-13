---
title: 使用 Azure DNS 別名記錄和 Azure App Service 的自訂網域驗證來防止子域接管
description: 瞭解如何避免子域接管的常見高嚴重性威脅
services: security
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2020
ms.author: memildin
ms.openlocfilehash: bde4b21f9dfff62ef43afc9c9d8e5a858631d304
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91447367"
---
# <a name="prevent-dangling-dns-entries-and-avoid-subdomain-takeover"></a>防止無關聯的 DNS 專案，並避免子域接管

本文說明子域接管的一般安全性威脅，以及您可以採取的步驟來減輕風險。


## <a name="what-is-subdomain-takeover"></a>什麼是子域接管？

子域接管是常見的高嚴重性威脅，適用于定期建立和刪除許多資源的組織。 當您有指向取消布建 Azure 資源的 [DNS 記錄](https://docs.microsoft.com/azure/dns/dns-zones-records#dns-records) 時，就會發生子域接管。 這類 DNS 記錄也稱為「無關聯 DNS」專案。 CNAME 記錄特別容易受到這種威脅。 子域接管可讓惡意執行者將組織網域的流量，重新導向至執行惡意活動的網站。

子域接管的常見案例：

1. **創造：**

    1. 您可以使用 (FQDN) 的完整功能變數名稱來布建 Azure 資源 `app-contogreat-dev-001.azurewebsites.net` 。

    1. 您可以使用將 `greatapp.contoso.com` 流量路由傳送至 Azure 資源的子域，在 DNS 區域中指派 CNAME 記錄。

1. **解除**

    1. 不再需要 Azure 資源之後，就會取消布建或刪除它。 
    
        此時， `greatapp.contoso.com` *應* 從您的 DNS 區域中移除 CNAME 記錄。 如果未移除 CNAME 記錄，則會將其公告為作用中網域，但不會將流量路由傳送至使用中的 Azure 資源。 這是「無關聯的」 DNS 記錄的定義。

    1. 無關聯的子域 `greatapp.contoso.com` 現在很容易受到攻擊，而且可以藉由指派給另一個 Azure 訂用帳戶的資源來進行。

1. **收購：**

    1. 使用常用的方法和工具，威脅執行者會探索無關聯子域。  

    1. 威脅執行者會布建具有您先前所控制資源之相同 FQDN 的 Azure 資源。 在此範例中是 `app-contogreat-dev-001.azurewebsites.net`。

    1. 傳送至子域的流量 `myapp.contoso.com` 現在會路由傳送至惡意執行者的資源，以控制其內容。



![從取消布建網站進行子域接管](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-subdomain-takeover"></a>子域接管的風險

當 DNS 記錄指向無法使用的資源時，記錄本身應該已經從您的 DNS 區域中移除。 如果未刪除，則為「無關聯 DNS」記錄，並可建立子域接管的可能性。

無關聯的 DNS 專案可讓威脅執行者控制相關聯的 DNS 名稱，以裝載惡意網站或服務。 組織子域上的惡意網頁和服務可能會導致：

- **遺失子域內容的控制權** -負的，請將您組織無法保護其內容的安全，以及品牌損毀和信任喪失。

- **從不受信任的訪客搜集 Cookie** -web apps 通常會將會話 cookie 公開給子域 ( *. contoso.com) ，因此任何子域都可以存取它們。 威脅執行者可以使用子域接管來建立看似美觀的頁面、誘騙不安全的使用者進行流覽，以及搜集 cookie (甚至是安全的 cookie) 。 常見的誤解是，使用 SSL 憑證可保護您的網站和使用者的 cookie，而不是接管。 不過，威脅執行者可以使用被攔截的子域來申請和接收有效的 SSL 憑證。 有效的 SSL 憑證會授與他們安全 cookie 的存取權，並可進一步提高惡意網站的認知合法性。

- **網路釣魚行銷活動** -可能會在網路釣魚活動中使用真正看起來的子域。 這適用于惡意網站和 MX 記錄，讓威脅執行者能夠接收發給已知安全品牌合法子域的電子郵件。

- **進一步的風險** ：惡意網站可能會用來呈報至其他傳統攻擊，例如 XSS、CSRF、CORS 略過等等。



## <a name="identify-dangling-dns-entries"></a>識別無關聯的 DNS 專案

若要識別組織內可能是無關聯的 DNS 專案，請使用 Microsoft 的 GitHub 託管 PowerShell 工具 ["DanglingDnsRecords"](https://aka.ms/DanglingDNSDomains)。

此工具可協助 Azure 客戶使用與訂用帳戶或租使用者上建立的現有 Azure 資源相關聯的 CNAME 來列出所有網域。

如果您的 Cname 位於其他 DNS 服務並指向 Azure 資源，請在該工具的輸入檔中提供 Cname。

此工具支援下表所列的 Azure 資源。 此工具會將所有租使用者的 Cname 解壓縮或做為輸入。


| 服務                   | 類型                                        | FQDNproperty                               | 範例                         |
|---------------------------|---------------------------------------------|--------------------------------------------|---------------------------------|
| Azure Front Door          | microsoft.network/frontdoors                | 屬性 cName                           | `abc.azurefd.net`               |
| Azure Blob 儲存體        | microsoft.storage/storageaccounts           | >primaryendpoints.blob blob           | `abc. blob.core.windows.net`    |
| Azure CDN                 | microsoft.cdn/profiles/endpoints            | properties。 hostName                        | `abc.azureedge.net`             |
| 公用 IP 位址       | microsoft.network/publicipaddresses         | dnsSettings. fqdn                | `abc.EastUs.cloudapp.azure.com` |
| Azure 流量管理員     | microsoft.network/trafficmanagerprofiles    | 屬性。                  | `abc.trafficmanager.net`        |
| Azure 容器執行個體  | microsoft.containerinstance/containergroups | 屬性 ipAddress. fqdn                  | `abc.EastUs.azurecontainer.io`  |
| Azure API 管理      | microsoft.apimanagement/service             | hostnameConfigurations。 hostName | `abc.azure-api.net`             |
| Azure App Service         | microsoft.web/sites                         | defaultHostName                 | `abc.azurewebsites.net`         |
| Azure App Service 插槽 | microsoft.web/sites/slots                   | defaultHostName                 | `abc-def.azurewebsites.net`     |



### <a name="prerequisites"></a>必要條件

以具有下列許可權的使用者身分執行查詢：

- 至少讀者層級的 Azure 訂用帳戶存取權
- Azure resource graph 的讀取權限

如果您是組織租使用者的全域管理員，請使用提高 [存取權以管理所有 Azure 訂用帳戶和管理群組](../../role-based-access-control/elevate-access-global-admin.md)的指引，來提升您的帳戶以存取您組織的所有訂用帳戶。


> [!TIP]
> 如果您有大型的 Azure 環境，Azure Resource Graph 有節流和分頁限制。 
> 
> [深入瞭解如何使用大型的 Azure 資源資料集](../../governance/resource-graph/concepts/work-with-data.md)。
> 
> 此工具會使用訂用帳戶批次處理來避免這些限制。

### <a name="run-the-script"></a>執行指令碼

深入瞭解 PowerShell 腳本、 **Get-DanglingDnsRecords.ps1**，並從 GitHub 下載： https://aka.ms/DanglingDNSDomains 。

## <a name="remediate-dangling-dns-entries"></a>補救無關聯的 DNS 專案 

檢查您的 DNS 區域，並找出無關聯或已移除的 CNAME 記錄。 如果您發現子域是無關聯或已接管，請使用下列步驟來移除易受攻擊的子域並減輕風險：

1. 從您的 DNS 區域中，移除所有指向已不再布建之資源 Fqdn 的 CNAME 記錄。

1. 若要讓流量路由傳送至您控制項中的資源，請使用在無關聯子域的 CNAME 記錄中指定的 Fqdn 布建額外的資源。

1. 請參閱您的應用程式程式碼，以參考特定子域，並更新任何不正確或過時的子域參考。

1. 調查是否發生任何入侵，並根據您組織的事件回應程式採取行動。 以下是調查此問題的秘訣和最佳作法。

    如果您的應用程式邏輯是將 OAuth 認證之類的秘密傳送到無關聯子域，或將隱私權機密資訊傳送到無關聯的子域，則該資料可能已經公開給協力廠商。

1. 瞭解當資源取消布建時，不會從您的 DNS 區域中移除 CNAME 記錄的原因，並採取步驟以確保在未來取消布建 Azure 資源時，會適當地更新 DNS 記錄。


## <a name="prevent-dangling-dns-entries"></a>防止無關聯的 DNS 專案

確定您的組織已實行程式來防止無意義的 DNS 專案，而產生的子域接管是安全性程式很重要的一部分。

某些 Azure 服務提供的功能可協助建立預防措施，如下所述。 避免此問題的其他方法，必須透過您組織的最佳作法或標準作業程式來建立。


### <a name="use-azure-dns-alias-records"></a>使用 Azure DNS 別名記錄

Azure DNS 的 [別名記錄](../../dns/dns-alias.md#scenarios) 可讓 DNS 記錄的生命週期與 Azure 資源結合，以防止無關聯的參考。 例如，限定為別名記錄以指向公用 IP 位址或流量管理員設定檔的 DNS 記錄。 如果您刪除這些基礎資源，則 DNS 別名記錄會變成空的記錄集。 它不再參考已刪除的資源。 請務必注意，您可以使用別名記錄來保護它的限制。 目前，此清單僅限於：

- Azure Front Door
- 流量管理員設定檔
- Azure 內容傳遞網路 (CDN) 端點
- 公用 IP

雖然現今的服務供應專案有限，但建議您盡可能使用別名記錄來防止子域接管。

[深入瞭解 Azure DNS 別名記錄的功能](../../dns/dns-alias.md#capabilities)。



### <a name="use-azure-app-services-custom-domain-verification"></a>使用 Azure App Service 的自訂網域驗證

建立 Azure App Service 的 DNS 專案時，請建立 asuid。子域具有網域驗證識別碼的 TXT 記錄。 當這類 TXT 記錄存在時，任何其他 Azure 訂用帳戶都無法驗證自訂網域，也就是將它移出。 

這些記錄不會防止他人以您的 CNAME 專案中的相同名稱建立 Azure App Service。 如果沒有能夠證明功能變數名稱的擁有權，威脅執行者就無法接收流量或控制內容。

[深入瞭解如何將現有的自訂 DNS 名稱對應至 Azure App Service](../../app-service/app-service-web-tutorial-custom-domain.md)。



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>建立並自動化流程，以降低威脅

開發人員和營運團隊通常會執行清除程式，以避免發生無關聯的 DNS 威脅。 下列作法有助於確保您的組織能夠避免遭受此威脅。 

- **建立預防的程式：**

    - 教育應用程式開發人員在刪除資源時，重新路由傳送位址。

    - 在解除委任服務時，將 [移除 DNS 專案] 放在必要的檢查清單上。

    - 將 [刪除鎖定](../../azure-resource-manager/management/lock-resources.md) 放置在具有自訂 DNS 專案的任何資源上。 刪除鎖定可作為指標，在取消布建資源之前，必須先移除對應。 這類量值僅適用于結合內部教育計畫時。

- **建立探索的程式：**

    - 定期檢查您的 DNS 記錄，以確保您的子域都已對應至 Azure 資源：

        - 存在-查詢指向 Azure 子域（例如 *. azurewebsites.net 或 *. cloudapp.azure.com）之資源的 DNS 區域， (查看) [的 azure 網域參考清單](azure-domains.md) 。
        - 您會擁有-確認您擁有 DNS 子域設為目標的所有資源。

    - 維護 Azure 完整功能變數名稱的服務類別目錄， (FQDN) 端點和應用程式擁有者。 若要建立您的服務類別目錄，請執行下列 Azure Resource Graph 查詢腳本。 此腳本會投射您有權存取之資源的 FQDN 端點資訊，並將其輸出于 CSV 檔案中。 如果您可以存取租使用者的所有訂用帳戶，腳本會考慮所有這些訂用帳戶，如下列範例腳本所示。 若要將結果限制為一組特定的訂用帳戶，請編輯腳本，如下所示。


- **建立補救程式：**
    - 當找到無關聯的 DNS 專案時，您的小組必須調查是否發生任何折衷。
    - 調查當資源解除委任時，位址未重新路由的原因。
    - 請刪除不再使用的 DNS 記錄，或將其指向您的組織所擁有的正確 Azure 資源 (FQDN) 。
 

## <a name="next-steps"></a>後續步驟

若要深入瞭解您可以用來防禦子域接管的相關服務和 Azure 功能，請參閱下列頁面。

- [防止使用 Azure DNS 的無關聯 DNS 記錄](../../dns/dns-alias.md#prevent-dangling-dns-records)

- [在 Azure App Service 中新增自訂網域時，請使用網域驗證識別碼](../../app-service/app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id)

- [快速入門：使用 Azure PowerShell 執行您的第一個 Resource Graph 查詢](../../governance/resource-graph/first-query-powershell.md)