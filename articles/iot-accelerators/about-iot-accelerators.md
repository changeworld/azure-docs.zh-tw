---
title: IoT 解決方案加速器簡介 - Azure | Microsoft Docs
description: 了解 Azure IoT 解決方案加速器。 IoT 解決方案加速器是完整、端對端、隨時可部署的 IoT 解決方案。
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: timlt
ms.openlocfilehash: 193ceab0b2df1ab833a86eb748c18271a8e33b71
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2020
ms.locfileid: "96852909"
---
# <a name="what-are-azure-iot-solution-accelerators"></a>Azure IoT 解決方案加速器是什麼？

雲端式 IoT 解決方案通常會使用自訂程式碼和雲端服務來管理裝置的連線、資料處理和分析，以及呈現。

IoT 解決方案加速器是完整且可立即部署的 IoT 解決方案，可讓您實作常見的 IoT 案例。 這些案例包括連線的處理站和裝置模擬。 當您部署解決方案加速器時，該部署中會包含所有必要的雲端式服務以及任何必要的應用程式程式碼。

解決方案加速器是建立自有 IoT 解決方案的起點。 所有解決方案加速器的原始程式碼都是開放原始碼，並可在 GitHub 中取得。 建議您下載並自訂解決方案加速器以因應您的需求。

您也可以先使用解決方案加速器作為學習工具，再從零開始建置自訂的 IoT 解決方案。 解決方案加速器會實作經過證實適用於雲端式 IoT 解決方案的做法，以供您遵循。

每個解決方案加速器中的應用程式程式碼均包含 Web 應用程式，以供您管理解決方案加速器。

> [!NOTE]
> 遠端監視和預測性維護解決方案已從 [Azure IoT 解決方案加速器](https://www.azureiotsolutions.com/Accelerators)網站中移除。 如需詳細資訊，請參閱[什麼是 Azure IoT 解決方案加速器 (舊版)？](/previous-versions/azure/iot-accelerators/about-iot-accelerators.md)。

## <a name="supported-iot-scenarios"></a>所支援的 IoT 案例

目前有兩個解決方案加速器可供您部署：

### <a name="connected-factory"></a>連線的處理站

使用[連線的工廠解決方案加速器](iot-accelerators-connected-factory-features.md)可使用 [OPC 統一架構](https://opcfoundation.org/about/opc-technologies/opc-ua/)介面收集工業資產所傳來的遙測資料以及控制這些資產。 工業資產可能包含工廠生產線內的組裝台和測試台。

您可以使用連線工廠儀表板來監視及管理您的工業裝置：

:::image type="content" source="./media/about-iot-accelerators/cf-dashboard-inline.png" alt-text="此螢幕擷取畫面顯示已連線的處理站解決方案儀表板。" lightbox="./media/about-iot-accelerators/cf-dashboard-expanded.png":::

### <a name="device-simulation"></a>裝置模擬

使用[裝置模擬解決方案加速器](iot-accelerators-device-simulation-overview.md)可執行會產生真實遙測資料的模擬裝置。 您可以使用此解決方案加速器來測試其他解決方案加速器的行為，也可以用它來測試您自己的自訂 IoT 解決方案。

您可以使用裝置模擬 Web 應用程式來設定及執行模擬作業：

:::image type="content" source="./media/about-iot-accelerators/ds-dashboard-inline.png" alt-text="螢幕擷取畫面，顯示裝置模擬解決方案儀表板。" lightbox="./media/about-iot-accelerators/ds-dashboard-expanded.png":::

## <a name="design-principles"></a>設計原則

所有解決方案加速器皆遵循相同的設計原則和目標。 它們都設計成：

* **可調整**，讓您可以連接和管理數百萬個已連線的裝置。
* **可延伸**，讓您可以進行自訂以符合您的需求。
* **可理解**，讓您可以了解其運作方式，以及其實作方式。
* **模組化**，讓您可以抽換替代服務。
* **安全**，可結合 Azure 安全性與內建的連線能力和裝置安全性功能。

## <a name="architectures-and-languages"></a>架構和語言

原始的解決方案加速器是使用模型檢視控制器 (MVC) 架構，透過 .NET 所撰寫的。 Microsoft 正在將解決方案加速器更新為新的微服務架構。 下表顯示解決方案加速器的目前狀態，並包含 GitHub 存放庫的連結：

| 解決方案加速器   | 架構  | Languages     |
| ---------------------- | ------------- | ------------- |
| 連線的處理站      | MVC           | [.NET](https://github.com/Azure/azure-iot-connected-factory)          |
| 裝置模擬      | 微服務 | [.NET](https://github.com/Azure/device-simulation-dotnet)          |

若要深入了解微服務架構，請參閱 [Azure IoT 參考架構簡介](/azure/architecture/reference-architectures/iot/)。

## <a name="deployment-options"></a>部署選項

您可以從 [Microsoft Azure IoT 解決方案加速器](https://www.azureiotsolutions.com/Accelerators#)網站或使用命令列，來部署解決方案加速器。

解決方案加速器的執行成本是合併[基礎 Azure 服務的執行成本](https://azure.microsoft.com/pricing)。 當您選擇部署選項時，就會看到所用 Azure 服務的詳細資料。

## <a name="next-steps"></a>後續步驟

若要試用其中一個 IoT 解決方案加速器，請參閱快速入門[試用已連線的中心解決方案](quickstart-connected-factory-deploy.md)。
