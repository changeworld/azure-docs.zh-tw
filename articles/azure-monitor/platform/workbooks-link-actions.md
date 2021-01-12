---
title: Azure 監視器活頁簿連結動作
description: 如何在 Azure 監視器活頁簿中使用連結動作
ms.topic: conceptual
ms.date: 01/07/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: 3e94f1a70d5fa2a6e132dc6dc6f95439959b07f0
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98122121"
---
# <a name="link-actions"></a>連結動作

您可以透過活頁簿連結步驟，或透過方格、[標題](workbooks-tile-visualizations.md)或[圖表](workbooks-graph-visualizations.md)[的資料行設定來存取](workbooks-grid-visualizations.md)連結動作。

## <a name="general-link-actions"></a>一般連結動作

| 連結動作 | 按一下時的動作 |
|:------------- |:-------------|
| `Generic Details` | 顯示內容方格內容視圖中的資料列值。 |
| `Cell Details` | 顯示內容方格內容視圖中的資料格值。 當儲存格包含具有資訊 (的動態型別時很有用，例如，具有位置、角色實例等要求屬性的 json ) 。 |
| `Url` | 儲存格的值應為有效的 HTTP url，而且儲存格將會是在新的索引標籤中開啟該 url 的連結。|

## <a name="application-insights"></a>Application Insights

| 連結動作 | 按一下時的動作 |
|:------------- |:-------------|
| `Custom Event Details` | 開啟 Application Insights 搜尋詳細資料，其中包含資料格的自訂事件識別碼 (`itemId`) 。 |
| `* Details` | 類似于自訂事件詳細資料，但相依性、例外狀況、網頁檢視、要求和追蹤除外。 |
| `Custom Event User Flows` | 開啟在資料格中的自訂事件名稱上切換的 Application Insights 消費者流程體驗。 |
| `* User Flows` | 類似于自訂事件消費者流程例外狀況、頁面流覽和要求除外。 |
| `User Timeline` | 以資料格中的使用者識別碼 () 開啟使用者時間軸 `user_Id` 。 |
| `Session Timeline` | 開啟儲存格中值的 Application Insights 搜尋體驗 (例如，搜尋文字 ' abc '，其中 abc 是資料格) 中的值。 |

`*` 表示上表的萬用字元

## <a name="azure-resource"></a>Azure 資源

| 連結動作 | 按一下時的動作 |
|:------------- |:-------------|
| `ARM Deployment` | 部署 Azure Resource Manager 範本。  選取此專案時，會顯示其他欄位，讓作者設定要開啟的 Azure Resource Manager 範本、範本的參數等等。 [請參閱 Azure Resource Manager 部署連結設定](#azure-resource-manager-deployment-link-settings)。 |
| `Create Alert Rule` | 建立資源的警示規則。  |
| `Custom View` | 開啟自訂視圖。 選取這個專案時，會顯示其他欄位，讓作者設定視圖延伸、視圖名稱，以及用來開啟視圖的任何參數。 [請參閱自訂視圖](#custom-view-link-settings)。 |
| `Metrics` | 開啟計量視圖。  |
| `Resource overview` | 根據資料格中的資源識別碼值，在入口網站中開啟資源的視圖。 作者也可以選擇性地設定 `submenu` 值，以在資源檢視中開啟特定的功能表項目。 |
| `Workbook (template)` | 開啟活頁簿範本。  選取這個專案時，會顯示其他欄位，讓作者設定要開啟的範本等等。  |

## <a name="link-settings"></a>連結設定

使用連結轉譯器時，可以使用下列設定：

![連結設定的螢幕擷取畫面](./media/workbooks-link-actions/link-settings.png)

| 設定 | 說明 |
|:------------- |:-------------|
| `View to open` | 允許作者選取上面列舉的其中一個動作。 |
| `Menu item` | 如果選取了 [資源總覽]，這就是資源總覽中要開啟的功能表項目。 這可以用來開啟警示或活動記錄，而不是資源的「總覽」。 每個 Azure 的功能表項目值都不同 `Resourcetype` 。|
| `Link label` | 如果有指定，此值會顯示在方格資料行中。 如果未指定此值，將會顯示儲存格的值。 如果您想要顯示另一個值（例如熱度圖或圖示），請 `Link` 改為使用適當的轉譯器，然後選取 `Make this item a link` 選項。 |
| `Open link in Context Blade` | 如果有指定，連結將會開啟為視窗右邊的快顯視窗「內容」，而不是以完整模式開啟。 |

使用選項時 `Make this item a link` ，可以使用下列設定：

| 設定 | 說明 |
|:------------- |:-------------|
| `Link value comes from` | 當您將儲存格顯示為具有連結的轉譯器時，此欄位會指定連結中所使用的「連結」值來自何處，讓作者可以從方格中其他資料行的下拉式清單中選取。 例如，資料格可能是熱度圖值，但您想要讓連結開啟資料列中資源識別碼的資源總覽。 在這種情況下，您會將連結值設定為來自 `Resource Id` 欄位。
| `View to open` | 同上。 |
| `Menu item` | 同上。 |
| `Open link in Context Blade` | 同上。 |

## <a name="azure-resource-manager-deployment-link-settings"></a>Azure Resource Manager 部署連結設定

如果選取的連結類型為，則 `ARM Deployment` 作者必須指定其他設定，才能開啟 Azure Resource Manager 部署。 設定有兩個主要索引標籤。

### <a name="template-settings"></a>範本設定

此區段會定義範本的來源，以及用來執行 Azure Resource Manager 部署的參數。

| 來源 | 說明 |
|:------------- |:-------------|
|`Resource group id comes from` | 資源識別碼可用來管理已部署的資源。 訂用帳戶可用來管理已部署的資源和成本。 資源群組是用來組織及管理您所有資源的資料夾。 如果未指定此值，部署將會失敗。 從 `Cell` 、 `Column` 、 `Static Value` 或 `Parameter` 在 [連結來源](#link-sources)中選取。|
|`ARM template URI from` | Azure Resource Manager 範本本身的 URI。 範本 URI 必須可供將部署範本的使用者存取。 從 `Cell` 、 `Column` 、 `Parameter` 或 `Static Value`  在 [連結來源](#link-sources)中選取。 針對初學者，請看看我們的 [快速入門範本](https://azure.microsoft.com/resources/templates/)。|
|`ARM Template Parameters` | 此區段會定義用於上述定義之範本 URI 的範本參數。 這些參數將用來在 [執行] 頁面上部署範本。 方格包含展開工具列按鈕，可協助您使用範本 URI 中定義的名稱來填滿參數，並將其設定為靜態空白值。 只有在方格中沒有任何參數，且已設定範本 URI 時，才能使用這個選項。 下半部是參數輸出的外觀預覽。 選取 [重新整理]，以目前的變更來更新預覽。 參數通常是值，而參考是指可指向 keyvault 使用者有權存取之秘密的一些內容。 <br/><br/> **範本檢視器分頁限制** -不會正確轉譯參考參數，而且會顯示為 null/值，因此使用者將無法從 [範本檢視器] 索引標籤正確地部署參考參數。|

![Azure Resource Manager 範本設定的螢幕擷取畫面](./media/workbooks-link-actions/template-settings.png)

### <a name="ux-settings"></a>UX 設定

此區段會設定使用者在執行 Azure Resource Manager 部署之前將看到的內容。

| 來源 | 說明 |
|:------------- |:-------------|
|`Title from` | 用於執行視圖的標題。 從 `Cell` 、 `Column` 、 `Parameter` 或 `Static Value` 在 [連結來源](#link-sources)中選取。|
|`Description from` | 這是用來在使用者想要部署範本時，為使用者提供實用描述的 markdown 文字。 從 `Cell` 、 `Column` 、 `Parameter` 或 `Static Value`  在 [連結來源](#link-sources)中選取。 <br/><br/> **注意：** 如果 `Static Value` 選取 []，將會顯示多行的文字方塊。 在這個文字方塊中，您可以使用來解析參數 `{paramName}` 。 此外，您也可以將資料行視為參數，方法是在資料 `_column` 行名稱之後附加（例如） `{columnName_column}` 。 在以下的範例影像中，我們可以藉 `VMName` 由撰寫來參考資料行 `{VMName_column}` 。 冒號之後的值是 [參數格式](workbooks-parameters.md#parameter-options)器，在此案例中為 `value` 。|
|`Run button text from` | 用於執行 (執行) 按鈕上的標籤，以部署 Azure Resource Manager 範本。 這是使用者將選取以開始部署 Azure Resource Manager 範本的內容。|

![Azure Resource Manager UX 設定的螢幕擷取畫面](./media/workbooks-link-actions/ux-settings.png)

設定這些設定之後，當使用者選取該連結時，它就會開啟具有 ux 設定中所述 UX 的視圖。 如果使用者選取此 `Run button text from` 值，將會使用 [範本設定](#template-settings)中的值來部署 Azure Resource Manager 範本。 View 範本將開啟 [範本檢視器] 索引標籤，讓使用者在部署之前檢查範本和參數。

![執行 Azure Resource Manager 視圖的螢幕擷取畫面](./media/workbooks-link-actions/run-tab.png)

## <a name="custom-view-link-settings"></a>自訂視圖連結設定

您可以使用這個來開啟 Azure 入口網站中的自訂視圖。 確認所有設定和設定。 不正確的值會在入口網站中造成錯誤，或無法正確開啟 views。 有兩種方式可以透過或設定設定 `Form` `URL` 。

> [!NOTE]
> 使用功能表的 [視圖] 無法在內容索引標籤中開啟。如果已設定功能表的視圖在內容索引標籤中開啟，則在選取連結時，不會顯示任何內容索引標籤。

### <a name="form"></a>表單

| 來源 | 說明 |
|:------------- |:-------------|
|`Extension name` | 主控視圖名稱的延伸模組名稱。|
|`View name` | 要開啟的視圖名稱。|

#### <a name="view-inputs"></a>查看輸入

有兩種類型的輸入、格線和 JSON。 `Grid`針對簡單索引鍵和值索引標籤輸入使用，或選取 `JSON` 以指定嵌套的 JSON 輸入。

- Grid
    - `Parameter Name`： View 輸入參數的名稱。
    - `Parameter Comes From`： View 參數的值應該來自何處。 從 `Cell` 、 `Column` 、 `Parameter` 或 `Static Value` 在 [連結來源](#link-sources)中選取。
    > [!NOTE]
    > 如果 `Static Value` 選取 []，可以使用文字方塊中的括弧連結來解析參數 `{paramName}` 。 您可以在資料行名稱之後附加，將資料行視為參數資料行 `_column` `{columnName_column}` 。

    - `Parameter Value`：根據 `Parameter Comes From` ，這會是可用參數、資料行或靜態值的下拉式清單。

    ![編輯資料行設定從表單顯示自訂視圖設定的螢幕擷取畫面。](./media/workbooks-link-actions/custom-tab-settings.png)
- JSON
    - 在編輯器上以 JSON 格式指定索引標籤輸入。 `Grid`和模式、參數和資料行一樣，可以使用 `{paramName}` for 參數和資料行來參考 `{columnName_column}` 。 選取 `Show JSON Sample` 時，將會顯示所有已解決參數的預期輸出，以及使用者針對視圖輸入的資料行。

    ![顯示開啟自訂視圖設定的螢幕擷取畫面，其中包含 JSON 上的 view 輸入。](./media/workbooks-link-actions/custom-tab-json.png)

### <a name="url"></a>URL

貼上包含副檔名的入口網站 URL、視圖名稱，以及開啟視圖所需的任何輸入。 選取之後 `Initialize Settings` ，它會填入作者的， `Form` 以新增/修改/移除任何視圖輸入。

![顯示資料行設定顯示 URL 中自訂視圖設定的螢幕擷取畫面。 ](./media/workbooks-link-actions/custom-tab-settings-url.png)

## <a name="workbook-template-link-settings"></a>活頁簿 (範本) 連結設定

如果選取的連結類型為 `Workbook (Template)` ，則作者必須指定其他設定，才能開啟正確的活頁簿範本。 下列設定具有方格如何針對每個設定尋找適當值的選項。

| 設定 | 說明 |
|:------------- |:-------------|
| `Workbook owner Resource Id` | 這是「擁有」活頁簿之 Azure 資源的資源識別碼。 這通常是 Application Insights 資源或 Log Analytics 工作區。 在 Azure 監視器中，這也可能是常值字串 `"Azure Monitor"` 。 儲存活頁簿時，活頁簿將會連結到此。 |
| `Workbook resources` | Azure 資源識別碼的陣列，可指定活頁簿中使用的預設資源。 例如，如果要開啟的範本顯示虛擬機器計量，此處的值會是虛擬機器資源識別碼。  許多時候，擁有者和資源會設定為相同的設定。 |
| `Template Id` | 指定要開啟之範本的識別碼。 如果這是來自資源庫的社區範本 (最常見的案例) ，請在範本的路徑前面加 `Community-` 上前置詞， `Community-Workbooks/Performance/Apdex` 例如 `Workbooks/Performance/Apdex` 範本。 如果這是儲存的活頁簿/範本的連結，就是該專案的完整 Azure 資源識別碼。 |
| `Workbook Type` | 指定要開啟的活頁簿範本種類。 最常見的情況是使用 `Default` 或 `Workbook` 選項，以使用目前活頁簿中的值。 |
| `Gallery Type` | 這會指定要在開啟之範本的「資源庫」視圖中顯示的資源庫類型。 最常見的情況是使用 `Default` 或 `Workbook` 選項，以使用目前活頁簿中的值。 |
|`Location comes from` | 如果您要開啟特定的活頁簿資源，則應該指定 [位置] 欄位。 如果未指定位置，則尋找活頁簿內容的速度會慢很多。 如果您知道位置，請加以指定。 如果您不知道位置或正在開啟沒有特定位置的範本，請將此欄位保留為「預設」。|
|`Pass specific parameters to template` | 選取此參數可將特定參數傳遞至範本。 如果選取此選項，則只會將指定的參數傳遞至範本，否則會將目前活頁簿中的所有參數傳遞至範本，而且在這種情況下，這兩個活頁簿中的參數 *名稱* 必須相同，才能讓此參數值正常運作。|
|`Workbook Template Parameters` | 此區段會定義傳遞至目標範本的參數。 名稱應該與目標範本中的參數名稱相符。 從 `Cell` 、、和中選取 [值] `Column` `Parameter` `Static Value` 。 名稱和值不得為空白，以將該參數傳遞至目標範本。|

針對上述每個設定，作者必須挑選連結的活頁簿中的值來源。 請參閱 [連結來源](#link-sources)

開啟活頁簿連結時，新的活頁簿視圖將會傳遞上述設定中設定的所有值。

![活頁簿連結設定的螢幕擷取畫面](./media/workbooks-link-actions/workbook-link-settings.png)

![活頁簿範本參數設定的螢幕擷取畫面](./media/workbooks-link-actions/workbook-template-link-settings-parameter.png)

## <a name="link-sources"></a>連結來源

| 來源 | 說明 |
|:------------- |:-------------|
| `Cell` | 這會使用方格中該資料格的值做為連結值 |
| `Column` | 若選取此選項，則會顯示另一個欄位，讓作者在方格中選取另一個資料行。  該資料列的資料行值將用於連結值中。 這通常用來啟用方格的每個資料列，以開啟不同的範本，方法是將 `Template Id` 欄位設定為 `column` ，如果 `Workbook resources` 欄位設定為包含 Azure 資源識別碼的資料行，則開啟不同資源的相同活頁簿範本 |
| `Parameter` | 選取時，會顯示另一個欄位，讓作者選取參數。 按一下連結時，該參數的值將會用來做為值 |
| `Static value` | 若選取此選項，則會顯示另一個欄位，讓作者輸入將在連結的活頁簿中使用的靜態值。 當方格中的所有資料列都使用相同的欄位值時，通常會使用這種方式。 |
| `Step` | 使用在活頁簿的目前步驟中設定的值。 這在查詢和計量步驟中很常見，可將連結的活頁簿中的活頁簿資源設定為 *查詢/計量步驟* 中所使用的資源，而不是目前的活頁簿。 |
| `Workbook` | 使用目前活頁簿中設定的值。 |
| `Default` | 如果未指定任何值，則使用預設值。 這常見於資源庫類型，其中預設資源庫會由擁有者資源的類型設定 |

## <a name="next-steps"></a>後續步驟

- [控制](workbooks-access-control.md) 和共用活頁簿資源的存取權。
- 瞭解如何在活頁 [簿中使用群組](workbooks-groups.md)。