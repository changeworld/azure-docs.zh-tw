---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: b2dec95e0258933b50d4437f1cb317639b62883d
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "67174022"
---
### <a name="upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-storsomple-adapter-for-sharepoint"></a>將 SharePoint 2010 升級至 SharePoint 2013 然後再安裝 StorSomple Adapter for SharePoint
> [!IMPORTANT]
> 先前透過 RBS 移到外部儲存體的任何檔案，必須等到升級完成並重新啟用 RBS 功能之後才能使用。 為了限制使用者受影響的程度，請在規劃的維護期間執行任何升級或重新安裝。
> 
> 

#### <a name="to-upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-adapter"></a>將 SharePoint 2010 升級為 SharePoint 2013，然後安裝配接器
1. 在 SharePoint 2010 伺服陣列中，記下外部化 BLOB 的 BLOB 存放區路徑和在其中啟用 RBS 的內容資料庫。 
2. 安裝和設定新的 SharePoint 2013 伺服器陣列。 
3. 將資料庫、應用程式和網站集合從 SharePoint 2010 伺服器陣列移至新的 SharePoint 2013 伺服器陣列。 如需指示，請移至 [SharePoint 2013 升級程序的概觀](https://technet.microsoft.com/library/cc262483.aspx)。
4. 在新的伺服器陣列上安裝 StorSimple Adapter for SharePoint 移至[安裝 StorSimple Adapter for SharePoint](#install-the-storsimple-adapter-for-sharepoint) 以了解程序。
5. 使用您在步驟 1 記下的資訊，為同一組內容資料庫啟用 RBS 並提供 BLOB 存放區路徑，需與 SharePoint 2010 安裝中所使用的路徑相同。 移至 [設定 RBS](#configure-rbs) 以了解程序。 完成這個步驟之後，先前已外部化的檔案必須能夠從新的伺服器陣列存取。 

### <a name="upgrade-the-storsimple-adapter-for-sharepoint"></a>升級 StorSimple Adapter for SharePoint
> [!IMPORTANT]
> 基於下列原因，您應該將此升級排程於規劃的維護視窗期間：
> 
> * 在重新安裝配接器之前，先前已外部化的內容將無法使用。
> * 在您解除安裝舊版 StorSimple Adapter for SharePoint 之後，安裝新版之前，任何上傳至網站的內容都將儲存在內容資料庫中。 安裝新的配接器之後，您必須將該內容移至 StorSimple 裝置。 您可以使用 SharePoint 隨附`RBS Migrate()`的 Microsoft PowerShell Cmdlet 來遷移內容。 如需詳細資訊，請參閱 [將內容移入或移出 RBS](https://technet.microsoft.com/library/ff628255.aspx)。 
> 
> 

#### <a name="to-upgrade-the-storsimple-adapter-for-sharepoint"></a>升級 StorSimple Adapter for SharePoint
1. 解除安裝舊版 StorSimple Adapter for SharePoint。
   
   > [!NOTE]
   > 這會自動停用內容資料庫上的 RBS。 不過，現有的 BLOB 會保留在 StorSimple 裝置上。 因為已停用 RBS 且 BLOB 尚未移轉回內容資料庫，對這些 BLOB 的任何要求都會失敗。 
   > 
   > 
2. 安裝新的 StorSimple Adapter for SharePoint。 新的配接器會自動辨識先前已為 RBS 啟用或停用的內容資料庫，並使用先前的設定。

