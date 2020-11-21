---
title: 如何新增 IoT 中樞事件來源-Azure 時間序列深入解析 |Microsoft Docs
description: 瞭解如何將 IoT 中樞事件來源新增至您的 Azure 時間序列深入解析環境。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 09ee98bf8bbe0067f9c79ce4767f3cf68b83fb43
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016797"
---
# <a name="add-an-iot-hub-event-source-to-your-azure-time-series-insight-environment"></a>將 IoT 中樞事件來源新增至您的 Azure 時間序列深入解析環境

此文章說明如何使用 Azure 入口網站，將從 Azure IoT 中樞讀取資料的事件來源新增至您的 Azure 時間序列深入解析環境。

> [!NOTE]
> 本文中的指示適用于 Azure 時間序列深入解析 Gen 1 和 Azure 時間序列深入解析 Gen 2 環境。

## <a name="prerequisites"></a>必要條件

* 建立 [Azure 時間序列深入解析環境](./tutorials-set-up-tsi-environment.md)。
* [使用 Azure 入口網站建立 IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。
* IoT 中樞必須要有傳入的作用中訊息事件。
* 在 IoT 中樞內建立專用的取用者群組，讓 Azure 時間序列深入解析環境可供使用。 每個 Azure 時間序列深入解析事件來源都必須有自己專用的取用者群組，而該群組不會與任何其他取用者共用。 如果有多個讀取器取用來自同一個取用者群組的事件，則所有讀取器可能會出現失敗。 如需詳細資訊，請參閱 [Azure IoT 中樞開發人員指南](../iot-hub/iot-hub-devguide.md)。

### <a name="add-a-consumer-group-to-your-iot-hub"></a>將取用者群組新增至 IoT 中樞

應用程式會使用取用者群組從 Azure IoT 中樞提取資料。 若要可靠地從 IoT 中樞讀取資料，請提供僅供此 Azure 時間序列深入解析環境使用的專屬取用者群組。

新增取用者群組至 IoT 中樞：

1. 在 [Azure 入口網站](https://portal.azure.com)中，尋找並開啟您的 IoT 中樞。

1. 在 [ **設定**] 底下，選取 [ **內建端點**]，然後選取 [ **事件** ] 端點。

   [![在 [內建端點] 頁面中，選取 [事件] 按鈕](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png#lightbox)

1. 在 [取用者群組] 下，輸入取用者群組的唯一名稱。 當您建立新的事件來源時，請在您的 Azure 時間序列深入解析環境中使用相同的名稱。

1. 選取 [儲存]。

## <a name="add-a-new-event-source"></a>新增事件來源

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在左側功能表中，選取 [所有資源]  。 選取您的 Azure 時間序列深入解析環境。

1. 在 [ **設定**] 底下，選取 [ **事件來源**]，然後選取 [ **新增**]。

   [![選取事件來源，然後選取 [新增] 按鈕](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png#lightbox)

1. 在 [ **新事件來源** ] 窗格的 [ **事件來源名稱**] 中，輸入此 Azure 時間序列深入解析環境的唯一名稱。 例如，輸入 **event-stream**。

1. 對於 [來源]，請選取 [Iot 中樞]。

1. 為 [輸入選項] 選取一個值：

   * 如果您的其中一個訂用帳戶已經有 IoT 中樞，請選取 [從可用的訂閱帳戶使用 IoT 中樞]。 此選項是最簡單的方法。

     [![選取 [新事件來源] 窗格中的選項](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png#lightbox)

   * 下表說明 [從可用的訂用帳戶使用 Iot 中樞] 選項的必要屬性：

       [![[新事件來源] 窗格 - [從可用的訂用帳戶使用 Iot 中樞] 選項中要設定的屬性](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png#lightbox)

       | 屬性 | 描述 |
       | --- | --- |
       | 訂用帳戶 | 所要的 iot 中樞所屬的訂用帳戶。 |
       | IoT 中樞名稱 | 所選 iot 中樞的名稱。 |
       | IoT 中樞原則名稱 | 選取共用存取原則。 您可以在 [IoT 中樞設定] 索引標籤上找到共用存取原則。每個共用存取原則都有名稱、您設定的許可權，以及存取金鑰。 事件來源的共用存取原則必須有 **服務連線** 權限。 |
       | IoT 中樞原則金鑰 | 索引鍵已預先填入。 |

   * 如果 IoT 中樞對於您的訂用帳戶是外部，或如果想要選擇進階選項，請選取 [手動提供 IoT 中樞設定]。

      下表說明 [手動提供 Iot 中樞設定] 的必要屬性：

       | 屬性 | 說明 |
       | --- | --- |
       | 訂用帳戶識別碼 | 所要的 iot 中樞所屬的訂用帳戶。 |
       | 資源群組 | 建立 IoT 中樞所在的資源群組名稱。 |
       | IoT 中樞名稱 | 您 IoT 中樞的名稱。 當您建立 IoT 中樞時，也會輸入 IoT 中樞的名稱。 |
       | IoT 中樞原則名稱 | 共用存取原則。 您可以在 [IoT 中樞設定] 索引標籤上建立共用存取原則。每個共用存取原則都有名稱、您設定的許可權，以及存取金鑰。 事件來源的共用存取原則必須有 **服務連線** 權限。 |
       | IoT 中樞原則金鑰 | 用來驗證 Azure 服務匯流排命名空間存取權的共用存取金鑰。 在這裡輸入主要金鑰或次要金鑰。 |

   * 這兩個選項都會共用下列設定選項：

       | 屬性 | 說明 |
       | --- | --- |
       | IoT 中樞取用者群組 | 從 IoT 中樞讀取事件的取用者群組。 強烈建議您使用專屬於您事件來源的取用者群組。 |
       | 事件序列化格式 | 目前，JSON 是目前唯一可用的序列化格式。 事件訊息必須是這種格式，否則無法讀取任何資料。 |
       | 時間戳記屬性名稱 | 若要判斷此值，您需要了解傳送至 IoT 中樞的訊息資料採用那一種訊息格式。 這個值是您想要使用作為事件時間戳記之訊息資料中特定事件屬性的 **名稱**。 此值區分大小寫。 若保留空白，會使用事件來源中的 **事件加入佇列時間** 作為事件時間戳記。 |

1. 新增您新增至 IoT 中樞的專用 Azure 時間序列深入解析取用者組名。

1. 選取 [建立]。

1. 建立事件來源之後，Azure 時間序列深入解析會自動開始將資料串流至您的環境。

## <a name="next-steps"></a>下一步

* [定義資料存取原則](./concepts-access-policies.md)來保護資料。

* [將事件傳送](time-series-insights-send-events.md) 至事件來源。

* 在 [Azure 時間序列深入解析瀏覽器](https://insights.timeseries.azure.com)中存取您的環境。