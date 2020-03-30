---
title: StorSimple Snapshot Manager 備份原則 | Microsoft Docs
description: 描述如何使用 StorSimple Snapshot Manager MMC 嵌入式管理單元，來建立和管理控制已排定之備份的備份原則。
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 04415d0b-42f0-4737-8afa-257fb2dbe5d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 7b2df6c8384e78094d80a17d8982b0db9edf73e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933367"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>使用 StorSimple Snapshot Manager 來建立和管理備份原則
## <a name="overview"></a>總覽
備份原則會建立一個在本機或雲端中備份磁碟區資料的排程。 建立備份原則時，您也可以指定保留原則。 （您最多可以保留 64 個快照。有關備份策略的詳細資訊，請參閱[StorSimple 8000 系列的備份類型：混合雲解決方案](storsimple-overview.md)。 [Backup types](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies)

本教學課程說明如何：

* 建立備份原則 
* 編輯備份原則
* 刪除備份原則

## <a name="create-a-backup-policy"></a>建立備份原則 
請使用下列程序來建立新的備份原則。

#### <a name="to-create-a-backup-policy"></a>若要建立備份原則
1. 按一下桌面圖示，以啟動 StorSimple Snapshot Manager。
2. 在 [領域]**** 窗格中，於 [備份原則]**** 上按一下滑鼠右鍵，然後按一下 [建立備份原則]****。

    ![建立備份原則 ](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    [建立原則]**** 對話方塊隨即出現。

    ![建立原則 - 一般索引標籤](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. 在 [一般]**** 索引標籤上，完成下列資訊：

   1. 在 [名稱]**** 文字方塊中，輸入原則的名稱。
   2. 在 [磁碟區群組]**** 文字方塊中，輸入與原則相關聯的磁碟區群組名稱。
   3. 選取 [本機快照]**** 或 [雲端快照]****。
   4. 選取要保留的快照數目。 如果選取 [全部]****，將保留 64 個快照 (上限)。
4. 按一下 **[排程]** 索引標籤。

    ![建立原則 - 排程索引標籤](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. 在 [排程]**** 索引標籤上，完成下列資訊：

   1. 按一下 [啟用]**** 核取方塊，以排定下次備份。
   2. 在 [設定]**** 下，選取 [一次]****、[每日]****、[每週]**** 或 [每月]****。
   3. 在 [開始]**** 文字方塊中，按一下行事曆圖示，然後選取開始日期。
   4. 在 [進階設定]**** 下，您可以設定選用的重複排程及結束日期。
   5. 按一下 [確定]****。

在您建立備份原則之後，下列資訊會出現在 [結果]**** 窗格中：

* [名稱]**** – 備份原則的名稱。
* [類型]**** – 本機快照或雲端快照。
* [磁碟區群組]**** – 與原則相關聯的磁碟區群組。
* [保留]**** – 保留的快照數目；上限為 64。
* [已建立]**** – 這個原則的建立日期。
* [已啟用]**** – 原則目前是否生效：**True** 表示生效；**False** 表示未生效。

## <a name="edit-a-backup-policy"></a>編輯備份原則
請使用下列程序來編輯現有的備份原則。

#### <a name="to-edit-a-backup-policy"></a>若要編輯備份原則
1. 按一下桌面圖示，以啟動 StorSimple Snapshot Manager。
2. 在 [領域]**** 窗格中，按一下 [備份原則]**** 節點。 所有備份原則都會出現在 [結果]**** 窗格中。
3. 在您要編輯的原則上按一下滑鼠右鍵，然後按一下 [編輯]****。

    ![編輯備份原則](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. 當 [建立原則]**** 視窗出現時，請輸入您的變更，然後按一下 [確定]****。

## <a name="delete-a-backup-policy"></a>刪除備份原則
請使用下列程序來刪除備份原則。

#### <a name="to-delete-a-backup-policy"></a>若要刪除備份原則
1. 按一下桌面圖示，以啟動 StorSimple Snapshot Manager。
2. 在 [領域]**** 窗格中，按一下 [備份原則]**** 節點。 所有備份原則都會出現在 [結果]**** 窗格中。
3. 在您要刪除的備份原則上按一下滑鼠右鍵，然後按一下 [刪除]****。
4. 當確認訊息出現時，請按一下 [是]****。

    ![確認刪除備份原則](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>後續步驟
* 了解如何 [使用 StorSimple Snapshot Manager 來管理您的 StorSimple 解決方案](storsimple-snapshot-manager-admin.md)。
* 了解如何 [使用 StorSimple Snapshot Manager 來檢視和管理備份工作](storsimple-snapshot-manager-manage-backup-jobs.md)。
