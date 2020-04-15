---
title: 基線與自訂檢查
description: 瞭解 IoT 基線的 Azure 安全中心的概念。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2019
ms.author: mlottner
ms.openlocfilehash: c52a3e55e3801eaaac885b9a3c364283f74906ba
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311648"
---
# <a name="azure-security-center-for-iot-baseline-and-custom-checks"></a>用於 IoT 基線和自訂檢查的 Azure 安全中心

本文介紹了 IoT 基線的 Azure 安全中心,並總結了基線自定義檢查的所有相關屬性。

## <a name="baseline"></a>基準

基線為每個設備建立標準行為,並更容易確定異常行為或偏離預期規範。

## <a name="baseline-custom-checks"></a>基線自訂檢查

基線自定義檢查使用設備的**模塊識別孿生**為每個設備基線建立自定義檢查清單。

## <a name="setting-baseline-properties"></a>設定基線屬性

1. 在 IoT 中心中,找到並選擇要更改的設備。
1. 單擊設備,然後單擊**Azureiot 安全**模組。
1. 點選**模組識別孿生**。
1. 將**基準自定義檢查**檔上載到設備。
1. 將基線屬性添加到安全模組,然後按一下「**儲存**」 。

### <a name="baseline-custom-check-file-example"></a>基線自訂檢查檔案範例

要設定基線自訂檢查:

   ```json
    "desired": {
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
        "baselineCustomChecksEnabled": {
          "value" : true
        },
        "baselineCustomChecksFilePath": {
          "value" : "/home/user/full_path.xml"
        },
        "baselineCustomChecksFileHash": {
          "value" : "#hashexample!"
        }
      }
    },
   ```

## <a name="baseline-custom-check-properties"></a>基線自訂檢查屬性

| 名稱| 狀態 | 有效值| 預設值| 描述 |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|基線自訂檢查啟用|必要: 真 |合法值:**布林** |預設值:**假** |發送高優先順序消息之前的最大時間間隔。|
|基線自訂檢查檔案路徑 |必要: 真|合法值:**字串**, **null** |預設值 **:null** |基線 xml 設定的完整路徑|
|基線自訂檢查檔案哈希 |必要: 真|合法值:**字串**, **null** |預設值 **:null** |`sha256sum`xml 設定檔的。 有關詳細資訊,請使用[sha256sum 參考](https://linux.die.net/man/1/sha256sum)。 |

要檢視其他基線範例,請參閱[自訂基線範例 -1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml)和[自訂基線範例 -2](https://ascforiot.blob.core.windows.net/public/oms_audits.xml)。

## <a name="next-steps"></a>後續步驟

- 存取[原始安全資料](how-to-security-data-access.md)
- [調查裝置](how-to-investigate-device.md)
- 瞭解並探討[安全建議](concept-recommendations.md)
- 瞭解和探索[安全警報](concept-security-alerts.md)
