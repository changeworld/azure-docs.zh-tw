---
title: 修復 Azure 匯入/匯出匯入作業 - v1 | Microsoft Docs
description: 了解如何修復使用 Azure 匯入/匯出服務建立和執行的匯入作業。
author: twooley
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/23/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: b3ba9844bf8412e169322fd4953cbc259a94e174
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88525770"
---
# <a name="repairing-an-import-job"></a>修復匯入作業
Microsoft Azure 匯入/匯出服務可能無法將某些檔案或某個檔案的部分複製到 Windows Azure Blob 服務。 部分的失敗原因包括︰  
  
-   損毀的檔案  
  
-   損壞的磁碟機  
  
-   儲存體帳戶金鑰在傳輸檔案時變更。  
  
您可以使用匯入作業的複製記錄檔來執行 Microsoft Azure 匯入/匯出工具。 此工具會將遺漏的檔案（或檔案的部分）上傳至您的 Windows Azure 儲存體帳戶，以完成匯入作業。  
  
## <a name="repairimport-parameters"></a>RepairImport 參數

您可以搭配 **RepairImport** 指定下列參數： 
  
|||  
|-|-|  
|**/r：**<RepairFile\>|**必要。** 修復檔案的路徑，可追蹤修復進度，並可讓您繼續中斷的修復。 每個磁碟機需要一個修復檔案，而且只能有一個。 當您啟動指定磁片磁碟機的修復時，請將路徑傳遞至修復檔案，但該檔案尚不存在。 若要恢復中斷的修復，您應傳入現有修復檔案名稱。 請一律指定對應于目標磁片磁碟機的修復檔。|  
|**/logdir：**<LogDirectory\>|**選。** 記錄檔目錄。 詳細資訊記錄檔會寫入至這個目錄。 如未指定記錄檔目錄，則會使用目前的目錄作為記錄檔目錄。|  
|**/d:**<TargetDirectories\>|**必要。** 一或多個以分號分隔的目錄，其中包含所匯入的原始檔。 也可以使用匯入磁片磁碟機，但如果原始檔案有替代位置，則不需要此檔案。|  
|**/bk：**<BitLockerKey\>|**選。** 如果您想要讓工具解除鎖定可以使用原始檔案的加密磁片磁碟機，請指定 BitLocker 金鑰。|  
|**/sn:**<StorageAccountName\>|**必要。** 匯入作業的儲存體帳戶名稱。|  
|**/sk:**<StorageAccountKey\>|只有在未指定容器 SAS 時才**需要**。 匯入作業之儲存體帳戶的帳戶金鑰。|  
|**/csas:**<ContainerSas\>|只有在未指定儲存體帳戶金鑰時才**需要**。 存取與匯入作業相關聯的 Blob 所用的容器 SAS。|  
|**/CopyLogFile：**<DriveCopyLogFile\>|**必要。** 磁碟機複製記錄檔 (詳細資訊記錄檔或錯誤記錄檔) 的路徑。 此檔案是由 Windows Azure 匯入/匯出服務所產生，您可以從與作業相關聯的 blob 儲存體下載。 複製記錄檔包含所要修復之失敗 blob 或檔案的相關資訊。|  
|**/PathMapFile:**<DrivePathMapFile\>|**選。** 如果您在相同的作業中匯入多個同名的檔案，則為用來解決多義性之文字檔的路徑。 第一次執行此工具時，它可以在此檔案中填入所有模稜兩可的名稱。 稍後執行此工具時，會使用此檔案來解決多義性。|  
  
## <a name="using-the-repairimport-command"></a>使用 RepairImport 命令  
若要透過網路串流資料來修復匯入資料，您必須指定內含您使用 `/d` 參數匯入之原始檔的目錄。 此外，請指定您從儲存體帳戶下載的複製記錄檔。 用於修復部分失敗之匯入作業的典型命令行如下所示︰  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
在以下的複製記錄檔範例中，針對匯入作業送交的磁碟機上有一個 64K 的檔案片段已損毀。 因為這項失敗是唯一指出的，所以作業中的其餘 blob 已成功匯入。  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
 <DriveId>9WM35C2V</DriveId>  
 <Blob Status="CompletedWithErrors">  
 <BlobPath>pictures/animals/koala.jpg</BlobPath>  
 <FilePath>\animals\koala.jpg</FilePath>  
 <Length>163840</Length>  
 <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
 <PageRangeList>  
  <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted" />  
 </PageRangeList>  
 </Blob>  
 <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
將此複製記錄檔傳遞至 Azure 匯入/匯出工具時，此工具會嘗試透過網路複製遺漏的內容來完成此檔案匯入。 在上述範例之後，此工具會在 `C:\Users\bob\Pictures` 和 `X:\BobBackup\photos` 兩個目錄中尋找原始檔案 `\animals\koala.jpg`。 如果檔案 `C:\Users\bob\Pictures\animals\koala.jpg` 存在，則 Azure 匯入/匯出工具會將遺漏的資料範圍複製到對應的 blob `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg`。  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>解決使用 RepairImport 時的衝突  
在某些情況下，工具可能找不到或無法開啟必要的檔案，因為下列其中一個原因：找不到檔案、無法存取檔案、檔案名不明確，或是檔案內容已不再正確。  
  
如果工具嘗試找出， `\animals\koala.jpg` 而且在和上有該名稱的檔案，則可能會發生不明確的錯誤 `C:\Users\bob\pictures` `X:\BobBackup\photos` 。 也就是說，`C:\Users\bob\pictures\animals\koala.jpg` 和 `X:\BobBackup\photos\animals\koala.jpg` 兩者都存在於匯入作業磁碟機上。  
  
`/PathMapFile` 選項可讓您解決這些錯誤。 您可以指定檔案名，其中包含工具無法正確識別的檔案清單。 下列命令列範例會填入 `9WM35C2V_pathmap.txt`：  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
工具會接著將有問題的檔案路徑寫入至 `9WM35C2V_pathmap.txt`，一行一個。 比方說，此檔案在執行命令後可能包含下列項目︰  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 針對清單中的每個檔案，您應該嘗試找出並開啟檔案，以確保該檔案可供工具使用。 如果您想要明確地告訴工具要在哪裡尋找檔案，請修改路徑對應檔案，並在同一行上新增每個檔案的路徑，並以定位字元分隔：  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
讓必要檔案可供工具使用，或更新路徑對應檔之後，您可以重新執行工具，以完成匯入程序。  
  
## <a name="next-steps"></a>接下來的步驟
 
* [設定 Azure 匯入/匯出工具](storage-import-export-tool-setup-v1.md)   
* [準備匯入作業的硬碟](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [使用複製記錄檔來檢查作業狀態](storage-import-export-tool-reviewing-job-status-v1.md)   
* [修復匯出作業](../storage-import-export-tool-repairing-an-export-job-v1.md)
