---
title: 預配和管理預覽環境 - Azure 時間序列 |微軟文檔
description: 瞭解如何預配和管理 Azure 時間序列見解預覽環境。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 1ec0d9c7ecf16c60c32abdf08b358268f460edb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087225"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>佈建及管理 Azure 時間序列深入解析預覽版

此文說明如何使用 [Azure 入口網站](https://portal.azure.com/)建立及管理 Azure 時間序列深入解析預覽版環境。

## <a name="overview"></a>總覽

Azure 時間序列見解 預覽環境是即*用即付*（PAYG） 環境。

預配 Azure 時間序列見解預覽環境時，可以創建以下 Azure 資源：

* Azure 時間序列深入解析預覽版環境  
* Azure 儲存體一般用途 v1 帳戶
* 可選的暖存儲，用於更快、無限制的查詢

> [!TIP]
> * [瞭解如何規劃您的環境](./time-series-insights-update-plan.md)。
> * 閱讀有關如何[添加事件中心源](./time-series-insights-how-to-add-an-event-source-eventhub.md)或如何[添加 IoT 中心源](./time-series-insights-how-to-add-an-event-source-iothub.md)。

您將了解如何：

1. **（可選）** 將每個 Azure 時間序列見解預覽環境與事件源相關聯。 您還將提供時間戳記 ID 屬性和唯一消費者組，以確保環境有權訪問相應的事件。

   > [!NOTE]
   > 預配環境時，上述步驟是可選的。 如果跳過此步驟，則必須稍後將事件源附加到環境，以便在環境中訪問資料。

1. 佈建完成後，您可以修改您的存取原則與其他環境屬性，以符合您的商業需求。

## <a name="create-the-environment"></a>建立環境

要創建 Azure 時間序列見解預覽環境，請執行以下準備：

1. 選擇**PAYG**作為**層**。 提供環境名稱，並選擇要使用的訂閱組和資源組。 然後選擇受支援的位置來承載環境。

   [![建立 Azure 時間序列深入解析執行個體。](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. 輸入時間序列識別碼。

    > [!NOTE]
    > * 時間序列 ID 區分*大小寫*且*不可變*。 (設定之後就無法變更。)
    > * 時間序列指示數最多隻能*有三*個鍵。
    > * 閱讀有關如何[選擇時間序列 ID](time-series-insights-update-how-to-id.md)的更多內容

1. 通過選擇存儲帳戶名稱並指定複製選項來創建 Azure 存儲帳戶。 這樣做會自動建立 Azure 儲存體一般用途 v1 帳戶。 該帳戶與以前選擇的 Azure 時間序列見解預覽環境在同一區域中創建。

    [![冷存儲配置](media/v2-update-manage/create-and-manage-cold-store.png)](media/v2-update-manage/create-and-manage-cold-store.png#lightbox)

1. **（可選）** 如果要對環境中的最新資料進行更快、無限制的查詢，請為環境啟用暖存儲。 在創建時間序列見解預覽環境後，還可以通過左側功能窗格中的 **"存儲配置"** 選項創建或刪除暖存儲。

    [![暖存儲配置](media/v2-update-manage/create-and-manage-warm-storage.png)](media/v2-update-manage/create-and-manage-warm-storage.png#lightbox)

1. **（可選）** 您可以立即添加事件源。 您還可以等到實例預配後。

   * 時間序列見解支援[Azure IoT 中心](./time-series-insights-how-to-add-an-event-source-iothub.md)和 Azure[事件中心](./time-series-insights-how-to-add-an-event-source-eventhub.md)作為事件源選項。 儘管在創建環境時只能添加單個事件源，但以後可以添加另一個事件源。 
   
     在添加事件源時，可以選擇現有消費者組或創建新消費者組。 最好創建唯一的消費者組，以確保 Azure 時間序列預覽環境對所有事件都可見。

   * 選擇相應的時間戳記屬性。 預設情況下，Azure 時間序列見解使用每個事件源的消息排隊時間。

     > [!TIP]
     > 消息排隊時間可能不是在批次處理事件方案或歷史資料上載方案中使用的最佳配置設置。 在這種情況下，請確保驗證您使用或不使用時間戳屬性的決定。

     [![事件源配置選項卡](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. 確認環境已按照所需方式進行預配和配置。

    [![[檢閱 + 建立] 索引標籤](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>管理環境

您可以使用 Azure 入口網站管理 Azure 時間序列深入解析預覽版環境。 PAYG Azure 時間序列見解預覽環境與通過 Azure 門戶管理環境時要牢記的通常可用的 S1 或 S2 環境之間有幾個關鍵區別：

* Azure 門戶預覽**概述**邊欄選項卡具有以下更改：

  * 容量被刪除，因為它不適用於 PAYG 環境。
  * 將添加**時間序列 ID**屬性。 它會決定分割您的資料的方式。
  * 已移除參考資料集。
  * 顯示的 URL 會將您導向 [Azure 時間序列深入解析預覽版總管](./time-series-insights-update-explorer.md)。
  * 已列出您的 Azure 儲存體帳戶名稱。

* Azure 門戶的 **"配置**"邊欄選項卡在 Azure 時間序列見解預覽版中刪除，因為 PAYG 環境不可配置。 但是，您可以使用**存儲配置**來配置新引入的暖存儲。

* Azure 門戶的**參考資料**邊欄選項卡在 Azure 時間序列見解預覽中刪除，因為參考資料不是 PAYG 環境的一部分。

[![Azure 入口網站中的時間序列深入解析預覽版環境](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>後續步驟

- 通過閱讀["規劃環境"，](./time-series-insights-update-plan.md)瞭解有關時間序列見解（通常可用的環境和預覽環境）的更多。

- 瞭解如何[添加事件中心源](./time-series-insights-how-to-add-an-event-source-eventhub.md)。

- 配置[IoT 中心源](./time-series-insights-how-to-add-an-event-source-iothub.md)。
