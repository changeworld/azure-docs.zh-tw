---
title: 設定 Azure 檔案同步
description: 設定 Azure 檔案同步。共同的文字區塊，在遷移檔之間共用。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 53d7af6b43ff24ab12501570385162759f4c7633
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209580"
---
這個步驟會將您在先前步驟中已在 Windows Server 上設定的所有資源和資料夾結合在一起。

* 登入 [Azure 入口網站](https://portal.azure.com)。
* 找出您的儲存體同步服務資源。
* 在儲存體同步服務資源內，為每個 Azure 檔案共用建立新的*同步群組*。 在 Azure 檔案同步的術語中，Azure 檔案共用會成為您在建立同步處理群組時所描述的同步拓撲中的*雲端端點*。 當您建立同步群組時，請為它提供一個熟悉的名稱，讓您能夠辨識這裡同步處理哪一組檔案。 請確定您使用相符的名稱來參考 Azure 檔案共用。
* 建立同步群組後，您會看到它出現在同步群組清單中的資料列。 按一下 [名稱] （連結）以顯示同步處理群組的內容。 您會在 [雲端端點] 底下看到您的 Azure 檔案共用。
* 找出 [ *+ 新增伺服器端點*] 命令按鈕。 您已布建之本機伺服器上的資料夾，將成為此*伺服器端點*的路徑。
