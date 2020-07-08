---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: 16647b6a13e64073ab570d36a8a380d0e36bd855
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "73799708"
---
- 請勿將檔案直接複製到任何預先建立共用。 您需要在共用底下建立資料夾，然後將檔案複製到該資料夾。
- StorageAccount_BlockBlob** 和 StorageAccount_PageBlob** 底下的資料夾是容器。 例如，容器會建立為 StorageAccount_BlockBlob/container** 和 StorageAccount_PageBlob/container**。
- 直接在 StorageAccount_AzureFiles** 底下建立的每個資料夾會轉譯成 Azure 檔案共用。
- 如果雲端中現有的 Azure 物件 (例如 Blob 或檔案) 與要複製的物件同名，資料箱將會覆寫雲端中的檔案。
- 寫入 StorageAccount_BlockBlob** 和 StorageAccount_PageBlob** 共用中的每個檔案，分別會以區塊 Blob 和分頁 Blob 的形式上傳。
- Azure blob 儲存體不支援目錄。 如果您在 StorageAccount_BlockBlob** 資料夾底下建立資料夾，則會以 Blob 名稱建立虛擬資料夾。 對於 Azure 檔案服務，則會維持實際的目錄結構。
- 在*StorageAccount_BlockBlob*下建立的任何空白目錄階層（不含任何檔案），也不會上傳*StorageAccount_PageBlob*資料夾。
- 如果將資料上傳至 Azure 時發生任何錯誤，則會在目標儲存體帳戶中建立錯誤記錄。 在上傳完成後，系統會提供此錯誤記錄的路徑，而您可以檢閱記錄以執行更正動作。 請勿在未驗證上傳資料的情況下，從來源刪除資料。
- 將資料上傳至 Azure 檔案儲存體時，不會保留檔案中繼資料和 NTFS 許可權。 例如，複製資料時，不會保留檔案的*最後修改屬性*。
