---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 8ad682ccd7feb9c7f089e9cf0f066cb1be8c756c
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2020
ms.locfileid: "97505319"
---
若要擷取自訂模型訓練資料的 SAS URL，請移至 Azure 入口網站中的儲存體資源，然後選取 [儲存體總管] 索引標籤。導覽至您的容器、按一下滑鼠右鍵，然後選取 [取得共用存取簽章]。 務必取得您容器的 SAS，而不是儲存體帳戶本身的 SAS。 確定 [讀取] 和 [列出] 權限均已勾選，再按一下 [建立]。 然後，將 [URL] 區段中的值複製到暫存位置。 其格式應該為：`https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`。
   > [!div class="mx-imgBorder"]
   > ![替代文字](../media/quickstarts/get-sas-url.png)