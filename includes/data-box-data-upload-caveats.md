---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 09/30/2020
ms.author: alkohli
ms.openlocfilehash: 80411ac191d0385ddafd4a3cc490d322ce039689
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91644983"
---
- 請勿將檔案直接複製到任何預先建立共用。 您必須在共用底下建立資料夾，然後將檔案複製到該資料夾。
- StorageAccount_BlockBlob** 和 StorageAccount_PageBlob** 底下的資料夾是容器。 例如，容器會建立為 StorageAccount_BlockBlob/container** 和 StorageAccount_PageBlob/container**。
- 直接在 StorageAccount_AzureFiles** 底下建立的每個資料夾會轉譯成 Azure 檔案共用。
- 如果要複製的物件與已在雲端中的 Azure 物件（例如 blob 或檔案）有相同的名稱，資料箱將會覆寫雲端中的檔案。
- 寫入 StorageAccount_BlockBlob** 和 StorageAccount_PageBlob** 共用中的每個檔案，分別會以區塊 Blob 和分頁 Blob 的形式上傳。
- Azure blob 儲存體不支援目錄。 如果您在 StorageAccount_BlockBlob** 資料夾底下建立資料夾，則會以 Blob 名稱建立虛擬資料夾。 對於 Azure 檔案服務，則會維持實際的目錄結構。
- 任何空的目錄階層 (沒有任何) 在 *StorageAccount_BlockBlob* 下建立的檔案，且 *StorageAccount_PageBlob* 資料夾未上傳。
- 如果將資料上傳至 Azure 時發生任何錯誤，則會在目標儲存體帳戶中建立錯誤記錄。 當上傳完成時，可以使用此錯誤記錄檔的路徑，而您可以查看記錄以採取更正動作。 請勿在不驗證上傳的資料的情況下，從來源刪除資料。
- 當資料上傳至 Azure 檔案儲存體時，您可以使用 [保留檔案 acl、屬性和時間戳記的指引，](../articles/databox/data-box-file-acls-preservation.md)來保留檔案中繼資料和 NTFS 許可權 Azure 資料箱。