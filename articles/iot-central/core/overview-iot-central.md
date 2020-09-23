---
title: 什麼是 Azure IoT 中心 | Microsoft Docs
description: Azure IoT Central 是一個 IoT 應用程式平台，能簡化 IoT 解決方案的建立，有助於降低 IoT 管理、作業與開發的負擔及費用。 本文提供 Azure IoT 中心的功能概觀。
author: dominicbetts
ms.author: dobett
ms.date: 12/10/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 4ad1bcabc2e30e9e636883219d42f96335e32e78
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90987360"
---
# <a name="what-is-azure-iot-central"></a>何謂 Azure IoT 中心？

IoT Central 是一個 IoT 應用程式平台，可降低開發、管理及維護企業級 IoT 解決方案的負擔和成本。 選擇使用 IoT Central 進行建置，可讓您有機會將時間、金錢和心力專注在 IoT 資料的企業轉型上，而不只是維護和更新複雜且不斷演進的 IoT 基礎結構。

Web UI 可讓您監視裝置狀況、建立規則，以及在數百萬個裝置的整個生命週期中管理裝置及其資料。 此外，它可讓您將 IoT 智慧擴充至企業營運應用程式，以根據裝置的深入解析採取行動。

本文針對 IoT Central 提供下列概述：

- 與專案相關聯的典型角色。
- 如何建立您的應用程式。
- 如何將裝置連線到您的應用程式。
- 如何管理您的應用程式。
- IoT Central 中的 Azure IoT Edge 功能。
- 如何將採用 Azure IoT Edge 執行階段的裝置連線至您的應用程式。

## <a name="personas"></a>角色

IoT Central 文件提及四個會與 IoT Central 應用程式互動的角色：

- 「解決方案建置者」負責[定義連線至應用程式的裝置類型](howto-set-up-template.md)，以及為操作員自訂應用程式。
- 「操作員」[管理已連線到應用程式的裝置](howto-manage-devices.md)。
- 「管理員」負責進行管理工作，例如管理應用程式內的[使用者角色和權限](howto-administer.md)。
- 「裝置開發人員」會為連線到您應用程式的[裝置](concepts-telemetry-properties-commands.md)或 [IoT Edge 模組](concepts-iot-edge.md)，建立可在其中執行的程式碼。

## <a name="create-your-iot-central-application"></a>建立您的 IoT Central 應用程式

解決方案建置者可使用 IoT Central，為組織建立自訂、雲端裝載的 IoT 解決方案。 自訂 IoT 解決方案通常包括：

- 雲端式應用程式，可接收來您的裝置的遙測，並可讓您管理這些裝置。
- 多個裝置，這些裝置會執行已連線到雲端式應用程式的自訂程式碼。

您可以快速部署新的 IoT Central 應用程式，然後在瀏覽器中自訂它來滿足您的特定需求。 您可以從一般的「應用程式範本」開始，或使用其中一個專注於特定業界、適用於[零售](../retail/overview-iot-central-retail.md)、[能源](../energy/overview-iot-central-energy.md)、[政府](../government/overview-iot-central-government.md)或[醫療保健](../healthcare/overview-iot-central-healthcare.md)的應用程式範本來開始。

身為解決方案建置者，您會使用 Web 型工具，為連線到您應用程式的裝置建立「裝置範本」。 裝置範本是定義某種裝置之特性和行為的藍圖，例如：

- 它所傳送的遙測。
- 操作員可以修改的業務屬性。
- 由裝置設定而且在應用程式中唯讀的裝置屬性。
- 屬性由操作員所設定，會決定裝置的行為。

此[裝置範本](howto-set-up-template.md)包含：

- 一個_裝置功能模型_，說明裝置所應實作的功能，例如它所傳送的遙測資料和它所報告的屬性。
- 未儲存在裝置上的雲端屬性。
- 屬於 IoT Central 應用程式一部分的自訂、儀表板和表單。

### <a name="create-device-templates"></a>建立裝置範本

身為解決方案建置者，您可以利用數個選項來建立裝置範本：

- 在 IoT Central 中設計裝置範本，然後在您的裝置程式碼中實作其裝置功能模型。
- 使用 Visual Studio 程式碼建立裝置功能模型，然後將模型發佈至存放庫。 從模型實作您的裝置程式碼，並將您的裝置連線至 IoT Central 應用程式。 IoT Central 會從存放庫中尋找裝置功能模型，並為您建立簡單的裝置範本。
- 使用 Visual Studio Code 建立裝置功能模型。 從模型實作您的裝置程式碼。 手動將裝置功能模型匯入您的 IoT Central 應用程式中，然後新增您的 IoT Central 應用程式所需的任何雲端屬性、自訂和儀表板。

身為解決方案建置者，您可以使用 IoT Central 來產生測試裝置的程式碼，以驗證您的裝置範本。

如果您是裝置開發人員，請參閱 [IoT Central 裝置開發概觀](./overview-iot-central-developer.md)以了解如何實作使用這些裝置範本的裝置簡介。

### <a name="customize-the-ui"></a>自訂 UI

解決方案建置者也可以為負責應用程式日常使用的操作員，自訂 IoT Central 應用程式 UI。 解決方案建置者可以進行的自訂包括：

- 在裝置範本上定義屬性和設定的版面配置。
- 設定自訂儀表板，協助操作員更快發現見解及解決問題。
- 設定自訂分析，以探索來自已連線裝置的時間序列資料。

## <a name="manage-your-devices"></a>管理您的裝置

操作員可以使用 IoT Central 應用程式來[管理您 IoT Central 解決方案中的裝置](howto-manage-devices.md)。 操作員會執行下列工作：

- 監視已連線到應用程式的裝置。
- 針對裝置問題進行移難排解並修復。
- 佈建新裝置。

身為解決方案建置者，您可以[定義對來自所連線裝置之資料串流操作的自訂規則和動作](howto-configure-rules.md)。 操作員可以在裝置層級啟用或停用這些規則，以控制和自動執行應用程式內的工作。

在使用專為大規模運作而設計的 IoT 解決方案時，結構化的裝置管理方法便顯得重要。 只是將裝置連線至雲端並不夠，您還必須讓裝置能夠保持連線且狀況良好。 操作員可以使用下列 IoT Central 功能，在整個應用程式生命週期中管理裝置：

### <a name="dashboards"></a>儀表板

內建[儀表板](./howto-set-up-template.md#generate-default-views)提供了可自訂的 UI 供您監視裝置的健康情況和遙測。 從[應用程式範本](howto-use-app-templates.md)中預先建置的儀表板來開始，或建立自己的儀表板以專門因應操作員的需要。 您可以與應用程式中的所有使用者共用儀表板，或讓儀表板保持私用狀態。

### <a name="rules-and-actions"></a>執行和動作

根據裝置狀態和遙測來建立[自訂規則](tutorial-create-telemetry-rules.md)，以識別需要注意的裝置。 設定動作以通知適當人員，並確保能及時採取更正措施。

### <a name="jobs"></a>工作

[作業](howto-run-a-job.md)可讓您藉由設定屬性或呼叫命令，將單一或大量更新套用至裝置。

## <a name="integrate-with-other-services"></a>與其他服務整合

作為應用程式平台，IoT Central 可讓您將 IoT 資料轉換成商業見解，以推動可行的結果。 [規則](./tutorial-create-telemetry-rules.md)、[資料匯出](./howto-export-data.md)及[公用 REST API](https://docs.microsoft.com/learn/modules/manage-iot-central-apps-with-rest-api/) 均是如何整合 IoT Central 與企業營運應用程式的範例：

![IoT Central 用來轉換 IoT 資料的方法](media/overview-iot-central/transform.png)

您可以建置自訂分析管線來處理裝置的遙測並儲存結果，藉此來產生商業見解，例如判斷機器效率趨勢或預測工廠未來會使用多少能源。 在 IoT Central 應用程式中設定資料匯出，將遙測、裝置屬性變更和裝置範本變更匯出至其他服務，以供您使用慣用工具來分析、儲存及以視覺方式呈現資料。

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>使用 REST API 建置自訂的 IoT 解決方案和整合

建置如下的 IoT 解決方案：

- 可遠端設定和控制裝置的行動附屬應用程式。
- 可讓現有企業營運應用程式與 IoT 裝置和資料互動的自訂整合。
- 可用於裝置模型化、上線、管理和資料存取的裝置管理應用程式。

## <a name="administer-your-application"></a>管理您的應用程式

IoT Central 應用程式完全由 Microsoft 裝載，可降低管理您的應用程式的系統管理負荷。 管理員可以利用[使用者角色和權限](howto-administer.md)，管理對您應用程式的存取。

## <a name="pricing"></a>定價

您可以使用 7 天免費試用版或標準版定價方案來建立 IoT Central 應用程式。

- 使用「免費」方案建立的應用程式可以免費試用七天，且最多可支援五個裝置。 您可以在到期前，隨時將其轉換為使用標準定價方案。
- 您使用「標準」方案建立的應用程式會根據每個裝置來計費，您可以選擇**標準 1** 或**標準 2** 定價方案，而且前兩個裝置免費。 深入了解 [IoT Central 價格](https://aka.ms/iotcentral-pricing)。

## <a name="quotas"></a>配額

每個 Azure 訂用帳戶都有預設配額，而此配額會對 IoT 解決方案的範圍造成影響。 目前，IoT Central 會將您可在訂用帳戶中部署的應用程式數目限制為 10 個。 如果您需要提高此限制，請連絡 [Microsoft 支援服務](https://azure.microsoft.com/support/options/)。

## <a name="known-issues"></a>已知問題

- 連續資料匯出不支援 Avro 格式 (不相容)。
- 目前不支援 GeoJSON。
- 目前不支援地圖底圖。
- 不支援陣列結構描述類型。
- 僅支援 C 裝置 SDK 和 Node.js 裝置與服務 SDK。
- IoT Central 目前適用於美國、歐洲、亞太地區、澳大利亞、英國及日本地區。
- 您無法在英國和日本地區中使用**自訂應用程式 (舊版)** 的應用程式範本。
- 裝置功能模型必須具有在相同檔案中以內嵌方式定義的所有介面。

## <a name="next-steps"></a>後續步驟

既然您已大致了解 IoT Central，以下是一些建議的後續步驟：

- 了解可用於[建立 IoT 解決方案的 Azure 技術和服務](../../iot-fundamentals/iot-services-and-technologies.md)。
- 如果您是裝置開發人員，而且想要深入了解一些程式碼，建議的下一個步驟是[建立用戶端應用程式並將其連線到您的 Azure IoT Central 應用程式](./tutorial-connect-device-nodejs.md)。
- 熟悉 [Azure IoT 中心 UI](overview-iot-central-tour.md)。
- 從[建立 Azure IoT 中心應用程式](quick-deploy-iot-central.md)著手。
- 了解如何[讓 Azure IoT Edge 裝置連線](./tutorial-add-edge-as-leaf-device.md)。
- 深入了解 [Azure IoT 技術和服務](../../iot-fundamentals/iot-services-and-technologies.md)。
