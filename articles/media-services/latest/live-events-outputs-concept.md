---
標題： Azure 媒體服務 v3 中的即時事件和即時輸出概念： Azure 媒體服務描述：本主題提供 Azure 媒體服務 v3 中即時事件和即時輸出的總覽。
服務： media services documentationcenter： ' ' author： IngridAtMicrosoft manager： femila editor： ' '

ms. 服務：媒體服務 ms. 工作負載：媒體 ms.tgt_pltfrm： na ms.devlang： ne ms。主題：概念 ms. 日期： 10/23/2020 ms. 作者： inhenkel

---
# <a name="live-events-and-live-outputs-in-media-services"></a>媒體服務中的即時活動和即時輸出

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure 媒體服務可讓您在 Azure 雲端上將實況活動傳遞給客戶。 若要在媒體服務 v3 中設定即時串流事件，您必須瞭解本文中討論的概念。

> [!TIP]
> 針對從媒體服務 v2 Api 遷移的客戶， **實況活動** 實體會取代 v2 中的 **通道** ，而 **即時輸出** 會取代 **程式**。

## <a name="live-events"></a>實況活動

[實況活動](/rest/api/media/liveevents) 負責擷取和處理即時影片摘要。 當您建立實況活動時，系統會建立主要和次要輸入端點，供您用來從遠端編碼器傳送即時信號。 遠端即時編碼器會使用 [RTMP](https://www.adobe.com/devnet/rtmp.html) 或 [Smooth Streaming](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) (分散) 輸入通訊協定，將投稿摘要傳送到該輸入端點。 若為 RTMP 內嵌通訊協定，可以在 [清除 (]) 中傳送內容， `rtmp://` 或在網路上安全地加密 (`rtmps://`) 。 針對 Smooth Streaming 內嵌通訊協定，支援的 URL 配置為 `http://` 或 `https://`。  

## <a name="live-event-types"></a>即時事件類型

[實況活動](/rest/api/media/liveevents)可以設定為傳遞 (內部部署即時編碼器會傳送多位元率串流) 或 *即時編碼* (內部部署即時編碼器會 *傳送* 單一位元速率串流) 。 使用 [LiveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype)在建立期間設定類型：

* **LiveEventEncodingType： None**：內部部署即時編碼器會傳送多位元率串流。 內嵌資料流程會通過即時事件，而不會進行任何進一步的處理。 也稱為傳遞模式。
* **LiveEventEncodingType**：內部部署即時編碼器會將單一位元速率串流傳送至實況活動，而媒體服務會建立多位元率串流。 如果投稿摘要是720p 或更高的解析度， **Default720p** 預設會將一組6個解析/位元速率配對編碼。
* **LiveEventEncodingType. Premium1080p**：內部部署即時編碼器會將單一位元速率串流傳送至實況活動，而媒體服務會建立多位元率串流。 Default1080p 預設值會指定解析/位元速率配對的輸出集。

### <a name="pass-through"></a>傳遞

![使用媒體服務的傳遞即時事件範例圖](./media/live-streaming/pass-through.svg)

使用傳遞 **實況** 活動時，您會依賴內部部署即時編碼器來產生多位元率的影片串流，並使用 RTMP 或分散的通訊協定) 將該串流作為投稿摘要傳送給實況活動 (。 實況活動接著會透過傳入的影片串流，而不需要任何進一步的處理。 這類的傳遞實況活動已針對長時間執行的即時活動或24x365 線性即時串流進行優化。 建立這種類型的實況活動時，請指定 None (LiveEventEncodingType) 。

您可以使用 H.264/AVC 或 H.265/HEVC 視訊轉碼器以及 AAC (AAC-LC、HE-AACv1 或 HE-AACv2) 音訊轉碼器，以高達 4K 的解析度和每秒 60 個畫面的畫面播放速率傳送發佈摘要。 如需詳細資訊，請參閱 [即時事件種類比較](live-event-types-comparison.md)。

> [!NOTE]
> 當您在一段長時間內執行多個事件，而且已投資內部部署編碼器時，使用傳遞方法是進行即時串流的最經濟實惠方式。 請參閱 [定價](https://azure.microsoft.com/pricing/details/media-services/) 詳細資料。
>

請參閱 [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).中的 .NET 程式碼範例。

### <a name="live-encoding"></a>即時編碼  

![使用媒體服務的即時編碼範例圖表](./media/live-streaming/live-encoding.svg)

搭配媒體服務使用即時編碼時，您可以設定內部部署即時編碼器，以使用 RTMP 或 Fragmented-Mp4 通訊協定) ，將單一位元速率的影片作為發佈摘要傳送至實況活動 (。 接著，您會設定實況活動，讓它將該傳入的單一位元速率串流編碼為 [多位元率影片串流](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)，並讓輸出可供傳遞，以透過 MPEG、HLS 和 Smooth Streaming 等通訊協定播放裝置。

當您使用即時編碼時，您只能以每秒30個畫面格速率的解析度，以每秒30個畫面格速率的解析度傳送投稿摘要，並使用 h.264/AVC video 編解碼器和 AAC (AAC-LC、He-aacv1 或 He-aacv2) 音訊編解碼器。 請注意，傳遞實況活動最多可支援60個畫面格每秒最高4K 的解析度。 如需詳細資訊，請參閱 [即時事件種類比較](live-event-types-comparison.md)。

即時編碼器的輸出中所包含的解析度和位元速率是由預設值所決定。 如果使用 **標準** 即時編碼器 (LiveEventEncodingType) ，則 *Default720p* 預設會指定一組六個解析度/位速率組，從720p 以 3.5 Mbps 向下至192p，以 200 kbps。 否則，如果使用 **Premium1080p** 即時編碼器 (LiveEventEncodingType. Premium1080p) ，則 *Default1080p* 預設值會指定一組六個解析度/位速率組，從1080p 以 3.5 Mbps 向下到180p，以 200 kbps 為單位。 如需相關資訊，請參閱[系統預設值](live-event-types-comparison.md#system-presets)。

> [!NOTE]
> 如果您需要自訂即時編碼預設值，請透過 Azure 入口網站開啟支援票證。 指定所需的解析度和位元速率表。 如果要求標準即時編碼器的預設值) ，或在 1080p (要求 Premium1080p 即時) 編碼器的預設值，以及最多6個層級，請確認 720p (只有一個圖層。

## <a name="creating-live-events"></a>建立實況活動

### <a name="options"></a>選項。

當您建立即時事件時，可以指定下列選項：

* 您可以提供實況活動的名稱和描述。
* 雲端編碼包含傳遞 (沒有任何雲端編碼) 、標準 (高達 720p) 或 Premium (高達 1080p) 。 針對標準和 Premium 編碼，您可以選擇編碼影片的延展模式。
  * 無：嚴格遵守編碼預設值中指定的輸出解析度，而不考慮輸入影片的圖元外觀比例或顯示外觀比例。
  * AutoSize：覆寫輸出解析度，並將其變更為符合輸入的顯示外觀比例（不含填補）。 例如，如果輸入是1920x1080，而且編碼預設會要求 1280 x 1280，則預設中的值會被覆寫，而且輸出會在1280x720 上，其會維持輸入的外觀比例為16:9。
  * 自動調整：使用黑邊或要件方塊來填補輸出 (，) 以接受輸出解析度，同時確保輸出中的使用中影片區域與輸入具有相同的外觀比例。 例如，如果輸入是1920x1080，而且編碼預設會要求 1280 x 1280，則輸出將會是 1280 x 1280，其中包含1280x720 的內部矩形，其外觀比例為16:9，而左邊和右邊的要件方塊區域是280圖元。
* 目前) 支援 RTMP 和 Smooth Streaming 通訊協定 (的串流通訊協定。 當實況活動或其相關聯的即時輸出正在執行時，您無法變更通訊協定選項。 如果您需要不同的通訊協定，請為每個串流通訊協定建立個別的實況活動。
* 輸入識別碼，這是實況活動輸入資料流程的全域唯一識別碼。
* 靜態主機名稱前置詞，其中包含無 (在這種情況下，將會使用隨機的128位十六進位字串) 、使用即時事件名稱，或使用自訂名稱。  當您選擇使用客戶名稱時，此值為自訂主機名稱前置詞。
* 您可以設定輸入主要畫面格間隔（這是 HLS 輸出中每個媒體區段的持續 (時間，以秒為單位) ），以減少即時廣播與播放之間的端對端延遲。 值應為介於0.5 到20秒的非零整數。  如果未設定 *任何* 輸入或輸出主要畫面格間隔，此值會預設為2秒。 只有在傳遞事件上才允許主要畫面格間隔。
* 建立事件時，您可以將它設定為自動啟動。 當自動啟動設定為 true 時，即時事件將會在建立後啟動。 當實況活動開始執行時，就會立即開始計費。 您必須在實況活動資源上明確呼叫 Stop，以停止進一步的計費。 或者，您可以在準備好開始進行串流處理時啟動事件。

> [!NOTE]
> 標準和 Premium 編碼的最大畫面播放速率為 30 fps。

## <a name="standby-mode"></a>待命模式

當您建立即時事件時，可以將它設定為待命模式。 當事件處於待命模式時，您可以編輯描述、靜態主機名稱前置詞，以及限制輸入和預覽存取設定。  待命模式仍然是可計費的模式，但價格與您啟動即時資料流時的價格不同。

如需詳細資訊，請參閱 [實況活動狀態和帳單](live-event-states-billing.md)。

* 內嵌和預覽的 IP 限制。 您可以定義允許將影片內嵌到此即時事件的 IP 位址。 允許的 IP 位址可以指定為單一 IP 位址 (例如 ‘10.0.0.1’)、使用 IP 位址和 CIDR 子網路遮罩的 IP 範圍 (例如 ‘10.0.0.1/22’)，或是使用 IP 位址和小數點十進位子網路遮罩的 IP 範圍 (例如 '10.0.0.1(255.255.252.0)')。
<br/><br/>
如果未指定 IP 位址而且也未定義規則，則任何 IP 位址都不允許。 若要允許任何 IP 位址，請建立規則，並設定 0.0.0.0/0。<br/>IP 位址必須是下列其中一種格式：具有四個數字或 CIDR 位址範圍的 IpV4 位址。
<br/><br/>
如果您想要在自己的防火牆上啟用特定 Ip，或想要將即時事件的輸入限制為 Azure IP 位址，請從 [Azure 資料中心 IP 位址範圍](https://www.microsoft.com/download/details.aspx?id=41653)下載 JSON 檔案。 如需此檔案的詳細資訊，請選取頁面上的 [ **詳細資料** ] 區段。

* 建立事件時，您可以選擇開啟即時轉譯。 預設會停用即時轉譯。 如需即時轉譯的詳細資訊，請參閱 [即時](live-transcription.md)轉譯。

### <a name="naming-rules"></a>命名規則

* 最大實況活動名稱為32個字元。
* 此名稱應遵循此 [RegEx](/dotnet/standard/base-types/regular-expression-language-quick-reference) 模式： `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$` 。

另請參閱 [串流端點命名慣例](streaming-endpoint-concept.md#naming-convention)。

> [!TIP]
> 若要確保您的即時事件名稱是唯一的，您可以產生 GUID，然後移除所有連字號和大括弧 (如果有任何) 。 字串在所有實況活動中都是唯一的，且其長度保證為32。

## <a name="live-event-ingest-urls"></a>實況活動內嵌 Url

建立即時事件之後，您可以取得將提供給即時內部部署編碼器的內嵌 Url。 即時編碼器會使用這些 URL 來輸入即時資料流。 如需詳細資訊，請參閱[建議的內部部署即時編碼器](recommended-on-premises-live-encoders.md)。

>[!NOTE]
> 從 2020-05-01 API 版本，虛名 Url 稱為靜態主機名稱

您可以使用非虛名 URL 或虛名 URL。

> [!NOTE]
> 針對要預測的內嵌 URL，設定「虛名」模式。

* 非虛名 URL

    非虛名 URL 是媒體服務 v3 中的預設模式。 您可能會快速取得實況活動，但只有在啟動實況活動時，才會知道內嵌 URL。 如果您停止/開始實況活動，URL 將會變更。 當使用者想要使用應用程式來串流應用程式，而該應用程式想要儘快取得實況活動，且動態內嵌 URL 不是問題時，非虛名會很有用。

    如果用戶端應用程式不需要在建立實況活動之前預先產生內嵌 URL，請讓媒體服務自動產生實況活動的存取權杖。

* 虛名 URL

    使用硬體廣播編碼器的大型媒體廣播商偏好虛名模式，而且不想要在啟動實況活動時重新設定其編碼器。 這些廣播想要預測的內嵌 URL，此 URL 不會隨著時間而改變。

    > [!NOTE]
    > 在 Azure 入口網站中，虛名 URL 的名稱為「*靜態主機名稱前置* 詞」。

    若要在 API 中指定此模式， `useStaticHostName` 請 `true` 在建立時將設定為， (預設值為 `false`) 。 當 `useStaticHostname` 設為 true 時，會 `hostnamePrefix` 指定主機名稱的第一個部分指派給實況事件預覽和內嵌端點。 最終的主機名稱會是此前置詞的組合、媒體服務帳戶名稱，以及 Azure 媒體服務資料中心的簡短程式碼。

    若要避免在 URL 中使用隨機權杖，您也必須在建立時將您自己的存取權杖 (`LiveEventInput.accessToken`) 。  存取權杖必須是有效的 GUID 字串 (包含或不含連字號) 。 設定模式之後，即無法更新。

    存取權杖在您的資料中心內必須是唯一的。 如果您的應用程式需要使用虛名 URL，建議您一律為存取權杖建立新的 GUID 實例， (而不是重複使用任何現有的 GUID) 。

    使用下列 Api 來啟用虛名 URL，並將存取權杖設定為有效的 GUID (例如 `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`) 。  

    |Language|啟用虛名 URL|設定存取權杖|
    |---|---|---|
    |REST|[vanityUrl](/rest/api/media/liveevents/create#liveevent)|[LiveEventInput. accessToken](/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--虛名-url](/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--存取權杖](/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[LiveEvent. VanityUrl](/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput. AccessToken](/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|

### <a name="live-ingest-url-naming-rules"></a>即時內嵌 URL 命名規則

* 下方的 *random* \(隨機\) 字串是 128 位元的十六進位數字 (由 32 個字元的 0-9 a-f 所組成)。
* *您的存取權杖*：您在使用虛名模式時所設定的有效 GUID 字串。 例如： `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"` 。
* *資料流程名稱*：指出特定連接的資料流程名稱。 資料流程名稱值通常是由您所使用的即時編碼器所加入。 您可以設定即時編碼器使用任何名稱來描述連接，例如： "video1_audio1"、"video2_audio1"、"stream"。

#### <a name="non-vanity-url"></a>非虛名 URL

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>虛名 URL

在下列路徑中， `<live-event-name>` 表示提供給事件的名稱，或建立實況活動時所使用的自訂名稱。

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>實況活動預覽 URL

當實況活動開始接收投稿摘要之後，您就可以使用其預覽端點來預覽和驗證您是否收到即時串流，然後再進行進一步的發佈。 檢查預覽資料流程是否良好之後，您可以使用實況活動，讓即時串流可透過一或多個 (預先建立的) 串流端點來傳遞。 若要完成此動作，請在實況活動上建立新的 [即時輸出](/rest/api/media/liveoutputs) 。

> [!IMPORTANT]
> 請先確定視訊流向預覽 URL，再繼續操作！

## <a name="live-event-long-running-operations"></a>即時事件長時間執行的作業

如需詳細資訊，請參閱 [長時間執行的作業](media-services-apis-overview.md#long-running-operations)。

## <a name="live-outputs"></a>即時輸出

當資料流程流入實況活動之後，您就可以建立 [資產](/rest/api/media/assets)、 [即時輸出](/rest/api/media/liveoutputs)和 [串流定位器](/rest/api/media/streaminglocators)來開始串流事件。 即時輸出會封存資料流程，並透過 [串流端點](/rest/api/media/streamingendpoints)將其提供給檢視器。  

如需有關即時輸出的詳細資訊，請參閱 [使用雲端 DVR](live-event-cloud-dvr.md)。

## <a name="frequently-asked-questions"></a>常見問題集

請參閱 [常見問題](frequently-asked-questions.md#live-streaming) 文章。

## <a name="ask-questions-and-get-updates"></a>提出問題並取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

[即時串流教學課程](stream-live-tutorial-with-api.md)
