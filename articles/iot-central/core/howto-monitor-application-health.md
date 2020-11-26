---
title: 監視 Azure IoT Central 應用程式的健全狀況 |Microsoft Docs
description: 以操作員或系統管理員的身分，監視連接至 IoT Central 應用程式之裝置的整體健全狀況。
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1fb17d56c546511fee291f30b103a5310c8015cc
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96122274"
---
# <a name="monitor-the-overall-health-of-the-devices-connected-to-an-iot-central-application"></a>監視連線到 IoT Central 應用程式之裝置的整體健全狀況

> [!NOTE]
> 計量僅適用于第3版 IoT Central 應用程式。 若要瞭解如何檢查您的應用程式版本，請參閱 [應用程式的相關資訊](./howto-get-app-info.md)。

*本文適用于操作員和系統管理員。*

在本文中，您將瞭解如何使用 IoT Central 所提供的一組度量，來評估連接至 IoT Central 應用程式之裝置的整體健全狀況。

預設會為您的 IoT Central 應用程式啟用計量，並從 [Azure 入口網站](https://portal.azure.com/)存取計量。 [Azure 監視器資料平臺會公開這些計量](../../azure-monitor/platform/data-platform-metrics.md)，並提供數種方式讓您與它們互動。 例如，您可以在 PowerShell 或 Azure CLI 中使用 Azure 入口網站、REST API 或查詢中的圖表。

### <a name="trial-applications"></a>試用版應用程式

使用免費試用方案的應用程式沒有相關聯的 Azure 訂用帳戶，因此不支援 Azure 監視器計量。 您可以 [將應用程式轉換成標準定價方案](./howto-view-bill.md#move-from-free-to-standard-pricing-plan) ，並取得這些計量的存取權。

## <a name="view-metrics-in-the-azure-portal"></a>查看 Azure 入口網站中的計量

下列步驟假設您有一個 [IoT Central 的應用程式](./quick-deploy-iot-central.md) ，且有一些 [連接的裝置](./tutorial-connect-device.md)。

若要在入口網站中查看 IoT Central 計量：

1. 在入口網站中流覽至您的 IoT Central 應用程式資源。 根據預設，IoT Central 資源位於名為 **iotc-explorer** 的資源群組中。
1. 若要從應用程式的度量建立圖表，請選取 [**監視**] 區段中的 [**計量**]。

![Azure 計量](media/howto-monitor-application-health/metrics.png)

### <a name="azure-portal-permissions"></a>Azure 入口網站許可權

存取 Azure 入口網站中的計量是由 [Azure 角色型存取控制](../../role-based-access-control/overview.md)所管理。 使用 Azure 入口網站將使用者新增至 IoT Central 的應用程式/資源群組/訂用帳戶，以授與他們存取權。 您必須在入口網站中新增使用者，即使已將使用者新增至 IoT Central 應用程式也是如此。 使用 [Azure 內建角色](../../role-based-access-control/built-in-roles.md) 進行更細微的存取控制。

## <a name="iot-central-metrics"></a>IoT Central 計量

如需 IoT Central 目前可用計量的清單，請參閱 [支援的計量 Azure 監視器](../../azure-monitor/platform/metrics-supported.md#microsoftiotcentraliotapps)。

### <a name="metrics-and-invoices"></a>計量和發票

計量可能會與 Azure IoT Central 發票上顯示的數位不同。 發生這種情況的原因有很多，例如：

- IoT Central [標準定價方案](https://azure.microsoft.com/pricing/details/iot-central/) 包含兩個裝置，而且有不同的訊息配額可供免費。 雖然免費專案會從計費中排除，但仍會計算在計量中。

- IoT Central 會自動產生應用程式中每個裝置範本的一個測試裝置識別碼。 裝置範本的 [ **管理測試裝置** ] 頁面上會顯示此裝置識別碼。 解決方案產生器可選擇在發佈之前先 [驗證其裝置範本](./overview-iot-central.md#create-device-templates) ，方法是產生使用這些測試裝置識別碼的程式碼。 雖然這些裝置會從計費中排除，但仍會計算在計量中。

- 雖然計量可能會顯示裝置到雲端通訊的子集，裝置與雲端之間的所有通訊都 [會計為計費的訊息](https://azure.microsoft.com/pricing/details/iot-central/)。

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何使用應用程式範本，建議的下一個步驟是瞭解如何 [從 Azure 入口網站管理 IoT Central](howto-manage-iot-central-from-portal.md)