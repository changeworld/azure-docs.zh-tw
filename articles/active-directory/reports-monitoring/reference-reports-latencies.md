---
title: Azure Active Directory 報告延遲 | Microsoft Docs
description: 深入了解在您的 Azure 入口網站中針對顯示報告事件所花費的時間長度
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d031546bb4f1f05e9ea2abb5b74fe911b0b507f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74007710"
---
# <a name="azure-active-directory-reporting-latencies"></a>Azure Active Directory 報告延遲

延遲是 Azure Active Directory (Azure AD) 報告資料在 [Azure 入口網站](https://portal.azure.com)中顯示出來所花費的時間長度。 本文會列出不同報告類型的預期延遲。 

## <a name="activity-reports"></a>活動報告

活動報告類型有兩種︰

- [登入](concept-sign-ins.md) – 提供受控應用程式和使用者登入活動的使用情況相關資訊
- [稽核記錄](concept-audit-logs.md) - 可提供使用者和群組、受控應用程式和目錄活動的相關系統活動資訊

下表列出活動報告的延遲資訊。 

> [!NOTE]
> **延遲 (第 95 個百分位數)** 指的是報告 95% 記錄所花費的時間，**延遲 (第 99 個百分位數)** 指的則是報告 99% 記錄所花費的時間。 
>

| Report | 延遲 (第 95 個百分位數) |延遲 (第 99 個百分位數)|
| :-- | --- | --- |
| 稽核記錄 | 2 分鐘  | 5 分鐘  |
| 登入 | 2 分鐘  | 5 分鐘 |

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>在取得進階授權之後，我能夠多快能看見活動資料？

如果您在使用免費授權時即已經有活動資料，您便可以在升級時立即看見該資料。 如果您沒有任何資料，則您升級到進階授權後，必須花一或兩天的時間，資料才會顯示在報告中。

## <a name="security-reports"></a>安全性報告

安全性報告有兩種類型：

- [有風險的登入](concept-risky-sign-ins.md) - 有風險的登入表示非使用者帳戶合法擁有者的某人嘗試登入。 
- [標幟為有風險的使用者](concept-user-at-risk.md) - 有風險的使用者表示可能被盜用的使用者帳戶。 

下表列出安全性報告的延遲資訊。

| Report | 最小值 | Average | 最大值 |
| :-- | --- | --- | --- |
| 有風險的使用者          | 5 分鐘   | 15 分鐘  | 2 小時  |
| 有風險的登入         | 5 分鐘   | 15 分鐘  | 2 小時  |

## <a name="risk-detections"></a>風險偵測

Azure AD 會使用調適性機器學習服務演算法和啟發學習法，來偵測與您使用者帳戶相關的可疑動作。 每個偵測到的可疑動作都會儲存在名為**風險偵測**的記錄中。

下表列出風險偵測的延遲資訊。

| Report | 最小值 | Average | 最大值 |
| :-- | --- | --- | --- |
| 從匿名 IP 位址登入 |5 分鐘 |15 分鐘 |2 小時 |
| 從不熟悉的位置登入 |5 分鐘 |15 分鐘 |2 小時 |
| 認證外洩的使用者 |2 小時 |4 小時 |8 小時 |
| 不可能到達非典型位置的移動 |5 分鐘 |1 小時 |8 小時  |
| 從受感染的裝置登入 |2 小時 |4 小時 |8 小時  |
| 從具有可疑活動的 IP 位址登入 |2 小時 |4 小時 |8 小時  |


## <a name="next-steps"></a>後續步驟

* [Azure AD 報表總覽](overview-reports.md)
* [以程式設計方式存取 Azure AD 報告](concept-reporting-api.md)
* [Azure Active Directory 風險偵測](concept-risk-events.md)
