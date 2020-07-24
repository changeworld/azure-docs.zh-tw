---
title: 監視 Azure IoT Central 應用程式的健康情況 |Microsoft Docs
description: 身為操作員或系統管理員，監視連接到 IoT Central 應用程式之裝置的整體健全狀況。
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: d246adbf8e87d5503bc19bcd497dd00370c0a867
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87001896"
---
# <a name="monitor-the-overall-health-of-the-devices-connected-to-an-iot-central-application"></a>監視連線到 IoT Central 應用程式之裝置的整體健全狀況

> [!NOTE]
> 計量僅適用于第3版 IoT Central 應用程式。 若要瞭解如何檢查您的應用程式版本，請參閱[關於您的應用程式](./howto-get-app-info.md)。

*本文適用于操作員和系統管理員。*

在本文中，您將瞭解如何使用 IoT Central 所提供的計量集來評估連線到您 IoT Central 應用程式之裝置的整體健全狀況。

預設會為您的 IoT Central 應用程式啟用計量，並從[Azure 入口網站](https://portal.azure.com/)存取。 [Azure 監視器資料平臺會公開這些計量](../../azure-monitor/platform/data-platform-metrics.md)，並提供數種方式讓您與之互動。 例如，您可以使用 Azure 入口網站中的圖表、PowerShell 中的 REST API 或查詢，或 Azure CLI。

### <a name="trial-applications"></a>試用版應用程式

使用免費試用方案的應用程式沒有相關聯的 Azure 訂用帳戶，因此不支援 Azure 監視器計量。 您可以[將應用程式轉換成標準定價方案](./howto-view-bill.md#move-from-free-to-standard-pricing-plan)，並取得這些計量的存取權。

## <a name="view-metrics-in-the-azure-portal"></a>查看 Azure 入口網站中的計量

下列步驟假設您有一些已[連線裝置](./tutorial-connect-device-nodejs.md)的[IoT Central 應用程式](./quick-deploy-iot-central.md)。

若要在入口網站中查看 IoT Central 計量：

1. 在入口網站中流覽至您的 IoT Central 應用程式資源。 根據預設，IoT Central 資源會位於名為**iotc-explorer**的資源群組中。
1. 若要從應用程式的計量建立圖表，請選取 [**監視**] 區段中的 [**計量**]。

![Azure 計量](media/howto-monitor-application-health/metrics.png)

### <a name="azure-portal-permissions"></a>Azure 入口網站許可權

Azure 入口網站中計量的存取權是由[Azure 角色型存取控制](../../role-based-access-control/overview.md)所管理。 使用 Azure 入口網站將使用者新增至 IoT Central 應用程式/資源群組/訂用帳戶，以授與他們存取權。 您必須在入口網站中新增使用者，即使他們已經新增至 IoT Central 應用程式也一樣。 使用[Azure 內建角色](../../role-based-access-control/built-in-roles.md)來進行更精細的存取控制。

## <a name="iot-central-metrics"></a>IoT Central 計量

如需目前可供 IoT Central 的計量清單，請參閱[支援的計量與 Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftiotcentraliotapps)。

### <a name="metrics-and-invoices"></a>計量和發票

計量可能與您的 Azure IoT Central 發票上顯示的數位不同。 發生這種情況的原因有數種，例如：

- IoT Central[標準定價方案](https://azure.microsoft.com/pricing/details/iot-central/)包含兩個裝置，而且有不同的訊息配額可免費取得。 雖然免費專案會從計費中排除，但仍會在計量中計算。

- IoT Central 在應用程式中會自動產生每個裝置範本的一個測試裝置識別碼。 裝置範本的 [**管理測試裝置**] 頁面上會顯示此裝置識別碼。 解決方案產生器可以在發行使用這些測試裝置識別碼的程式碼之前，選擇[驗證其裝置範本](./overview-iot-central.md#create-device-templates)。 雖然這些裝置會從計費中排除，但仍會在計量中計算。

- 雖然計量可能會顯示裝置到雲端通訊的子集，裝置與雲端之間的所有通訊都會[計算為一則計費訊息](https://azure.microsoft.com/pricing/details/iot-central/)。

## <a name="next-steps"></a>接下來的步驟

既然您已瞭解如何使用應用程式範本，建議的下一個步驟是瞭解如何[從 Azure 入口網站管理 IoT Central](howto-manage-iot-central-from-portal.md)
