---
title: 快速入門：在 IoT 中樞內啟用適用於 IoT 的 Azure 資訊安全中心服務
description: 了解如何在您的 Azure IoT 中樞上架及啟用適用於 IoT 的 Azure 資訊安全中心安全性服務。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: 0b1f09cfaf107802e1ce6586b3f96b14269aaced
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "74664849"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>快速入門：在 IoT 中樞內讓適用於 IoT 的 Azure 資訊安全中心上線

本文說明如何在現有的 IoT 中樞上啟用適用於 IoT 的 Azure 資訊安全中心服務。 如果您目前沒有 IoT 中樞，請參閱[使用 Azure 入口網站建立 IoT 中樞](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal)來著手。 

> [!NOTE]
> 適用於 IoT 的 Azure 資訊安全中心目前只支援標準層的 IoT 中樞。


## <a name="prerequisites-for-enabling-the-service"></a>啟用服務的必要條件

- Log Analytics 工作區
  - 適用於 IoT 的 Azure 資訊安全中心預設會在 Log Analytics 工作區中儲存兩種類型 (**安全性警示**和**建議**) 的資訊。 
  - 您可以選擇新增其他資訊類型 (**未經處理的事件**) 的儲存體。 請注意，在 Log Analytics 中儲存**未經處理的事件**會帶來額外的儲存體成本。 
- IoT 中樞 (標準層)
- 符合所有[服務必要條件](service-prerequisites.md) 

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>在您的 IoT 中樞上啟用適用於 IoT 的 Azure 資訊安全中心 

若要在 IoT 中樞上啟用安全性︰ 

1. 在 Azure 入口網站中開啟您的 **IoT 中樞**。 
1. 在 [安全性]  功能表之下，按一下 [保護您的 IoT 解決方案]  。    


恭喜！ 您已在 IoT 中樞上完成適用於 IoT 的 Azure 資訊安全中心啟用。 

### <a name="geolocation-and-ip-address-handling"></a>地理位置和 IP 位址處理

為了保護您的 IoT 解決方案，系統已預設為會收集並儲存 IoT 裝置、IoT Edge 和 IoT 中樞內進行傳入和傳出連線的 IP 位址。 這在偵測可疑 IP 來源的異常連線時是不可或缺的資訊。 例如，嘗試從已知殭屍網路的 IP 來源，或您地理位置外部的 IP 來源建立連線時。 適用於 IoT 的 Azure 資訊安全中心服務提供隨時啟用和停用收集 IP 位址資料的彈性。 

若要啟用或停用 IP 位址資料的收集： 

1. 開啟您的 IoT 中樞，然後從 [安全性]  功能表中選取 [概觀]  。 
2. 選擇 [設定]  畫面，並視需要修改地理位置和/或 IP 處理設定。

### <a name="log-analytics-creation"></a>建立 Log Analytics

當適用於 IoT 的 Azure 資訊安全中心開啟時，系統會建立預設的 Azure Log Analytics 工作區來儲存您 IoT 裝置、IoT Edge 和 IoT 中樞的原始安全性事件、警示和建議。 每位客戶每月內嵌到 Azure Log Analytics 服務的前 5 GB 資料免費。 前 31 天會免費保留內嵌於您 Azure Log Analytics 工作區中的每 GB 資料。 深入了解 [Log Analytics](https://azure.microsoft.com/pricing/details/monitor/) 定價。

若要變更 Log Analytics 的工作區設定：

1. 開啟您的 IoT 中樞，然後從 [安全性]  功能表中選取 [概觀]  。 
2. 選擇 [設定]  畫面，並視需要修改 Log Analytics 設定的工作區設定。

### <a name="customize-your-iot-security-solution"></a>自訂您的 IoT 安全性解決方案
根據預設，開啟適用於 IoT 的 Azure 資訊安全中心解決方案會自動保護您 Azure 訂用帳戶下的所有 IoT 中樞。 

若要在特定 IoT 中樞上開啟或關閉適用於 IoT 的 Azure 資訊安全中心服務： 

1. 開啟您的 IoT 中樞，然後從 [安全性]  功能表中選取 [概觀]  。 
2. 選擇 [設定]  畫面，並視需要修改您 Azure 訂用帳戶中任何 IoT 中樞的安全性設定。


## <a name="next-steps"></a>後續步驟

請前往下一篇文章，以設定您的解決方案...

> [!div class="nextstepaction"]
> [設定您的解決方案](quickstart-configure-your-solution.md)
