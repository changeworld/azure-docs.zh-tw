---
title: 快速入門：使用 Azure 入口網站下載登入報告 | Microsoft Docs
description: 了解如何使用 Azure 入口網站下載登入報告
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 9131f208-1f90-4cc1-9c29-085cacd69317
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86bc72f69903134afa3750ad6b72486a713b6cc0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011909"
---
# <a name="quickstart-download-a-sign-in-report-using-the-azure-portal"></a>快速入門：使用 Azure 入口網站下載登入報告

在此快速入門中，您將學到如何下載您租用戶過去 24 小時內的登入資料。 您可以從 Azure 入口網站最多下載 250,000 筆記錄。 這些記錄會依「最近期」方式排序，因此根據預設，您會取得最近的 250,000 筆記錄。 

## <a name="prerequisites"></a>Prerequisites

您需要：

* 一個 Azure Active Directory 租用戶，而且您必須具有進階版授權以顯示登入活動報告。 請參閱[開始使用 Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) 來升級 Azure Active Directory 版本。 請注意，如果您在升級前沒有任何活動資料，則在升級至進階授權之後，報告需要幾天的時間才會顯示出資料。
* 所擔任的租用戶角色為 **安全性系統管理員**、**安全性讀取者**、**報告讀取者** 或 **全域系統管理員** 的使用者。 此外，租用戶中的任何使用者都可以存取自己的登入資訊。

## <a name="quickstart-download-a-sign-in-report"></a>快速入門：下載登入報告

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。
2. 從左側瀏覽窗格選取 [Azure Active Directory]  並使用 [切換目錄]  按鈕來選取您的 Active Directory。
3. 從儀表板選取 [Azure Active Directory]  ，然後選取 [登入]  。 
4. 在 [日期] 篩選下拉式清單中選擇 [過去 24 小時]，然後選取 [套用] 以檢視過去 24 小時的登入。 
5. 選取 [下載]  按鈕、選取 **CSV** 作為檔案格式，並指定檔案名稱，以下載包含篩選記錄的 CSV 檔案。 

![報告](./media/quickstart-download-sign-in-report/download-sign-ins.png)

## <a name="next-steps"></a>後續步驟

* [Azure Active Directory 入口網站中的登入活動報告](concept-sign-ins.md)
* [Azure Active Directory 報告保留期](reference-reports-data-retention.md)
* [Azure Active Directory 報告延遲](reference-reports-latencies.md)
