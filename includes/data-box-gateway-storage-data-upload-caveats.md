---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 385ebffb4780543b532c81b89f1847f5c84cd0ac
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581763"
---
下列注意事項適用於正移至 Azure 的資料。

- 我們建議不要將多個裝置寫入至相同的容器。
- 如果您有現有的 Azure 物件 (例如 blob 或雲端中的檔案) 與所複製的物件名稱相同，則裝置將會覆寫雲端中的檔案。
- 在共用資料夾下建立的空目錄階層 (不含任何檔案) 則不會上傳至 Blob 容器。
- 您可以使用拖放檔案總管或透過命令列來複製資料。 如果要複製的檔案匯總大小超過 10 GB，建議您使用大量複製程式，例如 `Robocopy` 或 `rsync` 。 大量複製工具會重試複製作業的間歇性錯誤，並提供額外的復原能力。
- 如果與 Azure 儲存體容器相關的共用上傳不符合於建立時針對共用所定義之 Blob 類型的 Blob，則這類 Blob 將不會被更新。 例如，如果您在裝置上建立區塊 blob 共用，請將共用關聯至具有分頁 blob 的現有雲端容器、重新整理該共用以下載檔案，然後修改已在雲端中儲存為分頁 blob 的一些已重新整理的檔案，您將會看到上傳失敗。
- 在共用中建立檔案之後，不支援重新命名檔案。
- 從共用中刪除檔案並不會刪除儲存體帳戶中的項目。
- 如果使用 `rsync` 複製資料，則不支援 `rsync -a` 選項。