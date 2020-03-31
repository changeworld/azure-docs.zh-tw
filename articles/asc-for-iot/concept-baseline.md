---
title: 瞭解 IoT 基線的 Azure 安全中心*微軟文檔
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
ms.openlocfilehash: ce5a0625a16c5a02d03ee74f894c585820414fa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72176608"
---
# <a name="azure-security-center-for-iot-baseline-and-custom-checks"></a>用於 IoT 基線和自訂檢查的 Azure 安全中心

本文介紹了 IoT 基線的 Azure 安全中心，並總結了基線自訂檢查的所有相關屬性。

## <a name="baseline"></a>基準

基線為每個設備建立標準行為，並更容易確定異常行為或偏離預期規範。  

## <a name="baseline-custom-checks"></a>基線自訂檢查

基線自訂檢查使用設備的**模組標識孿生**為每個設備基線建立自訂檢查清單。 

## <a name="setting-baseline-properties"></a>設置基線屬性

1. 在 IoT 中心中，找到並選擇要更改的設備。
1. 按一下設備，然後按一下**Azureiot 安全**模組。
1. 按一下**模組標識孿生**。
1. 將**基準自訂檢查**檔上載到設備。
1. 將基線屬性添加到安全模組，然後按一下"**保存**"。

### <a name="baseline-custom-check-file-example"></a>基線自訂檢查檔示例

要配置基線自訂檢查：

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
|基線自訂檢查啟用|必需： 真 |有效值：**布林** |預設值：**假** |發送高優先順序消息之前的最大時間間隔。|
|基線自訂檢查檔路徑 |必需： 真|有效值：**字串**， **null** |預設值 **：null** |基線 xml 配置的完整路徑|
|基線自訂檢查檔雜湊 |必需： 真|有效值：**字串**， **null** |預設值 **：null** |`sha256sum`xml 設定檔的。 有關詳細資訊，請使用[sha256sum 參考](https://linux.die.net/man/1/sha256sum)。 |

要查看其他基線示例，請參閱[自訂基線示例 -1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml)和[自訂基線示例 -2](https://ascforiot.blob.core.windows.net/public/oms_audits.xml)。

## <a name="next-steps"></a>後續步驟

- 訪問[原始安全資料](how-to-security-data-access.md)
- [調查裝置](how-to-investigate-device.md)
- 瞭解並探討[安全建議](concept-recommendations.md)
- 瞭解和探索[安全警報](concept-security-alerts.md)
