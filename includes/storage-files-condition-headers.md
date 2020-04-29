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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "70737486"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>從瀏覽器使用 Azure 檔案儲存體從 Web 應用程式 ConditionHeadersNotSupported 時發生錯誤

透過使用條件式標頭的應用程式（例如網頁瀏覽器）存取裝載于 Azure 檔案儲存體中的內容時，會發生 ConditionHeadersNotSupported 錯誤。 錯誤指出不支援條件標頭。

![Azure 檔案儲存體條件式標頭錯誤](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>原因

尚未支援條件式標頭。 執行它們的應用程式必須在每次存取檔案時要求完整檔案。

### <a name="workaround"></a>因應措施

上傳新的檔案時，[快取控制項] 屬性預設為 [無快取]。 若要強制應用程式每次都要求檔案，檔案的快取控制屬性必須從「無快取」更新為「無快取，不需要重新驗證」。 這可以使用[Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)來達成。

![Azure 檔案儲存體條件式標頭的儲存體 explorer 內容快取修改](media/storage-files-condition-headers/storage-explorer-cache.png)