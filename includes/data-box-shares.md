---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 06/05/2020
ms.author: alkohli
ms.openlocfilehash: 5aaf0ce747b14b2fa9f2fcd9a65b774aa7d2db3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87102524"
---
根據選取的儲存體帳戶，資料箱最多會建立：

* 每個相關聯的儲存體帳戶三個共用 (針對 GPv1 與 GPv2)。
* 一個適用於進階儲存體的共用。
* 一個適用於 Blob 儲存體帳戶的共用。

在區塊 Blob 與分頁 Blob 共用底下，第一層實體是容器，而第二層實體是 Blob。 在 Azure 檔案共用底下，第一層實體是共用，第二層實體是檔案。

下表顯示您資料箱上的共用 UNC 路徑，以及用於上傳資料的 Azure 儲存體路徑 URL。 最終的 Azure 儲存體路徑 URL 可以衍生自 UNC 共用路徑。
 
| Blob 與檔案 | 路徑和 URL |
| --------------- | -------------- |
| Azure 區塊 Blob | <li>共用的 UNC 路徑：`\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Azure 儲存體 URL：`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure 分頁 Blob  | <li>共用的 UNC 路徑：`\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Azure 儲存體 URL：`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure 檔案       |<li>共用的 UNC 路徑：`\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Azure 儲存體 URL：`https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

