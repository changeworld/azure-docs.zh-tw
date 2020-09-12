---
title: 適用于 Azure RTO 內建 & 可自訂警示和建議的安全性模組
description: 深入瞭解使用 Azure IoT 安全性模組-RTO 的安全性警示和建議的補救方法。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: ebb4edf10433cce981b4718835676d0a5d76ba40
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514830"
---
# <a name="security-module-for-azure-rtos-security-alerts-and-recommendations-preview"></a>Azure RTO 安全性警示和建議的安全性模組 (預覽) 

適用于 Azure RTO 的安全性模組會使用先進的分析和威脅情報持續分析您的 IoT 解決方案，以警示您潛在的惡意活動和可疑的系統修改。 您也可以根據您對預期裝置行為和基準的知識來建立自訂警示。

適用于 Azure RTO 警示的安全性模組可作為潛在入侵的指標，並應進行調查和補救。 Azure RTO 建議的安全性模組會識別要補救和更新的弱式安全性狀態。 

在本文中，您將找到根據預設範圍觸發的內建警示和建議清單，並根據預期或基準行為以您自己的值進行自訂。 

如需有關警示自訂如何在適用於 IoT 的 Azure 資訊安全中心服務中運作的詳細資訊，請參閱 [可自訂的警示](concept-customizable-security-alerts.md)。 下表詳細說明使用適用于 Azure RTO 的安全性模組時，可供自訂的特定警示和建議。 

## <a name="security-module-for-azure-rtos-supported-security-alerts"></a>適用于 Azure RTO 的安全性模組支援的安全性警示

### <a name="device-related-security-alerts"></a>裝置相關的安全性警示

|裝置相關的安全性警示活動  |警示名稱  |
|---------|---------|
|IP 位址| 偵測到與可疑 IP 位址的通訊|
|X.509 裝置憑證指紋|X.509 裝置憑證指紋不符|
|X.509 憑證| X.509 憑證已過期|
|SAS 權杖| 過期的 SAS 權杖|
|SAS 權杖| SAS 權杖簽章無效|

### <a name="iot-hub-related-security-alerts"></a>IoT 中樞相關的安全性警示

|IoT 中樞安全性警示活動  |警示名稱  |
|---------|---------|
|新增憑證    |  偵測到將憑證新增至 IoT 中樞的嘗試失敗       |
|新增或編輯診斷設定    | 偵測到嘗試新增或編輯 IoT 中樞的診斷設定      |
|刪除憑證    |  偵測到嘗試從 IoT 中樞刪除憑證失敗       |
|刪除診斷設定    |  偵測到嘗試從 IoT 中樞刪除診斷設定      |
|已刪除憑證    | 偵測到從 IoT 中樞刪除憑證        |
|新憑證     |  偵測到將新憑證新增至 IoT 中樞       |

## <a name="security-module-for-azure-rtos-supported-customizable-alerts"></a>適用于 Azure RTO 的安全性模組支援自訂警示

### <a name="device-related-customizable-alerts"></a>裝置相關可自訂警示

|裝置相關活動 |警示名稱  |
|---------|---------|
|作用中的連線|作用中的連接數目不在允許的範圍內|
|**MQTT**通訊協定中的雲端到裝置訊息|**MQTT**通訊協定中的雲端到裝置訊息數目不在允許範圍內|
|輸出連接| 連至不允許的 IP 的輸出連線|

### <a name="hub-related-customizable-alerts"></a>中樞相關可自訂警示 

|中樞相關活動  |警示名稱  |
|---------|---------|
|命令佇列清除     |  在允許的範圍之外清除的命令佇列數目       |
|**MQTT**通訊協定中的雲端到裝置訊息    |  **MQTT**通訊協定中的雲端到裝置訊息數目超出允許的範圍       |
|**MQTT**通訊協定中的裝置到雲端訊息    | **MQTT**通訊協定中的裝置到雲端訊息數目超出允許的範圍        |
|直接方法叫用     |  允許範圍外的直接方法叫用數目       |
|已拒絕 **MQTT** 通訊協定中的雲端到裝置訊息     |   **MQTT**通訊協定中允許範圍以外的已拒絕雲端到裝置訊息數目      |
|對應項模組的更新     |  允許範圍以外的對應項模組更新數目       |
|未經授權的作業    |  允許範圍以外的未經授權作業數目       |

## <a name="security-module-for-azure-rtos-supported-recommendations"></a>適用于 Azure RTO 的安全性模組支援建議

### <a name="device-related-recommendations"></a>裝置相關的建議

|裝置相關活動  |建議名稱 |
|---------|---------|
|驗證認證    |  多個裝置使用的相同驗證認證       |

### <a name="hub-related-recommendations"></a>中樞相關的建議

|IoT 中樞相關活動  |建議名稱 |
|---------|---------|
|IP 篩選原則   |  預設 IP 篩選原則應設定為 [**拒絕**]  |
|IP 篩選規則| IP 篩選規則包含大型 IP 範圍|
|診斷記錄|在 IoT 中樞中啟用診斷記錄的建議|

### <a name="all-azure-security-center-for-iot-alerts-and-recommendations"></a>所有適用於 IoT 的 Azure 資訊安全中心警示和建議

如需所有適用於 IoT 的 Azure 資訊安全中心服務相關警示和建議的完整清單，請參閱 IoT [安全性警示](concept-security-alerts.md)、iot 安全性 [建議](concept-recommendations.md)。

## <a name="next-steps"></a>接下來的步驟

- [快速入門：適用于 Azure RTO 的安全性模組](quickstart-azure-rtos-security-module.md)
- [設定和自訂 Azure RTO 的安全性模組](how-to-azure-rtos-security-module.md)
- 請參閱 [AZURE RTO API 的安全性模組](azure-rtos-security-module-api.md)