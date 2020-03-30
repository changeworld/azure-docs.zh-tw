---
title: 配置 Azure 檔同步
description: 配置 Azure 檔同步。在遷移文檔之間共用的通用文字區塊。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 53d7af6b43ff24ab12501570385162759f4c7633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209580"
---
此步驟將您在之前的步驟中在 Windows Server 上設置的所有資源和資料夾結合在一起。

* 登錄到[Azure 門戶](https://portal.azure.com)。
* 查找存儲同步服務資源。
* 在每個 Azure 檔共用的存儲同步服務資源中創建新*的同步組*。 在 Azure 檔同步術語中，Azure 檔共用將成為您通過創建同步組描述的同步拓撲中的*雲終結點*。 創建同步組時，請為它指定一個熟悉的名稱，以便在此處識別同步的檔集。 請確保引用具有匹配名稱的 Azure 檔共用。
* 創建同步組後，您將看到同步組清單中顯示的行。 按一下名稱（連結）以顯示同步組的內容。 您將在"雲終結點"下看到 Azure 檔共用。
* 找到命令按鈕以 *= 添加伺服器終結點*。 預配的本機伺服器上的資料夾將成為此*伺服器終結點*的路徑。
