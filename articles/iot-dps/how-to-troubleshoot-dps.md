---
title: 使用 Azure IoT 中樞 DPS 診斷和疑難排解中斷連線
description: 瞭解如何針對 Azure IoT 中樞裝置布建服務 (DPS) 的裝置連線能力，進行常見錯誤的診斷和疑難排解
author: xujing-ms
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: xujing
ms.openlocfilehash: 2d68314aab636180ff17e330c49c9859b8851b06
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94950681"
---
# <a name="troubleshooting-with-azure-iot-hub-device-provisioning-service"></a>使用 Azure IoT 中樞裝置布建服務進行疑難排解

IoT 裝置的連線能力問題可能很難進行疑難排解，因為有許多可能的失敗點，例如證明失敗、註冊失敗等等。本文提供有關如何透過 [Azure 監視器](../azure-monitor/overview.md)偵測和疑難排解裝置連線問題的指引。

## <a name="using-azure-monitor-to-view-metrics-and-set-up-alerts"></a>使用 Azure 監視器來查看計量和設定警示

下列程式說明如何查看和設定 IoT 中樞裝置布建服務計量的警示。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 流覽至您的 IoT 中樞裝置布建服務。

3. 選取 [計量]。

4. 選取所需的度量。 
   <br />DPS 目前有三個計量：

    | 標準名稱 | 描述 |
    |-------|------------|
    | 證明嘗試數 | 嘗試使用裝置布建服務進行驗證的裝置數目|
    | 註冊嘗試數 | 成功驗證之後，嘗試向 IoT 中樞註冊的裝置數目|
    | 指派的裝置 | 成功指派給 IoT 中樞的裝置數目|

5. 選取所需的匯總方法，以建立度量的視覺化視圖。 

6. 若要設定計量的警示，請從 [計量] 分頁的右上方選取 [ **新增警示規則** ]，同樣地，您可以移至 [ **警示** ] 分頁，然後選取 [ **新增警示規則**]。

7. 選取 [ **新增條件**]，然後遵循提示來選取所需的計量和臨界值。

若要深入瞭解，請參閱 [Microsoft Azure 中的傳統警示？](../azure-monitor/platform/alerts-overview.md)

## <a name="using-log-analytic-to-view-and-resolve-errors"></a>使用記錄分析來查看和解決錯誤

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 瀏覽至您的 IoT 中樞。

3. 選取 [診斷設定]。

4. 選取 [開啟診斷]。

5. 啟用所需的記錄檔收集。

    | 記錄檔名稱 | 說明 |
    |-------|------------|
    | DeviceOperations | 與裝置連接事件相關的記錄 |
    | ServiceOperations | 使用服務 SDK 的相關事件記錄 (例如建立或更新註冊群組) |

6. 開啟 [ **傳送至 Log Analytics** ] ([查看定價](https://azure.microsoft.com/pricing/details/log-analytics/)) 。 

7. 移至 [裝置布建服務資源] 下 Azure 入口網站中的 [ **記錄** ] 索引標籤。

8. 按一下 [ **執行** ] 以查看最近的事件。

9. 如果有結果，請尋找 `OperationName` 、 `ResultType` 、 `ResultSignature` 和 `ResultDescription` (錯誤訊息) ，以取得錯誤的詳細資料。


## <a name="common-error-codes"></a>常見的錯誤碼
使用此表格來了解和解決常見錯誤。

| 錯誤碼| 描述 | HTTP 狀態碼 |
|-------|------------|------------|
| 400 | 要求的主體無效;例如，無法剖析，或無法驗證物件。| 400錯誤格式 |
| 401 | 無法驗證授權權杖;例如，它已過期或不適用於要求的 URI。 此錯誤碼也會傳回給裝置，作為 TPM 證明流程的一部分。 | 401 未經授權|
| 404 | 裝置布建服務實例或資源 (例如註冊) 不存在。 |404 找不到 |
| 412 | 要求中的 ETag 不符合現有資源的 ETag （依 >RFC7232）。 | 412先決條件失敗 |
| 429 | 服務正在節流作業。 如需特定服務限制，請參閱 [IoT 中樞裝置布建服務限制](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-device-provisioning-service-limits)。 | 429太多要求 |
| 500 | 發生內部錯誤。 | 500 內部伺服器錯誤|