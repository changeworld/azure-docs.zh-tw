---
title: 使用 Azure DNS 別名記錄和 Azure App Service 的自訂網域驗證來防止子域接管
description: 瞭解如何避免子域接管常見的高嚴重性威脅
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
ms.date: 06/23/2020
ms.author: memildin
ms.openlocfilehash: 3d63ccc2c47bca9410b5b9105b90aa1f0cf5854a
ms.sourcegitcommit: 14bf4129a73de2b51a575c3a0a7a3b9c86387b2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87439275"
---
# <a name="prevent-dangling-dns-entries-and-avoid-subdomain-takeover"></a>防止無關聯的 DNS 專案，並避免子域接管

本文說明子域接管的常見安全性威脅，以及您可以採取哪些步驟來減輕此問題。


## <a name="what-is-subdomain-takeover"></a>什麼是子域接管？

子域接管是常見的高嚴重性威脅，適用于定期建立和刪除許多資源的組織。 當您有指向取消布建 Azure 資源的 DNS 記錄時，就可能發生子域接管。 這類 DNS 記錄也稱為「無關聯 DNS」專案。 CNAME 記錄特別容易受到此威脅的影響。

子域接管的常見案例：

1. 隨即建立網站。 

    在此範例中是 `app-contogreat-dev-001.azurewebsites.net`。

1. CNAME 專案會新增至指向網站的 DNS。 

    在此範例中，已建立下列易記名稱： `greatapp.contoso.com` 。

1. 幾個月之後，就不再需要網站，因此在未刪除對應的 DNS 專案的**情況下**刪除它。 

    CNAME DNS 專案現在是「無關聯」。

1. 在網站刪除之後，威脅動作專案幾乎會立即探索遺失的網站，並在建立自己的網站 `app-contogreat-dev-001.azurewebsites.net` 。

    現在，適用于的流量會 `greatapp.contoso.com` 移至威脅執行者的 Azure 網站，而威脅執行者會控制所顯示的內容。 

    惡意的 DNS 遭到入侵，Contoso 的子域「GreatApp」是子域接管的受害者。 

![取消布建網站的子域接管](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-subdomain-takeover"></a>子域接管的風險

當 DNS 記錄指向無法使用的資源時，記錄本身應已從您的 DNS 區域中移除。 如果尚未刪除，它會是「無關聯的 DNS」記錄，並建立子域接管的可能性。

無關聯的 DNS 專案讓威脅執行者可以控制相關聯的 DNS 名稱，以裝載惡意網站或服務。 組織子域上的惡意頁面和服務可能會導致：

- **失去對子域內容的控制**-負面按下您的組織無法保護其內容，以及品牌損毀和失去信任。

- **從不受歡迎的訪客收集的 Cookie** -web 應用程式通常會向子域（*. contoso.com）公開會話 cookie，因此任何子域都可以存取它們。 威脅動作專案可以使用子域接管來建立美觀的頁面，並誘騙不受歡迎的使用者造訪，並收集其 cookie （甚至是安全的 cookie）。 常見的誤解是使用 SSL 憑證來保護您的網站和使用者的 cookie，而不受接管。 不過，威脅執行者可以使用攔截的子域來申請並接收有效的 SSL 憑證。 有效的 SSL 憑證會授與他們安全 cookie 的存取權，並可進一步提高惡意網站的認知合法性。

- **網路釣魚活動**-可以在網路釣魚活動中使用真實的子域。 這適用于惡意網站，也適用于允許威脅執行者接收已知安全品牌合法子域的電子郵件的 MX 記錄。

- **進一步的風險**-惡意網站可以用來呈報至其他傳統的攻擊，例如 XSS、CSRF、CORS 略過等。



## <a name="preventing-dangling-dns-entries"></a>防止無關聯的 DNS 專案

確保您的組織已執行程式來防止無關聯的 DNS 專案，而產生的子域接管是安全性程式中很重要的一部分。

您今天可以使用的預防措施如下所示。


### <a name="use-azure-dns-alias-records"></a>使用 Azure DNS 別名記錄

Azure DNS 的[別名記錄](https://docs.microsoft.com/azure/dns/dns-alias#scenarios)可以藉由結合 DNS 記錄與 Azure 資源的生命週期，來防止無關聯的參考。 例如，限定為別名記錄以指向公用 IP 位址或流量管理員設定檔的 DNS 記錄。 如果您刪除這些基礎資源，DNS 別名記錄就會變成空的記錄集。 它不再參考已刪除的資源。 請務必注意，您可以使用別名記錄來保護它的限制。 今天，清單僅限於：

- Azure Front Door
- 流量管理員設定檔
- Azure 內容傳遞網路（CDN）端點
- 公用 IP

雖然現今有限的服務供應專案，但我們建議您盡可能使用別名記錄來防禦子域接管。

[深入瞭解](https://docs.microsoft.com/azure/dns/dns-alias#capabilities)Azure DNS 別名記錄的功能。



### <a name="use-azure-app-services-custom-domain-verification"></a>使用 Azure App Service 的自訂網域驗證

建立 Azure App Service 的 DNS 專案時，請建立 asuid。子域含有網域驗證識別碼的 TXT 記錄。 當這類 TXT 記錄存在時，其他 Azure 訂用帳戶就不能驗證自訂網域，而是將它移到。 

這些記錄不會防止有人使用您 CNAME 專案中的相同名稱來建立 Azure App Service。 若沒有證明功能變數名稱擁有權的能力，威脅執行者就無法接收流量或控制內容。

[深入瞭解](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain)如何將現有的自訂 DNS 名稱對應至 Azure App Service。



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>建立並自動化程式以減輕威脅

通常由開發人員和營運小組執行清理程式，以避免出現無關聯的 DNS 威脅。 下列作法可協助確保您的組織避免遭受此威脅。 

- **建立預防程式：**

    - 教育您的應用程式開發人員在刪除資源時，重新路由位址。

    - 解除委任服務時，請在必要檢查清單上放置「移除 DNS 專案」。

    - 將[刪除鎖定](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources)放在具有自訂 DNS 專案的任何資源上。 刪除鎖定會當做指標，表示在取消布建資源之前必須先移除對應。 這類量值只能在與內部教育計畫結合時使用。

- **建立探索的程式：**

    - 定期檢查您的 DNS 記錄，以確保您的子域都會對應至 Azure 資源，其會：

        - 已存在-針對指向 Azure 子域（例如 *. azurewebsites.net 或 *. cloudapp.azure.com）的資源查詢您的 DNS 區域（請參閱[此參考清單](azure-domains.md)）。
        - 您擁有-確認您擁有 DNS 子域目標的所有資源。

    - 維護 Azure 完整功能變數名稱（FQDN）端點和應用程式擁有者的服務類別目錄。 若要建立您的服務類別目錄，請執行下列 Azure Resource Graph 查詢腳本。 此腳本會針對您有權存取的資源，投射其 FQDN 端點資訊，並將其輸出至 CSV 檔案。 如果您可以存取租使用者的所有訂用帳戶，腳本會考慮所有這些訂閱，如下列範例腳本所示。 若要將結果限制為一組特定的訂閱，請編輯腳本，如下所示。

        >[!IMPORTANT]
        > **許可權**-以可存取您所有 Azure 訂用帳戶的使用者身分執行查詢。 
        >
        > **限制**-Azure Resource Graph 具有節流和分頁限制，如果您有大型的 Azure 環境，就應該考慮這一點。 [深入瞭解](https://docs.microsoft.com/azure/governance/resource-graph/concepts/work-with-data)如何使用大型 Azure 資源資料集。 下列範例腳本會使用訂用帳戶批次處理來避免這些限制。

        ```powershell
        
            # Fetch the full array of subscription IDs.
            $subscriptions = Get-AzSubscription

            $subscriptionIds = $subscriptions.Id
                   # Output file path and names
                   $date = get-date
                   $fdate = $date.ToString("MM-dd-yyy hh_mm_ss tt")
                   $fdate #log to console
                   $rpath = [Environment]::GetFolderPath("MyDocuments") + '\' # Feel free to update your path.
                   $rname = 'Tenant_FQDN_Report_' + $fdate + '.csv' # Feel free to update the document name.
                   $fpath = $rpath + $rname
                   $fpath #This is the output file of FQDN report.

            # query
            $query = "where type in ('microsoft.network/frontdoors',
                                    'microsoft.storage/storageaccounts',
                                    'microsoft.cdn/profiles/endpoints',
                                    'microsoft.network/publicipaddresses',
                                    'microsoft.network/trafficmanagerprofiles',
                                    'microsoft.containerinstance/containergroups',
                                    'microsoft.apimanagement/service',
                                    'microsoft.web/sites',
                                    'microsoft.web/sites/slots')
                        | extend FQDN = case(
                            type =~ 'microsoft.network/frontdoors', properties['cName'],
                            type =~ 'microsoft.storage/storageaccounts', parse_url(tostring(properties['primaryEndpoints']['blob'])).Host,
                            type =~ 'microsoft.cdn/profiles/endpoints', properties['hostName'],
                            type =~ 'microsoft.network/publicipaddresses', properties['dnsSettings']['fqdn'],
                            type =~ 'microsoft.network/trafficmanagerprofiles', properties['dnsConfig']['fqdn'],
                            type =~ 'microsoft.containerinstance/containergroups', properties['ipAddress']['fqdn'],
                            type =~ 'microsoft.apimanagement/service', properties['hostnameConfigurations']['hostName'],
                            type =~ 'microsoft.web/sites', properties['defaultHostName'],
                            type =~ 'microsoft.web/sites/slots', properties['defaultHostName'],
                            '')
                        | project id, ['type'], name, FQDN
                        | where isnotempty(FQDN)";

            # Paging helper cursor
            $Skip = 0;
            $First = 1000;

            # If you have large number of subscriptions, process them in batches of 2,000.
            $counter = [PSCustomObject] @{ Value = 0 }
            $batchSize = 2000
            $response = @()

            # Group the subscriptions into batches.
            $subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

            # Run the query for each subscription batch with paging.
            foreach ($batch in $subscriptionsBatch)
            { 
                $Skip = 0; #Reset after each batch.

                $response += do { Start-Sleep -Milliseconds 500;   if ($Skip -eq 0) {$y = Search-AzGraph -Query $query -First $First -Subscription $batch.Group ; } `
                else {$y = Search-AzGraph -Query $query -Skip $Skip -First $First -Subscription $batch.Group } `
                $cont = $y.Count -eq $First; $Skip = $Skip + $First; $y; } while ($cont)
            }

            # View the completed results of the query on all subscriptions.
            $response | Export-Csv -Path $fpath -Append 

        ```

        `FQDNProperty`如先前 Resource Graph 查詢中所指定的類型和其值的清單：

        |資源名稱  | `<ResourceType>`  | `<FQDNproperty>`  |
        |---------|---------|---------|
        |Azure Front Door|microsoft.network/frontdoors|屬性 cName|
        |Azure Blob 儲存體|microsoft.storage/storageaccounts|Primaryendpoints.blob. blob|
        |Azure CDN|microsoft.cdn/profiles/endpoints|屬性。主機名稱|
        |公用 IP 位址|microsoft.network/publicipaddresses|dnsSettings. fqdn|
        |Azure 流量管理員|microsoft.network/trafficmanagerprofiles|屬性. dnsConfig. fqdn|
        |Azure 容器執行個體|microsoft.containerinstance/containergroups|屬性。 fqdn|
        |Azure API 管理|microsoft.apimanagement/service|hostnameConfigurations. hostName|
        |Azure App Service|microsoft.web/sites|屬性。 defaultHostName|
        |Azure App Service 插槽|microsoft.web/sites/slots|屬性。 defaultHostName|


- **建立補救程式：**
    - 找到無關聯的 DNS 專案時，您的小組必須調查是否發生任何洩露。
    - 調查資源解除委任時，位址不會重新路由的原因。
    - 刪除不再使用的 DNS 記錄，或將它指向貴組織所擁有的正確 Azure 資源（FQDN）。
 

## <a name="next-steps"></a>後續步驟

若要深入瞭解您可以用來防禦子域接管的相關服務和 Azure 功能，請參閱下列頁面。

- [Azure DNS 支援使用自訂網域的別名記錄](https://docs.microsoft.com/azure/dns/dns-alias#prevent-dangling-dns-records)

- [在 Azure App Service 中新增自訂網域時，請使用網域驗證識別碼](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#get-domain-verification-id) 

- [快速入門：使用 Azure PowerShell 執行您的第一個 Resource Graph 查詢](https://docs.microsoft.com/azure/governance/resource-graph/first-query-powershell)
