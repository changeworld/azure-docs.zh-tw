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
ms.date: 07/08/2020
ms.custom: seodec18
ms.openlocfilehash: d067d4a7fff385deea946ffa5475e1eb83548a50
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096413"
---
# <a name="provision-and-manage-azure-time-series-insights-gen2"></a>布建和管理 Azure 時間序列深入解析 Gen2

本文說明如何使用[Azure 入口網站](https://portal.azure.com/)建立和管理 Azure 時間序列深入解析 Gen2 環境。

## <a name="overview"></a>總覽

當您布建 Azure 時間序列深入解析 Gen2 環境時，您會建立下列 Azure 資源：

* 採用隨用隨付定價模型的 Azure 時間序列深入解析 Gen2 環境
* Azure 儲存體帳戶
* 選擇性的暖存放區，可提供更快速且無限制的查詢

> [!TIP]
> * 瞭解[如何規劃您的環境](./time-series-insights-update-plan.md)。
> * 閱讀如何[新增事件中樞來源](./time-series-insights-how-to-add-an-event-source-eventhub.md)或如何[新增 IoT 中樞來源](./time-series-insights-how-to-add-an-event-source-iothub.md)。

您將了解如何：

1.  將每個 Azure 時間序列深入解析 Gen 2 環境與事件來源產生關聯。 您也會提供時間戳記識別碼屬性和唯一的取用者群組，以確保環境能夠存取適當的事件。

1. 布建完成後，您可以修改您的存取原則和其他環境屬性，以符合您的商務需求。

   > [!NOTE]
   > 在布建環境時，第一個步驟是選擇性的。 如果您略過此步驟，您必須在稍後將事件來源連結至環境，讓資料可以開始流入您的環境，並可透過查詢來存取。

## <a name="create-the-environment"></a>建立環境

若要建立 Azure 時間序列深入解析 Gen 2 環境：
1. 在[Azure 入口網站](https://portal.azure.com/)上的*物聯網*下建立 Azure 時間序列深入解析資源。

1. 選取 [ **Gen2 （L1）** ] 做為**層級**。 提供環境名稱，然後選擇要使用的訂用帳戶群組和資源群組。 然後選取支援的位置來裝載環境。

   [![建立 Azure 時間序列深入解析執行個體。](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. 輸入時間序列識別碼。

    > [!NOTE]
    > * 時間序列識別碼會區分*大小寫*，且為不*變*。 (設定之後就無法變更。)
    > * 時間序列識別碼最多可以有*三個*索引鍵。 將它視為資料庫中的主鍵，其唯一代表會將資料傳送至環境的每個裝置感應器。 它可以是一個屬性，或最多三個屬性的組合。
    > * 閱讀更多有關[如何選擇時間序列識別碼的](time-series-insights-update-how-to-id.md)資訊

1. 選取儲存體帳戶名稱、帳戶種類，並指定[複寫選項，](https://docs.microsoft.com/azure/storage/common/redundancy-migration?tabs=portal)以建立 Azure 儲存體帳戶。 這麼做會自動建立 Azure 儲存體帳戶。 根據預設，將會建立[一般用途 v2](https://docs.microsoft.com/azure/storage/common/storage-account-overview)帳戶。 帳戶會建立在與您先前選取的 Azure 時間序列深入解析 Gen2 環境相同的區域中。 或者，您也可以在建立新的 Azure 時間序列 Gen2 環境時，透過[ARM 範本](./time-series-insights-manage-resources-using-azure-resource-manager-template.md)攜帶您自己的儲存體（BYOS）。 

1. **（選擇性）** 如果您想要更快速且無限的查詢環境中最新的資料，請為您的環境啟用暖存放區。 建立 Azure 時間序列深入解析 Gen2 環境之後，您也可以透過左側流覽窗格中的 [**存放裝置**設定] 選項來建立或刪除暖存放區。

1. **（選擇性）** 您現在可以新增事件來源。 您也可以等到實例布建完成後再進行。

   * Azure 時間序列深入解析支援[Azure IoT 中樞](./time-series-insights-how-to-add-an-event-source-iothub.md)和[Azure 事件中樞](./time-series-insights-how-to-add-an-event-source-eventhub.md)作為事件來源選項。 雖然您在建立環境時只能新增單一事件來源，但您稍後可以加入另一個事件來源。 
   
     當您新增事件來源時，可以選取現有的取用者群組，或建立新的取用者群組。 請注意，事件來源需要唯一的取用者群組，您的環境才能將資料讀入其中。

   * 選擇適當的時間戳記屬性。 根據預設，Azure 時間序列深入解析會針對每個事件來源使用訊息排入佇列的時間。

     > [!TIP]
     > 以訊息排入佇列的時間可能不是最佳設定，無法在批次事件案例或歷程記錄資料上傳案例中使用。 在這種情況下，請務必確認您的決定要使用或不使用 Timestamp 屬性。

     [![[事件來源設定] 索引標籤](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. 確認您的環境已以您想要的方式布建和設定。

    [![審核 + 建立索引標籤](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>管理環境

您可以使用 Azure 入口網站來管理 Azure 時間序列深入解析 Gen2 環境。 當您透過 Azure 入口網站來管理您的環境時，Gen2 環境與 Gen1 S1 或 Gen1 S2 環境之間有幾個主要差異：

* [Azure 入口網站 Gen2**總覽**] 分頁具有下列變更：

  * 已移除容量，因為它不適用於 Gen2 環境。
  * 已加入**時間序列識別碼**屬性。 它會決定分割您的資料的方式。
  * 已移除參考資料集。
  * 顯示的 URL 會將您導向至 [ [Azure 時間序列深入解析 Explorer](./time-series-insights-update-explorer.md)]。
  * 已列出您的 Azure 儲存體帳戶名稱。

* Azure 入口網站的 [**設定**] 分頁已移除，因為縮放單位不適用於 Azure 時間序列深入解析 Gen2 環境。 不過，您可以使用**存放裝置**設定來設定新引進的暖存放區。

* Azure 入口網站的**參考資料**] 分頁會在 Azure 時間序列深入解析 Gen2 中移除，因為參考資料概念已由[時間序列模型（TSM）](./time-series-insights-update-how-to-tsm.md)取代。

[![Azure 入口網站中的 Azure 時間序列深入解析 Gen2 環境](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>後續步驟

- 閱讀[規劃您的環境](./time-series-insights-update-plan.md)，以深入瞭解 Azure 時間序列深入解析正式運作的環境和 Gen2 環境。

- 瞭解如何[新增事件中樞來源](./time-series-insights-how-to-add-an-event-source-eventhub.md)。

- 設定[IoT 中樞來源](./time-series-insights-how-to-add-an-event-source-iothub.md)。
