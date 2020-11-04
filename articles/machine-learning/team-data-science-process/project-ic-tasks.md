---
title: Team Data Science Process 中個別參與者的工作
description: 資料科學 team 專案上個別參與者工作的詳細逐步解說。
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4ecb5fef9c9b14bde72de29a45e29d7e16131bd1
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321917"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Team Data Science Process 中個別參與者的工作

本主題將概述 *個別參與者* 完成的工作，以在 [Team Data 科學流程](overview.md) 中設定專案 (TDSP) 。 目標是在 TDSP 上標準化的共同作業小組環境中工作。 TDSP 的設計目的是要協助改善共同作業和團隊學習。 如需人員角色及其相關工作（由資料科學小組在 TDSP 上標準化）的大綱，請參閱 [Team Data 科學流程角色和](roles-tasks.md)工作。

下圖顯示專案個別參與者 (資料科學家) 完成以設定其團隊環境的工作。 如需有關如何在 TDSP 下執行資料科學專案的指示，請參閱 [執行資料科學專案](./agile-development.md)。 

![個別參與者工作](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **ProjectRepository** 是您的專案小組負責共用專案範本和資產的儲存機制。
- **>teamutilities** 是您的小組特別為您的小組維護的公用程式存放庫。 
- **GroupUtilities** 是您的群組所維護的儲存機制，可在整個群組之間共用有用的公用程式。 

> [!NOTE] 
> 本文使用 Azure Repos 和資料科學虛擬機器 (DSVM) 設定 TDSP 環境，因為這是如何在 Microsoft 執行 TDSP 的方式。 如果您的小組使用其他程式碼裝載或開發平臺，個別的參與者工作也相同，但是完成這些工作的方式可能會不同。

## <a name="prerequisites"></a>必要條件

本教學課程假設您的 [群組管理員](group-manager-tasks.md)、 [小組負責人](team-lead-tasks.md)和 [專案負責人](project-lead-tasks.md)已設定下列資源和許可權：

- 適用于您資料科學單位的 Azure DevOps **組織**
- 專案負責人設定的 **專案存放庫** ，可共用專案範本和資產
- 由群組管理員和小組負責人設定的 **GroupUtilities** 和 **>teamutilities** 存放庫（如果適用）
- 為您的小組或專案設定共用資產的 Azure 檔案 **儲存體** （如果適用）
- 您要複製並推送回專案儲存機制的 **許可權** 

若要複製存放庫並修改本機電腦或 DSVM 上的內容，或將 Azure 檔案儲存體掛接到您的 DSVM，您必須考慮此檢查清單：

- Azure 訂用帳戶。
- 在您的電腦上安裝 Git。 如果您是使用 DSVM，則會預先安裝 Git。 否則，請參閱[平台和工具附錄](platforms-and-tools.md#appendix)。
- 如果您想要使用 DSVM，則會在 Azure 中建立並設定 Windows 或 Linux DSVM。 如需詳細資訊和指示，請參閱 [資料科學虛擬機器檔](../data-science-virtual-machine/index.yml)。
- 若為 Windows DSVM，則會在您的電腦上安裝 [Git 認證管理員 (GCM) ](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) 。 在 *README.md* 檔案中，向下卷到 [ **下載並安裝** ] 區段，然後選取 **最新的安裝程式** 。 從安裝程式頁面下載 *.exe* 安裝程式，然後執行它。 
- 針對 Linux DSVM，會在您的 DSVM 上設定 SSH 公開金鑰，並將其新增至 Azure DevOps。 如需詳細資訊和指示，請參閱 [平臺和工具附錄](platforms-and-tools.md#appendix)中的 **建立 SSH 公開金鑰** 一節。 
- 您需要掛接至 DSVM 之任何 Azure 檔案儲存體的 Azure 檔案儲存體資訊。 

## <a name="clone-repositories"></a>複製存放庫

若要在本機使用存放庫，並將您的變更推送至共用的小組和專案存放庫，您必須先將存放庫 *複製或複製* 到本機電腦。 

1. 在 Azure DevOps 中，移至您的小組專案摘要頁面 *： HTTPs \/ / \<server name> / \<organization name> / \<team name> ：* ，例如 **HTTPs： \/ /dev.azure.com/DataScienceUnit/MyTeam** 。
   
1. 選取左側導覽列中的 [ **存放庫** ]，然後在頁面頂端選取您要複製的儲存機制。
   
1. 在 [存放庫] 頁面上，選取右上方的 [ **複製** ]。
   
1. 在 [ **複製存放庫** ] 對話方塊中，針對 HTTP 連線選取 **HTTPS** ，或針對 Ssh **連線** 選取 [ **SSH** ]，並將 [複製 URL] 下的 [複製 URL] 複製到剪貼簿。
   
   ![複製存放庫](./media/project-ic-tasks/clone.png)
   
1. 在您的本機電腦或 DSVM 上，建立下列目錄：
   
   - 針對 Windows： **C:\GitRepos**
   - 針對 Linux： **$home/gitrepos**
   
1. 變更至您所建立的目錄。
   
1. 在 Git Bash 中， `git clone <clone URL>` 針對您要複製的每個存放庫執行命令。 
   
   例如，下列命令會將 **>teamutilities** 儲存機制複製到本機電腦上的 *>myteam* 目錄。 
   
   **HTTPS 連接：**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH 連接：**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```
   
1. 確認您可以在本機專案目錄中看到複製之存放庫的資料夾。
   
   ![三個本機存放庫資料夾](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="mount-azure-file-storage-to-your-dsvm"></a>將 Azure 檔案儲存體掛接到您的 DSVM

如果您的小組或專案在 Azure 檔案儲存體中有共用的資產，請將檔案儲存體掛接到您的本機電腦或 DSVM。 遵循在 [本機電腦或 DSVM 上掛接 Azure 檔案儲存體](team-lead-tasks.md#mount-azure-file-storage-on-your-local-machine-or-dsvm)的指示。

## <a name="next-steps"></a>後續步驟

以下是 Team Data 科學程式所定義的其他角色和工作的詳細描述連結：

- [資料科學小組的群組管理員工作](group-manager-tasks.md)
- [資料科學小組的小組負責人工作](team-lead-tasks.md)
- [資料科學小組的專案負責人工作](project-lead-tasks.md)