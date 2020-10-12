---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9c734ff03b1cf277c7e0967d8b76b1941434f414
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86050237"
---
在此程序中，您將會：

1. [準備執行維護程式可執行檔](#to-prepare-to-run-the-maintainer) 。
2. [為立即刪除遺棄的 BLOB 準備內容資料庫和資源回收筒](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs)。
3. [執行 Maintainer.exe](#to-run-the-maintainer)。
4. [還原內容資料庫和資源回收筒設定](#to-revert-the-content-database-and-recycle-bin-settings)。

#### <a name="to-prepare-to-run-the-maintainer"></a>準備執行維護程式
1. 在 Web 前端伺服器上，以系統管理員身分開啟 SharePoint 2013 管理命令介面。
2. 瀏覽至資料夾開機磁碟機**:\Program Files\Microsoft SQL Remote Blob Storage 10.50\Maintainer\.
3. 將 **Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config** 重新命名為 **web.config**。
4. 使用 `aspnet_regiis -pdf connectionStrings` 解密 web.config 檔案。
5. 在解密的 web.config 檔案中的 `connectionStrings` 節點下，為您的 SQL Server 執行個體和內容資料庫名稱新增連接字串。 請參閱下列範例。
   
    `<add name="RBSMaintainerConnectionWSSContent" connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=&quot;Remote Blob Storage Maintainer for WSS_Content&quot;" providerName="System.Data.SqlClient" />`
6. 使用 `aspnet_regiis –pef connectionStrings` 重新加密 web.config 檔案。 
7. 將 web.config 重新命名為 Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config。 

#### <a name="to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs"></a>為立即刪除被遺棄的 BLOB 準備內容資料庫和資源回收筒
1. 在 SQL Server 上的 SQL Management Studio 中，為目標內容資料庫執行下列更新查詢： 

    `use WSS_Content`

    `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`

    `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`

2. 在 Web 前端伺服器上的**管理中心**下，為所需的內容資料庫編輯 **Web 應用程式一般設定**以暫時停用資源回收筒。 這個動作將同時清空任何相關網站集合的資源回收筒。 若要這樣做，請按一下 [**管理中心**  ->  **應用程式管理**  ->  **web 應用程式 (管理 web 應用程式) **  ->  **SharePoint-80**  ->  **一般應用程式設定**]。 將**資源回收筒狀態**設為 **關閉**。
   
    ![Web 應用程式一般設定](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### <a name="to-run-the-maintainer"></a>執行維護程式
* 在 web 前端伺服器上的 SharePoint 2013 管理命令介面中，執行維護程式，如下所示：
  
    `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`
  
  > [!NOTE]
  > 此時 StorSimple 只支援 `GarbageCollection` 作業。 此外請注意，針對 Microsoft.Data.SqlRemoteBlobs.Maintainer.exe 所發出的參數會區分大小寫。 
  > 
  > 

#### <a name="to-revert-the-content-database-and-recycle-bin-settings"></a>還原內容資料庫和資源回收筒設定
1. 在 SQL Server 上的 SQL Management Studio 中，為目標內容資料庫執行下列更新查詢：
   
      `use WSS_Content`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`
2. 在 Web 前端伺服器上的**管理中心**中，為所需的內容資料庫編輯 **Web 應用程式一般設定**以重新啟用資源回收筒。 若要這樣做，請按一下 [**管理中心**  ->  **應用程式管理**  ->  **web 應用程式 (管理 web 應用程式) **  ->  **SharePoint-80**  ->  **一般應用程式設定**]。 將資源回收筒狀態設為**開啟**。

