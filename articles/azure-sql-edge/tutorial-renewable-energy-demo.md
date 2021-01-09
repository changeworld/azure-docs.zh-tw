---
title: 在 Contoso 風力發電廠的渦輪機上部署 Azure SQL Edge
description: 在本教學課程中，您會在 Contoso 風力發電廠的渦輪機上使用 Azure SQL Edge 進行喚醒偵測。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 12/18/2020
ms.openlocfilehash: e966d756744210dc8f6739b96501dd91f0d8d1b5
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2020
ms.locfileid: "97723468"
---
# <a name="using-azure-sql-edge-to-build-smarter-renewable-resources"></a>使用 Azure SQL Edge 建置更聰明的可續用資源

此 Azure SQL Edge 示範是以 Contoso Renewable Energy 為基礎，這是使用 SQL DB Edge 進行資料處理上架的風力發電廠。 

此示範會帶您了解如何解決因為在裝置上偵測到紊流而引發的警示。 您會將模型定型並部署到 SQL DB Edge，以更正偵測到的風力喚醒資料，以達到將電源輸出最佳化的目的。

Azure SQL Edge - Channel 9 上的可續用能源示範影片：
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Edge-Demo-Renewable-Energy/player]

## <a name="setting-up-the-demo-on-your-local-computer"></a>在您的本機電腦上設定示範
您會使用 Git 將示範中的所有檔案複製到您的本機電腦。 

1. 請從[這裡](https://git-scm.com/download)安裝 Git。
2. 開啟命令提示字元，並瀏覽至應該下載存放庫的資料夾。 
3. 發出命令 https://github.com/microsoft/sql-server-samples.git。
4. 在複製存放庫的位置中，瀏覽至 **'sql-server-samples\samples\demos\azure-sql-edge-demos\Wind Turbine Demo'** 。
5. 遵循 README.md 中的指示來設定示範環境，並執行示範。