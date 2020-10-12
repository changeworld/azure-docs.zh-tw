---
title: 管理和檢視 StorSimple 8000 系列的作業 | Microsoft Docs
description: 說明 StorSimple 裝置管理員服務作業刀鋒視窗，以及如何使用該視窗來追蹤最近、當前和排程的備份作業。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: dc23a0a68471b82c990d5d8a3086e2e71e88d0c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85513932"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>使用 StorSimple 裝置管理員服務來檢視和管理作業 (Update 3 和更新版本)

## <a name="overview"></a>概觀
[作業]**** 刀鋒視窗提供單一中央入口網站，可針對連接到 StorSimple 裝置管理員服務的裝置，檢視及管理在裝置上啟動的作業。 您可以針對多個裝置，檢視已排程、執行中、完成、已取消和失敗的工作。 結果會以表格式格式呈現。

![[作業] 刀鋒視窗](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

您可以透過篩選欄位，快速找到您所感興趣的工作，例如：

* **狀態** – 作業可能在進行中、成功、已取消、失敗、取消中，或是成功但發生錯誤。
* **時間範圍** – 您可以根據日期和時間範圍來篩選作業。 範圍可以是過去 1 小時、過去 24 小時、過去 7 天、過去 30 天、過去一年，或自訂日期。
* **類型** – 作業類型可以是排程備份、手動備份、還原備份、複製磁碟區、容錯移轉磁碟區容器、建立固定在本機的磁碟區、修改磁碟區、安裝更新、收集支援記錄，以及建立雲端設備。
* **裝置** – 工作會在連接到服務的特定裝置上進行初始化。
  
篩選的工作接著會根據下列屬性製成表格：
  
* **名稱** – 排程備份、手動備份、還原備份、複製磁碟區、容錯移轉磁碟區容器、建立固定在本機的磁碟區、修改磁碟區、安裝更新、收集支援記錄，以及建立雲端設備。
* **狀態** – 執行中、完成、已取消、失敗、取消中，或是完成但發生錯誤。
* **實體** – 工作可以與磁碟區、備份原則或裝置相關聯。 例如，複製工作與磁碟區相關聯，而排程的備份工作則與備份原則相關聯。 裝置工作是透過災害復原 (DR) 或還原作業而建立。
* **裝置** – 用來啟動工作之裝置的名稱。
* **開始** 時間–開始作業的時間。
* **期間** – 完成作業所需要的時間。

工作清單每隔 30 秒會重新整理。

您可以在此頁面上執行下列工作相關的動作：

* 檢視作業詳細資料
* 取消工作

## <a name="view-job-details"></a>檢視作業詳細資料
執行下列步驟來檢視任何工作的詳細資料。

#### <a name="to-view-job-details"></a>若要檢視工作詳細資料
1. 移至 StorSimple 裝置管理員服務，然後按一下 [作業]****。

2. 在 [作業]**** 刀鋒視窗中，利用適當的篩選條件執行查詢，以顯示您感興趣的作業。 您可以搜尋完成、執行中或已取消工作。

    ![[作業] 刀鋒視窗](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. 選取並按一下 [作業]。

    ![[作業] 刀鋒視窗](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. 在 [作業詳細資料] 刀鋒視窗中，您可以檢視狀態、詳細資料、時間統計資料和資料統計資料。
   
    ![工作詳細資料](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>取消工作
執行下列步驟來取消執行中工作。

> [!NOTE]
> 有些工作無法取消，例如修改磁碟區以變更磁碟區類型或是展開磁碟區。


### <a name="to-cancel-a-job"></a>取消工作
1. 在 [工作]**** 頁面上，使用適當的篩選器執行查詢，以顯示您要取消的執行中工作。 選取工作。

2. 用滑鼠右鍵按一下選取的作業以叫用操作功能表，然後按一下 [取消]****。

    ![工作詳細資料](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. 當提示確認時，請按一下 [是]****。 即會取消此工作。

## <a name="next-steps"></a>接下來的步驟
* 了解如何 [管理您的 StorSimple 備份原則](storsimple-8000-manage-backup-policies-u2.md)。
* 了解如何[使用 StorSimple 裝置管理員服務管理 StorSimple 裝置](storsimple-8000-manager-service-administration.md)。

