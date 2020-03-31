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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "70737486"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>使用瀏覽器中 Azure 檔從 Web 應用程式不支援的錯誤條件標題

當通過使用條件標頭（如 Web 瀏覽器）的應用程式訪問 Azure 檔中託管的內容時，將發生條件標題不受支援的錯誤，訪問將失敗。 錯誤指出不支援條件標頭。

![Azure 檔條件標頭錯誤](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>原因

尚不支援條件標頭。 實現它們的應用程式需要在每次訪問該檔時請求完整檔。

### <a name="workaround"></a>因應措施

上載新檔時，緩存控制屬性預設為"無緩存"。 要強制應用程式每次請求該檔，需要將檔的緩存控制屬性從"無緩存"更新為"無緩存、無存儲、必須重新驗證"。 這可以使用 Azure[存儲資源管理器](https://azure.microsoft.com/features/storage-explorer/)來實現。

![存儲資源管理器內容緩存修改 Azure 檔條件標頭](media/storage-files-condition-headers/storage-explorer-cache.png)