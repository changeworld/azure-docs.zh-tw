---
title: 什麼是 OPC 對應項 - Azure | Microsoft Docs
description: 此文章提供 OPC 對應項的概觀。 OPC 對應項可透過 REST API 提供對產業裝置的探索、註冊和遠端控制等功能。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 9825e5e85c8da27b0f3fc2b9d78fcc9ca3513389
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91281689"
---
# <a name="what-is-opc-twin"></a>什麼是 OPC 對應項？

> [!IMPORTANT]
> 當我們更新本文時，請參閱 [Azure 產業 IoT](https://azure.github.io/Industrial-IoT/) 以取得最新的內容。

OPC 對應項是由使用 Azure IoT Edge 與 IoT 中樞連接雲端和工廠網路的微服務所組成。 OPC 對應項可透過 REST API 提供對產業裝置的探索、註冊和遠端控制等功能。 OPC 對應項不需要 OPC 統一架構 (OPC UA) SDK、其程式設計語言無從驗證，而且可以包含在無伺服器工作流程中。 本文說明數個 OPC 對應項的使用案例。

## <a name="discovery-and-control"></a>探索和控制
您可以使用 OPC 對應項進行簡單的探索和註冊。

### <a name="simple-discovery-and-registration"></a>簡單的探索和註冊
OPC 對應項可讓工廠操作員掃描工廠網路，以便探索和註冊 OPC UA 伺服器。 此外，工廠操作員也可以使用已知的探索 URL 手動註冊 OPC UA 裝置。 例如，若要在使用 OPC 對應項模組的 IoT Edge 閘道安裝於廠房之後連線至所有 OPC UA 裝置，工廠操作員可從遠端觸發網路的掃描，並檢視所有的 OPC UA 伺服器。 

### <a name="simple-control"></a>簡單的控制
OPC 對應項可讓工廠操作員自動或手動從雲端即時回應事件，並重新設定其廠房機器。 OPC 對應項提供的 REST API 可在 OPC UA 伺服器上叫用服務、瀏覽其位址空間，以及讀取/寫入變數和執行方法。 例如，鍋爐可使用溫度 KPI 來控制產品線。 溫度感應器會使用 OPC 發行者發佈資料的變更。 工廠操作員會收到溫度已達到閾值的警示。 生產線會透過 OPC 對應項自動減緩速度。 工廠操作員會收到生產線減速的通知。

## <a name="authentication"></a>驗證
您可以使用 OPC 對應項進行簡單的驗證，以及使用簡單的開發人員體驗。

### <a name="simple-authentication"></a>簡單的驗證 
OPC 對應項會使用 Azure Active Directory (AAD) 型驗證和端對端的稽核。 例如，OPC 對應項可讓應用程式建置於 OPC 對應項之上，以判斷操作員已對機器執行的動作。 在機器端，這會透過 OPC UA 稽核來執行。 在雲端中，則藉由在 REST API 上儲存不可變的用戶端稽核記錄和 AAD 驗證來執行。

### <a name="simple-developer-experience"></a>簡單的開發人員體驗 
OPC 對應項可透過 REST API 用於以任何程式設計語言撰寫的應用程式。 開發人員將 OPC UA 用戶端整合到解決方案時，並不需要 OPC UA SDK 的知識。 OPC 對應項可順暢地整合到無狀態的無伺服器架構中。 例如，針對警示和事件儀表板開發應用程式的完整堆疊 Web 開發人員，可以使用 OPC 對應項以 JavaScript 或 TypeScript 寫入邏輯來回應事件，而不需要 C、C# 或完整 OPC UA 堆疊實作的知識。 

## <a name="next-steps"></a>後續步驟

現在您已了解 OPC 對應項及其使用方式，以下是建議執行的下一個步驟：

[什麼是 OPC 保存庫](overview-opc-vault.md)