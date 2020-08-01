---
title: 適用于 FHIR 的 Azure IoT 連接器（預覽）-疑難排解指南和操作說明
description: 如何疑難排解常見的 Azure IoT Connector for FHIR （預覽）錯誤訊息和條件和複製對應檔案
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 07/30/2020
ms.author: jasteppe
ms.openlocfilehash: 8c372a865e34b2cbd1b5b3e6d8619c3ef0f438e0
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460417"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>適用于 FHIR 的 Azure IoT 連接器（預覽）疑難排解指南

本文提供針對 FHIR 錯誤訊息和條件的常見 Azure IoT 連接器進行疑難排解的步驟。  

您也將瞭解如何建立適用于 FHIR JSON 對應檔案的 Azure IoT 連接器複本，以便在 Azure 入口網站之外進行編輯和封存，或在開啟支援票證時提供給 Azure 技術支援。 

## <a name="error-messages-and-fixes"></a>錯誤訊息和修正

|訊息   |條件  |修正         |
|----------|-----------|------------|
|對應名稱無效，對應名稱應為 device 或 FHIR|提供的對應類型不是 device 或 FHIR|使用這兩種支援的對應類型之一（例如： Device 或 FHIR）|
|未定義重新產生金鑰參數|重新產生金鑰要求|在重新產生金鑰要求中包含參數|
|已達到可在此訂用帳戶中布建的 IoT 連接器 * 實例數目上限|已達到適用于 FHIR 訂用帳戶配額的 Azure IoT Connector （預設為（2）每個訂用帳戶）|刪除其中一個現有的 Azure IoT Connector for FHIR 實例，使用尚未達到訂用帳戶配額或要求增加訂用帳戶配額的其他訂用帳戶|
|已啟用 Azure API for FHIR 資源的 IoT 連接器不支援移動資源|嘗試在有一或多個 Azure IoT Connector for FHIR 實例的 Azure API for FHIR 資源上執行移動作業|刪除現有的 Azure IoT Connector for FHIR 實例，以執行並完成移動作業|
|未布建 IoT 連接器|當父系（Azure IoT Connector for FHIR）尚未布建時，嘗試使用子服務（連接 & 對應）|布建適用于 FHIR 的 Azure IoT 連接器|
|不支援要求|不支援特定的 API 要求|使用正確的 API 要求|
|帳戶不存在|嘗試新增適用于 FHIR 的 Azure IoT 連接器和 Azure API for FHIR 資源不存在|建立 Azure API for FHIR 資源，然後重新嘗試操作|
|IoT 連接器不支援 Azure API for FHIR 資源 FHIR 版本|嘗試使用具有不相容 Azure API for FHIR 資源版本的 Azure IoT Connector for FHIR|建立新的 Azure API for FHIR 資源（版本 R4），或使用現有的 Azure API for FHIR 資源（版本 R4）

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-mapping-files"></a>建立 Azure IoT Connector for FHIR （預覽）對應檔案的複本
複製適用于 FHIR 對應檔案的 Azure IoT Connector，對於在 Azure 入口網站網站以外的編輯和封存很有用，並可在開啟支援票證時提供給 Azure 技術支援。

> [!NOTE]
> JSON 是目前唯一支援的裝置和 FHIR 對應檔案格式。

> [!TIP]
> 深入瞭解適用于 FHIR 裝置的 Azure IoT 連接器[和 FHIR 對應的 JSON](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates)檔案

1. 在 [**增益集]** 區段中，于 [Azure API for FHIR 資源] 儀表板的左下方選取 [ **IoT 連接器（預覽）]** 。

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="IoT 連接器" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. 選取您要從中複製對應檔案的「**連接器**」。

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT 連接器" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> 這個程式也可以用來複製和儲存「**設定 FHIR 對應**」 JSON 的內容。

3. 選取 **[設定裝置對應]**。

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="IoT 連接器" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. 選取 JSON 的內容並進行複製作業（例如：選取 Ctrl + c）。 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT 連接器" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. 執行貼上作業（例如：選取 Ctrl + v）至編輯器中的新檔案（例如： [Visual Studio Code]、[記事本]），並使用 *. json 副檔名儲存檔案。

> [!TIP]
> 如果您將針對適用于 FHIR 的 Azure IoT 連接器開啟[Azure 技術支援](https://azure.microsoft.com/support/create-ticket/)票證，請務必包含對應檔案的複本，以協助進行疑難排解程式。

## <a name="next-steps"></a>後續步驟

查看關於 FHIR 的 Azure IoT 連接器的常見問題。

>[!div class="nextstepaction"]
>[適用于 FHIR 的 Azure IoT 連接器（預覽）常見問題](fhir-faq.md#iot-connector-preview)

* 在 Azure 入口網站中，適用于 FHIR 的 Azure IoT 連接器稱為 IoT 連接器（預覽）。

FHIR 是 HL7 的註冊商標，必須搭配 HL7 權限方可使用。