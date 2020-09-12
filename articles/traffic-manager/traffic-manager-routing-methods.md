---
title: Azure 流量管理員 - 流量路由方法
description: 本文將協助您了解流量管理員所使用的不同流量路由方法
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: duau
ms.openlocfilehash: 3cf493beab6dfe1767ae35ea36732dc364e29736
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89401651"
---
# <a name="traffic-manager-routing-methods"></a>流量管理員路由傳送方法

Azure 流量管理員支援六種流量路由方法，以決定如何將網路流量路由傳送至不同的服務端點。 針對任何設定檔，流量管理員會將其相關的流量路由方法套用至它收到的每個 DNS 查詢。 流量路由方法決定 DNS 回應中傳回哪一個端點。

流量管理員可提供下列流量路由方法：

* **[優先順序](#priority-traffic-routing-method)：** 如果您想要針對所有流量使用主要服務端點，請選取 [ **優先順序** ]，並在主要或備份端點無法使用的情況下提供備份。
* **[加權](#weighted)：** 當您想要將流量分配給一組端點（不論是平均或根據您定義的加權）時，請選取 [ **加權** ]。
* **[效能](#performance)：** 當您的端點位於不同的地理位置，而您希望使用者使用「最靠近」的端點時，請選取 [ **效能** ] （以最低的網路延遲為依據）。
* **[地理](#geographic)︰** 選取 [地理]****，以根據使用者的 DNS 查詢來自哪個地理位置，將使用者導向特定端點 (Azure、外部或巢狀)。 在必須知道使用者的地理區域，並據以路由傳送使用者的情況下，這可讓流量管理員客戶應付自如。 例如，遵守資料主權規定、內容和使用者經驗的當地語系化，以及測量來自不同區域的流量。
* **[多值](#multivalue)：** 針對流量管理員設定檔選取 [多值]****，其只能將 IPv4/IPv6 位址當作端點。 當系統收到此設定檔的查詢時，會傳回所有狀況良好的端點。
* **[子網路](#subnet)：** 選取 [子網路]**** 流量路由方法，以將使用者 IP 位址範圍集對應到流量管理員設定檔中的特定端點。 當收到要求時，傳回的端點會是對應至該要求來源 IP 位址的端點。 


所有流量管理員設定檔都支援監視端點健康狀態和自動容錯移轉。 如需詳細資訊，請參閱 [流量管理員端點監視](traffic-manager-monitoring.md)。 單一「流量管理員」設定檔只能使用一個流量路由方法。 您可以隨時為您的設定檔選取不同的流量路由方法。 變更會在 1 分鐘內套用，不會造成任何停機時間。 透過使用巢狀「流量管理員」設定檔可以將流量路由方法加以結合。 巢狀可讓您建立精密又有彈性的流量路由設定，以滿足更大又複雜的應用程式的需求。 如需詳細資訊，請參閱 [巢狀流量管理員設定檔](traffic-manager-nested-profiles.md)。

## <a name="priority-traffic-routing-method"></a>優先順序流量路由方法

組織通常會部署一或多個備份服務，以防萬一主要服務停止運作時，可確保服務的可靠性。 「優先順序」流量路由方法可讓 Azure 客戶輕鬆實作此容錯移轉模式。

![Azure 流量管理員「優先順序」流量路由方法](media/traffic-manager-routing-methods/priority.png)

流量管理員設定檔包含服務端點的優先順序清單。 根據預設，流量管理員會將所有流量傳送至主要 (優先順序最高) 端點。 如果主要端點無法使用，流量管理員會將流量路由傳送至第二個端點。 如果主要和次要端點都無法供使用，系統就會將流量傳送到第三個端點，依此類推。 端點的可用性是取決於已設定的狀態 (已啟用或已停用) 和持續的端點監視。

### <a name="configuring-endpoints"></a>設定端點

在 Azure Resource Manager 中，您可以使用「優先順序」屬性來明確設定每個端點的點端優先順序。 這個屬性的值介於 1 到 1000 之間。 值越低代表優先順序越高。 端點無法共用優先順序值。 設定屬性是選擇性的。 如果省略，則會使用以端點順序為基礎的預設優先順序。

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>加權流量路由方法
「加權」流量路由方法可讓您平均分散流量，或使用預先定義的權數。

![Azure 流量管理員「加權」流量路由方法](media/traffic-manager-routing-methods/weighted.png)

在「加權」流量路由方法中，您需要在流量管理員設定檔設定中指派權數給每個端點。 權數是從 1 到 1000 的整數。 這是選擇性參數。 如果省略，流量管理員會使用預設權數 '1'。 權數越高，優先順序也就越高。

針對每個收到的 DNS 查詢，流量管理員會隨機選擇可用的端點。 選擇端點之機率是根據指派給所有可用端點的權數。 所有端點都使用相同的權數會形成平均的流量分配。 在特定端點上使用較高或較低的權數會導致 DNS 回應中較經常或較不常傳回這些端點。

加權方法支援一些實用的案例︰

* 應用程式逐步升級：配置要路由傳送到新端點的流量百分比，並隨時間逐漸增加流量到 100%。
* 應用程式移轉至 Azure︰建立具有 Azure 和外部端點的設定檔。 調整端點的權數來優先使用新的端點。
* 額外容量的雲端負載平衡：將內部部署快速展開到雲端的方式是將它放在流量管理員設定檔後面。 當您需要在雲端中增加額外容量時，您可以新增或啟用更多的端點並指定進入每個端點的流量比例。

除了使用 Azure 入口網站之外，您可以使用 Azure PowerShell、CLI 和 REST API 來設定加權。

請務必了解用戶端和用戶端用來解析 DNS 名稱的遞迴 DNS 伺服器會快取 DNS 回應。 此快取可能會影響加權的流量分配。 有大量用戶端和遞迴 DNS 伺服器時，流量分配會正常運作。 不過，如果只有少量用戶端或遞迴 DNS 伺服器，則快取可能會大幅扭曲流量分配。

常見使用案例包括：

* 開發與測試環境
* 應用程式間通訊
* 以較小的使用者群為對象的應用程式，有共同的遞迴 DNS 基礎結構 (例如，透過 Proxy 連接的公司員工)

這些 DNS 快取效果對所有 DNS 型流量路由系統來說很常見，不僅限於「Azure 流量管理員」。 在某些情況下，明確清除 DNS 快取也許是一種因應措施。 在其他情況下，使用替代的流量路由方法可能更為合適。

## <a name="performance-traffic-routing-method"></a><a name = "performance"></a>效能流量路由方法

如果將端點部署在全球的兩個或更多個位置，然後將流量路由傳送至「最靠近」您的位置，即可改善許多應用程式的回應速度。 「效能」流量路由方法提供這項功能。

![Azure 流量管理員「效能」流量路由方法](media/traffic-manager-routing-methods/performance.png)

「最靠近」的端點不一定是地理距離測量上最靠近的端點。 「效能」流量路由方法會測量網路延遲，以決定最靠近的端點。 流量管理員會維護「網際網路延遲資料表」，以追蹤 IP 位址範圍與每個 Azure 資料中心之間的往返時間。

流量管理員會在「網際網路延遲資料表」中查閱傳入 DNS 要求的來源 IP 位址。 接著，流量管理員會在 Azure 資料中心內選擇該 IP 位址範圍內延遲最低的可用端點，並在 DNS 回應中傳回該端點。

如[流量管理員的運作方式](traffic-manager-how-it-works.md)中所述，流量管理員不會直接從用戶端接收 DNS 查詢。 相反地，DNS 查詢是來自用戶端已設定使用的遞迴 DNS 服務。 因此，用來判斷「最靠近」端點的 IP 位址不是用戶端的 IP 位址，而是遞迴 DNS 服務的 IP 位址。 實際上，此 IP 位址是用戶端的理想 Proxy。


流量管理員會定期更新「網際網路延遲資料表」，以反映全球網際網路的變動和新的 Azure 區域。 不過，隨著網際網路上即時的負載變化，應用程式效能會改變。 效能流量路由不會監視特定服務端點上的負載。 不過，如果端點變得無法使用，流量管理員就不會將它加入 DNS 查詢回應中。


注意事項：

* 如果您的設定檔中有多個端點在相同的 Azure 區域，則流量管理員會將流量平均分散至該區域中可用的端點。 如果您在某個區域內偏好採用不同的流量分配，您可以使用[巢狀流量管理員設定檔](traffic-manager-nested-profiles.md)。
* 如果最接近的 Azure 區域中所有啟用的端點都降級，流量管理員會將流量移到下一個最接近的 Azure 區域中的端點。 如果您想要定義慣用的容錯移轉順序，請使用[巢狀流量管理員設定檔](traffic-manager-nested-profiles.md)。
* 對外部端點或巢狀端點使用「效能」流量路由方法時，您必須指定這些端點的位置。 選擇最接近部署的 Azure 區域。 這些區域是「網際網路延遲資料表」所支援的值。
* 端點的選擇演算法具有決定性。 來自同一個用戶端的重複 DNS 查詢會導向同一個端點。 一般而言，用戶端在漫遊時會使用不同的遞迴 DNS 伺服器。 用戶端可能會路由傳送至不同的端點。 「網際網路延遲資料表」的更新也會影響路由。 因此，效能流量路由方法不保證用戶端一定會路由傳送至相同的端點。
* 當「網際網路延遲資料表」變更時，您可能會發現某些用戶端導向不同的端點。 只要取得最新的延遲資料，此路由變更會更精確。 隨著網際網路的持續發展，這些更新對於維護「效能」流量路由的精確度也變得不可或缺。

## <a name="geographic-traffic-routing-method"></a><a name = "geographic"></a>地理流量路由方法

流量管理員設定檔可以設定成使用地理路由方法，以根據使用者的 DNS 查詢來自哪個地理位置，將使用者導向特定端點 (Azure、外部或巢狀)。 在必須知道使用者的地理區域，並據以路由傳送使用者的情況下，這可讓流量管理員客戶應付自如。 例如，遵守資料主權規定、內容和使用者經驗的當地語系化，以及測量來自不同區域的流量。
設定地理路由的設定檔時，必須指派一組地理區域給該設定檔相關聯的每個端點。 地理區域可以達到下列細微層級 
- 世界 – 任何區域
- 地區分組 – 例如非洲、中東、澳大利亞/太平洋等 
- 國家/地區 – 例如愛爾蘭、秘魯、香港特別行政區等 
- 州/省 – 例如美國加州、澳大利亞昆士蘭、加拿大亞伯達省等 (請注意︰僅澳大利亞、加拿大和美國的州/省才支援此細微層級)。

將一個或一組區域指派給端點時，來自這些區域的任何要求只會路由傳送至該端點。 流量管理員會使用 DNS 查詢的來源 IP 位址，判斷使用者的查詢來自哪個區域 – 這通常是使用者執行查詢的本機 DNS 解析程式的 IP 位址。  

![Azure 流量管理員「地理」流量路由方法](./media/traffic-manager-routing-methods/geographic.png)

流量管理員會讀取 DNS 查詢的來源 IP 位址，判斷查詢的來源地理區域。 然後尋找是否有此地理區域對應的端點。 此查閱會從最低的資料細微性層級開始 (州/省支援的州/省，否則，會在國家/地區層級) ，而且會一直到最高等級，也就是 **全世界**。 經過此周遊找到的第一個相符項目，就指定為要在查詢回應中傳回的端點。 符合「巢狀」類型端點時，則會根據路由方法，傳回該子設定檔中的端點。 此行為有下列幾個特點︰

- 當路由類型是「地理路由」時，在流量管理員設定檔中，一個地理區域只能對應至一個端點。 這可確保使用者路由是具決定性，讓客戶在地理界限必須明確的情況下應付自如。
- 如果使用者的區域出現在兩個不同端點的地理對應中，流量管理員會選取細微度最低的端點，不會考慮將該區域的要求路由傳送至另一個端點。 例如，假設「地理路由」類型設定檔有兩個端點 - 端點 1 和端點 2。 端點 1 設定為接收來自愛爾蘭的流量，端點 2 設定接收來自歐洲的流量。 如果要求是來自愛爾蘭，則永遠會路由傳送至端點 1。
- 因為一個區域只能對應至一個端點，不論端點是否狀況良好，流量管理員都會傳回此端點。

    >[!IMPORTANT]
    >強烈建議客戶使用地理路由方法，並與「巢狀」類型端點相關聯，且子設定檔至少各包含兩個端點。
- 如果找到符合的端點，但該端點處於**已停止**狀態，則流量管理員會傳回 NODATA 回應。 在此情況下，不會再於地理區域階層中往上進一步查閱。 對於巢狀端點類型，當子設定檔處於**已停止**或**已停用**狀態時，此行為也適用。
- 如果端點顯示**已停用**狀態，則區域比對過程不會考慮此端點。 對於巢狀端點類型，當端點處於**已停用**狀態時，此行為也適用。
- 如果查詢的來源地理區域在該設定檔中沒有對應，流量管理員會傳回 NODATA 回應。 因此，強烈建議客戶使用地理路由搭配一個端點，最好是「巢狀」類型，且子設定檔內至少有兩個端點，並指派區域**世界**。 這也可確保會處理未對應到區域的任何 IP 位址。

如[流量管理員的運作方式](traffic-manager-how-it-works.md)中所述，流量管理員不會直接從用戶端接收 DNS 查詢。 相反地，DNS 查詢是來自用戶端已設定使用的遞迴 DNS 服務。 因此，用來判斷區域的 IP 位址不是用戶端的 IP 位址，而是遞迴 DNS 服務的 IP 位址。 實際上，此 IP 位址是用戶端的理想 Proxy。

### <a name="faqs"></a>常見問題集

* [地理路由派上用場的使用案例有哪些？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-geographic-routing-is-useful)

* [如何決定應該要使用效能路由方法或使用地理路由方法？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method)

* [流量管理員的地理路由支援哪些區域？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing)

* [流量管理員如何判斷使用者從何處執行查詢？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-determine-where-a-user-is-querying-from)

* [它保證流量管理員可以在每個案例中正確判斷使用者確切的地理位置嗎？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case)

* [端點必須實際位於地理路由設定的相同區域嗎？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing)

* [您可以在未設定為執行地理路由的設定檔中指派地理區域給端點嗎？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing)

* [當我嘗試將現有設定檔的路由方法變更為地理時為何會發生錯誤？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic)

* [為什麼強烈建議客戶建立巢狀設定檔，而不是在啟用地理路由的設定檔下新增端點？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled)

* [支援此路由類型的 API 版本有任何限制嗎？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type)

## <a name="multivalue-traffic-routing-method"></a><a name = "multivalue"></a>多值流量路由方法
**多值**流量路由方法可讓您在單一 DNS 查詢回應中取得多個狀況良好的端點。 這可讓呼叫端在傳回的端點沒有回應時，與其他端點進行用戶端重試。 此模式可提高服務的可用性，且可減少透過新的 DNS 查詢來取得健康狀態良好端點的相關延遲。 只有在所有端點類型為「外部」且指定為 IPv4 或 IPv6 位址時，才適用多值路由方法。 當系統收到此設定檔的查詢時，會傳回所有狀況良好的端點，您可以設定回傳計數上限來限制回傳數量。

### <a name="faqs"></a>常見問題集

* [MultiValue 路由派上用場的使用案例有哪些？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-multivalue-routing-is-useful)

* [使用 MultiValue 路由時，會傳回多少個端點？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-endpoints-are-returned-when-multivalue-routing-is-used)

* [使用 MultiValue 路由時，會取得一組相同的端點嗎？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used)

## <a name="subnet-traffic-routing-method"></a><a name = "subnet"></a>子網路流量路由方法
**子網路**流量路由方法可讓您將一組使用者 IP 位址範圍對應至設定檔中的特定端點。 在那之後，如果流量管理員收到該設定檔的 DNS 查詢，它會檢查該要求的來源 IP 位址 (在大部分情況下，這會是呼叫端使用之 DNS 解析程式的連出 IP 位址)，判斷其所對應至的端點，並將會在查詢回應中傳回該端點。 

要對應至端點的 IP 位址可指定為 CIDR 範圍 (例如 1.2.3.0/24) 或位址範圍 (例如 1.2.3.4 - 5.6.7.8)。 與端點相關聯的 IP 範圍在該設定檔中必須是唯一的，且不能與相同設定檔中不同端點的 IP 位址集重疊。
如果您定義不含任何位址範圍的端點，則該端點會用來作為後援，並從任何剩餘的子網路取得流量。 如果未包含任何後援端點，流量管理員就會針對任何未定義的範圍傳送 NODATA 回應。 因此，強烈建議您定義後援端點，或確定會跨您的端點指定所有可能的 IP 範圍。

子網路路由可為來自特定 IP 空間的使用者提供不同體驗。 例如，使用子網路路由，客戶可以讓來自公司辦公室的所有要求路由至不同端點，他們可能在其中測試其應用程式的內部版本。 另一種情況是如果您想要為來自特定 ISP 連線的使用者提供不同體驗 (例如封鎖來至指定 ISP 的使用者)。

### <a name="faqs"></a>常見問題集

* [子網路路由派上用場的使用案例有哪些？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-subnet-routing-is-useful)

* [流量管理員如何知道終端使用者的 IP 位址？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-know-the-ip-address-of-the-end-user)

* [使用「子網路」路由時，如何指定 IP 位址？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-ip-addresses-when-using-subnet-routing)

* [使用「子網路」路由時，如何指定後援端點？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing)

* [如果「子網路」路由類型設定檔中已停用端點，則會發生什麼事？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile)


## <a name="next-steps"></a>接下來的步驟

瞭解如何使用[流量管理員端點監視](traffic-manager-monitoring.md)來開發高可用性應用程式




