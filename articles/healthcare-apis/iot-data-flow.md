---
title: 概念： Azure IoT Connector 中適用于 FHIR (preview) 功能的資料流程 Azure API for FHIR
description: 瞭解適用于 FHIR 的 Azure IoT Connector (preview) 的資料流程。 適用于 FHIR (preview 的 Azure IoT Connector) 內嵌、標準化、群組、轉換，以及將 IoMT 資料保存到 Azure API for FHIR。
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: punagpal
ms.openlocfilehash: 43b7bcba97617d6931fd5c191e62e833a25bf89d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87513363"
---
# <a name="azure-iot-connector-for-fhir-preview-data-flow"></a>Azure IoT Connector for FHIR (預覽) 資料流程

本文概要說明 Azure IoT Connector for FHIR * 的資料流程。 您將瞭解 Azure IoT Connector 中的不同資料處理階段，以 FHIR 將裝置資料轉換成以 FHIR 為基礎的 [觀察](https://www.hl7.org/fhir/observation.html) 資源。

![Azure IoT Connector for FHIR 資料流程](media/concepts-iot-data-flow/iot-connector-data-flow.png)

上圖顯示使用 Azure IoT Connector FHIR 的常見資料流程。 

以下是 Azure IoT Connector 針對 FHIR 收到的資料經過的不同階段。

## <a name="ingest"></a>擷取
內嵌是在 Azure IoT Connector 中接收裝置資料以進行 FHIR 的第一個階段。 裝置資料的內嵌端點裝載于 [Azure 事件中樞](https://docs.microsoft.com/azure/event-hubs/)。 Azure 事件中樞平臺支援高規模和輸送量，且每秒可接收和處理數百萬則訊息。 它也可讓 Azure IoT Connector FHIR 以非同步方式取用訊息，以在處理裝置資料時免除裝置等待的需求。

> [!NOTE]
> JSON 是裝置資料目前唯一支援的格式。

## <a name="normalize"></a>規範
正規化是從上述 Azure 事件中樞取出裝置資料，並使用裝置對應範本處理的下一個階段。 此對應程式會導致將裝置資料轉換成正規化架構。 

正規化程式不僅可簡化稍後階段的資料處理，還可讓您將一個輸入訊息投射到多個正規化訊息中。 例如，裝置可能會在單一訊息中傳送內文溫度、脈衝率、血糖和 respiration 率的多個重要徵兆。 此輸入訊息會建立四個不同的 FHIR 資源。 每個資源都代表不同的重要正負號，並將輸入訊息投射到四個不同的正規化訊息中。

## <a name="group"></a>群組
群組是下一個階段中可用的正規化訊息使用三個不同的參數分組的下一個階段：裝置身分識別、測量類型和時間週期。

裝置身分識別和測量類型群組可讓您使用 [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) 測量類型。 此類型可讓您從 FHIR 中的裝置，提供精確的方法來表示以時間為基礎的測量序列。 時間週期控制 Azure IoT Connector 針對 FHIR 所產生的觀察資源寫入 Azure API for FHIR 的延遲。

> [!NOTE]
> 時間週期值預設為15分鐘，無法設定為預覽。

## <a name="transform"></a>轉換
在 [轉換] 階段中，群組正規化的訊息會透過 FHIR 對應範本處理。 符合範本類型的訊息會轉換成以 FHIR 為基礎的觀察資源（透過對應指定）。

此時，也會使用訊息中出現的裝置識別碼，從 FHIR 伺服器抓取 [裝置](https://www.hl7.org/fhir/device.html) 資源及其相關聯的 [患者](https://www.hl7.org/fhir/patient.html) 資源。 這些資源會新增為所建立之觀察資源的參考。

> [!NOTE]
> 系統會快取所有身分識別查閱，以降低 FHIR 伺服器的負載。 如果您打算重複使用具有多個患者的裝置，建議您建立患者專用的虛擬裝置資源，並在訊息內容中傳送虛擬裝置識別碼。 虛擬裝置可以連結至實際的裝置資源作為父代。

如果 FHIR 伺服器中沒有指定裝置識別碼的裝置資源存在，則結果會取決於 `Resolution Type` 建立時設定的值。 當設定為時 `Lookup` ，會忽略特定的訊息，而管線將繼續處理其他傳入的訊息。 如果設定為 `Create` ，則適用于 FHIR 的 Azure IoT Connector 會在 FHIR 伺服器上建立一個裸機裝置和患者資源。  

## <a name="persist"></a>Persist
在轉換階段產生觀察 FHIR 資源之後，資源就會儲存在 Azure API for FHIR 中。 如果 FHIR 資源是新的，則會在伺服器上建立。 如果 FHIR 資源已經存在，將會更新。

## <a name="next-steps"></a>接下來的步驟

按一下下一個步驟，以瞭解如何建立裝置和 FHIR 對應範本。

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR 對應範本](iot-mapping-templates.md)

*在 Azure 入口網站中，Azure IoT Connector for FHIR 稱為 IoT 連接器 (預覽)。

FHIR 是 HL7 的註冊商標，必須搭配 HL7 權限方可使用。
