---
title: 連線處理站解決方案功能 - Azure | Microsoft Docs
description: 本文介紹了連接工廠預配置解決方案的功能的概述，如雲儀表板、規則和警報。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dobett
ms.openlocfilehash: c868aa0f1c2449ccf163523c9ded25a31d1d84c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820099"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>連線處理站 IoT 解決方案加速器是什麼？

連線處理站是 Microsoft 的 Azure 產業 IoT 參考架構實作，封裝為開放原始碼解決方案。 您可以用它來當作商業產品的起點。 您可以將連線處理站解決方案的預先建立版本，從 [Azure IoT 解決方案加速器](https://www.azureiotsolutions.com/#solutions/types/CF)部署到 Azure 訂用帳戶。

![連線處理站解決方案的概觀](./media/iot-accelerators-connected-factory-features/dashboard.png)

「連線的處理站」解決方案加速器[程式碼可從 GitHub 取得](https://github.com/Azure/azure-iot-connected-factory)。

連線處理站包括下列功能：

## <a name="industrial-device-interoperability"></a>產業裝置互通性

- 使用 OPC UA 介面連線到產業資產。
- 使用模擬的生產線 (在 Docker 容器中執行 OPC UA 伺服器) 並從中查看即時遙測。
- 從雲端儀表板瀏覽 OPC UA 伺服器的 OPC UA 資訊模型。

## <a name="remote-management"></a>遠端管理

- 從雲端儀表板設定您的 OPC UA 資產 (呼叫方法、讀取和寫入資料)。
- 從雲端儀表板發行和取消發行 OPC UA 資產中的遙測資料。

## <a name="cloud-dashboard"></a>雲端儀表板

- 直接在雲端儀表板中檢視遙測預覽。
- 使用時間序列深入解析總管儀表板，來檢視遙測資料的趨勢，並建立相互關聯。
- 從雲端儀表板查看計算的設備綜合效率 (OEE) 和關鍵效能指標 (KPI)。
- 從樹狀拓撲和互動式地圖上檢視產業資產階層。
- 從雲端儀表板檢視、接收及關閉警示。

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Azure 時間序列深入解析](../time-series-insights/time-series-insights-overview.md)是針對儲存、視覺化及查詢大量時間序列資料而建置。 連線處理站會利用這項服務。
- 整合此服務的連線處理站可讓您對裝置資料執行深入且即時的分析。

## <a name="rules-and-alerts"></a>規則和警示

[設定警示的閾值規則](iot-accelerators-connected-factory-configure.md)。

## <a name="end-to-end-security"></a>端對端安全性

- 使用角色型存取控制 (RBAC) 設定使用者的安全性權限。
- 端對端加密是使用 OPC UA 驗證 (使用 X.509 憑證) 及安全性權杖來加以實作。

## <a name="customizability"></a>自訂能力

- 自訂解決方案以滿足特定的業務需求。
- 完整的解決方案原始程式碼可在 GitHub 上取得。 請參閱[連接出廠預配置的解決方案](https://github.com/Azure/azure-iot-connected-factory)存儲庫。

## <a name="next-steps"></a>後續步驟

要瞭解有關互聯工廠解決方案加速器的更多資訊，請參閱快速入門[嘗試基於雲的解決方案來管理我的工業物聯網設備](quickstart-connected-factory-deploy.md)。
