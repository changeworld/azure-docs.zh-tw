---
title: Team Data Science Process 角色和工作
description: 資料科學群組的主要元件、人員角色和相關工作的大綱。
author: marktab
manager: marktab
editor: marktab
services: machine-learning
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c1ed731943abf0efdd99ea54d2318fa402835e08
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76720005"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Team Data Science Process 角色和工作

Team 資料科學程式（TDSP）是由 Microsoft 開發的架構，可提供結構化的方法來有效率地建立預測性分析解決方案和智慧型應用程式。 本文概述資料科學小組在此程式上標準化的重要人員角色和相關工作。

這篇簡介文章會連結到如何設定 TDSP 環境的教學課程。 這些教學課程提供使用 Azure DevOps Projects、Azure Repos 存放庫和 Azure Boards 的詳細指引。  其動機目標是透過模型化和部署，從概念中移出。

教學課程會使用 Azure DevOps，因為這是在 Microsoft 執行 TDSP 的方式。 Azure DevOps 藉由整合以角色為基礎的安全性、工作專案管理和追蹤，以及程式碼裝載、共用和原始檔控制，來促進共同作業。 本教學課程也會使用 Azure[資料科學虛擬機器](https://aka.ms/dsvm)（DSVM）作為分析桌面，其中已預先設定數個熱門的資料科學工具，並與 Microsoft 軟體和 Azure 服務整合。 

您可以使用教學課程，利用其他程式碼裝載、agile 規劃和開發工具和環境來執行 TDSP，但是有些功能可能無法使用。

## <a name="structure-of-data-science-groups-and-teams"></a>資料科學群組和小組的結構

企業中的資料科學函數通常會組織成下列階層：

- 資料科學群組
  - 群組內的資料科學小組

在這種結構中，有群組組長和小組負責人。 資料科學專案通常是由資料科學小組所完成。 資料科學小組具有專案管理和治理工作的專案組長，以及個別的資料科學家和工程師來執行專案的資料科學和資料工程部分。 初始專案設定和控管是由群組、小組或專案負責人進行。

## <a name="definition-and-tasks-for-the-four-tdsp-roles"></a>四個 TDSP 角色的定義和工作
假設資料科學單位是由群組內的小組組成，TDSP 人員有四個不同的角色：

1. **群組管理員**：管理企業中的整個資料科學單位。 資料科學單位可能有多個小組，而每個小組負責不同商業垂直市場中的多個資料科學專案。 團隊管理員可能會將他們的工作委派給代理人，但是與角色相關的工作會改變。
   
2. **小組負責人**：管理企業資料科學單位中的小組。 小組是由多個資料科學家所組成。 針對小型資料科學單位，群組管理員和小組負責人可能是同一人。
   
3. **專案組長**：管理特定資料科學專案上個別資料科學家的每日活動。
   
4. **Project 個別參與者**：資料科學家、商務分析師、資料工程師、架構設計人員，以及執行資料科學專案的其他人。

> [!NOTE]
> 根據企業的結構和大小而定，一個人可能扮演一個以上的角色，或一個以上的人可能會填滿一個角色。

### <a name="tasks-to-be-completed-by-the-four-roles"></a>由四個角色完成的工作

下圖顯示每個 Team 資料科學流程角色的最上層工作。 針對每個 TDSP 角色，此架構和下列更詳細的工作概述，可協助您根據您的責任選擇所需的教學課程。

![角色和工作概觀](./media/roles-tasks/overview-tdsp-top-level.png)

## <a name="group-manager-tasks"></a>群組管理員工作

群組管理員或指定的 TDSP 系統管理員會完成下列工作以採用 TDSP：

- 在組織內建立 Azure DevOps**組織**和群組專案。 
- 在 Azure DevOps 群組專案中建立**專案範本存放庫**，並從 Microsoft TDSP 小組所開發的專案範本存放庫植入。 Microsoft TDSP 專案範本存放庫提供：
  - **標準化目錄結構**，包括資料、程式碼和檔的目錄。
  - 一組**標準化的檔範本**，可引導有效率的資料科學程式。
- 建立**公用程式存放庫**，並從 Microsoft TDSP 小組所開發的公用程式存放庫植入。 Microsoft 的 TDSP 公用程式存放庫提供一組實用的公用程式，讓資料科學家的工作更有效率。 Microsoft 公用程式存放庫包含互動式資料探索、分析、報告和基準模型化和報告的公用程式。
- 設定組織帳戶的**安全性控制原則**。

如需詳細指示，請參閱[資料科學小組的群組管理員](group-manager-tasks.md)工作。

## <a name="team-lead-tasks"></a>小組負責人工作

小組負責人或指定的專案系統管理員完成下列工作，以採用 TDSP：

- 在群組的 Azure DevOps 組織中建立 team**專案**。
- 在專案中建立**專案範本存放庫**，並從群組管理員或委派所設定的群組專案範本存放庫植入。
- 建立**小組公用程式存放庫**，並從群組公用程式存放庫植入，然後將小組特有的公用程式新增至存放庫。
- 選擇性地建立[Azure 檔案儲存體](https://azure.microsoft.com/services/storage/files/)，為小組儲存有用的資料資產。 其他小組成員可以在其分析桌面上掛接此共用雲端檔案存放區。
- 選擇性地在小組的**DSVM**上掛接 Azure 檔案儲存體，並在其中新增小組資料資產。
- 藉由新增小組成員和設定其許可權來設定**安全性控制**。

如需詳細指示，請參閱[資料科學小組的小組負責人](team-lead-tasks.md)工作。


## <a name="project-lead-tasks"></a>專案負責人工作

專案負責人會完成下列工作以採用 TDSP：

- 在 team 專案中建立**專案存放庫**，並從專案範本存放庫植入。
- 選擇性地建立**Azure 檔案儲存體**來儲存專案的資料資產。
- 選擇性地將 Azure 檔案儲存體掛接至**DSVM** ，並在其中新增專案資料資產。
- 藉由新增專案成員和設定其許可權來設定**安全性控制**。

如需詳細指示，請參閱[資料科學小組的專案負責人](project-lead-tasks.md)工作。

## <a name="project-individual-contributor-tasks"></a>專案個別參與者工作

專案個別參與者（通常是資料科學家）會使用 TDSP 來執行下列工作：

- 複製專案負責人所設定的**專案存放庫**。
- 選擇性地將共用的小組和專案**Azure 檔案儲存體**掛接到其**資料科學虛擬機器**（DSVM）。
- 執行專案。

如需在專案上架的詳細指示，請參閱[資料科學小組的專案個別參與者](project-ic-tasks.md)工作。

## <a name="data-science-project-execution-workflow"></a>資料科學專案執行工作流程

藉由遵循相關的教學課程，資料科學家、專案組長和小組主管可以建立工作專案，以追蹤專案從開始到結束的所有工作和階段。 使用 Azure Repos 提升資料科學家之間的共同作業，並確保在專案執行期間產生的成品是由所有專案成員控制和共用的版本。 Azure DevOps 可讓您將 Azure Boards 工作專案與 Azure Repos 存放庫分支連結，並輕鬆地追蹤工作專案已完成的作業。

下圖概述專案執行的 TDSP 工作流程：

![一般資料科學專案工作流程](./media/roles-tasks/overview-project-execute.png)

工作流程步驟可分為三個活動：

- 專案組長進行短期衝刺計畫
- 資料科學家在分支上開發 `git` 成品以解決工作專案
- 專案負責人或其他小組成員執行程式碼審查，並將工作分支合併到主要分支

如需專案執行工作流程的詳細指示，請參閱[資料科學專案的敏捷式開發](agile-development.md)。

## <a name="tdsp-project-template-repository"></a>TDSP 專案範本存放庫

使用 Microsoft TDSP 小組的[專案範本存放庫](https://github.com/Azure/Azure-TDSP-ProjectTemplate)來支援有效率的專案執行和共同作業。 存放庫提供您可用於自己的 TDSP 專案的標準化目錄結構和檔範本。

## <a name="next-steps"></a>後續步驟

深入探索 Team Data Science Process 定義的角色和工作描述：

- [資料科學小組的群組管理員工作](group-manager-tasks.md)
- [資料科學小組的小組負責人工作](team-lead-tasks.md)
- [資料科學小組的專案負責人工作](project-lead-tasks.md)
- [資料科學小組的專案個別參與者工作](project-ic-tasks.md)
