---
title: 視覺化 Power BI 儀表板中的 Azure IoT Central 資料 | Microsoft Docs
description: 使用 Azure IoT Central 的 Power BI 解決方案，將 IoT Central 資料 視覺化並加以分析。
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 10/4/2019
ms.topic: conceptual
ms.openlocfilehash: f996bb2d5126ef038ca872aee1f1893979a5229b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "87080993"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>視覺化與分析 Power BI 儀表板中的 Azure IoT Central 資料

*本主題適用于系統管理員和解決方案開發人員。*

:::image type="content" source="media/howto-connect-powerbi/iot-continuous-data-export.png" alt-text="Power BI 解決方案管線":::

使用適用于 Azure IoT Central V3 的 Power BI 解決方案，建立功能強大的 Power BI 儀表板，以監視 IoT 裝置的效能。 在 Power BI 儀表板中，您可以：

- 追蹤裝置在一段時間內傳送的資料量
- 比較不同遙測串流之間的資料磁片區
- 篩選出特定裝置所傳送的資料
- 查看資料表中最新的遙測資料

此解決方案會設定管線，以從您的[連續資料匯出](howto-export-data-blob-storage.md)Azure Blob 儲存體帳戶讀取資料。 管線會使用 Azure Functions、Azure Data Factory 和 Azure SQL Database 來處理和轉換資料。 您可以在您下載為 .PBIX 檔案的 Power BI 報表中，將資料視覺化並加以分析。 所有資源都是在您的 Azure 訂用帳戶中建立，因此您可以自訂每個元件以符合您的需求。

## <a name="prerequisites"></a>先決條件

若要完成此操作指南中的步驟，您必須具備有效的 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

設定解決方案需要下列資源：

- 第3版 IoT Central 應用程式。 若要瞭解如何檢查您的應用程式版本，請參閱[關於您的應用程式](./howto-get-app-info.md)。 若要瞭解如何建立 IoT Central 應用程式，請參閱[建立 Azure IoT Central 應用程式](./quick-deploy-iot-central.md)。
- 連續資料匯出設定為將遙測、裝置和裝置範本匯出至 Azure Blob 儲存體。 若要深入瞭解，請參閱[如何將 IoT 資料匯出至 Azure 中的目的地](howto-export-data.md)。
  - 請確定只有您的 IoT Central 應用程式會將資料匯出至 blob 容器。
  - 您的[裝置必須傳送 JSON 編碼的訊息](../../iot-hub/iot-hub-devguide-messages-d2c.md)。 裝置必須 `contentType:application/JSON` `contentEncoding:utf-8` `contentEncoding:utf-16` `contentEncoding:utf-32` 在訊息系統屬性中指定 and 或 or。
- Power BI Desktop （最新版本）。 請參閱[Power BI 下載](https://powerbi.microsoft.com/downloads/)。
- Power BI Pro （如果您要與其他人共用儀表板）。

> [!NOTE]
> 如果您使用第2版 IoT Central 應用程式，請參閱舊版檔網站上[Power BI 儀表板中的視覺化和分析 Azure IoT Central 資料](https://docs.microsoft.com/previous-versions/azure/iot-central/core/howto-connect-powerbi)。

## <a name="install"></a>安裝

若要設定管線，請流覽至 [ **Microsoft AppSource** ] 網站上的 [[適用于 Azure IoT Central V3 的 Power BI 解決方案](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central)] 頁面。 選取 [**立即取得**]，並遵循指示進行。

當您開啟 .PBIX 檔案時，請務必閱讀並遵循封面頁上的指示。 這些指示說明如何將報表連接到您的 SQL 資料庫。

## <a name="report"></a>報告

.PBIX 檔案包含 [**裝置和遙測**] 報告會顯示裝置已傳送之遙測的歷程記錄。 它會提供不同遙測類型的明細，同時也會顯示裝置傳送的最新遙測。

:::image type="content" source="media/howto-connect-powerbi/report.png" alt-text="Power BI 裝置和遙測報告":::

## <a name="pipeline-resources"></a>管線資源

您可以在 Azure 入口網站中存取組成管線的所有 Azure 資源。 所有資源都在您設定管線時所建立的資源群組中。

:::image type="content" source="media/howto-connect-powerbi/azure-deployment.png" alt-text="資源群組 Azure 入口網站視圖":::

下列清單描述管線中每個資源的角色：

### <a name="azure-functions"></a>Azure Functions

Azure 函數應用程式會在每次 IoT Central 將新檔案寫入至 Blob 儲存體時觸發。 函式會從遙測、裝置和裝置範本 blob 解壓縮資料，以填入 Azure Data Factory 使用的中繼 SQL 資料表。

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory 連接至 SQL Database 做為連結服務。 它會執行預存程式來處理資料，並將它儲存在分析資料表中。

Azure Data Factory 會每隔15分鐘執行一次，以將最新批次的資料載入至 SQL 資料表（這是**輪轉視窗觸發**程式目前最少的數位）。

### <a name="azure-sql-database"></a>Azure SQL Database

Azure Data Factory 會產生一組 Power BI 的分析資料表。 您可以在 Power BI 中探索這些架構，並使用它們來建立您自己的視覺效果。

## <a name="estimated-costs"></a>預估成本

Microsoft AppSource 網站上[適用于 Azure IoT Central V3 頁面的 Power BI 解決方案](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central)，包括您所部署資源的成本估計工具連結。

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何在 Power BI 中將資料視覺化，建議的下一個步驟是瞭解[如何管理裝置](howto-manage-devices.md)。
