---
title: 專案聲學 常見問題
titlesuffix: Azure Cognitive Services
description: 本頁面提供 Project Acoustics 相關常見問題的解答，其中包括下載指示和製作流程。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fa4b6499260219b0eb8ea4df4b4ccfd5263b57bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75770198"
---
# <a name="project-acoustics-frequently-asked-questions"></a>專案聲學 常見問題

## <a name="what-is-project-acoustics"></a>何謂 Project Acoustics？

專案聲學外掛程式套件是一個聲學系統，用於在運行時之前計算聲波行為，類似于靜態照明。 雲端可執行繁重的波動物理學計算，因此執行階段 CPU 成本很低。  

## <a name="where-can-i-download-the-plugin"></a>我可以從哪裡下載外掛程式？

您可以下載[專案聲學統一外掛程式](https://www.microsoft.com/download/details.aspx?id=57346)或[專案聲學虛幻外掛程式](https://www.microsoft.com/download/details.aspx?id=58090)。

## <a name="does-project-acoustics-support-ltxgt-platform"></a>專案聲學是否支援&lt;x&gt;平臺？

專案聲學平臺支援根據客戶需求而發展。 請通過[專案聲學問題論壇](https://github.com/microsoft/ProjectAcoustics/issues)與我們聯繫，詢問對其他平臺的支援。

## <a name="is-azure-used-at-runtime"></a>在執行階段是否會使用 Azure？

否，只有在場景設定的計算前階段期間，才會使用雲端整合。
 
## <a name="what-is-simulation-input"></a>什麼是模擬輸入？ 

模擬輸入係指您的 3D 場景、虛擬環境或遊戲關卡。 Project Acoustics 會執行可仔細建立聲音物理特性模型的 3D 體積波動模擬，包括平滑的遮蔽和散射。
 
## <a name="what-is-the-runtime-cost"></a>有哪些執行階段成本？

聲場的每一畫面每一來源大約會耗用 0.01% 的 CPU。 RAM 使用量則取決於場景大小，範圍從 10 到 100 MB。
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>我是否需要簡化關卡幾何？ 控制三角面計數？ 讓網格防水？

否。 此系統會直接內嵌詳細的關卡幾何。 系統會將其體素化來進行內部處理。
 
## <a name="whats-in-the-runtime-lookup-table"></a>什麼是執行階段查閱資料表？

ACE 檔包括一個多個源和攔截器位置對之間的聲學參數表，以及用於參數插值的體素化場景幾何體。
 
## <a name="can-project-acoustics-handle-moving-sources"></a>專案聲學可以處理移動源嗎？

是的，Project 聲學會查閱查閱資料表並更新每個刻度上的音訊 DSP，以便它可以處理移動源和攔截器。
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>專案聲學可以處理動態幾何？ 關閉中的門？ 被吹走的牆面？

否。 聲場參數是根據遊戲關卡的靜態狀態預先計算的。 我們建議將門幾何體排除在聲學外，然後根據可破壞和可移動的遊戲物件的狀態使用已建立的技術應用額外的遮擋。
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>專案聲學是否使用聲學材料？

是。 它會從您關卡中的實體材質名稱挑選材質來決定吸收性。
 
## <a name="what-do-the-probes-represent"></a>「探查」代表什麼？

探查是針對可能之玩家位置進行的取樣。 每個探查代表一個源自探查位置的個別場景波動模擬。 在執行階段，會從附近的探查位置插補聆聽者位置的聲場參數。
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>為什麼要在雲端進行那麼多的計算？ 它能為我提供什麼？

Project Acoustics 會將每個架構層面納入考量，即使針對極複雜的虛擬環境，也能提供既準確又可靠的聲場參數。 它提供了平滑的遮擋和阻塞和動態混響變化，而無需繪圖卷的手動工作。 所有這些都可在執行階段減輕 CPU 的負擔。

## <a name="what-exactly-happens-during-baking"></a>進行「製作」時，實際會發生什麼事？

烘焙由以每個攔截器探測器為中心的立體類比區域的聲波類比組成。

## <a name="is-my-source-content-secure"></a>我的源內容是否安全？

專案聲學不會將源場景幾何體上載到雲。 相反，類比在場景的體素化上運行，該體化與探測位置資料結合使用，並以專有格式存儲。     

## <a name="next-steps"></a>後續步驟
* 嘗試[專案聲學統一示例內容](unity-quickstart.md)或[虛幻示例內容](unreal-quickstart.md)

