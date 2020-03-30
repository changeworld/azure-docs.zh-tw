---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: d43b879057001d62ea72bd2e011ad52957d47470
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189004"
---
## <a name="sample-templates"></a>範例範本
您可以在此找到 UI 自訂的範例範本：

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

此專案包含以下範本：
- [海洋藍](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [石板灰色](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

要使用示例：

1. 在本地電腦上克隆回購。 選擇範本資料夾`/ocean_blue`或`/slate_gray`。
1. 將範本資料夾和`/assets`資料夾下的所有檔上載到 Blob 存儲，如前幾節所述。
1. 接下來，打開`\*.html`或`/ocean_blue``/slate_gray`的根中的每個檔，將相對 URL 的所有實例替換為步驟 2 中上載的 css、圖像和字體檔的 URL。 例如：
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    至
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. 保存`\*.html`檔並將其上載到 Blob 存儲。
1. 現在修改策略，指向您的 HTML 檔案，如前所述。
1. 如果看到缺少字體、圖像或 CSS，請在擴展程式和\*.html 檔中檢查引用。
