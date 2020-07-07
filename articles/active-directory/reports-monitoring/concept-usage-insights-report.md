---
title: 使用方式和深入解析報表 |Microsoft Docs
description: Azure Active Directory 入口網站中的使用方式和深入解析報表簡介
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74008271"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Azure Active Directory 入口網站中的使用量和見解報告

使用 [使用量和深入解析] 報告，您可以取得以應用程式為中心的登入資料檢視。 您可以找到下列問題的答案：

*   我的組織中最常使用的應用程式為何？
*   哪些應用程式的登入失敗？ 
*   每個應用程式的最上層登入錯誤為何？

## <a name="prerequisites"></a>必要條件 

若要從 usage 和 insights 報表存取資料，您需要：

* Azure AD 租用戶
* 用來查看登入資料的 Azure AD premium （P1/P2）授權
* 全域管理員、安全性系統管理員、安全性讀取者或報告讀取者角色中的使用者。 此外，任何使用者（非系統管理員）都可以存取自己的登入。 

## <a name="access-the-usage-and-insights-report"></a>存取使用量和見解報告

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。
2. 選取正確的目錄，然後選取 [ **Azure Active Directory** ]，然後選擇 [**企業應用程式**]。
3. 從 [**活動**] 區段中，選取 [**使用量 & 深入**解析] 以開啟報表。 

![使用量和深入解析報告](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>使用報表

[使用量和深入解析] 報告會顯示具有一或多個登入嘗試的應用程式清單，並可讓您依成功登入的次數、失敗的登入和成功率進行排序。

按一下清單底部的 [載入更多]，可讓您在頁面上查看其他應用程式。 您可以選取日期範圍，以查看範圍內使用的所有應用程式。

您也可以將焦點設定在特定的應用程式上。 選取 [ **view 登入活動**]，以查看一段時間內應用程式的登入活動，以及最常見的錯誤。  

當您選取 [應用程式使用量] 圖表中的日期時，您會取得應用程式的登入活動詳細清單。  

![使用量和深入解析報告](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>後續步驟

* [登入報告](concept-sign-ins.md)