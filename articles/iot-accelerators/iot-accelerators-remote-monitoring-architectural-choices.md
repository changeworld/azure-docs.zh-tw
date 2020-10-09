---
title: 遠端監視解決方案架構選擇 - Azure | Microsoft Docs
description: 本文說明在遠端監視中所做的架構及技術選擇
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: conceptual
ms.openlocfilehash: 1bd08596a30db7322a72b4269fddfe0b9df19119
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "61447163"
---
# <a name="remote-monitoring-architectural-choices"></a>遠端監視架構選擇

Azure IoT 遠端監視解決方案加速器是開放原始碼、MIT 授權的解決方案加速器。 為了協助您加快 IoT 開發程序，它會顯示常見的 IoT 案例，例如：

- 裝置連線能力
- 裝置管理
- 串流處理

遠端監視解決方案會遵循建議的 [Azure IoT 參考架構](https://aka.ms/iotrefarchitecture)。

本文說明在每個遠端監視子系統中所做的架構及技術選擇。 不過，Microsoft 在遠端監視解決方案中所做的技術選擇，不是實作遠端監視 IoT 解決方案的唯一方式。 您應該將技術實作視為建置成功應用程式的基準，而且應該修改它以：

- 在您的組織中運用可用的技能與經驗。
- 符合垂直應用程式的需求。

## <a name="architectural-choices"></a>架構選擇

Microsoft 針對 IoT 應用程式所建議的架構是以雲端原生、微服務且無伺服器為基礎。 您應該將 IoT 應用程式的不同子系統建置為能獨立部署和調整的個別服務。 這些屬性能夠在更新個別子系統時進行更好的調整且更有彈性，並提供為每個子系統選擇適當技術的彈性。

您可以使用一項以上的技術來實作微服務。 例如，您可以選擇下列任何選項來實作微服務：

- 使用容器技術 (例如 Docker) 搭配無伺服器技術 (例如 Azure Functions)。
- 在 PaaS 服務中裝載您的微服務，例如 Azure App Service。

## <a name="technology-choices"></a>技術選擇

本節將詳細說明在遠端監視解決方案中針對每個核心子系統所做的技術選擇。

![核心圖表](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png)

### <a name="cloud-gateway"></a>雲端閘道

Azure IoT 中樞可作為遠端監視解決方案雲端閘道。 [IoT 中樞](https://azure.microsoft.com/services/iot-hub/)提供與裝置的安全雙向通訊。

對於 IoT 裝置連線，您可以使用：

- [IoT 中樞裝置 SDK](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) 可為您的裝置實作原生用戶端應用程式。 此 SDK 會提供包裝 IoT 中樞 REST API 的包裝函式，並處理重試之類的案例。
- 與 Azure IoT Edge 整合，可部署及管理在您裝置上的容器中執行的自訂模組。
- 與 IoT 中樞的自動裝置管理整合，以管理大量連線的裝置。

### <a name="stream-processing"></a>串流處理

針對串流處理，遠端監視解決方案會使用 Azure 串流分析進行複雜的規則處理。 如果您想要使用更簡單的規則，有自訂微服務可支援簡單規則處理，即使此設定不屬於既有部署的一部分也一樣。 參考架構建議使用 Azure Functions 進行簡單的規則處理，以及使用 Azure 串流分析 進行複雜的規則處理。

### <a name="storage"></a>儲存體

針對儲存體，遠端監視解決方案加速器會同時使用 Azure 時間序列深入解析和 Azure Cosmos DB。 Azure 時間序列深入解析會儲存來自您的已連線裝置並經由 IoT 中樞傳輸的訊息。 解決方案加速器會針對其他儲存體使用 Azure Cosmos DB，例如冷儲存體、規則定義、警示和組態設定。

Azure Cosmos DB 是針對 IoT 應用程式所建議的一般用途暖儲存體解決方案。 不過，Azure 時間序列深入解析和 Azure Data Lake 等解決方案適用於許多使用案例。 透過 Azure 時間序列深入解析，您可以識別趨勢和異常狀況來針對時間序列感應器資料獲得更深入的見解。 這項功能可讓您進行根本原因分析，並且避免代價高昂的停機時間。

> [!NOTE]
> Azure China 雲端目前不提供時間序列見解。 Azure China 雲端中新的遠端監視解決方案加速器部署，將 Cosmos DB 用於所有儲存體。

### <a name="business-integration"></a>商務整合

遠端監視解決方案中的商務整合僅限於產生警示，其放置於暖儲存體中。 整合解決方案與 Azure Logic Apps，以實作更深入的商務整合案例。

### <a name="user-interface"></a>使用者介面

Web UI 是利用 JavaScript React 所建置的。 React 提供業界常用的 Web UI 架構，而且類似於其他常用架構 (例如 Angular)。

### <a name="runtime-and-orchestration"></a>執行階段與協調流程

遠端監視解決方案會使用 Docker 容器來執行具有 Kubernetes 的子系統，可作為協調器進行水平調整。 此架構可啟用每個子系統的個別縮放定義。 不過，此架構會產生 DevOps 成本，讓虛擬機器和容器保持最新狀態且安全無虞。

Docker 的替代方案包括在 PaaS 服務中裝載您的微服務，例如 Azure App Service。 Kubernetes 的替代方案包括 Service Fabric、DC/OS 或 Swarm 等協調器。

## <a name="next-steps"></a>後續步驟

* 在[此處](https://www.azureiotsolutions.com/)部署遠端監視解決方案。
* 在 [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) \(英文\) 和[Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/) \(英文\) 中探索 GitHub 程式碼。  
* 在[這裡](https://aka.ms/iotrefarchitecture) \(英文\) 深入了解 IoT 參考架構。
