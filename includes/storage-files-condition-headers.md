---
title: 包含檔案
description: 包含檔案
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4604616cd4f2d6c75c272586df1331fc405061cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "70737486"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>從瀏覽器使用 Azure 檔案儲存體從 Web 應用程式 ConditionHeadersNotSupported 錯誤

當透過使用條件式標頭的應用程式（例如網頁瀏覽器）存取 Azure 檔案儲存體中裝載的內容時，會發生 ConditionHeadersNotSupported 錯誤，存取會失敗。 錯誤指出不支援條件標頭。

![Azure 檔案儲存體條件式標頭錯誤](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>原因

尚不支援條件式標頭。 每次存取檔案時，執行這些應用程式的應用程式都需要要求完整的檔案。

### <a name="workaround"></a>因應措施

上傳新檔案時，快取控制項屬性預設為「無快取」。 若要強制應用程式每次都要求檔案，必須將檔案的快取控制屬性從「無快取」更新為「無快取、無存放區、必須重新驗證」。 您可以使用 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)來達成此目的。

![Azure 檔案儲存體條件式標頭的儲存 explorer 內容快取修改](media/storage-files-condition-headers/storage-explorer-cache.png)