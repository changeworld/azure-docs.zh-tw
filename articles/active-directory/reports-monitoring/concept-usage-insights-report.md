---
title: 使用方式和見解報告 |微軟文檔
description: Azure 活動目錄門戶中的使用方式和見解報告簡介
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 3fba300d-18fc-4355-9924-d8662f563a1f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b3db86137207ae726c7befc393f62590fd1456d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74008271"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Azure 活動目錄門戶中的使用方式和見解報告

使用使用方式和見解報告，您可以獲取以應用程式為中心的登錄資料檢視。 您可以找到以下問題的答案：

*   組織中最常用的應用程式是什麼？
*   哪些應用程式的登錄失敗最多？ 
*   每個應用程式的頂級登錄錯誤是什麼？

## <a name="prerequisites"></a>Prerequisites 

要從使用方式和見解報告中訪問資料，您需要：

* Azure AD 租用戶
* Azure AD 高級版 （P1/P2） 許可證，用於查看登錄資料
* 全域管理員、安全管理員、安全讀取器或報表讀取器角色中的使用者。 此外，任何使用者（非管理員）都可以訪問自己的登錄。 

## <a name="access-the-usage-and-insights-report"></a>訪問使用方式和見解報告

1. 導航到[Azure 門戶](https://portal.azure.com)。
2. 選擇正確的目錄，然後選擇**Azure 活動目錄**並選擇**企業應用程式**。
3. 在 **"活動"** 部分中，選擇 **"使用方式&見解**以打開報表。 

![使用量和深入解析報告](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>使用報表

使用方式和見解報告顯示嘗試一個或多個登錄的應用程式清單，並允許您按成功登錄數、登錄失敗次數和成功率進行排序。

按一下清單底部的更多載入允許您查看頁面上的其他應用程式。 您可以選擇日期範圍以查看已在範圍內使用的所有應用程式。

您還可以將焦點設置為特定應用程式。 選擇**視圖登錄活動**以查看應用程式隨時間的變化登錄活動以及頂部錯誤。  

在應用程式使用圖中選擇一天時，您將獲得應用程式登錄活動的詳細清單。  

![使用量和深入解析報告](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>後續步驟

* [登入報告](concept-sign-ins.md)