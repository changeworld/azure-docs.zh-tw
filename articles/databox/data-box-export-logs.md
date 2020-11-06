---
title: 追蹤和記錄 Azure 資料箱，Azure Data Box Heavy 匯出順序的事件 |Microsoft Docs
description: 描述如何在 Azure 資料箱的各個階段追蹤和記錄事件，並 Azure Data Box Heavy 匯出順序。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 3a915ac8de83a5e183660ec4a3d05044eafff4a9
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "94337503"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy-export-orders"></a>Azure 資料箱和 Azure Data Box Heavy 匯出訂單的追蹤和事件記錄

資料箱或 Data Box Heavy 匯出順序會經歷下列步驟：訂購、設定、資料複製、傳回和資料清除。 對應至順序中的每個步驟，您可以採取多個動作來控制訂單的存取權、審核事件、追蹤順序，以及解讀產生的各種記錄。

本文詳細說明可用來追蹤和審核資料箱或 Data Box Heavy 之匯出訂單的各種機制或工具。 本文中的資訊適用于、Data Box 及 Data Box Heavy。 在後續的章節中，任何對資料箱的參考也適用于 Data Box Heavy。

下表顯示資料箱匯出順序步驟的摘要，以及可在每個步驟期間追蹤和審核訂單的工具。

| 資料箱匯出訂單階段       | 追蹤和審核的工具                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| 建立訂單               | [透過 Azure RBAC 設定訂單的存取控制](#set-up-access-control-on-the-order) <br> [依序啟用詳細資訊記錄](#enable-verbose-log-in-the-order)                                                    |
| 訂單處理            | [追蹤順序](#track-the-order) <ul><li> Azure 入口網站 </li><li> 貨運承運商網站 </li><li>電子郵件通知</ul> |
| 設定裝置              | 記錄在[活動記錄](#query-activity-logs-during-setup)中的裝置認證存取              |
| 從裝置複製資料        | [審核複製記錄](#copy-log) <br> 複製資料之前，請先[參閱詳細資訊記錄](#verbose-log)            |
| 從裝置抹除資料   | [查看監管記錄的鏈](#get-chain-of-custody-logs-after-data-erasure) ，包括審核記錄和訂單記錄                |


## <a name="set-up-access-control-on-the-order"></a>設定訂單上的存取控制

您可以在第一次建立訂單時，控制可存取訂單的人員。 在不同範圍設定 Azure 角色，以控制資料箱訂單的存取權。 Azure 角色可決定存取權的類型–讀寫、唯讀、讀寫作業的子集。

可以為 Azure 資料箱服務定義的兩個角色為：

- **資料箱讀取器** -依領域定義的順序 (s) 的唯讀存取權。 他們只能查看訂單的詳細資料。 他們無法存取與儲存體帳戶相關的任何其他詳細資料，也無法編輯訂單詳細資料，例如位址等等。
- **資料箱參與者** -只有 *在已有儲存體帳戶的寫入存取權時* ，才能建立將資料傳送到指定儲存體帳戶的訂單。 如果他們沒有儲存體帳戶的存取權，他們甚至無法建立資料箱訂單來將資料複製到帳戶。 此角色不會定義任何儲存體帳戶的相關許可權，也不會授與儲存體帳戶的存取權。  

若要限制訂單的存取權，您可以：

- 指派訂單層級的角色。 使用者只具有角色所定義的許可權，才能與特定的資料箱訂單互動，而不是其他任何專案。
- 在資源群組層級指派角色，使用者即可存取資源群組內的所有資料箱訂單。

如需有關建議使用的 Azure RBAC 的詳細資訊，請參閱 [AZURE rbac 的最佳做法](../role-based-access-control/best-practices.md)。

## <a name="enable-verbose-log-in-the-order"></a>依序啟用詳細資訊記錄

當您放置資料箱的匯出順序時，您可以選擇啟用詳細資訊記錄檔的收集。 以下是您可以啟用詳細資訊記錄的順序畫面：

![選取匯出選項](media/data-box-deploy-export-ordered/azure-data-box-export-order-export-option.png)

當您選取 [ **包含詳細資訊記錄** 檔] 選項時，從您的 Azure 儲存體帳戶複製資料時，會產生詳細資訊記錄檔。 此記錄檔包含已成功匯出的所有檔案清單。

如需匯出順序的詳細資訊，請參閱 [建立資料箱的匯出順序](data-box-deploy-export-ordered.md)

## <a name="track-the-order"></a>追蹤訂單狀態

您可以透過 Azure 入口網站和貨運公司網站來追蹤您的訂單。 以下是可隨時追蹤資料箱順序的機制：

- 若要在裝置位於 Azure 資料中心或您的內部部署時追蹤訂單，請移至您的 **資料箱訂單，>** Azure 入口網站中的總覽。

    ![視圖順序狀態和追蹤](media/data-box-logs/overview-view-status-1.png)

- 若要在裝置傳輸時追蹤訂單，請移至區域貨運公司網站（例如，美國的 UPS 網站）。 提供與您訂單相關聯的追蹤號碼。
- 資料箱也會根據建立訂單時所提供的電子郵件，在訂單狀態變更時傳送電子郵件通知。 如需所有資料箱訂單狀態的清單，請參閱 [視圖順序狀態](data-box-portal-admin.md#view-order-status)。 若要變更與訂單相關聯的通知設定，請參閱 [編輯通知詳細資料](data-box-portal-admin.md#edit-notification-details)。

## <a name="query-activity-logs-during-setup"></a>在安裝期間查詢活動記錄

- 您的資料箱會以鎖定狀態抵達您的內部部署。 您可以使用 Azure 入口網站中提供的裝置認證來取得訂單。  

    當資料箱設定完成時，您可能需要知道誰已存取裝置認證。 若要找出存取 **裝置認證** 分頁的人員，您可以查詢活動記錄。  任何牽涉到存取 **裝置詳細資料 > 認證** 分頁的動作都會登入活動記錄中作為 `ListCredentials` 動作。

    ![查詢活動記錄](media/data-box-logs/query-activity-log-1.png)

- 每次登入資料箱都會即時記錄。 不過，這項資訊僅適用于成功完成訂單後的 [保管審核記錄鏈](#chain-of-custody-audit-logs) 。

## <a name="view-logs-during-data-copy"></a>在資料複製期間查看記錄

從資料箱複製資料之前，您可以下載並檢查複製到資料箱之資料的 *複製記錄* 檔和 *詳細資訊記錄* 檔。 從 Azure 中的儲存體帳戶將資料複製到您的資料箱時，會產生這些記錄。 

### <a name="copy-log"></a>複製記錄檔

從資料箱複製資料之前，請從 [連線 **和複製]** 頁面下載複製記錄檔。

以下是在資料從 Azure 複製到資料箱裝置時， *複製記錄* 檔的範例輸出。

```output
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
  <TotalFiles_Blobs>5521</TotalFiles_Blobs>
  <FilesErrored>0</FilesErrored>
</CopyLog>
``` 
    
以下是 *複製記錄* 檔有錯誤，且部分檔案無法從 Azure 複製的範例輸出。

```output
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>    
```

您可以使用下列選項來匯出這些檔案： 

- 您可以傳輸無法透過網路來複製的檔案。 
- 如果您的資料大小大於可用的裝置容量，則會發生部分複製，且所有未複製的檔案都會列在此記錄中。 您可以使用此記錄作為輸入 XML 來建立新的資料箱順序，然後複製這些檔案。

### <a name="verbose-log"></a>詳細資訊記錄

*詳細資訊記錄* 包含已從 Azure 儲存體帳戶成功匯出的所有檔案清單。 記錄中也包含檔案大小和總和檢查碼計算。

詳細資訊記錄檔的格式如下：

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

以下是詳細資訊記錄檔的範例輸出。 

```powershell
  <File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820">
  </File>
  ``````

詳細資訊記錄也會複製到 Azure 儲存體帳戶。 根據預設，記錄會寫入名為 `copylog` 的容器。 記錄檔會以下列命名慣例儲存：

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

複製記錄檔路徑也會顯示在入口網站的 [ **總覽** ] 分頁中。

<!-- add a screenshot-->

您可以使用這些記錄來確認從 Azure 複製的檔案符合複製到內部部署伺服器的資料。 

使用詳細資訊記錄檔：

- 以確認實際的名稱和從資料箱複製的檔案數目。
- 以確認檔案的實際大小。
- 確認 *crc64* 對應至非零字串。 迴圈冗余檢查 (CRC) 計算是在從 Azure 匯出期間完成。 從匯出開始，以及將資料從資料箱複製到內部部署伺服器之後的 CRCs 可以進行比較。 CRC 不符表示對應的檔案無法正確複製。


## <a name="get-chain-of-custody-logs-after-data-erasure"></a>在資料抹除之後取得監管記錄鏈

根據 NIST SP 800-88 修訂1指導方針，從資料箱磁片中清除資料之後，可取得監管記錄的鏈。 這些記錄包括監管審核記錄和訂單記錄的鏈。 BOM 或資訊清單檔也會連同 audit 記錄一起複製。

### <a name="chain-of-custody-audit-logs"></a>監管鏈審核記錄

監管鏈審核記錄包含開啟和存取資料箱上的共用，或在 Azure 資料中心以外的 Data Box Heavy 時的相關資訊。 這些記錄位於： `storage-account/azuredatabox-chainofcustodylogs`

以下是資料箱中的 audit 記錄範例：

```output
9/10/2018 8:23:01 PM : The operating system started at system time ‎2018‎-‎09‎-‎10T20:23:01.497758400Z.
9/10/2018 8:23:42 PM : An account was successfully logged on.
Subject:
    Security ID:        S-1-5-18
    Account Name:       WIN-DATABOXADMIN
    Account Domain: Workgroup
    Logon ID:       0x3E7
Logon Information:
    Logon Type:     3
    Restricted Admin Mode:  -
    Virtual Account:        No
    Elevated Token:     No
Impersonation Level:        Impersonation
New Logon:
    Security ID:        S-1-5-7
    Account Name:       ANONYMOUS LOGON
    Account Domain: NT AUTHORITY
    Logon ID:       0x775D5
    Linked Logon ID:    0x0
    Network Account Name:   -
    Network Account Domain: -
    Logon GUID:     {00000000-0000-0000-0000-000000000000}
Process Information:
    Process ID:     0x4
    Process Name:       
Network Information:
    Workstation Name:   -
    Source Network Address: -
    Source Port:        -
Detailed Authentication Information:
    Logon Process:      NfsSvr
    Authentication Package:MICROSOFT_AUTHENTICATION_PACKAGE_V1_0
    Transited Services: -
    Package Name (NTLM only):   -
    Key Length:     0
This event is generated when a logon session is created. It is generated on the computer that was accessed. 
The subject fields indicate the account on the local system which requested the logon. This is most commonly a service such as the Server service, or a local process such as Winlogon.exe or Services.exe. 
The logon type field indicates the kind of logon that occurred. The most common types are 2 (interactive) and 3 (network).
The New Logon fields indicate the account for whom the new logon was created, i.e. the account that was logged on.
The network fields indicate where a remote logon request originated. Workstation name is not always available and may be left blank in some cases.
The impersonation level field indicates the extent to which a process in the logon session can impersonate.
The authentication information fields provide detailed information about this specific logon request.
    - Logon GUID is a unique identifier that can be used to correlate this event with a KDC event.
    - Transited services indicate which intermediate services have participated in this logon request.
    - Package name indicates which sub-protocol was used among the NTLM protocols.
    - Key length indicates the length of the generated session key. This will be 0 if no session key was requested.
9/10/2018 8:25:58 PM : An account was successfully logged on.
```

## <a name="download-order-history"></a>下載訂單記錄

訂單記錄可在 Azure 入口網站中取得。 如果訂單已完成，且裝置清除 (磁片) 的資料清除完成，請移至您的裝置訂單，然後流覽至 [ **訂單詳細** 資料]。 [下載訂單記錄] 選項可供使用。 如需詳細資訊，請參閱 [下載訂單記錄](data-box-portal-admin.md#download-order-history)。

如果您有滾動訂單歷程記錄，您會看到：

- 裝置的貨運公司追蹤資訊。
- 具有 *SecureErase* 活動的事件。 這些事件會對應到磁片上的資料抹除。
- 資料箱記錄連結。 系統會顯示 *audit 記錄* 檔、 *複製記錄* 檔和 *BOM* 檔案的路徑。

以下是 Azure 入口網站的訂單歷程記錄範例：

```
-------------------------------
Microsoft Data Box Order Report
-------------------------------
Name                                               : gus-poland                              
StartTime(UTC)                              : 9/19/2018 8:49:23 AM +00:00                       
DeviceType                                     : DataBox                                           
-------------------
Data Box Activities
-------------------
Time(UTC)                 | Activity                       | Status          | Description

9/19/2018 8:49:26 AM      | OrderCreated                   | Completed       |
10/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |
10/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 10/3/2018 1:36:43 PM at AMSTERDAM-NLD                                                                                
10/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 10/4/2018 8:23:30 PM at AMSTERDAM-NLD                                                                        
10/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time : 10/4/2018 11:43:34 PM at AMSTERDAM-NLD
10/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in BRIGHTON-GBR. Local Time : 10/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
10/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 10/5/2018 9:13:24 AM at BRIGHTON-GBR                                                                               
10/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 10/5/2018 12:03:04 PM at BRIGHTON-GBR                                                                          
1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25 PM at BRIGHTON-GBR                                                                                       
1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at BRIGHTON-GBR. Local Time : 1/25/2019 8:03:55 PM at LAMBETH-GBR                                                                            
1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in BRIGHTON-GBR. Local Time : 1/25/2019 8:04:58 PM at BRIGHTON-GBR                                                                    
1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR
1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD. Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019 9:07:57 AM at AMSTERDAM-NLD                                                                             
1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019 1:35:56 PM at AMSTERDAM-NLD                                                                            
1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019 2:57:48 PM at AMSTERDAM-NLD
1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |
1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully.
1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.
2/12/2019 10:37:03 PM     | DataCopy                       | Resumed         |
2/13/2019 12:05:15 AM     | DataCopy                       | Resumed         |
2/15/2019 7:07:34 PM      | DataCopy                       | Completed       | Copy Completed.
2/15/2019 7:47:32 PM      | SecureErase                    | Started         |
2/15/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:<Device-serial-no> has been sanitized according to NIST 800-88 Rev 1.
------------------
Data Box Log Links
------------------
Account Name         : gusacct
Copy Logs Path       : databoxcopylog/gus-poland_<Device-serial-no>_CopyLog_<GUID>.xml
Audit Logs Path      : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
BOM Files Path       : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
```

## <a name="next-steps"></a>後續步驟

- 瞭解如何 [針對資料箱和 Data Box Heavy 的問題進行疑難排解](data-box-troubleshoot.md)。
