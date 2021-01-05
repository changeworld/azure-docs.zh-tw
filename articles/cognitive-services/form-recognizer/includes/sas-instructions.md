---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 3f4272f5445c8530e97726bd4fdca6bf662719c2
ms.sourcegitcommit: 02ed9acd4390b86c8432cad29075e2204f6b1bc3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/29/2020
ms.locfileid: "97807802"
---
若要擷取自訂模型訓練資料的 SAS URL，請移至 Azure 入口網站中的儲存體資源，然後選取 [儲存體總管] 索引標籤。導覽至您的容器、按一下滑鼠右鍵，然後選取 [取得共用存取簽章]。 務必取得您容器的 SAS，而不是儲存體帳戶本身的 SAS。 確定 [讀取] 和 [列出] 權限均已勾選，再按一下 [建立]。 然後，將 [URL] 區段中的值複製到暫存位置。 其格式應該為：`https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`。