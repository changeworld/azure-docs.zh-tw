---
title: 設定 Azure 檔案同步
description: 設定 Azure 檔案同步。跨遷移檔共用的通用文字區塊。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 562099c65273cad50596268b89055ec78d92d18a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143566"
---
此步驟會將您在 Windows Server 實例上設定的所有資源和資料夾，結合在先前的步驟中。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 找出您的儲存體同步服務資源。
1. 在儲存體同步服務資源內，為每個 Azure 檔案共用建立新的*同步群組*。 在 Azure 檔案同步的術語中，Azure 檔案共用會成為您在建立同步處理群組時所描述的同步拓撲中的*雲端端點*。 當您建立同步群組時，請為它提供一個熟悉的名稱，讓您能夠辨識在此同步處理哪一組檔案。 請確定您使用相符的名稱來參考 Azure 檔案共用。
1. 建立同步群組之後，它的資料列會出現在同步群組清單中。 選取 [名稱] （連結）以顯示同步處理群組的內容。 您會在 [**雲端端點**] 底下看到您的 Azure 檔案共用。
1. 找出 [ **+ 新增伺服器端點**] 按鈕。 您已布建之本機伺服器上的資料夾將會成為此*伺服器端點*的路徑。
