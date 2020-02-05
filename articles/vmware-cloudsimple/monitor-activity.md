---
title: Azure VMware 解決方案（AVS）-監視 AVS 私用雲端活動
description: 說明由 AVS 環境的 Azure VMware 解決方案中的活動可用的資訊，包括警示、事件、工作和審核。
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5d6fd1b92db62ab7cc9edd47c601910b8148bb95
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019667"
---
# <a name="monitor-vmware-solutions-avs-activity"></a>監視 VMware 解決方案（AVS）活動

AVS 活動記錄可讓您深入瞭解在 AVS 入口網站上完成的作業。 此清單包含警示、事件、工作和 audit。 使用活動記錄來判斷執行的人員、時間和作業。 活動記錄不包含使用者完成的任何讀取作業。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="access-the-avs-portal"></a>存取 AVS 入口網站

存取[AVS 入口網站](access-cloudsimple-portal.md)。

## <a name="activity-information"></a>活動資訊

若要存取活動頁面，請選取側邊功能表上的 [**活動**]。

![活動頁面總覽](media/activity-page-overview.png)

若要在 [活動] 頁面上查看任何活動的詳細資料，請選取活動。 [詳細資料] 面板會在右側開啟。 面板中的動作取決於活動的類型。 按一下 [ **X** ] 以關閉面板。

按一下資料行標頭以排序顯示。 您可以篩選要查看的特定值的資料行。 按一下 [**下載為 CSV** ] 圖示，以下載活動報告。

## <a name="alerts"></a>警示

警示是您的 AVS 環境中任何重要活動的通知。 警示包括會影響帳單或使用者存取權的事件。

若要認可警示並將其從清單中移除，請從清單中選取一或多個，然後按一下 [**確認**]。

下列資訊資料行適用于警示。 按一下 [**編輯資料行**]，然後選取您想要查看的資料行。

| Column | 說明 |
------------ | ------------- |
| 警示類型 | 警示的類別。|
| 時間 | 警示發生的時間。 |
| 嚴重性 | 警示的重要性。|
| 資源名稱 | 指派給資源的名稱，例如 AVS 私人雲端名稱。 |
| 資源類型 | 資源類別： AVS 私用雲端、雲端機架。 |
| 資源識別碼 | 資源的識別碼。 |
| 說明 | 觸發警示的描述。 |
| 已認可 | 指出是否已確認警示。 |

## <a name="events"></a>活動

事件會顯示在 AVS 入口網站上的使用者和系統活動。 [事件] 頁面會列出與特定資源相關聯的活動，以及影響的嚴重性。

下列資訊資料行適用于警示。 按一下 [**編輯資料行**]，然後選取您想要查看的資料行。

| Column | 說明 |
------------ | ------------- |
| 時間 | 事件發生的日期和時間。 |
| 事件類型 | 識別事件的數位代碼。 |
| 嚴重性 | 事件嚴重性。|
| 資源名稱 | 指派給資源的名稱，例如 AVS 私人雲端名稱。 |
| 資源類型 | 資源類別： AVS 私用雲端、雲端機架。 |
| 說明 | 觸發警示的描述。 |

## <a name="tasks"></a>工作

工作是一種需要30秒以上才能完成的 AVS 私用雲端活動。 （預期少於30秒的活動只會報告為事件）。開啟 [工作] 頁面，以追蹤您的 AVS 私用雲端工作的進度。

下列資訊資料行適用于警示。 按一下 [**編輯資料行**]，然後選取您想要查看的資料行。

| Column | 說明 |
------------ | ------------- |
| 工作識別碼 | 工作的唯一識別碼。 |
| 作業 | 工作執行的動作。 |
| User | 已指派使用者來完成工作。 |
| 資源名稱 | 指派給資源的名稱。 |
| 資源類型 | 資源類別： AVS 私用雲端、雲端機架。 |
| 資源識別碼 | 資源的識別碼。 |
| 開始 | 工作的開始時間。 |
| 結束 | 工作的結束時間。 |
| 狀態 | 目前的工作狀態。 |
| 經過時間 | 完成工作所需的時間（如果已完成）或目前正在進行中（如果進行中）。 |
| 說明 | 工作描述。 |

## <a name="audit"></a>稽核

Audit 記錄會追蹤使用者活動。 您可以使用「審核記錄」來監視所有使用者的使用者活動。

下列資訊資料行適用于警示。 按一下 [**編輯資料行**]，然後選取您想要查看的資料行。

| Column | 說明 |
------------ | ------------- |
| 時間 | Audit 專案的時間。 |
| 作業 | 工作執行的動作。 |
| User | 指派給工作的使用者。 |
| 資源名稱 | 指派給資源的名稱。 |
| 資源類型 | 資源類別： AVS 私用雲端、雲端機架。 |
| 資源識別碼 | 資源的識別碼。 |
| 結果 | 活動的結果，例如 [**成功**]。 |
| 花費時間 | 完成工作的時間。 |
| 說明 | 動作的描述。 |

## <a name="next-steps"></a>後續步驟

* [使用 Azure 上的 VMware Vm](quickstart-create-vmware-virtual-machine.md)
* 深入瞭解[AVS 私人](cloudsimple-private-cloud.md)雲端
