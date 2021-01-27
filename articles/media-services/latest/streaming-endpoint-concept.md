---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>必要欄位。 查看更多有關 aka.ms/skyeye/meta 的資訊。
標題：串流端點 (源) ： Azure 媒體服務描述：瞭解串流端點 (來源) 、動態封裝和串流服務，可直接將內容傳遞至用戶端播放機應用程式，或傳遞至內容傳遞網路 (CDN) 。 服務： media services documentationcenter： ' ' author： IngridAtMicrosoft manager： femila editor： ' '

ms. 服務：媒體-服務 ms. 工作負載： ms. 主題：概念 ms. 日期： 02/13/2020 ms. 作者： inhenkel
---

# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Azure 媒體服務中 (原始) 的串流端點

在 Microsoft Azure 媒體服務中， [串流端點](/rest/api/media/streamingendpoints) 代表動態 (即時) 封裝和原始服務，可以使用其中一種常見的串流媒體通訊協定 (HLS 或虛線) ，將即時和隨選內容直接傳遞至用戶端播放機應用程式。 此外， **串流端點** 也提供動態的 (即時) 加密至領先業界的 drm。 

當您建立媒體服務帳戶時，系統會為您建立 **預設** 串流端點 (處於已停止狀態)。 您可以在帳戶下建立更多串流端點 (請參閱) 的 [配額和限制](limits-quotas-constraints.md) 。

> [!NOTE]
> 若要開始串流處理視訊，您必須啟動要作為視訊串流處理來源的 **串流端點**。
>
> 只有當串流端點處於執行中狀態時，才會向您收費。

請務必同時參閱 [動態封裝](dynamic-packaging-overview.md) 主題。 

## <a name="naming-convention"></a>命名慣例

串流 URL 的主機名稱格式為： `{servicename}-{accountname}-{regionname}.streaming.media.azure.net` ，其中 `servicename` = 串流端點名稱或即時事件名稱。

使用預設串流端點時， `servicename` 會省略，因此 URL 為： `{accountname}-{regionname}.streaming.azure.net` 。

### <a name="limitations"></a>限制

* 串流端點名稱的最大值為24個字元。
* 此名稱應遵循此 [RegEx](/dotnet/standard/base-types/regular-expression-language-quick-reference) 模式： `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$` 。

## <a name="types"></a>類型

有兩種 **串流端點** 類型： **Standard** (preview) 和 **Premium**。 類型會根據您為串流端點配置的縮放單位數 (`scaleUnits`) 來定義。

最大串流單位限制通常是10。 請在 [這裡](https://azure.microsoft.com/support/create-ticket/) 與我們聯絡，以提高您帳戶的限制。

類型描述如下表所示：

|類型|縮放單位|描述|
|--------|--------|--------|  
|**標準**|0|預設串流端點是 **標準** 型別，可以藉由調整來變更為 Premium 類型 `scaleUnits` 。|
|**高級**|>0|**Premium** 串流端點適用于先進的工作負載，並提供專用且可調整的頻寬容量。 您可以藉由調整 `scaleUnits` (串流單位) 來移至 Premium 類型。 `scaleUnits` 提供您專用的輸出容量，您可以透過每次增量 200 Mbps 的方式來購買。 使用 **Premium** 類型時，每個啟用的單位都會提供額外的頻寬容量給應用程式。 |

> [!NOTE]
> 如果客戶想要將內容傳遞給大型網際網路觀眾，建議您在串流端點上啟用 CDN。

如需 SLA 資訊，請參閱 [定價和 sla](https://azure.microsoft.com/pricing/details/media-services/)。

## <a name="comparing-streaming-types"></a>比較串流類型

功能|標準|Premium
---|---|---
Throughput |最高可達 600 Mbps，並可在使用 CDN 時提供更高的有效輸送量。|每個串流單位 (SU) 200 Mbps。 使用 CDN 時，可提供更高的有效輸送量。
CDN|Azure CDN、協力廠商 CDN 或沒有 CDN。|Azure CDN、協力廠商 CDN 或沒有 CDN。
按比例計費| 每日|每日
動態加密|是|是
動態封裝|是|是
調整|自動相應增加至目標輸送量。|其他 SUs
IP 篩選/G20/自訂主機 <sup>1</sup>|是|是
漸進式下載|是|是
建議用法 |建議用於絕大多數的串流案例。|專業用法。

只有當端點上未啟用 CDN 時，才會直接在串流端點上使用<sup>1</sup> 。<br/>

## <a name="streaming-endpoint-properties"></a>串流端點屬性

本節提供一些串流端點屬性的詳細資料。 如需如何建立新串流端點的範例及所有屬性的說明，請參閱[串流端點](/rest/api/media/streamingendpoints/create)。

- `accessControl`：用來設定此串流端點的下列安全性設定： Akamai 簽章標頭驗證金鑰和允許連線至此端點的 IP 位址。 只有當設定為 false 時，才可以設定這個屬性 `cdnEnabled` 。

- `cdnEnabled`：指出是否已啟用此串流端點的 Azure CDN 整合 (預設為停用) 。 如果您將 `cdnEnabled` 設為 True，下列設定就會停用：`customHostNames` 和 `accessControl`。

    並非所有資料中心都支援 Azure CDN 整合。 若要檢查您的資料中心是否有可用的 Azure CDN 整合，請執行下列步驟：

  - 嘗試設定`cdnEnabled`設為 true。
  - 檢查 (PreconditionFailed) 的傳回結果，其訊息為「 `HTTP Error Code 412` 串流端點 CdnEnabled 屬性無法設定為 true，因為目前區域中無法使用 CDN 功能」。

    如果您收到此錯誤，資料中心不會支援此錯誤。 嘗試另一個資料中心。

- `cdnProfile`：當 `cdnEnabled` 設為 true 時，您也可以傳遞 `cdnProfile` 值。 `cdnProfile` 是 CDN 設定檔的名稱，而該設定檔就是要建立 CDN 端點的位置。 您可以提供現有的 cdnProfile，或使用新的。 如果值為 NULL，而且 `cdnEnabled` 是 True，則會使用預設值 "AzureMediaStreamingPlatformCdnProfile"。 如果提供的 `cdnProfile` 已經存在，則會在其下方建立端點。 如果設定檔不存在，則會自動建立新的設定檔。
- `cdnProvider`：啟用 CDN 時，您也可以傳遞 `cdnProvider` 值。 `cdnProvider` 會控制將使用哪一個提供者。 目前支援三個值："StandardVerizon"、"PremiumVerizon" 和 "StandardAkamai"。 如果未提供任何值 `cdnEnabled` ，且為 true，則會使用 "StandardVerizon" (這是) 的預設值。
- `crossSiteAccessPolicies`：用來指定不同用戶端的跨網站存取原則。 如需詳細資訊，請參閱[跨網域原則檔案規格](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html)和[使服務可跨網域界限使用](/previous-versions/azure/azure-services/gg185950(v=azure.100))。 這些設定只適用于 Smooth Streaming。
- `customHostNames`：用來設定串流端點，以接受導向至自訂主機名稱的流量。 這個屬性對於 Standard 和 Premium 串流端點是有效的，而且可以在 `cdnEnabled` 下列情況設定： false。

    網功能變數名稱稱的擁有權必須由媒體服務確認。 媒體服務 `CName` 會要求包含媒體服務帳戶識別碼的記錄，做為要新增至使用中網域的元件，藉以驗證功能變數名稱擁有權。 例如，若要將 "sports.contoso.com" 作為串流端點的自訂主機名稱，`<accountId>.contoso.com` 的記錄就必須設定為指向其中一個媒體服務驗證名稱。 驗證主機名稱是由 verifydns.<mediaservices-dns-zone> 所組成 \<mediaservices-dns-zone> 。

    以下是要在不同 Azure 區域的驗證記錄中使用的預期 DNS 區域。
  
  - 北美洲、歐洲、新加坡、香港特別行政區、日本：

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - 中國：

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    例如，將 `CName` "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" 對應至 "verifydns.media.azure.net" 的記錄證明，媒體服務識別碼945a4c4e-28ea-45cd-8ccb-a519f6b700ad 具有 contoso.com 網域的擁有權，因此在 contoso.com 下啟用任何名稱作為該帳戶下串流端點的自訂主機名稱。 若要尋找媒體服務識別碼的值，請前往 [Azure 入口網站](https://portal.azure.com/)，然後選取您的媒體服務帳戶。 **帳戶識別碼** 會出現在頁面的右上方。

    如果嘗試設定自訂主機名稱，但沒有適當的 `CName` 記錄驗證，則 DNS 回應會失敗，然後快取一段時間。 當適當的記錄就緒後，可能需要等待一段時間，才會重新驗證快取的回應。 根據自訂網域的 DNS 提供者而定，需要幾分鐘到一小時的時間來重新驗證記錄。

    除了 `CName` 對應至的以外 `<accountId>.<parent domain>` `verifydns.<mediaservices-dns-zone>` ，您還必須建立另一個 `CName` 對應自訂主機名稱 (例如， `sports.contoso.com`) 至媒體服務串流端點的主機名稱 (例如 `amstest-usea.streaming.media.azure.net`) 。

    > [!NOTE]
    > 位於相同資料中心的串流端點無法共用相同的自訂主機名稱。

    目前，媒體服務不支援將 TLS 用於自訂網域。

- `maxCacheAge` -覆寫媒體片段和隨選資訊清單上串流端點所設定的預設最大壽命 HTTP 快取控制標頭。 此值的設定會以秒為單位。
- `resourceState` -

    - 已停止：建立後串流端點的初始狀態
    - 開始：正在轉換為執行中狀態
    - 正在執行：能夠將內容串流至用戶端
    - 調整：縮放單位即將增加或減少
    - 正在停止：正在轉換為已停止狀態
    - 正在刪除：正在刪除

- `scaleUnits`：提供您專用的輸出容量，並以 200 Mbps 為增量單位來購買。 如果您需要移至 **進階** 類型，請調整 `scaleUnits`。

## <a name="why-use-multiple-streaming-endpoints"></a>為何要使用多個串流端點？

單一串流端點可以串流即時和隨選影片，而大部分的客戶只會使用一個串流端點。 本節提供一些範例，說明您可能需要使用多個串流端點的原因。

* 每個保留單位都允許 200 Mbps 的頻寬。 如果您需要超過 2000 Mbps (2 Gbps 的頻寬) ，您可以使用第二個串流端點和負載平衡來提供額外的頻寬。

    不過，CDN 是針對串流內容進行相應放大的最佳方式，但如果您傳遞的內容太多，CDN 會拉出超過 2 Gbps 的內容，則您可以將額外的串流端點新增 (來源) 。 在此情況下，您必須在兩個串流端點之間取得平衡的內容 Url。 這種方法可提供更佳的快取，而不是嘗試將要求隨機傳送給每個來源 (例如，透過流量管理員) 。 
    
    > [!TIP]
    > 通常，如果 CDN 提取超過 2 Gbps，則可能設定錯誤 (例如，沒有來源防護) 。
    
* 負載平衡不同的 CDN 提供者。 例如，您可以將預設的串流端點設定為使用 Verizon CDN，並建立第二個端點以使用 Akamai。 然後新增兩者之間的負載平衡，以達到多 CDN 平衡。 

    不過，客戶通常會使用單一來源，在多個 CDN 提供者之間進行負載平衡。
* 串流混合內容：隨選即時和影片。 

    即時和隨選內容的存取模式非常不同。 即時內容通常會同時為相同的內容取得許多需求。 實例) 的隨選影片 (長尾封存內容，在相同內容上的使用量低。 因此，快取的運作方式非常適合即時內容，而不是長尾的內容。

    假設您的客戶主要在監看即時內容，但只偶爾會監看隨選內容，而且是由相同的串流端點提供服務。 隨選內容的低使用量會佔用快取空間，此空間將會更妥善地儲存在實況內容中。 在此案例中，我們建議從一個串流端點和來自另一個串流端點的長尾內容提供即時內容。 這會改善實況活動內容的效能。
    
## <a name="scaling-streaming-with-cdn"></a>使用 CDN 調整串流作業的規模

查看下列文章：

- [CDN 總覽](../../cdn/cdn-overview.md)
- [使用 CDN 調整串流作業的規模](scale-streaming-cdn.md)

## <a name="ask-questions-and--get-updates"></a>提出問題並取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="see-also"></a>另請參閱

[動態封裝](dynamic-packaging-overview.md)

## <a name="next-steps"></a>後續步驟

[管理串流端點](manage-streaming-endpoints-howto.md)
