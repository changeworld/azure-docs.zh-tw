---
title: 概念： Azure API for FHIR 的 IoT 連接器（預覽）功能中的資料流程
description: 瞭解 IoT 連接器的資料流程。 IoT 連接器會內嵌、正規化、群組、轉換，並將 IoMT 資料保存到 Azure API for FHIR。
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: punagpal
ms.openlocfilehash: c2d150fcd35bc51478a1d7f4a0407abce1446c06
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097334"
---
# <a name="iot-connector-preview-data-flow"></a>IoT 連接器（預覽）資料流程

本文提供 IoT 連接器中資料流程的總覽。 您將瞭解 IoT 連接器內的不同資料處理階段，將裝置資料轉換成以 FHIR 為基礎的[觀察](https://www.hl7.org/fhir/observation.html)資源。

![IoT 連接器資料流程](media/concepts-iot-data-flow/iot-connector-data-flow.png)

上圖顯示 IoT 連接器內的不同資料流程階段。 

## <a name="ingest"></a>擷取
內嵌是將裝置資料接收到 IoT 連接器的第一個階段。 裝置資料的內嵌端點會裝載在[Azure 事件中樞](https://docs.microsoft.com/azure/event-hubs/)上。 Azure 事件中樞平臺可支援高規模和輸送量，並能夠每秒接收和處理數百萬則訊息。 它也可讓 IoT 連接器以非同步方式取用訊息，而不需要在處理裝置資料時等候裝置等待。

> [!NOTE]
> JSON 是裝置資料目前唯一支援的格式。

## <a name="normalize"></a>規範
正規化是下一個階段，其中會從上述 Azure 事件中樞抓取裝置資料，並使用裝置對應範本進行處理。 這個對應程式會導致將裝置資料轉換成正規化架構。 

正規化程式不僅簡化了稍後階段的資料處理，也提供將一個輸入訊息投影到多個正規化訊息的功能。 例如，裝置可能會在單一訊息中傳送多個重要正負號的身體溫度、脈衝速率、血壓和 respiration 率。 此輸入訊息會建立四個不同的 FHIR 資源。 每個資源都代表不同的重要正負號，並將輸入訊息投射到四個不同的正規化訊息。

## <a name="group"></a>群組
「群組」是下一個階段，可使用三個不同的參數來分組上一個階段中可用的正規化訊息：裝置身分識別、量測類型和時間週期。

裝置身分識別和測量類型群組可讓您使用[SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData)測量類型。 這種類型提供了一種簡單的方式，可從 FHIR 中的裝置呈現以時間為基礎的測量序列。 而時間週期則控制 IoT 連接器所產生的觀測資源寫入 Azure API for FHIR 的延遲。

> [!NOTE]
> [時段] 值預設為15分鐘，而且無法設定為 [預覽]。

## <a name="transform"></a>Transform
在轉換階段中，會透過 FHIR 對應範本來處理群組正規化的訊息。 符合範本類型的訊息會轉換成以 FHIR 為基礎的觀察資源，如透過對應指定。

此時，也會使用訊息中的裝置識別碼，從 FHIR 伺服器抓取[裝置](https://www.hl7.org/fhir/device.html)資源及其相關聯的[患者](https://www.hl7.org/fhir/patient.html)資源。 這些資源會加入做為所建立之觀察資源的參考。

> [!NOTE]
> 所有身分識別查詢都會在解析後快取，以降低 FHIR 伺服器上的負載。 如果您計畫重複使用具有多個患者的裝置，建議您在訊息裝載中建立患者和「傳送虛擬裝置識別碼」專用的虛擬裝置資源。 虛擬裝置可以連結到實際的裝置資源做為父系。

如果 FHIR 伺服器中沒有指定裝置識別碼的裝置資源，則結果會根據建立時設定的值而定 `Resolution Type` 。 當設定為時 `Lookup` ，會忽略特定訊息，而且管線會繼續處理其他傳入的訊息。 如果設定為 `Create` ，IoT 連接器會在 FHIR 伺服器上建立一個簡單的裝置和患者資源。  

## <a name="persist"></a>Persist
一旦在轉換階段中產生了觀察 FHIR 資源，資源就會儲存到 Azure API for FHIR 中。 如果 FHIR 資源是新的，則會在伺服器上建立。 如果 FHIR 資源已經存在，就會更新。

## <a name="next-steps"></a>後續步驟

按一下下方的下一個步驟，以瞭解如何建立裝置和 FHIR 對應範本。

>[!div class="nextstepaction"]
>[IoT 連接器對應範本](iot-mapping-templates.md)


FHIR 是 HL7 的註冊商標，必須搭配 HL7 權限方可使用。
