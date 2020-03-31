---
title: 監控私有雲活動
titleSuffix: Azure VMware Solution by CloudSimple
description: 描述 Azure VMware 解決方案（按雲簡單環境）中可用的活動資訊，包括警報、事件、任務和審核。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1228f3a54e02d4fe7a5133e2bfba55c38e34718a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019667"
---
# <a name="monitor-vmware-solution-by-cloudsimple-activity"></a>按雲簡單活動監控 VMware 解決方案

CloudSimple 活動日誌提供了對雲簡單門戶上執行的操作的洞察。  該清單包括警報、事件、任務和審核。  使用活動日誌確定執行操作的人員、時間以及執行什麼操作。  活動日誌不包括使用者完成的任何讀取操作。

## <a name="sign-in-to-azure"></a>登入 Azure

登錄到 中的[https://portal.azure.com](https://portal.azure.com)Azure 門戶。

## <a name="access-the-cloudsimple-portal"></a>存取 CloudSimple 入口網站

訪問[雲簡單門戶](access-cloudsimple-portal.md)。

## <a name="activity-information"></a>活動資訊

要訪問"活動"頁面，請在側功能表上選擇 **"活動**"。

![活動頁面概述](media/activity-page-overview.png)

要查看活動頁面上任何活動的詳細資訊，請選擇該活動。 右側將打開詳細資訊面板。 面板中的操作取決於活動類型。 按一下**X**關閉面板。

按一下列標題對顯示進行排序。  您可以篩選要查看的特定值的列。  按一下"**下載為 CSV"** 圖示下載活動報告。

## <a name="alerts"></a>警示

警報是雲簡單環境中任何重要活動的通知。  警報包括影響計費或使用者訪問的事件。

要確認警報並將其從清單中刪除，請從清單中選擇一個或多個警報，然後按一下"**確認**"。

以下資訊列可用於警報。 按一下 **"編輯"列**並選擇要查看的列。

| 資料行 | 描述 |
------------ | ------------- |
| 警示類型 | 警報類別。|
| Time | 發生警報的時間。 |
| Severity | 警報的重要性。|
| 資源名稱 | 分配給資源的名稱，如私有雲名稱。 |
| 資源類型 | 資源類別：私有雲、雲機架。 |
| 資源識別碼 | 資源的識別碼。 |
| 描述 | 警報觸發的原因的說明。 |
| 已認可 | 指示是否確認警報。 |

## <a name="events"></a>事件

事件在雲簡單門戶上顯示使用者和系統活動。 "事件"頁列出了與特定資源關聯的活動以及影響的嚴重性。

以下資訊列可用於警報。 按一下 **"編輯"列**並選擇要查看的列。

| 資料行 | 描述 |
------------ | ------------- |
| Time | 事件發生的日期和時間。 |
| 事件類型 | 標識事件的數位代碼。 |
| Severity | 事件嚴重性。|
| 資源名稱 | 分配給資源的名稱，如私有雲名稱。 |
| 資源類型 | 資源類別：私有雲、雲機架。 |
| 描述 | 警報觸發的原因的說明。 |

## <a name="tasks"></a>工作

任務是私有雲活動，預計需要 30 秒或更長時間才能完成。 （預期需要少於 30 秒的活動僅報告為事件。打開"任務"頁面，跟蹤私有雲的任務進度。

以下資訊列可用於警報。 按一下 **"編輯"列**並選擇要查看的列。

| 資料行 | 描述 |
------------ | ------------- |
| 工作識別碼 | 任務的唯一識別碼。 |
| 作業 | 任務執行的操作。 |
| User | 分配給完成任務的使用者。 |
| 資源名稱 | 分配給資源的名稱。 |
| 資源類型 | 資源類別：私有雲、雲機架。 |
| 資源識別碼 | 資源的識別碼。 |
| Start | 任務的開始時間。 |
| 結束 | 任務的結束時間。 |
| 狀態 | 當前任務狀態。 |
| 已使用的時間 | 任務完成（如果已完成）或當前正在執行的時間（如果正在進行）。 |
| 描述 | 任務說明。 |

## <a name="audit"></a>稽核

稽核記錄跟蹤使用者活動。 您可以使用稽核記錄監視所有使用者的使用者活動。

以下資訊列可用於警報。 按一下 **"編輯"列**並選擇要查看的列。

| 資料行 | 描述 |
------------ | ------------- |
| Time | 審核條目的時間。 |
| 作業 | 任務執行的操作。 |
| User | 分配給任務的使用者。 |
| 資源名稱 | 分配給資源的名稱。 |
| 資源類型 | 資源類別：私有雲、雲機架。 |
| 資源識別碼 | 資源的識別碼。 |
| 結果 | 活動的結果，如**成功**。 |
| 所走的時間 | 完成任務的時間。 |
| 描述 | 動作的描述。 |

## <a name="next-steps"></a>後續步驟

* [在 Azure 上取用 VMware VM](quickstart-create-vmware-virtual-machine.md)
* 瞭解有關[私有雲](cloudsimple-private-cloud.md)的更多
