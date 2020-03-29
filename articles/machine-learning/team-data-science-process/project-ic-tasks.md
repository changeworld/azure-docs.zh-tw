---
title: Team Data Science Process 中個別參與者的工作
description: 資料科學團隊專案各個參與者任務的詳細演練。
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d9942c31b63de77196b8b51b88376cb8ef74b990
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721246"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Team Data Science Process 中個別參與者的工作

本主題概述了*單個參與者*完成的任務，以在[團隊資料科學流程](overview.md)（TDSP） 中設置專案。 目標是在協作團隊環境中工作，在 TDSP 上實現標準化。 TDSP 旨在説明改善協作和團隊學習。 有關由在 TDSP 上標準化的資料科學團隊處理的人員角色及其相關任務的概述，請參閱[團隊資料科學流程角色和任務](roles-tasks.md)。

下圖顯示了專案單個貢獻者（資料科學家）完成的任務，以設置其團隊環境。 有關如何在 TDSP 下執行資料科學專案的說明，請參閱[資料科學專案的執行](project-execution.md)。 

![個人參與者任務](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **ProjectRepository**是專案團隊維護的存儲庫，用於共用專案範本和資產。
- **團隊實用程式**是您的團隊專門為您的團隊維護的實用程式存儲庫。 
- **GroupUtilities**是組維護的存儲庫，用於在整個組中共用有用的實用程式。 

> [!NOTE] 
> 本文使用 Azure 存儲庫和資料科學虛擬機器 （DSVM） 來設置 TDSP 環境，因為這是如何在 Microsoft 中實現 TDSP。 如果您的團隊使用其他代碼託管或開發平臺，則各個參與者任務相同，但完成這些任務的方式可能有所不同。

## <a name="prerequisites"></a>Prerequisites

本教程假定以下資源和許可權已由[小組經理](group-manager-tasks.md)、[團隊主管](team-lead-tasks.md)和[專案主管](project-lead-tasks.md)設置：

- 資料科學單位的 Azure DevOps**組織**
- 專案設置**的專案存儲庫**導致共用專案範本和資產
- **集團實用程式**和**團隊實用程式**存儲庫由團隊經理和團隊領導設置（如果適用）
- 為團隊或專案的共用資產設置的 Azure**檔存儲**（如果適用）
- 從中克隆並推送回專案存儲庫**的許可權** 

要克隆存儲庫並修改本地電腦或 DSVM 上的內容，或將 Azure 檔存儲裝載到 DSVM，需要考慮以下檢查表：

- Azure 訂用帳戶。
- Git 安裝在您的電腦上。 如果您使用的是 DSVM，則預先安裝 Git。 否則，請參閱[平台和工具附錄](platforms-and-tools.md#appendix)。
- 如果要使用 DSVM，則在 Azure 中創建和配置 Windows 或 Linux DSVM。 有關詳細資訊和說明，請參閱[資料科學虛擬機器文檔](/azure/machine-learning/data-science-virtual-machine/)。
- 對於 Windows DSVM，在電腦上安裝了[Git 認證管理員 （GCM）。](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) 在*README.md*檔中，向下滾動到 **"下載和安裝**"部分，然後選擇**最新的安裝程式**。 從安裝程式頁面下載 *.exe*安裝程式並運行它。 
- 對於 Linux DSVM，在 DSVM 上設置並在 Azure DevOps 中添加的 SSH 公開金鑰。 有關詳細資訊和說明，請參閱[平臺和工具附錄](platforms-and-tools.md#appendix)中的 **"創建 SSH 公開金鑰**"部分。 
- 需要裝載到 DSVM 的任何 Azure 檔存儲的 Azure 檔存儲資訊。 

## <a name="clone-repositories"></a>克隆存儲庫

要在本地使用存儲庫並將更改推送到共用團隊和專案存儲庫，請首先將存儲庫複製或*克隆*到本地電腦。 

1. 在 Azure DevOps 中，轉到團隊的專案摘要頁面*HTTPs：\//\<伺服器名稱>/\<組織名稱>/\<團隊名稱>，* 例如**HTTPs：\//dev.azure.com/DataScienceUnit/MyTeam**。
   
1. 選擇左側導航中的 **"Repos"，** 在頁面頂部選擇要克隆的存儲庫。
   
1. 在回購頁面上，選擇右上角的 **"克隆**"。
   
1. 在 **"克隆存儲庫"** 對話方塊中，為 HTTP 連接選擇**HTTPS，** 選擇**SSH**連接，並將**命令列**下的克隆 URL 複製到剪貼簿。
   
   ![複製存放庫](./media/project-ic-tasks/clone.png)
   
1. 在本地電腦或 DSVM 上，創建以下目錄：
   
   - 對於視窗 **：C：\GitRepos**
   - 對於**Linux：$home/GitRepos**
   
1. 更改為您創建的目錄。
   
1. 在 Git Bash 中`git clone <clone URL>`，運行要克隆的每個存儲庫的命令。 
   
   例如，以下命令將**TeamUtilities**存儲庫克隆到本地電腦上的*MyTeam*目錄。 
   
   **HTTPS 連接：**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH 連接：**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```
   
1. 確認您可以在本地專案目錄中看到克隆存儲庫的資料夾。
   
   ![三個本機存放區庫資料夾](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="mount-azure-file-storage-to-your-dsvm"></a>將 Azure 檔存儲安裝到 DSVM

如果團隊或專案在 Azure 檔存儲中共用資源，請將檔存儲裝載到本地電腦或 DSVM。 按照[本地電腦或 DSVM 上裝載 Azure 檔存儲](team-lead-tasks.md#mount-azure-file-storage-on-your-local-machine-or-dsvm)的說明進行操作。

## <a name="next-steps"></a>後續步驟

以下是團隊資料科學流程定義的其他角色和任務的詳細描述的連結：

- [資料科學小組的群組管理員工作](group-manager-tasks.md)
- [資料科學小組的小組負責人工作](team-lead-tasks.md)
- [資料科學小組的專案負責人工作](project-lead-tasks.md)

