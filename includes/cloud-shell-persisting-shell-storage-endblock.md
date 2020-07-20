---
author: cynthn
ms.service: azure
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: c2ed33aea77b5478e8d17f6bd0213ef3e778b806
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "67174316"
---
## <a name="transfer-local-files-to-cloud-shell"></a>將本機檔案傳輸至 Cloud Shell
`clouddrive` 目錄會與 Azure 入口網站儲存體刀鋒視窗同步。 使用此刀鋒視窗對檔案共用雙向傳輸本機檔案。 從 Cloud Shell 中更新的檔案，會在您重新整理刀鋒視窗時反映在檔案儲存體 GUI 中。

### <a name="download-files"></a>下載檔案

![本機檔案清單](../articles/cloud-shell/media/persisting-shell-storage/download.png)
1. 在 Azure 入口網站中，移至掛接的檔案共用。
2. 選取目標檔案。
3. 選取 [**下載**] 按鈕。

### <a name="upload-files"></a>上傳檔案

![要上傳的本機檔案](../articles/cloud-shell/media/persisting-shell-storage/upload.png)
1. 移至掛接的檔案共用。
2. 選取 [上傳]**** 按鈕。
3. 選取您要上傳的一或多個檔案。
4. 確認上傳。

您現在應會看到 Cloud Shell 的 `clouddrive` 目錄中可存取的檔案。