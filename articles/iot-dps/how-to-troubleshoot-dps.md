---
title: 使用 Azure IoT 中心 DPS 診斷和排除斷開連接
description: 瞭解如何通過 Azure IoT 中心設備佈建服務 （DPS） 的設備連接診斷和排除常見錯誤
author: xujing-ms
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: xujing
ms.openlocfilehash: 3cbab09c6b50abb590cfe9f2720713a8fa547aa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646467"
---
# <a name="troubleshooting-with-azure-iot-hub-device-provisioning-service"></a>使用 Azure IoT 中心設備佈建服務進行故障排除

IoT 設備的連接問題可能難以疑難排解，因為有許多可能的故障點，如證明失敗、註冊失敗等。本文提供有關如何通過[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)檢測和排除設備連接問題的指導。

## <a name="using-azure-monitor-to-view-metrics-and-set-up-alerts"></a>使用 Azure 監視器查看指標並設置警報

以下過程介紹如何在 IoT 中心設備佈建服務指標上查看和設置警報。 

1. 登錄到 Azure[門戶](https://portal.azure.com)。

2. 流覽到 IoT 中心設備佈建服務。

3. 選擇**指標**。

4. 選擇所需的指標。 
   <br />目前，DPS 有三個指標：

    | 標準名稱 | 描述 |
    |-------|------------|
    | 證明嘗試數 | 嘗試使用設備預配服務進行身份驗證的設備數|
    | 註冊嘗試數 | 成功身份驗證後嘗試註冊到 IoT 中心的設備數|
    | 分配的設備 | 成功分配給 IoT 中心的設備數|

5. 選擇所需的聚合方法以創建指標的可視視圖。 

6. 要設置指標警報，請從指標邊欄選項卡的右上角選擇 **"新警報規則**"，同樣，您也可以轉到 **"警報"** 邊欄選項卡並選擇 **"新警報規則**"。

7. 選擇 **"添加條件**"，然後按照以下提示選擇所需的指標和閾值。

要瞭解更多資訊，請參閱[Microsoft Azure 中的經典警報是什麼？](../azure-monitor/platform/alerts-overview.md)

## <a name="using-log-analytic-to-view-and-resolve-errors"></a>使用日誌分析查看和解決錯誤

1. 登錄到 Azure[門戶](https://portal.azure.com)。

2. 瀏覽至您的 IoT 中樞。

3. 選取 [診斷設定]****。

4. 選取 [開啟診斷]****。

5. 啟用收集所需的日誌。

    | 記錄檔名稱 | 描述 |
    |-------|------------|
    | DeviceOperations | 與設備連接事件相關的日誌 |
    | ServiceOperations | 與使用服務 SDK 相關的事件日誌（例如，創建或更新註冊組）|

6. 打開 **"發送到日誌分析**"（[請參閱定價](https://azure.microsoft.com/pricing/details/log-analytics/)）。 

7. 轉到"設備預配服務"資源下的 Azure 門戶中的 **"日誌"** 選項卡。

8. 按一下 **"運行"** 以查看最近的事件。

9. 如果有結果`OperationName`，則查找 、`ResultType`和`ResultSignature` `ResultDescription` （錯誤訊息）以獲取有關錯誤的更多詳細資訊。


## <a name="common-error-codes"></a>常見的錯誤碼
使用此表格來了解和解決常見錯誤。

| 錯誤碼| 描述 | HTTP 狀態碼 |
|-------|------------|------------|
| 400 | 請求正文無效;例如，無法對其進行分析，或者無法驗證該物件。| 400 格式錯誤 |
| 401 | 無法驗證授權權杖;例如，它已過期或不適用於請求的 URI。 此錯誤代碼也作為 TPM 認證流的一部分返回到設備。 | 401 未經授權|
| 404 | 設備預配服務實例或資源（例如註冊）不存在。 |404 找不到 |
| 412 | 請求中的 ETag 與現有資源的 ETag 不匹配，如 RFC7232。 | 412 先決條件失敗 |
| 429 | 服務正在限制操作。 有關特定服務限制，請參閱[IoT 中心設備預配服務限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#iot-hub-device-provisioning-service-limits)。 | 429 請求太多 |
| 500 | 發生內部錯誤。 | 500 內部伺服器錯誤|
