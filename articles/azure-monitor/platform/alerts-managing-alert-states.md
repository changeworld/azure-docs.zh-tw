---
title: 管理警示與智慧群組狀態
description: 管理警示和智慧群組執行個體的狀態
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.date: 09/24/2018
ms.openlocfilehash: b91c3d59a5a37457d67fdfbc646334fccb331df5
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92104796"
---
# <a name="manage-alert-and-smart-group-states"></a>管理警示與智慧群組狀態

Azure 監視器中的警示現在有[警示的狀態和監視條件](./alerts-overview.md)，同樣地，智慧群組也有[智慧型群組狀態](./alerts-smartgroups-overview.md?toc=%252fazure%252fazure-monitor%252ftoc.json)。 對狀態所作的變更，現在會擷取到與個別警示或智慧群組相關聯的歷程記錄。 這篇文章會引導您完成變更警示和智慧群組兩者狀態的程序。

## <a name="change-the-state-of-an-alert"></a>變更警示的狀態

1. 您可以透過下列不同方式變更警示的狀態： 
    * 在 [所有警示] 頁面中，按一下要變更狀態之警示旁邊的核取方塊，然後按一下 [變更狀態]。   
    ![螢幕擷取畫面顯示已選取變更狀態的 [所有警示] 頁面。](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * 在特定警示執行個體的 [警示詳細資料] 頁面中，您可以按一下 [變更狀態]   
    ![螢幕擷取畫面顯示已選取變更警示狀態的 [警示詳細資料] 頁面。](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * 在特定警示執行個體的 [警示詳細資料] 頁面，您也可以在 [智慧群組] 窗格中按一下要變更狀態之警示旁邊的核取方塊    
    ![螢幕擷取畫面顯示有某些實例具有核取記號的 [警示詳細資料] 頁面。](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * 在 [智慧群組詳細資料] 頁面中的成員警示清單中，您可以按一下要變更狀態之警示旁邊的核取方塊，然後按一下 [變更狀態] 以變更其狀態。   
    ![螢幕擷取畫面顯示 [智慧群組詳細資料] 頁面，您可以在其中選取要變更狀態的警示。](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. 按一下 [變更狀態] 時會開啟一個快顯視窗，讓您選取各種狀態 (New/Acknowledged/Closed)，如有必要也可輸入註解。   
![螢幕擷取畫面顯示 [詳細資料變更警示] 對話方塊。](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. 完成之後，狀態變更會記錄到個別警示的歷程記錄中。 開啟個別的 [詳細資料] 頁面並查看歷程記錄區段，即可檢視這些資料。    
![螢幕擷取畫面：顯示狀態變更的歷程記錄。](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>變更智慧群組的狀態
1. 您可以透過下列不同方式變更智慧群組的狀態：
    1. 在 [智慧群組] 清單頁面中，按一下要變更狀態之智慧群組旁邊的核取方塊，然後按一下 [變更狀態]  
    ![螢幕擷取畫面：顯示智慧群組的變更狀態頁面。](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. 在 [智慧型群組詳細資料] 頁面中，您可以按一下 [變更狀態]        
    ![螢幕擷取畫面顯示已選取 [變更智慧群組狀態] 的 [智慧群組詳細資料] 頁面。](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. 按一下 [變更狀態] 時會開啟一個快顯視窗，讓您選取各種狀態 (New/Acknowledged/Closed)，如有必要也可輸入註解。 
![螢幕擷取畫面：顯示智慧群組的 [變更狀態] 對話方塊。](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  變更智慧群組的狀態不會變更個別成員警示的狀態。

1. 完成之後，狀態變更會記錄到個別智慧群組的歷程記錄中。 開啟個別的 [詳細資料] 頁面並查看歷程記錄區段，即可檢視這些資料。     
![螢幕擷取畫面：顯示智慧群組的變更歷程記錄。](./media/alerts-managing-alert-states/state-sg-history.jpg)