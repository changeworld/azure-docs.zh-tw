---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/21/2019
ms.author: alkohli
ms.openlocfilehash: 8c87e14071b3bb40421ab655c172df739570e295
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174434"
---
下列注意事項適用於正移至 Azure 的資料。

- 我們建議不要將多個裝置寫入至相同的容器。
- 如果雲端中現有的 Azure 物件 (例如 Blob 或檔案) 與要複製的物件同名，裝置將會覆寫雲端中的檔案。
- 在共用資料夾下建立的空目錄階層 (不含任何檔案) 則不會上傳至 Blob 容器。
- 您可以使用檔資源管理器或通過命令列使用拖放來複製資料。 如果複製檔的聚合大小大於 10 GB，我們建議您使用批量複製程式，如 Robocopy 或 rsync。 批量複製工具重試複製操作，以進行間歇性錯誤，並提供額外的恢復能力。
- 如果與 Azure 儲存體容器相關的共用上傳不符合於建立時針對共用所定義之 Blob 類型的 Blob，則這類 Blob 將不會被更新。 例如，您在裝置上建立區塊 Blob 共用。 將該共用與具有分頁 Blob 的現有雲端容器相關聯。 重新整理該共用以下載檔案。 修改部分已重新整理，且已經在雲端中儲存為分頁 Blob 的檔案。 您將會看見上傳失敗。
