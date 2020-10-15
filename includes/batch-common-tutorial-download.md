---
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 11/09/2018
ms.author: lahugh
ms.openlocfilehash: bd51eabcff412de4928c682683b2a69b3e82e601
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "67174329"
---
### <a name="retrieve-output-files"></a>擷取輸出檔案

您可以使用 Azure 入口網站來下載 ffmpeg 工作所產生的 MP3 輸出檔案。 

1. 按一下 [所有服務]   > [儲存體帳戶]  ，然後按一下您的儲存體帳戶名稱。
2. 按一下 [Blob]   > [輸出]  。
3. 以滑鼠右鍵按一下其中一個 MP3 輸出檔案，然後按 [下載]  。 請依照瀏覽器的提示開啟或儲存檔案。

![下載輸出檔案](./media/batch-common-tutorial-download/download.png)

此範例雖未示範，但您也可以用程式設計方式從計算節點或儲存體容器下載檔案。
