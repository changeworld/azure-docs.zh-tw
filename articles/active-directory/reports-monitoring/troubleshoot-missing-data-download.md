---
title: 故障排除：下載的活動日誌中缺少資料 |微軟文檔
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74007727"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>我在下載的 Azure Active Directory 活動記錄中無法找到所有資料

## <a name="symptoms"></a>徵狀

我已下載活動記錄 (稽核或登入)，卻沒看到我所選擇時間的所有記錄。 原因為何？ 

 ![報告](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>原因

在 Azure 門戶中下載活動日誌時，我們將規模限制為 250，000 條記錄，按最近一次排序。 

## <a name="resolution"></a>解決方案

您可以利用 [Azure AD 報告 API](concept-reporting-api.md) 在任何指定時間點擷取最多一萬筆記錄。

## <a name="next-steps"></a>後續步驟

* [Azure Active Directory 報告常見問題集](reports-faq.md)

