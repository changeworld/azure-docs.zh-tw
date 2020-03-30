---
title: 從 Azure IoT 中心規則運行多個操作 |微軟文檔
description: 從單個 IoT 中央規則運行多個操作，並創建可以從多個規則運行的可重用操作組。
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
manager: philmea
ms.openlocfilehash: b447f44d0c95693e560fd5bbfbff8c8daeec964e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157682"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>將多個操作分組以從一個或多個規則運行

*本文適用于產生器和管理員。*

在 Azure IoT 中心中，創建規則以在滿足條件時運行操作。 規則基於設備遙測或事件。 例如，當設備溫度超過閾值時，可以通知操作員。 本文介紹如何使用 Azure[監視器](../../azure-monitor/overview.md)*操作組*將多個操作附加到 IoT 中心規則。 您可以將操作組附加到多個規則。 [操作組](../../azure-monitor/platform/action-groups.md)是由 Azure 訂閱的擁有者定義的通知首選項的集合。

## <a name="prerequisites"></a>Prerequisites

- 使用標準定價計畫創建的應用程式
- 創建和管理 Azure 監視器操作組的 Azure 帳戶和訂閱

## <a name="create-action-groups"></a>建立動作群組

您可以在 Azure 門戶或使用[Azure 資源管理器範本](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)[創建和管理操作組](../../azure-monitor/platform/action-groups.md)。

操作組可以：

- 發送電子郵件、短信或進行語音電話等通知。
- 運行操作，如調用 Webhook。

以下螢幕截圖顯示了發送電子郵件和 SMS 通知並調用 Webhook 的操作組：

![動作群組](media/howto-use-action-groups/actiongroup.png)

要在 IoT 中央規則中使用操作組，操作組必須與 IoT 中央應用程式位於同一 Azure 訂閱中。

## <a name="use-an-action-group"></a>使用操作組

要在 IoT 中央應用程式中使用操作組，請先創建規則。 向規則添加操作時，請選擇 Azure**監視器操作組**：

![選擇動作](media/howto-use-action-groups/chooseaction.png)

從 Azure 訂閱中選擇操作組：

![選擇操作組](media/howto-use-action-groups/chooseactiongroup.png)

選取 [儲存]****。 操作組現在顯示在觸發規則時要運行的動作清單中：

![保存的操作組](media/howto-use-action-groups/savedactiongroup.png)

下表匯總了發送到受支援的操作類型的資訊：

| 動作類型 | 輸出格式 |
| ----------- | -------------- |
| 電子郵件       | 標準 IoT 中央電子郵件範本 |
| sms         | Azure IoT 中心警報：${應用程式名稱} - 在 ${設備名稱}上觸發的"${設備名稱}"在 ${觸發器日期} ${觸發器時間} |
| 語音       | Azure I.O.T 中央警報：在設備上觸發的"${設備名稱}"規則"$_設備名稱"，在 ${觸發器日期} ${觸發器時間}，在應用程式 ${應用程式名稱} 中 |
| Webhook     | • "架構 Id"："AzureIoTCentralRuleWebhook"，"資料"：{[常規 Webhook 有效負載](howto-create-webhooks.md#payload)}} |

以下文本是操作組發送的 SMS 消息示例：

`iotcentral: Azure IoT Central alert: Contoso - "Low pressure alert" triggered on "Motion sensor 2" at March 20, 2019 10:12 UTC`

## <a name="next-steps"></a>後續步驟

現在，您已經瞭解如何將操作組與規則一起使用，建議的下一步是瞭解如何[管理您的設備](howto-manage-devices.md)。
