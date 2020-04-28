---
title: 診斷 Azure IoT 中樞 DPS 中斷連線並進行疑難排解
description: 瞭解如何針對 Azure IoT 中樞裝置布建服務（DPS）的裝置連線性進行常見錯誤的診斷和疑難排解
author: xujing-ms
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: xujing
ms.openlocfilehash: 3cbab09c6b50abb590cfe9f2720713a8fa547aa7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75646467"
---
# <a name="troubleshooting-with-azure-iot-hub-device-provisioning-service"></a>使用 Azure IoT 中樞裝置布建服務進行疑難排解

IoT 裝置的連線問題可能會很難進行疑難排解，因為有許多可能的失敗點，例如證明失敗、註冊失敗等等。本文提供有關如何透過[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)偵測和疑難排解裝置連線問題的指引。

## <a name="using-azure-monitor-to-view-metrics-and-set-up-alerts"></a>使用 Azure 監視器來查看計量和設定警示

下列程式描述如何查看和設定 IoT 中樞裝置佈建服務計量的警示。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 流覽至您的 IoT 中樞裝置佈建服務。

3. 選取 [**計量**]。

4. 選取所需的度量。 
   <br />目前有三個 DPS 的計量：

    | 標準名稱 | 描述 |
    |-------|------------|
    | 證明嘗試數 | 嘗試使用裝置布建服務進行驗證的裝置數目|
    | 註冊嘗試數 | 成功驗證後嘗試註冊 IoT 中樞的裝置數目|
    | 已指派裝置 | 成功指派給 IoT 中樞的裝置數目|

5. 選取 [所需的匯總方法] 以建立度量的視覺效果視圖。 

6. 若要設定計量的警示，請從 [計量] 分頁的右上角選取 [**新增警示規則**]，同樣地，您可以移至 [**警示**] 分頁，然後選取 [**新增警示規則**]。

7. 選取 [**新增條件**]，然後遵循提示來選取所需的度量和閾值。

若要深入瞭解，請參閱[什麼是 Microsoft Azure 中的傳統警示？](../azure-monitor/platform/alerts-overview.md)

## <a name="using-log-analytic-to-view-and-resolve-errors"></a>使用記錄分析來查看和解決錯誤

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 瀏覽至您的 IoT 中樞。

3. 選取 [診斷設定]****。

4. 選取 [開啟診斷]****。

5. 啟用所需的記錄檔收集。

    | 記錄檔名稱 | 描述 |
    |-------|------------|
    | DeviceOperations | 與裝置連接事件相關的記錄 |
    | ServiceOperations | 與使用服務 SDK 相關的事件記錄（例如建立或更新註冊群組）|

6. 開啟 [**傳送至 Log Analytics** ] （[請參閱定價](https://azure.microsoft.com/pricing/details/log-analytics/)）。 

7. 移至 [裝置布建服務資源] 底下 Azure 入口網站中的 [**記錄**] 索引標籤。

8. 按一下 [**執行**] 以查看最新的事件。

9. 如果有結果，請`OperationName`尋找、 `ResultType`、 `ResultSignature`和`ResultDescription` （錯誤訊息），以取得有關錯誤的詳細資料。


## <a name="common-error-codes"></a>常見的錯誤碼
使用此表格來了解和解決常見錯誤。

| 錯誤碼| 描述 | HTTP 狀態碼 |
|-------|------------|------------|
| 400 | 要求的主體無效;例如，無法剖析，或無法驗證物件。| 400格式不正確 |
| 401 | 無法驗證授權權杖;例如，它已過期或不適用於要求的 URI。 此錯誤碼也會在 TPM 證明流程中傳回給裝置。 | 401 未經授權|
| 404 | 裝置布建服務實例或資源（例如註冊）不存在。 |404 找不到 |
| 412 | 要求中的 ETag 與現有資源的 ETag 不相符，因為每個依據 RFC7232。 | 412前置條件失敗 |
| 429 | 服務正在對作業進行節流。 如需特定的服務限制，請參閱[IoT 中樞裝置佈建服務限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#iot-hub-device-provisioning-service-limits)。 | 429太多要求 |
| 500 | 發生內部錯誤。 | 500 內部伺服器錯誤|
