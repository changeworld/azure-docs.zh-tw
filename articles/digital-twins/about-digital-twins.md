---
title: 概觀 - Azure Digital Twins | Microsoft Docs
description: 深入了解 Azure Digital Twins，一個關於空間智慧的 Azure IoT 解決方案。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.date: 12/30/2019
ms.topic: overview
ms.service: digital-twins
services: digital-twins
ms.custom: mvc
ms.openlocfilehash: 91386eaf7753f2aefada2d1423a11b366c3ee924
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "79370894"
---
# <a name="overview-of-azure-digital-twins-preview"></a>Azure Digital Twins 預覽版概觀

[!INCLUDE [digital-twins-preview-limit-alert](../../includes/digital-twins-preview-limit-alert.md)]

Azure Digital Twins 預覽版是一項 Azure IoT 服務，可建立完整的實體環境模型。 此服務可建立空間智慧圖形，用模型來呈現人員、空間和裝置之間的關聯性與互動情形。

透過 Azure Digital Twins，您可以查詢來自實體空間 (而非來自許多不同感應器) 的資料。 這項服務可協助您建置可重複使用、具有高度擴充性、可感知空間的體驗，以便將數位和實體世界的串流資料彼此連結。 這些獨一無二的內容相關功能可增強您的應用程式。 

Azure Digital Twins 適用於各種環境類型，例如倉儲、辦公室、學校、醫院和銀行。 甚至可以用於運動場、工廠、停車場、公園、智慧電網和城市。 以下是 Azure Digital Twins 可發揮效用的部分案例：

- 預測工廠的維修需求。
- 分析輸電網路的即時能源需求。
- 最佳化辦公室的可用空間利用。
- 追蹤各種狀態的每日溫度。
- 監視忙碌的無人機路徑。
- 識別自動型交通工具。
- 分析建築物的佔用層級。
- 找出您店裡最忙碌的收銀機。

無論實際的商業案例為何，都可能透過 Azure Digital Twins 佈建對應的數位執行個體。

下列影片會深入探討 Azure Digital Twins。

> [!VIDEO https://www.youtube.com/embed/TvN_NxpgyzQ]

## <a name="key-capabilities"></a>主要功能

Azure Digital Twins 具有下列重要功能。

### <a name="spatial-intelligence-graph"></a>空間智慧圖形

[*空間智慧圖形*](./concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph) (或空間圖形)  是實體環境的虛擬表示法。 使用此功能建立人員、地點與裝置間的關聯性模型。

假設有一個智慧型公用事業應用程式，其涉及連接到鄰近地區的數個電力用量電錶。 智慧型公共事業公司必須精確地監視及預測電力使用量並進行計費。 每個裝置和感應器都必須根據位置和消費者的相關內容來建立模型。 您可以使用空間智慧圖形以模型呈現這些複雜的關聯性種類。

### <a name="digital-twin-object-models"></a>Digital Twins 物件模型

[Digital Twins 物件模型](./concepts-objectmodel-spatialgraph.md#digital-twins-object-models)是預先定義的裝置通訊協定和資料結構描述。 這些項目會配合您解決方案的網域專屬需求，以加速及簡化開發。

例如，會議室使用情况應用程式可以使用預先定義的空間類型，例如校園、大樓、樓層和會議室等。

### <a name="multiple-and-nested-tenants"></a>多個巢狀租用戶

您可以建置會安全地調整並可供多個租用戶重複使用的解決方案。 您也可以建立多個可供存取並以隔離且安全的方式使用的子租用戶。

例如，在單一建築物裡，可將空間使用情況應用程式設定為將某個租用戶資料與其他租用戶資料隔離。 或使用應用程式將單一租用戶的資料與許多建築物結合。

### <a name="advanced-compute-capabilities"></a>進階計算功能

透過[使用者定義函式](./concepts-user-defined-functions.md)，您可以針對傳入的[裝置資料](./concepts-device-ingress.md)定義及執行自訂函式，以傳送訊號給預先定義的端點。 這項進階功能可改善裝置工作的自訂和自動化能力。

以智慧型農業應用程式為例，其中的使用者定義函式會評估土壤濕度感應器的讀數和氣象預報。 然後，應用程式會傳送有關灌溉需求的訊號。

### <a name="built-in-access-control"></a>內建的存取控制

藉由使用存取和身分識別管理功能 (例如[角色型存取控制](./security-role-based-access-control.md)和 [Azure Active Directory](./security-authenticating-apis.md))，您可以安全地控制個人和裝置的存取權。

以設施管理應用程式為例，其已設定為允許房間內的人員設定某範圍內的溫度。 而設施管理員能將任何房間內的溫度設定為任何值。

### <a name="ecosystem"></a>生態系統

您可以將 Azure Digital Twins 執行個體連線到許多功能強大的 Azure 服務。 這些服務包括 Azure 串流分析、Azure AI 和 Azure 儲存體。 也包含了 Azure 地圖服務、Microsoft Mixed Reality、Dynamics 365 或 Office 365。

例如，智慧型辦公大樓應用程式會使用 Azure Digital Twins 來呈現位於許多樓層的小組和裝置。 由於裝置會將即時資料串流到已佈建的 Digital Twins 執行個體，所以串流分析會處理該項資料以提供可操作的重要見解。 資料會儲存在 Azure 儲存體，並轉換成可共用的檔案格式。 藉由使用 Office 365，可將檔案分散到整個組織中。

## <a name="solutions-that-benefit-from-azure-digital-twins"></a>受益於 Azure Digital Twins 的解決方案

Azure Digital Twins 可用來表示真實世界和其許多關聯性。 其簡化了 IoT 模型、資料處理、事件處理及裝置追蹤。 請想想以下幾個產業案例。 這些案例用法皆獲益於此服務：

* 對房屋管理公司顯示一段時間的空間佔有率，以了解其辦公大樓的最佳設定方式。
* 觸發行動裝置應用程式的工單票證。 在零售空間或運動場中，使用此服務來分派保全和安排看管人員等其他服務。
* 顯示大樓使用情形，即時呈現大樓內已使用的會議室。 然後，協助使用者預約符合其需求的工作區。
* 追蹤資產位於空間內的哪些地方。
* 用模型來呈現使用者偏好和電網限制，讓電動車的充電模式最佳化。

## <a name="azure-digital-twins-in-the-context-of-other-iot-services"></a>其他 IoT 服務內容中的 Azure Digital Twins

Azure Digital Twins 會使用 Azure IoT 中樞來連接 IoT 裝置和感應器，讓一切資訊隨時符合真實世界的最新情形。 下圖顯示 Azure Digital Twins 與其他 Azure IoT 服務的關聯性。

[![Azure Digital Twins 是建置在 Azure IoT 中樞之上的服務](media/overview/azure-digital-twins-in-iot-ecosystem.png)](media/overview/azure-digital-twins-in-iot-ecosystem.png#lightbox)

如需 IoT 的詳細資訊，請參閱 [Azure IoT 技術和解決方案](../iot-fundamentals/iot-services-and-technologies.md)。

## <a name="next-steps"></a>後續步驟

移至 Azure Digital Twins 的簡短示範：

>[!div class="nextstepaction"]
>[快速入門：使用 Azure Digital Twins 尋找空閒會議室](./quickstart-view-occupancy-dotnet.md)

深入探索使用 Azure Digital Twins 的設施管理應用程式：

>[!div class="nextstepaction"]
>[教學課程：部署 Azure Digital Twins 及設定空間圖形](./tutorial-facilities-setup.md)

了解 Azure Digital Twins 的核心概念：

>[!div class="nextstepaction"]
>[了解 Digital Twins 物件模型和空間智慧圖形](./concepts-objectmodel-spatialgraph.md)