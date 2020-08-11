---
title: 適用于 FHIR 的 Azure IoT 連接器 (預覽) -疑難排解指南和操作說明
description: 如何針對適用于 FHIR 的常見 Azure IoT 連接器進行疑難排解 (預覽) 錯誤訊息和條件和複製對應檔案
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.author: jasteppe
ms.openlocfilehash: 088d1e409f14fdba02311d1ff17eb655f6e41ad3
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88053451"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>適用于 FHIR 的 Azure IoT 連接器 (預覽) 疑難排解指南

本文提供針對 FHIR 的常見 Azure IoT 連接器進行疑難排解的步驟 * 錯誤訊息和條件。  

您也將瞭解如何建立適用于 FHIR 轉換對應的 Azure IoT 連接器複本 JSON (例如：裝置和 FHIR) 。  

您可以使用轉換對應 JSON 複本，在 Azure 入口網站之外進行編輯和封存。  

> [!TIP]
> 如果您將針對適用于 FHIR 的 Azure IoT 連接器開啟[Azure 技術支援](https://azure.microsoft.com/support/create-ticket/)票證，請務必包含轉換對應 JSON 的複本，以協助進行疑難排解程式。

## <a name="error-messages-and-fixes-for-azure-iot-connector-for-fhir-preview"></a>適用于 FHIR (preview) 的 Azure IoT Connector 的錯誤訊息和修正

|訊息|看到|條件|修正| 
|-------|---------|---------|---|
|對應名稱無效，對應名稱應為 device 或 FHIR。|API|提供的對應類型不是 device 或 FHIR。|使用這兩種支援的對應類型之一 (例如： Device 或 FHIR) 。|
|驗證失敗。 缺少必要資訊或其無效。|API 和 Azure 入口網站|嘗試儲存轉換對應遺漏所需的資訊或元素。|請加入遺漏的轉換對應資訊或專案，並嘗試再次儲存轉換對應。|
|未定義重新產生金鑰參數。|API|重新產生金鑰要求。|在重新產生金鑰要求中包含參數。|
|已達到可在此訂用帳戶中布建的 IoT 連接器實例數目上限。|API 和 Azure 入口網站|已達到適用于 FHIR 訂用帳戶配額的 Azure IoT Connector (預設為每個訂用帳戶)  (2) 。|刪除其中一個現有的 Azure IoT Connector for FHIR 實例。  請使用尚未到達訂用帳戶配額的其他訂用帳戶。  要求增加訂用帳戶配額。|
|啟用 Azure API for FHIR 資源的 IoT 連接器不支援移動資源。|API 和 Azure 入口網站|嘗試在有一或多個 Azure IoT Connector for FHIR 實例的 Azure API for FHIR 資源上執行移動作業。|刪除現有的實例 (s) 的 Azure IoT Connector for FHIR，以執行移動操作。|
|IoT 連接器未布建。|API|當父 (適用于 FHIR 的 Azure IoT 連接器) 尚未布建時，嘗試使用子服務 (連接 & 對應) 。|布建適用于 FHIR 的 Azure IoT 連接器。|
|不支援此要求。|API|不支援特定的 API 要求。|請使用正確的 API 要求。|
|帳戶不存在。|API|嘗試新增適用于 FHIR 的 Azure IoT 連接器和 Azure API for FHIR 資源不存在。|建立 Azure API for FHIR 資源，然後重新嘗試操作。|
|IoT 連接器不支援 Azure API for FHIR 資源 FHIR 版本。|API|嘗試使用具有不相容 Azure API for FHIR 資源版本的 Azure IoT Connector for FHIR。|建立新的 Azure API for FHIR 資源 (版本 R4) ，或使用現有的 Azure API for FHIR 資源 (版本 R4) 。

##  <a name="why-is-my-azure-iot-connector-for-fhir-preview-data-not-showing-up-in-azure-api-for-fhir"></a>為什麼我的 Azure IoT Connector for FHIR (預覽) 不會顯示在 Azure API for FHIR 中的資料？

|潛在問題  |修正            |
|------------------|-----------------|
|資料仍在處理中。|資料會以批次方式輸出至 Azure API for FHIR (每 ~ 15 分鐘) 。  資料可能仍在處理中，而且需要額外的時間，資料才會保存在 Azure API for FHIR 中。|
|裝置轉換對應 JSON 尚未設定。|設定並儲存符合規範的裝置轉換對應 JSON。|
|尚未設定 FHIR 轉換對應 JSON。|設定並儲存符合的 FHIR 轉換對應 JSON。|
|裝置訊息不包含裝置對應中定義的預期運算式。|確認裝置對應中定義的 JsonPath 運算式符合裝置訊息中定義的權杖。|
|尚未在 Azure API for FHIR (解析類型中建立裝置資源：僅限查閱) *。|在 Azure API for FHIR 中建立有效的裝置資源。 請確定裝置資源包含的識別碼符合傳入訊息中提供的裝置識別碼。|
|尚未在 Azure API for FHIR (解析類型中建立患者資源：僅限查閱) *。|在 Azure API for FHIR 中建立有效的患者資源。|
|未設定 Device. 病人參考，或參考 (解析類型無效：僅查詢) *。|請確定裝置資源包含對患者資源的有效[參考](https://www.hl7.org/fhir/device-definitions.html#Device.patient)。| 

* 參考[快速入門：使用 Azure 入口網站 (預覽) 部署 Azure Iot 連接器](iot-fhir-portal-quickstart.md#create-new-azure-iot-connector-for-fhir-preview)，以取得適用于 FHIR 解析類型的 Azure iot 連接器功能描述 (例如：查閱或建立) 。

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-conversion-mapping-json"></a>建立適用于 FHIR (預覽的 Azure IoT Connector 複本) 轉換對應 JSON
複製適用于 FHIR 對應檔案的 Azure IoT Connector，對於在 Azure 入口網站網站以外的編輯和封存很有説明。

當您開啟支援票證以協助進行疑難排解時，應該將對應檔案複製提供給 Azure 技術支援。

> [!NOTE]
> JSON 是目前唯一支援的裝置和 FHIR 對應檔案格式。

> [!TIP]
> 深入瞭解適用于 FHIR 裝置的 Azure IoT 連接器[和 FHIR 轉換對應 JSON](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates)

1. 在 [**增益集]** 區段中，于 [Azure API for FHIR 資源] 儀表板的左下方選取 [ **IoT 連接器 (預覽) ]** 。

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="IoT 連接器" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. 選取您要從中複製轉換對應 JSON 的「**連接器**」。

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT 連接器" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> 這個程式也可以用來複製和儲存「**設定 FHIR 對應**」 JSON 的內容。

3. 選取 **[設定裝置對應]**。

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="IoT 連接器" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. 選取 JSON 的內容，並執行複製作業 (例如：選取 Ctrl + c) 。 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT 連接器" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. 執行貼上作業 (例如：選取 Ctrl + v) 至編輯器中的新檔案 (例如： Visual Studio Code、記事本) 並使用 *. json 副檔名儲存檔案。

> [!TIP]
> 如果您將針對適用于 FHIR 的 Azure IoT 連接器開啟[Azure 技術支援](https://azure.microsoft.com/support/create-ticket/)票證，請務必包含轉換對應 JSON 的複本，以協助進行疑難排解程式。

## <a name="next-steps"></a>後續步驟

查看關於 FHIR 的 Azure IoT 連接器的常見問題。

>[!div class="nextstepaction"]
>[適用于 FHIR 的 Azure IoT 連接器常見問題](fhir-faq.md#azure-iot-connector-for-fhir-preview)

*在 Azure 入口網站中，Azure IoT Connector for FHIR 稱為 IoT 連接器 (預覽)。

FHIR 是 HL7 的註冊商標，必須搭配 HL7 權限方可使用。
