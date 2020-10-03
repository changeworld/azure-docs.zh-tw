---
title: 布建和管理 Gen 2 環境-Azure 時間序列 |Microsoft Docs
description: 瞭解如何布建和管理 Azure 時間序列深入解析 Gen 2 環境。
author: shipra1mishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: e54e8e9de1df4c8a1c870285d36e4580daaa698a
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2020
ms.locfileid: "91667821"
---
# <a name="provision-and-manage-azure-time-series-insights-gen2"></a>布建和管理 Azure 時間序列深入解析 Gen2

本文說明如何使用 [Azure 入口網站](https://portal.azure.com/)來建立和管理 Azure 時間序列深入解析 Gen2 環境。

## <a name="overview"></a>概觀

當您布建 Azure 時間序列深入解析 Gen2 環境時，您會建立下列 Azure 資源：

* 遵循隨用隨付定價模型的 Azure 時間序列深入解析 Gen2 環境
* Azure 儲存體帳戶
* 選擇性的暖存放區，可提供更快速且無限制的查詢

> [!TIP]
>
> * 瞭解 [如何規劃您的環境](./time-series-insights-update-plan.md)。
> * 瞭解如何 [新增事件中樞來源](./time-series-insights-how-to-add-an-event-source-eventhub.md) ，或如何 [新增 IoT 中樞來源](./time-series-insights-how-to-add-an-event-source-iothub.md)。

您將了解如何：

1. 將每個 Azure 時間序列深入解析 Gen 2 環境與事件來源產生關聯。 您也會提供時間戳記識別碼屬性和唯一的取用者群組，以確保環境具有適當事件的存取權。

1. 布建完成後，您可以修改存取原則和其他環境屬性，以符合您的商務需求。

   > [!NOTE]
   > 布建環境時，第一個步驟是選擇性的。 如果您略過此步驟，就必須稍後將事件來源附加至環境，以便資料可以開始流入您的環境，並可透過查詢來存取。

## <a name="create-the-environment"></a>建立環境

若要建立 Azure 時間序列深入解析 Gen 2 環境：

1. 在[Azure 入口網站](https://portal.azure.com/)的 [*物聯網*] 底下建立 Azure 時間序列深入解析資源。

1. 選取**Gen2 (L1) **作為階層 **。** 提供環境名稱，並選擇要使用的訂用帳戶群組和資源群組。 然後選取支援的位置來裝載環境。

   [![建立 Azure 時間序列深入解析執行個體。](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. 輸入時間序列識別碼。

    > [!NOTE]
    >
    > * 時間序列識別碼 *區分大小寫* 且 *不可變*。 (設定之後就無法變更。)
    > * 時間序列識別碼最多可以有 *三個* 索引鍵。 您可以把它想成是資料庫中的主鍵，這可唯一代表會將資料傳送到您環境的每個裝置感應器。 它可以是一個屬性，也可以是最多三個屬性的組合。
    > * 深入瞭解 [如何選擇時間序列識別碼](time-series-insights-update-how-to-id.md)

1. 選取儲存體帳戶名稱、帳戶類型並指定 [複寫選項，](https://docs.microsoft.com/azure/storage/common/redundancy-migration?tabs=portal) 以建立 Azure 儲存體帳戶。 這樣做會自動建立 Azure 儲存體帳戶。 預設會建立 [一般用途 v2](https://docs.microsoft.com/azure/storage/common/storage-account-overview) 帳戶。 帳戶會建立在與您先前選取的 Azure 時間序列深入解析 Gen2 環境相同的區域中。
或者，您也可以在建立新的 Azure 時間序列 Gen2 環境時，透過 [ARM 範本](./time-series-insights-manage-resources-using-azure-resource-manager-template.md) 將您自己的儲存體 (BYOS) 。

1. ** (選擇性) ** 如果您想要更快速且無限制地查詢您環境中的最新資料，請為您的環境啟用暖存放區。 您也可以在建立 Azure 時間序列深入解析 Gen2 環境之後，透過左方流覽窗格中的 [ **儲存體** 設定] 選項來建立或刪除暖存放區。

1. ** (選擇性) ** 您現在可以新增事件來源。 您也可以等候實例布建完成。

   * Azure 時間序列深入解析支援以事件來源選項 [Azure IoT 中樞](./time-series-insights-how-to-add-an-event-source-iothub.md) 和 [Azure 事件中樞](./time-series-insights-how-to-add-an-event-source-eventhub.md) 。 雖然您可以在建立環境時新增單一事件來源，但稍後可以新增另一個事件來源。

     當您新增事件來源時，您可以選取現有的取用者群組，或建立新的取用者群組。 請注意，事件來源要求您的環境必須有唯一的取用者群組，才能將資料讀入其中。

   * 選擇適當的時間戳記屬性。 根據預設，Azure 時間序列深入解析會針對每個事件來源使用訊息排入佇列的時間。

     > [!TIP]
     > 訊息排入佇列的時間可能不是最佳的設定，無法在批次事件案例或歷程記錄資料上傳案例中使用。 在這種情況下，請務必確認您的決定要使用，或不要使用時間戳屬性。

     [![[事件來源設定] 索引標籤](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. 確認您的環境已依照您想要的方式布建及設定。

    [![[檢查 + 建立] 索引標籤](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>管理環境

您可以使用 Azure 入口網站來管理 Azure 時間序列深入解析 Gen2 環境。 當您透過 Azure 入口網站管理您的環境時，Gen2 環境與 Gen1 S1 或 Gen1 S2 環境之間有幾個主要差異：

* Azure 入口網站 Gen2 **總覽**  分頁有下列變更：

  * 容量已移除，因為它不適用於 Gen2 環境。
  * 新增 **時間序列識別碼** 屬性。 它會決定分割您的資料的方式。
  * 已移除參考資料集。
  * 顯示的 URL 會將您導向 [Azure 時間序列深入解析 Explorer](./time-series-insights-update-explorer.md)。
  * 已列出您的 Azure 儲存體帳戶名稱。

* Azure 入口網站的 **設定** 分頁已移除，因為縮放單位不適用於 Azure 時間序列深入解析 Gen2 環境。 不過，您可以使用 **存放裝置** 設定來設定新引進的暖存放區。

* Azure 時間序列深入解析 Gen2 中已移除 Azure 入口網站的 **參考資料** 分頁，因為參考資料概念已由 [時間序列模型 (TSM) ](/azure/time-series-insights/concepts-model-overview)所取代。

[![Azure 入口網站中的 Azure 時間序列深入解析 Gen2 環境](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>後續步驟

* 藉由閱讀 [您的環境](./time-series-insights-update-plan.md)，深入瞭解 Azure 時間序列深入解析正式推出的環境和 Gen2 環境。

* 瞭解如何 [新增事件中樞來源](./time-series-insights-how-to-add-an-event-source-eventhub.md)。

* 設定 [IoT 中樞來源](./time-series-insights-how-to-add-an-event-source-iothub.md)。
