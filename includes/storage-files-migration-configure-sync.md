---
title: 設定 Azure 檔案同步
description: 設定 Azure 檔案同步。共通的文字區塊，會跨遷移檔共用。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 562099c65273cad50596268b89055ec78d92d18a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82143566"
---
此步驟會將您在先前步驟中已在 Windows Server 實例上設定的所有資源和資料夾結合在一起。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 找出您的儲存體同步服務資源。
1. 在儲存體同步服務資源中，為每個 Azure 檔案共用建立新的 *同步群組* 。 在 Azure 檔案同步術語中，Azure 檔案共用會成為您在建立同步處理群組時所描述的同步拓撲中的 *雲端端點* 。 當您建立同步群組時，請為它提供一個熟悉的名稱，讓您辨識在此同步處理的檔案集合。 請確定您參考了具有相符名稱的 Azure 檔案共用。
1. 建立同步群組之後，同步處理群組的清單中會出現一個資料列。 選取連結)  (名稱，以顯示同步處理群組的內容。 您將會在 **雲端端點**下看到您的 Azure 檔案共用。
1. 找出 [ **+ 新增伺服器端點** ] 按鈕。 您已布建之本機伺服器上的資料夾將會成為此 *伺服器端點*的路徑。
