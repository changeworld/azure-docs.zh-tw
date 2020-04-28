---
title: 疑難排解：下載的活動記錄中遺漏資料 |Microsoft Docs
description: 提供您已下載的 Azure Active Directory 活動記錄中遺漏資料的解決方案。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee07fdb6f8a4e69600297bdb16b6ad74793f10c9
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "74007727"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>我在下載的 Azure Active Directory 活動記錄中無法找到所有資料

## <a name="symptoms"></a>徵兆

我已下載活動記錄 (稽核或登入)，卻沒看到我所選擇時間的所有記錄。 原因為何？ 

 ![報告](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>原因

當您下載 Azure 入口網站中的活動記錄時，我們會將縮放比例限制為250000筆記錄（依最新的第一個排序）。 

## <a name="resolution"></a>解決方法

您可以利用 [Azure AD 報告 API](concept-reporting-api.md) 在任何指定時間點擷取最多一萬筆記錄。

## <a name="next-steps"></a>後續步驟

* [Azure Active Directory 報告常見問題集](reports-faq.md)

