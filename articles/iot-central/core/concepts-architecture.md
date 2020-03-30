---
title: Azure IoT 中心的架構概念 | Microsoft Docs
description: 本文介紹 Azure IoT 中心架構的重要相關概念
author: dominicbetts
ms.author: dobett
ms.date: 11/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 12ad231d81b6c134ebb8d4902b3f95c978e9622d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271638"
---
# <a name="azure-iot-central-architecture"></a>Azure IoT 中心架構



本文提供 Microsoft Azure IoT 中心架構的概觀。

![最上層架構](media/concepts-architecture/architecture.png)

## <a name="devices"></a>裝置

與 Azure IoT 中心應用程式交換資料的裝置。 裝置可以︰

- 傳送遙測等量值。
- 同步處理您應用程式的設定。

在 Azure IoT 中心，裝置範本會指定裝置可與您的應用程式交換的資料。 如需裝置範本的詳細資訊，請參閱[中繼資料管理](#metadata-management)。

若要深入了解如何將裝置連線到 Azure IoT 中心應用程式，請參閱[裝置連線能力](concepts-get-connected.md)。

## <a name="azure-iot-edge-devices"></a>Azure IoT Edge 裝置

如同使用 [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) 建立的裝置，您也可以將 [Azure IoT Edge 裝置](../../iot-edge/about-iot-edge.md)連線到 IoT Central 應用程式。 IoT Edge 允許您直接在 IoT Central 管理的 IoT 設備上運行雲智慧和自訂邏輯。 IoT Edge 執行階段可讓您：

- 在裝置上安裝和更新工作負載。
- 維護設備上的 IoT 邊緣安全標準。
- 確定 IoT Edge 模組始終在執行。
- 將模組健康情況報告至雲端，以便進行遠端監控。
- 管理下游分葉裝置與 IoT Edge 裝置之間、IoT Edge 裝置上的模組之間，以及 IoT Edge 裝置與雲端之間的通訊。

![Azure IoT 中心與 Azure IoT 邊緣](./media/concepts-architecture/iotedge.png)

IoT 中心為 IoT 邊緣設備啟用以下功能：

- 描述 IoT 邊緣裝置功能的設備範本，例如：
  - 部署清單上載功能，可説明您管理設備佇列的清單。
  - 在 IoT 邊緣設備上運行的模組。
  - 每個模組發送的遙測資料。
  - 每個模組報告的屬性。
  - 每個模組回應的命令。
  - IoT 邊緣閘道裝置功能模型和下游裝置功能模型之間的關係。
  - 未存儲在 IoT 邊緣設備上的雲屬性。
  - 屬於 IoT Central 應用程式一部分的自訂、儀表板和表單。

  有關詳細資訊，請參閱[將 Azure IoT 邊緣設備連接到 Azure IoT 中心應用程式](./concepts-iot-edge.md)一文。

- 使用 Azure IoT 設備預配服務大規模預配 IoT 邊緣設備的能力
- 規則和操作。
- 自訂儀表板和分析。
- 從 IoT 邊緣設備連續匯出遙測資料。

### <a name="iot-edge-device-types"></a>IoT 邊緣裝置類型

IoT Central 對 IoT 邊緣裝置類型進行分類如下：

- 葉設備。 IoT Edge 設備可以具有下游葉設備，但這些設備未預配到 IoT 中心。
- 具有下游設備的閘道設備。 閘道裝置和下游裝置都會佈建在 IoT Central 中

![IoT 中心，帶 IoT 邊緣概述](./media/concepts-architecture/gatewayedge.png)

### <a name="iot-edge-patterns"></a>IoT 邊緣模式

IoT 中心支援以下 IoT 邊緣設備模式：

#### <a name="iot-edge-as-leaf-device"></a>IoT 邊緣作為葉設備

![IoT 邊緣作為葉設備](./media/concepts-architecture/edgeasleafdevice.png)

IoT Edge 設備預配在 IoT 中心，任何下游設備及其遙測資料都表示來自 IoT 邊緣設備。 連接到 IoT Edge 設備的下游設備未預配到 IoT 中心。

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity"></a>IoT 邊緣閘道設備連接到具有標識的下游設備

![具有下游設備標識的 IoT 邊緣](./media/concepts-architecture/edgewithdownstreamdeviceidentity.png)

IoT 邊緣設備與連接到 IoT 邊緣設備的下游設備一起預配在 IoT 中心中。 當前不支援通過閘道預配下游設備的運行時支援。

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity-provided-by-the-iot-edge-gateway"></a>IoT 邊緣閘道設備連接到下游設備，具有 IoT 邊緣閘道提供的標識

![帶無標識的下游設備的 IoT 邊緣](./media/concepts-architecture/edgewithoutdownstreamdeviceidentity.png)

IoT 邊緣設備與連接到 IoT 邊緣設備的下游設備一起預配在 IoT 中心中。 當前不支援為下游設備提供標識的閘道的運行時支援以及下游設備的預配。 如果您自帶身份轉換模組，IoT Central 可以支援此模式。

## <a name="cloud-gateway"></a>雲端閘道

Azure IoT 中心使用 Azure IoT 中樞作為裝置連線的雲端閘道。 IoT 中樞可讓您︰

- 在雲端大規模擷取資料。
- 裝置管理。
- 安全的裝置連線能力。

若要深入了解 IoT 中樞，請參閱 [Azure IoT 中樞](https://docs.microsoft.com/azure/iot-hub/)。

若要深入了解 Azure IoT 中心的裝置連線能力，請參閱[裝置連線能力](concepts-get-connected.md)。

## <a name="data-stores"></a>資料存放區

Azure IoT 中心會在雲端儲存應用程式資料。 儲存的應用程式資料包括：

- 裝置範本。
- 裝置身分識別。
- 裝置中繼資料。
- 使用者和角色資料。

Azure IoT 中心會使用時間序列來儲存您的裝置所傳送的量值資料。 分析服務會使用裝置的時間序列資料。

## <a name="analytics"></a>分析

分析服務負責產生應用程式顯示的自訂報告資料。 操作者可以[自訂分析](howto-create-analytics.md) (適用於應用程式中顯示的分析)。 分析服務是以 [Azure 時間序列深入解析](https://azure.microsoft.com/services/time-series-insights/)為基礎，可處理您的裝置所傳送的量值資料。

## <a name="rules-and-actions"></a>執行和動作

[規則和動作](tutorial-create-telemetry-rules.md)會密切搭配運作，讓應用程式內的工作自動化。 建置者可以根據裝置遙測定義規則，例如超過所定義閾值的溫度。 Azure IoT 中心會使用資料流處理器來判斷何時符合規則條件。 符合規則條件時，就會觸發建置者所定義的動作。 例如，動作可以傳送一封電子郵件來通知工程師，裝置的溫度過高。

## <a name="metadata-management"></a>中繼資料管理

在 Azure IoT 中心應用程式中，裝置範本可定義各類型裝置的行為與功能。 例如，冰箱裝置範本可指定冰箱傳送給應用程式的遙測。

![範本架構](media/concepts-architecture/template-architecture.png)

在 IoT 中央應用程式設備範本中包含：

- **裝置功能模型**指定設備的功能，例如設備發送的遙測資料、定義設備狀態的屬性以及設備回應的命令。 裝置功能被組織成一個或多個介面。 有關裝置功能模型的詳細資訊，請參閱[IoT 隨插即用（預覽）](../../iot-pnp/overview-iot-plug-and-play.md)文檔。
- **雲屬性**指定設備的 IoT 中央存儲屬性。 這些屬性僅存儲在 IoT 中心中，永遠不會發送到設備。
- **視圖**指定產生器創建的儀表板和表單，以便操作員監視和管理設備。
- **自訂**允許產生器覆蓋裝置功能模型中的某些定義，使其與 IoT 中央應用程式更相關。

根據每個裝置範本，應用程式可以有一或多個模擬與真實裝置。

## <a name="data-export"></a>資料匯出

在 Azure IoT 集中應用程式中，可以[連續將資料匯出](howto-export-data.md)到自己的 Azure 事件中心和 Azure 服務匯流排實例。 您還可以定期將資料匯出到 Azure Blob 存儲帳戶。 IoT 中心可以匯出測量值、設備和設備範本。

## <a name="batch-device-updates"></a>批量設備更新

在 Azure IoT 集中應用程式中，您可以創建[和運行作業](howto-run-a-job.md)來管理連接的設備。 這些作業允許您對裝置屬性或設置執行批量更新，或運行命令。 例如，您可以創建作業以提高多個冷藏自動售貨機的風扇速度。

## <a name="role-based-access-control-rbac"></a>角色型存取控制 (RBAC)

管理員可以使用預定義的角色之一或創建自訂角色為 Azure IoT 中心應用程式[定義訪問規則](howto-manage-users-roles.md)。 角色確定使用者有權訪問的應用程式區域以及他們可以執行哪些操作。

## <a name="security"></a>安全性

Azure IoT 中心內的安全性功能包括：

- 資料在傳送過程及待用期間都會予以加密。
- 驗證會經由 Azure Active Directory 或 Microsoft 帳戶提供。 支援雙因素驗證。
- 完整租用戶隔離。
- 裝置層級安全性。

## <a name="ui-shell"></a>UI Shell

UI Shell 是現代化、回應式、HTML5 瀏覽器型應用程式。
管理員可以通過應用自訂主題和修改説明連結以指向您自己的自訂説明資源來自訂應用程式的 UI。 要瞭解有關 UI 自訂的更多詳細資訊，請參閱[自訂 Azure IoT 中央 UI](howto-customize-ui.md)一文。

操作員可以創建個人化的應用程式儀表板。 可以有多個儀表板，這些儀表板顯示不同的資料並在它們之間切換。

## <a name="next-steps"></a>後續步驟

現在，您已經瞭解了 Azure IoT 中央的體系結構，建議的下一步是瞭解 Azure IoT 中央的設備[連接](concepts-get-connected.md)。
