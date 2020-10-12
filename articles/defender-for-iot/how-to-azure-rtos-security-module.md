---
title: 設定和自訂 Azure RTO 的安全性模組
description: 瞭解如何設定及自訂 Azure RTO 的安全性模組。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 9106e00fe2146978f97b480e3afd3b7ed58c9130
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90933978"
---
# <a name="configure-and-customize-security-module-for-azure-rtos-preview"></a>設定和自訂 Azure RTO (preview) 的安全性模組

使用下列檔案來設定您的裝置行為。

## <a name="azure_iot_security_moduleincasc_porth"></a>azure_iot_security_module/inc/asc_port。h

 下表提供每個設定的預設行為： 

### <a name="general"></a>一般

| 名稱 | 類型 | 預設 | 詳細資料 |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | 字串 | --- | 裝置的唯一識別碼  |
| ASC_SECURITY_MODULE_PENDING_TIME  | 數字 | 300 | 安全性模組擱置時間（秒）。 如果時間超過 [狀態] 變更為 [暫停]。 |

#### <a name="collection"></a>集合

| 名稱 | 類型 | 預設 | 詳細資料 |
| - | - | - | - |
| ASC_HIGH_PRIORITY_INTERVAL | 數字 | 10 | 收集器高優先順序群組間隔（以秒為單位）。 |
| ASC_MEDIUM_PRIORITY_INTERVAL | 數字 | 30 | 收集器中優先順序群組間隔（以秒為單位）。 |
| ASC_LOW_PRIORITY_INTERVAL | 數字 | 145440  | 收集器低優先順序群組間隔（以秒為單位）。 |

#### <a name="collector-network-activity"></a>收集器網路活動

若要自訂收集器網路活動設定，請使用下列各項：

| 名稱 | 類型 | 預設 | 詳細資料 |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | 布林值 | false | 篩選 `TCP` 網路活動 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | 布林值 | false | 篩選 `UDP` 網路活動事件 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | 布林值 | false | 篩選 `ICMP` 網路活動事件 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | 布林值 | true | 只有當設定為 false capture 也廣播和多播時，才會捕捉單播傳入封包 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | 數字 | 64 | 要儲存在記憶體中的 IPv4 網路事件數目上限 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | 數字 | 64  | 要儲存在記憶體中的 IPv6 網路事件數目上限 |


## <a name="compile-flags"></a>編譯旗標
編譯旗標可讓您覆寫預先定義的設定。

### <a name="collectors"></a>Collectors
| 名稱 | 類型 | 預設 | 詳細資料 |
| - | - | - | - |
| collector_heartbeat_enabled | 布林值 | 開啟 | 啟用信號收集器 |
| collector_network_activity_enabled | 布林值 | 開啟 | 啟用網路活動收集器 |
| collector_system_information_enabled | 布林值 | 開啟 | 啟用系統資訊收集器 |

## <a name="supported-security-alerts-and-recommendations"></a>支援的安全性警示和建議

適用于 Azure RTO 的安全性模組支援特定的安全性警示和建議。 請務必 [檢查並自訂服務的相關警示和建議值](concept-rtos-security-alerts-recommendations.md) 。

## <a name="log-analytics-optional"></a>Log Analytics (選擇性) 

雖然選擇性且非必要，但當您想要進一步調查裝置事件和活動時，啟用和設定 Log Analytics 會很有説明。 瞭解如何設定和使用 [Log Analytics 搭配 Defender For IoT 服務](how-to-security-data-access.md#log-analytics) ，以深入瞭解。 

## <a name="next-steps"></a>後續步驟

- 檢查和自訂 Azure RTO[安全性警示和建議](concept-rtos-security-alerts-recommendations.md)的安全性模組
- 如有需要，請參閱 [適用于 AZURE RTO API 的安全性模組](azure-rtos-security-module-api.md) 。

