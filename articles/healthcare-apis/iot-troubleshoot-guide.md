---
title: 適用于 FHIR 的 Azure IoT 連接器（IoT 連接器）-疑難排解指南和操作說明
description: 如何疑難排解常見的 IoT 連接器錯誤訊息和條件和複製對應檔案
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jasteppe
ms.openlocfilehash: eff1272318413da7855134b0a8a44dd0a0711a6c
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285572"
---
# <a name="iot-connector-preview-troubleshooting-guide"></a>IoT 連接器（預覽）疑難排解指南

本文提供針對常見 IoT 連接器（預覽）錯誤訊息和條件進行疑難排解的步驟。  

您也將瞭解如何建立 IoT 連接器（預覽） JSON 對應檔案的複本，以在 Azure 入口網站之外進行編輯和封存。

## <a name="error-messages-and-fixes"></a>錯誤訊息和修正

|訊息   |條件  |修正         |
|----------|-----------|------------|
|對應名稱無效，對應名稱應為 device 或 FHIR|提供的對應類型不是 device 或 FHIR|使用這兩種支援的對應類型之一（例如： Device 或 FHIR）|
|未定義重新產生金鑰參數|重新產生金鑰要求|在重新產生金鑰要求中包含參數|
|已達到可在此訂用帳戶中布建的 IoT 連接器實例數目上限|已達到 IoT 連接器訂用帳戶配額（預設為每個訂用帳戶2個）|刪除其中一個現有的連接器，使用不同的訂用帳戶，但每個訂用帳戶配額尚未達到（2）個連接器，或要求增加訂用帳戶配額|
|已啟用 Azure API for FHIR 資源的 IoT 連接器不支援移動資源|嘗試在有一或多個 IoT 連接器的 Azure API for FHIR 資源上執行移動作業|刪除現有的連接器，以便執行/完成移動作業|
|Azure API for FHIR 資源已啟用私用連結。  IoT 連接器不支援私用連結|嘗試將 IoT 連接器新增到已啟用私人連結的 Azure API for FHIR 資源|選取或建立未啟用私人連結的 Azure API for FHIR 資源（版本 R4）|
|未布建 IoT 連接器|當父系（IoT 連接器）尚未布建時，嘗試使用子服務（連接 & 對應）|布建 IoT 連接器|
|不支援要求|不支援特定的 API 要求|使用正確的 API 要求|
|帳戶不存在|嘗試新增 IoT 連接器和 Azure API for FHIR 資源不存在|建立 Azure API for FHIR 資源，然後重新嘗試操作|
|IoT 連接器不支援 Azure API for FHIR 資源 FHIR 版本|嘗試使用具有不相容 Azure API for FHIR 資源版本的 IoT 連接器|建立新的 Azure API for FHIR 資源（版本 R4），或使用現有的 Azure API for FHIR 資源（版本 R4）

## <a name="creating-copies-of-the-iot-connector-preview-mapping-files"></a>建立 IoT 連接器（預覽）對應檔案的複本
> [!NOTE]
> JSON 是目前唯一支援的裝置和 FHIR 對應檔案格式。

> [!TIP]
> 複製 IoT 連接器對應檔案有助於在 Azure 入口網站之外編輯和封存，並在開啟支援票證時提供給 Azure 技術支援。
> 
> 深入瞭解 IoT 連接器[裝置和 FHIR 對應 JSON](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates)檔案

1. 在 [**增益集]** 區段中，于 [Azure API for FHIR 資源] 儀表板的左下方選取 [ **IoT 連接器（預覽）]** 。

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="IoT 連接器" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. 選取您要從中複製對應檔案的「**連接器**」。

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT 連接器" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

3. 選取 **[設定裝置對應]**。

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="IoT 連接器" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

> [!NOTE]
> 這個程式也可以用來複製/儲存「**設定 FHIR 對應**」 JSON 的內容。

4. 選取 JSON 的內容並進行複製作業（例如：選取 Ctrl + c）。 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT 連接器" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. 執行貼上作業（例如：選取 Ctrl + v）至編輯器中的新檔案（例如： [Visual Studio Code]、[記事本]），並使用 *. json 副檔名儲存檔案。

> [!TIP]
> 如果您要開啟 IoT 連接器的[Azure 技術支援](https://azure.microsoft.com/support/create-ticket/)票證，請務必包含對應檔案的複本，以協助進行疑難排解程式。

## <a name="next-steps"></a>後續步驟

查看 IoT 連接器的常見問題

>[!div class="nextstepaction"]
>[IoT 連接器常見問題](fhir-faq.md#iot-connector-preview)


FHIR 是 HL7 的註冊商標，必須搭配 HL7 權限方可使用。