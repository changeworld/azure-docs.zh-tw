---
title: StorSimple Snapshot Manager 使用者介面 | Microsoft Docs
description: 描述 StorSimple Snapshot Manager 使用者介面，並說明如何使用其來管理備份作業和備份目錄。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: c7d91892-2881-41a2-a7a2-908dc3646493
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: 35fa0fc134045585eebd21324352f9b4ff272e87
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954234"
---
# <a name="use-storsimple-snapshot-manager-user-interface-to-manage-backup-jobs-and-backup-catalog"></a>使用 StorSimple Snapshot Manager 使用者介面來管理備份作業與備份類別目錄

## <a name="overview"></a>概觀
StorSimple Snapshot Manager 有直覺式使用者介面，可讓您用來取得並管理備份。 本教學課程提供使用者介面的簡介，並接著說明如何使用每個元件。 如需 StorSimple Snapshot Manager 的詳細說明，請參閱 [何謂 StorSimple Snapshot Manager？](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>主控台說明
若要檢視使用者介面，請按一下桌面上的 StorSimple Snapshot Manager 圖示。 主控台視窗隨即出現，如下圖所示。

![StorSimple Snapshot Manager 密碼](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

主控台視窗有五個主要元素。 按一下適當的連結，以取得每個元素的完整說明。

* [功能表列](#menu-bar) 
* [工具列](#tool-bar) 
* [範圍窗格](#scope-pane) 
* [結果窗格](#results-pane) 
* [動作窗格](#actions-pane) 

此外，StorSimple Snapshot Manager 也支援 [鍵盤導覽和數個快速鍵](#keyboard-navigation-and-shortcuts)。

### <a name="console-accessibility"></a>主控台協助工具
StorSimple Snapshot Manager 使用者介面支援 Windows 作業系統和 Microsoft Management Console (MMC) 所提供的協助工具功能，以及某些 StorSimple Snapshot Manager 專用的鍵盤快速鍵。 

* 如需 Windows 協助工具功能的說明，請移至《 [Windows 的鍵盤快速鍵](https://support.microsoft.com/kb/126449)》。 
* 如需 MMC 協助工具功能的說明，請移至 《 [MMC 3.0 的協助工具](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc766075(v=ws.11))
* 如需 StorSimple Snapshot Manager 協助工具功能的說明，請移至《 [鍵盤瀏覽和快速鍵](#keyboard-navigation-and-shortcuts)》。

## <a name="menu-bar"></a>功能表列
主控台視窗頂端的功能表列包含 [[檔案]](#file-menu)、[[動作]](#action-menu)、[[檢視]](#view-menu)、[[我的最愛]](#favorites-menu)、[[視窗]](#window-menu) 和 [[說明]](#help-menu) 功能表。

按一下功能表列上的任何項目，即可查看可用的命令清單。 下列範例顯示在功能表列上選取的 [ **檢視** ] 功能表。

![已選取 [檢視] 功能表](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>[檔案] 功能表
**[檔案]** 功能表包含標準的 Microsoft Management Console (MMC) 命令。

#### <a name="menu-access"></a>功能表存取
若要檢視 **[檔案]** 功能表，請按一下功能表列的 **[檔案]**。 下列功能表隨即出現。

![StorSimple Snapshot Manager 檔案功能表](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>功能表說明
下表說明 [檔案] 功能表上出現的項目。

| 功能表項目 | 描述 |
|:--- |:--- |
| 新增 |按一下 [ **新增** ]，可根據 StorSimple Snapshot Manager 建立新的主控台。 |
| 開啟 |按一下 **[開啟舊檔]** 可開啟現有的主控台。 |
| 儲存 |按一下 **[儲存]** 可儲存目前的主控台。 |
| 另存新檔 |按一下 **[另存新檔]** 可以從目前主控台建立新的、重新命名的執行個體。 使用 [ **另存新檔** ] 選項，可自訂檢視並加以儲存，以供稍後擷取。 例如，您可以建立 StorSimple Snapshot Manager 嵌入式管理單元，指向特定伺服器。 |
| 新增/移除嵌入式管理單元 |按一下 **[新增/移除嵌入式管理單元]** 可新增或移除嵌入式管理單元，以及組織 **[領域]** 窗格中的節點。 如需詳細資訊，請移至《 [在 MMC 3.0 中新增、移除及組織嵌入式管理單元和延伸](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc722035(v=ws.11))》。 |
| 選項 |按一下 **[選項]** 可變更主控台圖示、指定使用者存取模式和權限，或刪除主控台檔案以增加可用的磁碟空間。 |
| 檔案路徑的清單 |按一下編號清單中的路徑，可重新開啟您最近開啟的檔案。 |
| 結束 |按一下 **[結束]** 可關閉 **[檔案]** 功能表。 |

### <a name="action-menu"></a>動作功能表
使用 **[動作]** 功能表，從可用的動作中選取。 可供您使用的項目視您在 **[領域]** 窗格或 **[結果]** 窗格中的選擇而定。

#### <a name="menu-access"></a>功能表存取
若要檢視 **[動作]** 功能表，請執行下列其中一項：

* 在 **[領域]** 窗格或 **[結果]** 窗格中的項目上按一下滑鼠右鍵。
* 在 **[領域]** 窗格或 **[結果]** 窗格中選取項目，然後按一下功能表列上的 **[動作]**。 

例如，如果您在 **[領域]** 窗格中選取頂端節點，然後按一下滑鼠右鍵或按一下功能表列中的 **[動作]**，則會出現下列功能表。

![StorSimple Snapshot Manager 動作功能表](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

**[動作]** 窗格 (在主控台右邊) 包含與 **[動作]** 功能表相同的動作清單。 此外，**[動作]** 窗格還包含 **[檢視]** 功能表選項，可讓您建立 **[結果]** 窗格的自訂檢視。

![已開啟 [檢視] 功能表的 [動作] 窗格](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>功能表說明
下表包含 StorSimple Snapshot Manager 動作的清單，按字母順序排列。 

* **[動作]** 欄位列出您可以在節點和結果上執行的動作。 
* **[瀏覽]** 欄位說明如何顯示適當的 **[動作]** 功能表，讓您可以選取動作。 有些動作會出現在多個 **[動作]** 功能表中。 對於這些動作，請從分項清單中選取一個 **[瀏覽]** 選項。 
* [說明] 欄說明如何使用 [動作] 功能表或 [動作] 窗格上的每個動作，並解釋其用途。

> [!NOTE]
> **[動作]** 窗格和 **[動作]** 功能表包含其他選項，例如 **[檢視]**、**[從這裡開啟新視窗]**、**[重新整理]**、**[匯出清單]** 及 **[說明]**。 這些選項可當做 MMC 一部分來使用，並不是 StorSimple Snapshot Manager 專用的。 下表包含這些選項的說明。
> 
> 

| 動作 | 導覽 | 描述 |
|:--- |:--- |:--- |
| Authenticate |按一下 [裝置] 節點，並以滑鼠右鍵按一下 [結果] 窗格中的裝置。 |按一下 [ **驗證** ]，以輸入您為裝置設定的密碼。 |
| 複製 |展開 **[備份類別目錄]**、展開 **[雲端快照]**，按一下有註明日期的備份，然後在 **[結果]** 窗格中選取磁碟區。 |按一下 **[複製]**，建立雲端快取的複本並儲存在您指定的位置中。 |
| 設定裝置 |在 **[裝置]** 節點上按一下滑鼠右鍵。 |按一下 [ **設定裝置** ]，以設定單一裝置或多個裝置來連接至 Windows 主機。 |
| 建立備份原則 |請執行下列其中一項：<ul><li>在 **[備份原則]** 上按一下滑鼠右鍵。</li><li>按一下或展開 **[磁碟區群組]**，然後在磁碟區群組上按一下滑鼠右鍵。</li><li>按一下或展開 **[備份類別目錄]**，然後在磁碟區群組上按一下滑鼠右鍵。</li></ul> |按一下 **[建立備份原則]**，設定磁碟區群組的排程備份。 |
| 建立磁碟區群組 |請執行下列其中一項：<ul><li>按一下 **[磁碟區]** 節點，然後在 **[結果]** 窗格中磁碟區上按一下滑鼠右鍵。</li><li>在 **[磁碟區群組]** 節點上按一下滑鼠右鍵。</li></ul> |按一下 **[建立磁碟區群組]**，指派磁碟區給磁碟區群組。 |
| 刪除 |按一下節點或結果 (此項目出現在許多 [動作] 功能表和 [動作] 窗格上。) |按一下 **[刪除]**，刪除您選取的節點或結果。 確認對話方塊出現時，請確認或取消刪除。 |
| 詳細資料 |按一下 [裝置] 節點，然後以滑鼠右鍵按一下 [結果] 窗格中的裝置。 |按一下 [ **詳細資料** ]，以查看裝置的組態詳細資料。 |
| 編輯 |按一下 **[備份原則]**，然後在 **[結果]** 窗格中的原則上按一下滑鼠右鍵。 |按一下 **[編輯]**，變更磁碟區群組的備份排程。 |
| 匯出清單 |按一下任何節點或結果 (此項目出現在所有 **[動作]** 功能表和 **[動作]** 窗格中)。 |按一下 **[匯出清單]**，將清單儲存為逗號分隔值 (CSV) 檔案。 然後，您可以將這個檔案匯入試算表應用程式進行分析。 |
| 説明 |按一下任何節點或結果。 (此項目出現在所有 **[動作]** 功能表和 **[動作]** 窗格上)。 |按一下 **[說明]**，在另一個瀏覽器視窗中開啟線上說明。 |
| 從這裡開啟新視窗 |按一下任何節點或結果 (此項目出現在所有 **[動作]** 功能表和 **[動作]** 窗格中)。 |按一下 [ **從這裡開啟新視窗** ]，以開啟新的 StorSimple Snapshot Manager 視窗。 |
| 重新整理 |按一下任何節點或結果 (此項目出現在所有 **[動作]** 功能表和 **[動作]** 窗格中)。 |按一下 [ **重新整理** ]，以更新目前顯示的 StorSimple Snapshot Manager 視窗。 |
| 重新整理裝置 |按一下 [裝置] 節點，並以滑鼠右鍵按一下 [結果] 窗格中的裝置。 |按一下 [ **重新整理裝置** ]，以利用 StorSimple Snapshot Manager 同步處理特定連接的裝置。 |
| 重新整理裝置 |在 **[裝置]** 節點上按一下滑鼠右鍵。 |按一下 [ **重新整理裝置** ]，以利用 StorSimple Snapshot Manager 同步處理連接的裝置清單。 |
| 重新掃描磁碟區 |在 **[磁碟區]** 節點上按一下滑鼠右鍵。 |按一下 **[重新掃描磁碟區]**，更新 **[結果]** 窗格中出現的磁碟區清單。 |
| 還原 |依序展開 **[備份類別目錄]**、磁碟區群組、**[本機快照]** 或 **[雲端快照]**，然後在備份上按一下滑鼠右鍵。 |按一下 **[還原]**，以所選備份中的資料取代目前磁碟區群組資料。 |
| 進行備份 |請執行下列其中一項：<ul><li>展開 **[磁碟區群組]**，然後在磁碟區群組上按一下滑鼠右鍵。</li><li>展開 **[備份類別目錄]**，然後在磁碟區群組上按一下滑鼠右鍵。</li></ul> |按一下 **[取得備份]**，立即開始備份工作。 |
| 切換匯入顯示 |以滑鼠右鍵按一下 [範圍] 窗格中的最上層節點 (範例中的 [StorSimple Snapshot Manager] 節點)。 |按一下 [切換匯入顯示]，以顯示或隱藏磁碟區群組，以及從 StorSimple 裝置管理員服務儀表板匯入的相關聯備份。 |

### <a name="view-menu"></a>檢視功能表
使用 [檢視] 功能表，來建立 [結果] 窗格內容的自訂檢視。 [檢視] 功能表包含 [新增/移除欄位] 及 [自訂] 選項。

#### <a name="menu-access"></a>功能表存取
您可以在功能表列上或在 [動作] 窗格中存取 [檢視] 功能表。

![StorSimple Snapshot Manager 檢視功能表](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>功能表說明
下表說明 [檢視] 功能表上出現的項目。

| 功能表項目 | 描述 |
|:--- |:--- |
| 新增/移除資料行 |按一下 [新增/移除欄位]，以在 [結果] 窗格中新增或移除欄位。 |
| 自訂 |按一下 [ **自訂** ]，可顯示或隱藏 StorSimple Snapshot Manager 主控台視窗中的項目。 |

### <a name="favorites-menu"></a>我的最愛功能表
使用 **[我的最愛]** 功能表來新增、移除和組織您常用的頁面檢視和工作。 

#### <a name="menu-access"></a>功能表存取
您可以在功能表列存取 **[我的最愛]** 功能表。

![StorSimple Snapshot Manager 我的最愛功能表](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>功能表說明
下表說明 [我的最愛] 功能表上出現的項目。

| 功能表項目 | 描述 |
|:--- |:--- |
| 加到我的最愛 |按一下 **[加到我的最愛]** 可將目前檢視加入至我的最愛清單中。 |
| 組織我的最愛 |按一下 [ **組織我的最愛** ]，可組織 [我的最愛] 資料夾的內容。 |

### <a name="window-menu"></a>視窗功能表
使用 [ **視窗** ] 功能表，可新增和重新排列 StorSimple Snapshot Manager 主控台視窗。

#### <a name="menu-access"></a>功能表存取
您可以在功能表列存取 **[視窗]** 功能表。

![StorSimple Snapshot Manager 視窗功能表](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

功能表底部的編號清單會顯示目前開啟的視窗。 按一下該清單中的任何視窗，即可將視窗帶至前景。 

#### <a name="menu-description"></a>功能表說明
下表描述 [視窗] 功能表上出現的項目。

| 功能表項目 | 描述 |
|:--- |:--- |
| 開新視窗 |按一下 **[開新視窗]** 可開啟新的主控台視窗 (除了現有的視窗)。 |
| Cascade |按一下 **[重疊顯示]** 可依重疊樣式來顯示已開啟的主控台視窗。 |
| 水平並排顯示 |按一下 **[水平並排]** 可依磚塊 (或方格) 格式顯示已開啟的主控台視窗。 |
| 排列圖示 |如果您已開啟多個主控台視窗，且散落於整個桌面上，請將它們縮到最小，然後按一下 **[排列圖示]**，在畫面底部將它們排列成水平列。 |

### <a name="help-menu"></a>說明功能表
使用 [ **說明** ] 功能表，可檢視 StorSimple Snapshot Manager 和 MMC 的可用線上說明。 您也可以檢視系統上目前安裝之 MMC 和 StorSimple Snapshot Manager 軟體版本的相關資訊。 

您可以在功能表列存取 **[說明]** 功能表。 您也可以從 [ **動作** ] 窗格存取 StorSimple Snapshot Manager 說明主題。

![StorSimple Snapshot Manager 說明功能表](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>功能表說明
下表說明描述功能表上出現的項目。

| 功能表項目 | 描述 |
|:--- |:--- |
| StorSimple Snapshot Manager 的相關說明 |按一下 [ **StorSimple Snapshot Manager 的相關說明** ]，可在個別視窗中開啟 StorSimple Snapshot Manager 說明。 |
| 說明主題 |按一下 **[說明主題]** 可在另一個視窗中開啟 MMC 線上說明。 |
| TechCenter 網站 |按一下 **[TechCenter 網站]** 可在另一個視窗中開啟 Microsoft TechNet 技術中心首頁。 |
| 關於 Microsoft Management Console |按一下 **[關於 Microsoft Management Console]** 可查看系統上安裝的 Microsoft Management Console 版本。 |
| 關於 StorSimple Snapshot Manager |按一下 [關於 StorSimple Snapshot Manager]  ，可查看系統上安裝的嵌入式管理單元版本。 |

## <a name="tool-bar"></a>工具列
位於功能表列下方的工具列包含導覽工作圖示。 每個圖示都是特定工作的捷徑。

### <a name="icon-descriptions"></a>圖示說明
下表描述工具列上出現的圖示。 

| 圖示 | 描述 |
|:--- |:--- |
| ![向左箭號](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) |按一下向左箭號圖示，可返回上一頁。 |
| ![向右箭號](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) |按一下向右箭號，可移至下一頁 (如果箭號呈現灰色，則動作無法使用)。 |
| ![向上圖示](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) |按一下向上圖示，可在主控台樹狀目錄 ([ **範圍** ] 窗格) 中往上一層。 |
| ![顯示/隱藏主控台樹狀目錄](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) |按一下顯示/隱藏樹狀目錄圖示，可顯示或隱藏 [ **範圍** ] 窗格。 |
| ![匯出清單](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) |按一下匯出清單圖示，可將清單匯出至您指定的 CSV 檔案。 |
| ![説明圖示](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |按一下說明圖示，可開啟線上 MMC 說明主題。 |
| ![顯示/隱藏動作窗格](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) |按一下顯示/隱藏 [動作] 窗格圖示，可顯示或隱藏 [動作] 窗格。 |

## <a name="scope-pane"></a>範圍窗格
[範圍]  窗格是 StorSimple Snapshot Manager UI 中最左邊的窗格。 其包含主控台 (或節點) 樹狀目錄，而且是 StorSimple Snapshot Manager 的主要導覽機制。 

### <a name="scope-pane-structure"></a>範圍窗格結構
[領域] 窗格包含以樹狀結構組織的一系列可點選物件 (節點)。 

![範圍窗格](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

* 若要展開或摺疊節點，請按一下節點名稱旁的箭號圖示。
* 若要檢視節點的狀態或內容，請按一下節點名稱。 資訊會出現在 **[結果]** 窗格中。 

[ **範圍** ] 窗格包含下列節點： 

* [裝置節點](#devices-node) 
* [磁碟區節點](#volumes-node) 
* [磁片區群組節點](#volume-groups-node) 
* [備份原則節點](#backup-policies-node) 
* [備份目錄節點](#backup-catalog-node) 
* [作業節點](#jobs-node) 

### <a name="scope-pane-tasks"></a>範圍窗格工作
您可以使用 [領域] 窗格對特定節點完成動作。 若要選取一項工作，請執行下列其中一項：

* 以滑鼠右鍵按一下節點，然後從出現的功能表中選取工作。
* 按一下該節點，然後按一下功能表列上的 [動作]。 從出現的功能表中選取工作。
* 按一下該節點，然後在 [動作] 窗格中選取動作。

當選取節點，並使用其中任何一種方法來查看工作清單時，只會顯示那些可在該節點上執行的動作。

### <a name="devices-node"></a>裝置節點
[ **裝置** ] 節點代表已連接至 StorSimple Snapshot Manager 的 StorSimple 裝置和 StorSimple 虛擬裝置。 選取此節點來連接並設定裝置，然後匯入其相關聯的磁碟區、磁碟區群組，以及現有的備份複本。 多個裝置可以連接至單一主機。

* 若要展開節點，請按一下 [裝置] 旁邊的箭頭圖示。
* 若要查看可用動作的功能表，請在 [裝置] 節點上按一下滑鼠右鍵，或在展開之檢視中出現的任一節點上按一下滑鼠右鍵。
* 若要查看已設定的裝置清單，請按一下 [範圍] 窗格中的 [裝置]。 裝置清單連同每個裝置的相關資訊清單會出現在 [ **結果** ] 窗格中。

### <a name="volumes-node"></a>磁碟區節點
[磁碟區] 節點代表對應至主機所掛接之磁碟區的磁碟機，包括透過 iSCSI 探索到的磁碟機，以及透過裝置探索到的磁碟機。 請使用這個節點，來檢視可用的磁碟區清單，並將個別磁碟區指派給磁碟區群組。

* 若要展開節點，請按一下 [磁碟區] 旁邊的箭頭圖示。
* 若要查看可用動作的功能表，請在 [磁碟區] 節點上按一下滑鼠右鍵，或在展開之檢視中出現的任一節點上按一下滑鼠右鍵。
* 若要查看磁碟區的清單，請按一下 [領域] 窗格中的 [磁碟區]。 磁碟區清單會連同每個磁碟區的相關資訊出現在 [結果] 窗格中。

### <a name="volume-groups-node"></a>磁碟區群組節點
磁碟區群組也稱為一致性群組。 每個磁碟區群組是應用程式相關磁碟區的集區，可協助確保備份作業期間應用程式的一致性。 使用 [磁碟區群組] 節點可設定這些群組並進行互動式備份或建立備份排程。 

* 若要展開節點，請按一下 [磁碟區群組] 旁邊的箭頭圖示。
* 若要查看可用動作的功能表，請在 [磁碟區群組] 節點上按一下滑鼠右鍵，或在展開之檢視中出現的任一節點上按一下滑鼠右鍵。
* 若要查看磁碟區群組的清單，請按一下 [領域] 窗格中的 [磁碟區群組]。 磁碟區群組清單會連同每個磁碟區群組的相關資訊出現在 [結果] 窗格中。

### <a name="backup-policies-node"></a>備份原則節點
備份原則是本機和雲端快照的作業排程。 使用 [備份原則] 節點可指定備份的建立頻率及保留期限。 

* 若要展開節點，請按一下 [備份原則] 旁邊的箭頭圖示。
* 若要查看可用動作的功能表，請在 [備份原則] 節點上按一下滑鼠右鍵，或在展開之檢視中出現的任一節點上按一下滑鼠右鍵。
* 若要查看備份原則的清單，請按一下 [領域] 窗格中的 [備份原則]。 備份原則清單會連同每個備份原則的相關資訊出現在 [結果] 窗格中。

> [!NOTE]
> 您最多可以保留 64 個備份。


### <a name="backup-catalog-node"></a>備份目錄節點
[ **備份目錄** ] 節點包含 Azure StorSimple 磁碟區的現場和異地備份清單。 此節點是依磁碟區群組組織，而且每個磁碟區群組容器包含本機快照 ([本機快照] 節點) 和雲端快照 ([雲端快照] 節點) 的個別結構。 展開時，每個磁碟區群組容器會列出以互動方式或透過已設定的原則所取得的所有成功備份。

* 若要展開節點，請按一下 [備份目錄] 旁邊的箭頭圖示。
* 若要查看可用動作的功能表，請在 [備份目錄] 節點上按一下滑鼠右鍵，或在展開之檢視中出現的任一節點上按一下滑鼠右鍵。
* 若要查看備份快照的清單，請按一下 [領域] 窗格中的 [備份目錄]。 快照清單會連同每個快照的相關資訊出現在 [結果] 窗格中。

### <a name="local-snapshots-node"></a>本機快照節點
[本機快照] 節點會列出特定磁碟區群組的本機快照。 這個節點位於 [領域] 窗格的 [備份類別目錄] 節點之下。 本機快照是儲存在 Azure StorSimple 裝置之磁碟區資料的時間點複本。 一般而言，可以快速建立和還原這種類型的備份。 您可以如同使用本機備份複本一般使用本機快照。

* 若要展開節點，請按一下 [本機快照] 旁邊的箭頭圖示。
* 若要查看可用動作的功能表，請在 [本機快照] 節點上按一下滑鼠右鍵，或在展開之檢視中出現的任一節點上按一下滑鼠右鍵。
* 若要查看本機快照的清單，請按一下 [領域] 窗格中的 [本機快照]。 快照清單會連同每個快照的相關資訊出現在 [結果] 窗格中。

### <a name="cloud-snapshots-node"></a>雲端快照節點
[雲端快照] 節點會列出特定磁碟區群組的雲端快照。 這個節點位於 [領域] 窗格的 [備份類別目錄] 節點之下。 雲端快照是儲存在雲端之磁碟區資料的時間點複本。 雲端快照相當於在不同的異地儲存體系統上複寫的快照。 雲端快照在災害復原案例中特別有用。

* 若要展開節點，請按一下 [雲端快照] 旁邊的箭頭圖示。
* 若要查看可用動作的功能表，請在 [雲端快照] 節點上按一下滑鼠右鍵，或在展開之檢視中出現的任一節點上按一下滑鼠右鍵。
* 若要查看雲端快照的清單，請按一下 [領域] 窗格中的 [雲端快照]。 快照清單會連同每個快照的相關資訊出現在 [結果] 窗格中。

### <a name="jobs-node"></a>作業節點
[工作] 節點包含已排定、執行中和最近完成之備份工作的相關資訊。 

* 若要展開節點，請按一下 [工作] 旁邊的箭頭圖示。
* 若要查看可用動作的功能表，請在 [工作] 節點上按一下滑鼠右鍵，或在展開之檢視中出現的任一節點上按一下滑鼠右鍵。
* 若要查看已排定工作的清單，請展開 [工作] 節點，然後按一下 [已排定]。 先前設定的工作清單及每個工作的相關資訊會出現在 [結果] 窗格中。 
* 若要查看最近完成的工作清單，請展開 [工作] 節點，然後按一下 [過去 24 小時]。 過去 24 小時內所完成的工作清單會出現在 [結果] 窗格中。 [結果] 窗格也包含每個已完成工作的相關資訊。
* 若要查看目前執行中的工作清單，請展開 [工作] 節點，然後按一下 [執行中]。 目前執行中的工作清單及每個工作的相關資訊會出現在 [結果] 窗格中。

## <a name="results-pane"></a>結果窗格
[ **結果** ] 窗格是 StorSimple Snapshot Manager UI 的中心窗格。 它包含您在 **[領域]** 窗格中選取的節點的清單和詳細狀態資訊。

### <a name="example"></a>範例
若要檢視下列範例，請按一下 [領域] 窗格中的 [磁碟區群組] 節點。 [結果] 窗格會顯示磁碟區群組清單和關於每個群組的詳細資料。

![結果窗格](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

您可以設定 [結果] 窗格中顯示的詳細資料：以滑鼠右鍵按一下 [範圍] 窗格中的節點，按一下 [檢視]，然後按一下 [新增/移除資料行]。

## <a name="actions-pane"></a>動作窗格
[ **動作** ] 窗格是 StorSimple Snapshot Manager UI 中的右窗格。 其中包含一個功能表，列出您對於在 [領域] 窗格或 [結果] 窗格中選取的節點、檢視或資料所能執行的作業。 [動作] 窗格所包含的命令，與可供 [領域] 窗格和 [結果] 窗格中的項目使用的 [動作] 功能表相同。 如需每個動作的說明，請參閱 [ **動作** ] 功能表一節中的資料表。

### <a name="examples"></a>範例
若要檢視下列範例，請在 [領域] 窗格中展開 [工作] 節點，然後按一下 [已排程]。 [動作] 窗格會顯示 [已排程]  節點的可用動作。

![動作窗格的已排程作業範例](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

若要檢視其他選項，請在 [領域] 窗格中展開 [工作] 節點、按一下 [已排程]，然後在 [結果] 窗格中按一下已排程的工作。 [動作] 窗格會顯示已排程工作的可用動作，如下列範例所示。

![動作窗格的作業動作範例](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>鍵盤瀏覽和快速鍵
StorSimple Snapshot Manager 可啟用 Windows 作業系統和 Microsoft Management Console (MMC) 的協助工具功能。 其也包含一些 StorSimple Snapshot Manager 專用的鍵盤導覽功能和快速鍵，如下列各節所述。

* [鍵盤導覽鍵](#keyboard-navigation-keys) 
* [功能表列快速鍵](#menu-bar-shortcut-keys) 
* [範圍窗格快速鍵](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>鍵盤導覽鍵
下表描述您可以用來導覽 StorSimple Snapshot Manager 使用者介面的按鍵。 

| 導覽鍵 | 動作 |
|:--- |:--- |
| 向下鍵 |使用向下鍵，可垂直移至功能表或窗格中的下一個項目。 |
| Enter |按 Enter 鍵，可完成動作，然後繼續進行下一個步驟。 例如，您可以按 Enter 鍵以選取 [下一步]、[確定] 或 [建立]，然後移至精靈中的下一個步驟。 |
| Esc |按 Esc 鍵，可關閉功能表或取消並關閉頁面。 |
| F1 |按 F1 鍵，可來檢視目前使用中視窗的說明主題。 |
| F5 |按 F5 鍵，可重新整理節點。 |
| F6 |按 F6 鍵可從 [領域] 窗格移至 [結果] 窗格。 |
| F10 |按 F10 鍵，可移至功能表列。 |
| 向左鍵 |使用向左鍵，可從某個功能表列選項水平移至前一個選項。 當您移至功能表列上的前一個項目時，前一個項目的動作 (或內容) 功能表即會出現。 |
| 向右鍵 |使用向右鍵，可從某個功能表列選項垂直移至下一個選項。 當您移至功能表列上的下一個項目時，新項目的動作 (或內容) 功能表即會出現。 |
| Tab 鍵 |使用 Tab 鍵，可移至主控台上的下一個窗格，或頁面中的下一個選取項目或文字方塊。 |
| 向上鍵 |使用向上鍵，可垂直移至功能表或窗格上的前一個項目。 |

### <a name="menu-bar-shortcut-keys"></a>功能表列快速鍵
下表描述功能表列的快速鍵組合。 在按下快速鍵和功能表之後，您可以使用功能表快速鍵 (功能表上加上底線的按鍵)。 如需功能表列的詳細資訊，請移至 [功能表列](#menu-bar)。

| 快速鍵 | 結果 | 功能表快速鍵 | 結果 |
|:--- |:--- |:--- |:--- |
| ALT+F |開啟 [檔案] 功能表。 |N |開啟新的主控台執行個體。 |
|  |O |開啟 [系統管理工具] 頁面。 | |
|  |S |儲存 StorSimple Snapshot Manager 主控台。 | |
|  |A |開啟 [另存新檔] 頁面。 | |
|  |M |開啟 [新增/移除嵌入式管理單元] 頁面。 | |
|  |P |開啟 [選項] 頁面。 | |
|  |H |開啟線上說明。 | |
| ALT+A |開啟 [動作] 功能表。 |I |開啟和關閉 [匯入顯示] 選項。 |
|  |W |開啟新的 StorSimple Snapshot Manager 主控台。 | |
|  |F |更新 StorSimple Snapshot Manager 主控台。 | |
|  |L |開啟 [匯出清單] 頁面。 | |
|  |H |開啟線上說明。 | |
| ALT+V |開啟 [檢視] 功能表。 |A |開啟 [新增/移除欄位] 頁面。 |
|  |U |開啟 [自訂檢視] 頁面。 | |
| ALT+O |開啟 [我的最愛] 功能表。 |A |開啟 [加到我的最愛] 頁面。 |
|  |O |開啟 [組織我的最愛] 頁面。 | |
| ALT+W |開啟 [視窗] 功能表。 |N |開啟另一個 StorSimple Snapshot Manager 視窗。 |
|  |C |以重疊顯示樣式顯示所有開啟的主控台視窗。 | |
|  |T |以格線模式顯示所有開啟的主控台視窗。 | |
|  |I |以水平列方式將圖示排列在畫面底部。 | |
| ALT+H |開啟 [說明] 功能表。 |H |開啟線上說明。 |
|  |T |開啟 Microsoft TechNet 技術中心網頁。 | |
|  |A |開啟 [關於 Microsoft Management Console] 頁面。 | |

### <a name="scope-pane-shortcut-keys"></a>範圍窗格快速鍵
下表說明 [領域] 窗格中各個節點的快速鍵組合。 

* [裝置節點快速鍵](#devices-node-shortcut-keys)
* [磁碟區節點快速鍵](#volumes-node-shortcut-keys)
* [磁碟區群組節點快速鍵](#volume-groups-node-shortcut-keys)
* [備份原則節點快速鍵](#backup-policies-node-shortcut-keys)
* [備份目錄節點快速鍵](#backup-catalog-node-shortcut-keys)
* [作業節點快速鍵](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>裝置節點快速鍵
| 功能表快速鍵 | 結果 |
|:--- |:--- |
| C |開啟 [設定裝置] 頁面。 |
| D |重新整理裝置清單和裝置詳細資料。 |
| V |開啟 [檢視] 功能表。 |
| W |開啟新的 StorSimple Snapshot Manager 主控台，且焦點在 [ **詳細資料** ] 節點。 |
| F |更新 StorSimple Snapshot Manager 主控台。 |
| L |開啟 [匯出清單] 頁面。 |
| H |開啟線上說明。 |

#### <a name="volumes-node-shortcut-keys"></a>磁碟區節點快速鍵
| 功能表快速鍵 | 結果 |
|:--- |:--- |
| V |更新磁碟區清單。 |
| V (按兩次) |開啟 [檢視] 功能表。 |
| W |開啟新的 StorSimple Snapshot Manager 主控台，且焦點在 [ **磁碟區** ] 節點。 |
| F |更新 StorSimple Snapshot Manager 主控台。 |
| L |開啟 [匯出清單] 頁面。 |
| H |開啟線上說明。 |

#### <a name="volume-groups-node-shortcut-keys"></a>磁碟區群組節點快速鍵
| 功能表快速鍵 | 結果 |
|:--- |:--- |
| G |開啟 [建立磁碟區群組] 頁面。 |
| V |開啟 [檢視] 功能表。 |
| W |開啟新的 StorSimple Snapshot Manager 主控台，且焦點在 [ **磁碟區群組** ] 節點。 |
| F |更新 StorSimple Snapshot Manager 主控台。 |
| L |開啟 [匯出清單] 頁面。 |
| H |開啟線上說明。 |

#### <a name="backup-policies-node-shortcut-keys"></a>備份原則節點快速鍵
| 功能表快速鍵 | 結果 |
|:--- |:--- |
| B |開啟 [建立原則] 頁面。 |
| V |開啟 [檢視] 功能表。 |
| W |開啟新的 StorSimple Snapshot Manager 主控台，且焦點在 [ **磁碟區群組** ] 節點。 |
| F |更新 StorSimple Snapshot Manager 主控台。 |
| L |開啟 [匯出清單] 頁面。 |
| H |開啟線上說明。 |

#### <a name="backup-catalog-node-shortcut-keys"></a>備份目錄節點快速鍵
| 功能表快速鍵 | 結果 |
|:--- |:--- |
| W |開啟新的 StorSimple Snapshot Manager 主控台，且焦點在 [ **磁碟區群組** ] 節點。 |
| F |更新 StorSimple Snapshot Manager 主控台。 |
| H |開啟線上說明。 |

#### <a name="jobs-node-shortcut-keys"></a>作業節點快速鍵
| 功能表快速鍵 | 結果 |
|:--- |:--- |
| V |開啟 [檢視] 功能表。 |
| W |開啟新的 StorSimple Snapshot Manager 主控台，且焦點在 [ **作業** ] 節點。 |
| F |更新 StorSimple Snapshot Manager 主控台。 |
| L |開啟 [匯出清單] 頁面。 |
| H |開啟線上說明 |

## <a name="next-steps"></a>後續步驟
* 了解如何 [使用 StorSimple Snapshot Manager 來管理您的 StorSimple 解決方案](storsimple-snapshot-manager-admin.md)。
* 了解如何 [使用 StorSimple Snapshot Manager 來連接和管理裝置](storsimple-snapshot-manager-manage-devices.md)。