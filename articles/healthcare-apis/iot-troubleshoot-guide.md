---
title: 適用于 FHIR 的 Azure IoT Connector (preview) -疑難排解指南和操作說明
description: 如何針對 FHIR (preview 的常見 Azure IoT Connector 進行疑難排解) 錯誤訊息和條件，以及複製對應檔
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 11/09/2020
ms.author: jasteppe
ms.openlocfilehash: 124c3b3667e847a5ee1bb8034ef01088c629d503
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540938"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>適用于 FHIR 的 Azure IoT Connector (preview) 疑難排解指南

本文提供針對 FHIR 的常見 Azure IoT Connector * 錯誤訊息和條件進行疑難排解的步驟。  

您也將瞭解如何建立適用于 FHIR 轉換對應 JSON (的 Azure IoT Connector 複本，例如：裝置和 FHIR) 。  

您可以使用轉換對應 JSON 複本，在 Azure 入口網站之外進行編輯和封存。  

> [!TIP]
> 如果您將針對 Azure IoT Connector for FHIR 開啟 [Azure 技術支援](https://azure.microsoft.com/support/create-ticket/) 票證，請務必包含轉換對應 JSON 的複本，以協助進行疑難排解程式。

## <a name="device-and-fhir-conversion-mapping-json-template-validations-for-azure-iot-connector-for-fhir-preview"></a>適用于 FHIR (preview) 的 Azure IoT Connector 的裝置和 FHIR 轉換對應 JSON 範本驗證
在本節中，您將瞭解 Azure IoT Connector for FHIR 執行的驗證程式，以驗證裝置和 FHIR 轉換對應 JSON 範本，然後才允許將其儲存以供使用。  裝置和 FHIR 轉換對應 JSON 需要這些元素。

**裝置對應**

|元素|必要|
|:-------|:------|
|TypeName|是|
|TypeMatchExpression|是|
|DeviceIdExpression|是|
|TimestampExpression|是|
|值 []。ValueName|是|
|值 []。ValueExpression|是|

> [!NOTE]
> 值 []。ValueName 和 Values []。ValueExpression
>
> 只有當陣列中有值專案時，才需要這些元素，因為沒有對應的值是有效的。 當傳送的遙測是事件時，就會使用此專案。 例如：開啟或移除穿戴式 IoMT 裝置時。  (s) 的元素沒有任何值，但 Azure 針對 FHIR 所 IoT Connector 的名稱符合和發出。 在 FHIR 轉換中，適用于 FHIR 的 Azure IoT Connector 會將其對應至以語義類型為基礎的程式碼能力概念-不會填入實際值。

**FHIR 對應**

|元素|必要|
|:------|:-------|
|TypeName|是|

> [!NOTE]
> 這是目前唯一驗證的必要 FHIR 對應元素。

## <a name="error-messages-and-fixes-for-azure-iot-connector-for-fhir-preview"></a>適用于 FHIR (preview) 的 Azure IoT Connector 的錯誤訊息和修正

|訊息|顯示|條件|修正| 
|-------|---------|---------|---|
|不正確對應名稱，對應名稱應為 device 或 FHIR。|API|提供的對應類型不是裝置或 FHIR。|使用其中一種支援的對應類型 (例如：裝置或 FHIR) 。|
|驗證失敗。 缺少必要資訊或其無效。|API 和 Azure 入口網站|嘗試儲存轉換對應遺失所需的資訊或元素。|新增遺漏的轉換對應資訊或元素，然後再次嘗試儲存轉換對應。|
|重新產生未定義的金鑰參數。|API|重新產生金鑰要求。|在重新產生金鑰要求中包含參數。|
|已達到可在此訂用帳戶中布建之 IoT Connector 實例的數目上限。|API 和 Azure 入口網站|已達到適用于 FHIR 訂用帳戶配額的 Azure IoT Connector (預設為每個訂用帳戶)  (2) 。|刪除其中一個現有的 Azure IoT Connector 實例以進行 FHIR。  使用尚未達到訂用帳戶配額的不同訂用帳戶。  要求增加訂用帳戶配額。|
|IoT Connector 啟用的 Azure API for FHIR 資源不支援移動資源。|API 和 Azure 入口網站|正在嘗試對具有一個或多個 Azure IoT Connector FHIR 實例的 Azure API for FHIR 資源進行移動作業。|刪除 Azure IoT Connector 的現有實例 () ，以便 FHIR 進行移動作業。|
|未布建 IoT Connector。|API|如果未布建父 (Azure IoT Connector for FHIR) ，則嘗試使用子服務 (連接 & 對應) 。|布建適用于 FHIR 的 Azure IoT Connector。|
|不支援此要求。|API|不支援特定的 API 要求。|使用正確的 API 要求。|
|帳戶不存在。|API|嘗試新增適用于 FHIR 的 Azure IoT Connector，但 Azure API for FHIR 資源不存在。|建立 Azure API for FHIR 資源，然後再次嘗試操作。|
|IoT Connector 不支援 Azure API for FHIR 資源 FHIR 版本。|API|嘗試使用 FHIR 的 Azure IoT Connector 與 Azure API for FHIR 資源的不相容版本。|建立新的 Azure API for FHIR 資源 (版本 R4) 或使用現有的 Azure API for FHIR 資源 (版本 R4) 。

## <a name="why-is-my-azure-iot-connector-for-fhir-preview-data-not-showing-up-in-azure-api-for-fhir"></a>為什麼我的 Azure IoT Connector for FHIR (preview) 資料未顯示在 Azure API for FHIR 中？

|潛在問題|修正|
|----------------|-----|
|資料仍在處理中。|資料會以批次方式輸出至 Azure API for FHIR， (每隔約15分鐘) 。  資料可能仍在處理中，而且需要額外的時間，資料才會保存在 Azure API for FHIR 中。|
|尚未設定裝置轉換對應 JSON。|設定並儲存符合規範的裝置轉換對應 JSON。|
|尚未設定 FHIR 轉換對應 JSON。|設定並儲存符合規範的 FHIR 轉換對應 JSON。|
|裝置訊息不包含在裝置對應中定義的預期運算式。|確認裝置對應中定義的 JsonPath 運算式符合裝置訊息中定義的權杖。|
|未在 Azure API for FHIR (解析類型中建立裝置資源：僅查閱) *。|在 Azure API for FHIR 中建立有效的裝置資源。 請確定裝置資源包含的識別碼符合傳入訊息中提供的裝置識別碼。|
|尚未在 Azure API for FHIR (的解決方案類型中建立患者資源：僅限查閱) *。|在 Azure API for FHIR 中建立有效的患者資源。|
|未設定設備. 患者參考，或參考無效 (解析類型：僅查閱) *。|請確定裝置資源包含患者資源的有效 [參考](https://www.hl7.org/fhir/device-definitions.html#Device.patient) 。| 

* 參考 [快速入門：部署 azure IoT Connector (preview) 使用 Azure 入口網站](iot-fhir-portal-quickstart.md#create-new-azure-iot-connector-for-fhir-preview) 來取得適用于 FHIR 解析類型的 azure IoT Connector 功能描述 (例如：查閱或建立) 。

## <a name="use-metrics-to-troubleshoot-issues-in-azure-iot-connector-for-fhir-preview"></a>使用計量來針對 Azure IoT Connector for FHIR (preview 中的問題進行疑難排解) 

適用于 FHIR 的 Azure IoT Connector 會產生多個計量，以提供資料流程流程的見解。 其中一個支援的計量稱為 *總計錯誤* ，可針對 FHIR 的 Azure IoT Connector 實例中發生的所有錯誤提供計數。

每個錯誤都會記錄許多相關聯的屬性。 每個屬性都會提供有關錯誤的不同層面，這可協助您找出問題並進行疑難排解。 此區段會列出針對 [ *總錯誤* ] 度量中的每個錯誤所捕捉的不同屬性，以及這些屬性的可能值。

> [!NOTE]
> 您可以流覽至適用于 FHIR (preview 的 Azure IoT Connector 實例的 [錯誤] 計量 *總計* ) 如 [AZURE IoT Connector for FHIR (preview) 計量] 頁面](iot-metrics-display.md)所述。

按一下 [ *錯誤總計* ] 圖形，然後按一下 [ *加入篩選* ] 按鈕，以使用下列所述的任何屬性來配量並骰子錯誤度量。

### <a name="the-operation-performed-by-the-azure-iot-connector-for-fhir-preview"></a>適用于 FHIR (preview 的 Azure IoT Connector 所執行的作業) 

這個屬性工作表示發生錯誤時，IoT Connector 所執行的作業。 作業通常代表處理裝置訊息時的資料流程階段。 以下是這個屬性的可能值清單。

> [!NOTE]
> 您可以在 [這裡](iot-data-flow.md)深入瞭解 Azure IoT Connector 中適用于 FHIR (preview) 的不同資料流程階段。

|資料流程階段|描述|
|---------------|-----------|
|安裝程式|IoT Connector 設定實例的特定作業|
|正規化|用來正規化裝置資料的資料流程階段|
|群組|將正規化資料分組的資料流程階段|
|FHIRConversion|將群組標準化資料轉換成 FHIR 資源的資料流程階段|
|未知|發生錯誤時，作業類型未知|

### <a name="the-severity-of-the-error"></a>錯誤的嚴重性

這個屬性工作表示所發生之錯誤的嚴重性。 以下是這個屬性的可能值清單。

|嚴重性|描述|
|---------------|-----------|
|警告|資料流程進程中有一些小問題，但是裝置訊息的處理未停止|
|錯誤|特定裝置訊息的處理已遇到錯誤，其他訊息可能會如預期般繼續執行|
|Critical|IoT Connector 有某些系統層級的問題，而且沒有任何訊息需要處理|

### <a name="the-type-of-the-error"></a>錯誤的類型

這個屬性工作表示給定錯誤的分類，基本上代表類似錯誤類型的邏輯群組。 以下是這個屬性的可能值清單。

|錯誤類型|描述|
|----------|-----------|
|DeviceTemplateError|與裝置對應範本相關的錯誤|
|DeviceMessageError|處理特定裝置訊息時發生錯誤|
|FHIRTemplateError|與 FHIR 對應範本相關的錯誤|
|FHIRConversionError|將訊息轉換成 FHIR 資源時發生錯誤|
|FHIRResourceError|FHIR 伺服器中與所參考之現有資源相關的錯誤 IoT Connector|
|FHIRServerError|與 FHIR 伺服器通訊時所發生的錯誤|
|GeneralError|所有其他類型的錯誤|

### <a name="the-name-of-the-error"></a>錯誤的名稱

這個屬性會提供特定錯誤的名稱。 以下是所有錯誤名稱的清單及其描述，以及相關聯的錯誤類型 (s) 、嚴重性和資料流程階段 (s) 。

|錯誤名稱|描述|錯誤類型 (s) |錯誤嚴重性|資料流程階段 (s) |
|----------|-----------|-------------|--------------|------------------|
|MultipleResourceFoundException|在 FHIR 伺服器中找到多個患者或裝置資源時，如果裝置訊息中有各自的識別碼，就會發生錯誤|FHIRResourceError|錯誤|FHIRConversion|
|TemplateNotFoundException|裝置或 FHIR 對應範本未設定 IoT Connector 的實例|DeviceTemplateError, FHIRTemplateError|Critical|正規化、FHIRConversion|
|CorrelationIdNotDefinedException|裝置對應範本中未指定相互關聯識別碼。 CorrelationIdNotDefinedException 是條件式錯誤，只有在 FHIR 觀察必須使用相互關聯識別碼來群組裝置度量，但未正確設定時，才會發生|DeviceMessageError|錯誤|正規化|
|PatientDeviceMismatchException|當 FHIR 伺服器上的裝置資源具有患者資源的參考，而該資源與訊息中的患者識別碼不相符時，就會發生此錯誤|FHIRResourceError|錯誤|FHIRConversionError|
|PatientNotFoundException|裝置 FHIR 資源（與裝置訊息中出現的裝置識別碼關聯）不會參考任何患者 FHIR 資源。 請注意，只有在使用 *查閱* 解析類型設定 IoT Connector 實例時，才會發生此錯誤|FHIRConversionError|錯誤|FHIRConversion|
|DeviceNotFoundException|與裝置訊息中的裝置識別碼相關聯的 FHIR 伺服器上不存在任何裝置資源|DeviceMessageError|錯誤|正規化|
|PatientIdentityNotDefinedException|當裝置對應範本上未設定從裝置訊息剖析患者識別碼的運算式，或裝置訊息中沒有患者識別碼時，就會發生此錯誤。 注意：只有當 IoT Connector 的解決類型設定為 [ *建立* ] 時，才會發生此錯誤|DeviceTemplateError|Critical|正規化|
|DeviceIdentityNotDefinedException|當裝置對應範本上未設定從裝置訊息剖析裝置識別碼的運算式，或裝置訊息中沒有裝置識別碼時，就會發生此錯誤|DeviceTemplateError|Critical|正規化|
|NotSupportedException|收到不支援格式的裝置訊息時，發生錯誤|DeviceMessageError|錯誤|正規化|

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-conversion-mapping-json"></a>建立 Azure IoT Connector for FHIR (preview 的複本) 轉換對應 JSON

複製適用于 FHIR 對應檔案的 Azure IoT Connector 可用於在 Azure 入口網站網站外部編輯和封存。

開啟支援票證以協助進行疑難排解時，應將對應檔案複製提供給 Azure 技術支援。

> [!NOTE]
> JSON 是目前唯一支援裝置和 FHIR 對應檔案的格式。

> [!TIP]
> 深入瞭解適用于 FHIR 裝置的 Azure IoT Connector [以及 FHIR 的轉換對應 JSON](./iot-mapping-templates.md)

1. 在 [ **增益集]** 區段中，選取 Azure API for FHIR 資源儀表板左下方的 **[IoT Connector (預覽) ]** 。

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="IoT Connector1" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. 選取您要從中複製轉換對應 JSON 的「 **連接器** 」。

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT Connector2" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> 此程式也可以用來複製和儲存「 **設定 FHIR 對應** 」的 JSON 內容。

3. 選取 **[設定裝置對應]** 。

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="IoT Connector3" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. 選取 JSON 的內容，並執行複製作業 (例如：選取 Ctrl + c) 。 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT Connector4" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. 進行貼上作業 (例如：選取 Ctrl + v) 到編輯器中的新檔案 (例如： Visual Studio Code、記事本) ，然後以 *. json 副檔名儲存檔案。

> [!TIP]
> 如果您將針對 Azure IoT Connector for FHIR 開啟 [Azure 技術支援](https://azure.microsoft.com/support/create-ticket/) 票證，請務必包含轉換對應 JSON 的複本，以協助進行疑難排解程式。

## <a name="next-steps"></a>後續步驟

查看 Azure IoT Connector for FHIR 的常見問題。

>[!div class="nextstepaction"]
>[適用于 FHIR 的 Azure IoT Connector 常見問題](fhir-faq.md)

*在 Azure 入口網站中，Azure IoT Connector for FHIR 稱為 IoT 連接器 (預覽)。

FHIR 是 HL7 的註冊商標，必須搭配 HL7 權限方可使用。