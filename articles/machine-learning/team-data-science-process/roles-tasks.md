---
title: Team Data Science Process 角色和工作
description: 資料科學組的關鍵元件、人員角色和相關任務的大綱。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720005"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Team Data Science Process 角色和工作

團隊資料科學流程 （TDSP） 是 Microsoft 開發的一個框架，它提供了一種結構化方法，用於高效地構建預測分析解決方案和智慧應用程式。 本文概述了資料科學團隊對此過程進行標準化的關鍵人員角色和相關任務。

本介紹性文章連結到有關如何設置 TDSP 環境的教程。 這些教程提供了使用 Azure DevOps 專案、Azure 存儲庫和 Azure 板的詳細指南。  激勵目標是從概念到建模，轉向部署。

教程使用 Azure DevOps，因為這是如何在 Microsoft 中實現 TDSP。 Azure DevOps 通過集成基於角色的安全性、工作項管理和跟蹤以及代碼託管、共用和原始程式碼管理，促進協作。 這些教程還使用 Azure[資料科學虛擬機器](https://aka.ms/dsvm)（DSVM） 作為分析桌面，其中有幾個流行的資料科學工具預先配置並與 Microsoft 軟體和 Azure 服務集成。 

您可以使用教程使用其他代碼託管、敏捷規劃和開發工具和環境來實現 TDSP，但某些功能可能不可用。

## <a name="structure-of-data-science-groups-and-teams"></a>資料科學組和團隊的結構

企業中的資料科學功能通常按以下層次結構組織：

- 資料科學組
  - 團隊內的資料科學團隊

在這樣的結構中，有小組領導和團隊領導。 通常，資料科學專案由資料科學團隊完成。 資料科學團隊擁有專案管理和治理任務的專案主管，以及負責執行專案資料科學和資料工程部分的單個資料科學家和工程師。 初始專案設置和治理由團隊、團隊或專案主管完成。

## <a name="definition-and-tasks-for-the-four-tdsp-roles"></a>四個 TDSP 角色的定義和任務
假設資料科學部門由團隊中的團隊組成，TDSP 人員有四個不同的角色：

1. **集團經理**：管理企業中的全部資料科學單元。 資料科學單位可能有多個小組，而每個小組負責不同商業垂直市場中的多個資料科學專案。 團隊管理員可能會將他們的工作委派給代理人，但是與角色相關的工作會改變。
   
2. **團隊領導**：管理企業資料科學部門的團隊。 小組是由多個資料科學家所組成。 對於小型資料科學部門，團隊經理和團隊領導可能是同一個人。
   
3. **專案負責人**：管理單個資料科學家對特定資料科學專案的日常活動。
   
4. **專案個人貢獻者**：資料科學家、商務分析師、資料工程師、架構師和執行資料科學專案的其他人。

> [!NOTE]
> 根據企業的結構和規模，一個人可以扮演多個角色，或者多人可以擔任一個角色。

### <a name="tasks-to-be-completed-by-the-four-roles"></a>由四個角色完成的任務

下圖顯示了每個團隊資料科學流程角色的頂級任務。 此架構和以下更詳細的每個 TDSP 角色的任務大綱可以説明您根據職責選擇所需的教程。

![角色和工作概觀](./media/roles-tasks/overview-tdsp-top-level.png)

## <a name="group-manager-tasks"></a>群組管理員工作

集團經理或指定的 TDSP 系統管理員完成以下任務以採用 TDSP：

- 在組織內創建 Azure **DevOps**組織和組專案。 
- 在 Azure DevOps 組專案中創建**專案範本存儲庫**，並從 Microsoft TDSP 團隊開發的專案範本存儲庫中獲取種子。 Microsoft TDSP 專案範本存儲庫提供：
  - **標準化的目錄結構**，包括資料、代碼和文檔的目錄。
  - 一組**標準化文件範本**，用於指導高效的資料科學過程。
- 創建**一個實用程式存儲庫**，並從 Microsoft TDSP 團隊開發的實用程式存儲庫中播種它。 來自 Microsoft 的 TDSP 實用程式存儲庫提供了一組有用的實用程式，使資料科學家的工作更加高效。 Microsoft 實用程式存儲庫包括用於互動式資料探索、分析、報告以及基線建模和報告的實用程式。
- 為組織帳戶設置**安全控制策略**。

有關詳細說明，請參閱[資料科學團隊的組經理任務](group-manager-tasks.md)。

## <a name="team-lead-tasks"></a>小組負責人工作

團隊主管或指定的專案管理員完成以下任務以採用 TDSP：

- 在組的 Azure DevOps 組織中創建團隊**專案**。
- 在專案中創建**專案範本存儲庫**，並從組經理或委託設置的組專案範本存儲庫中設置種子。
- 創建**團隊實用程式存儲庫**，從組實用程式存儲庫中種子它，並將特定于團隊的實用程式添加到存儲庫中。
- 可選地創建[Azure 檔存儲](https://azure.microsoft.com/services/storage/files/)以存儲團隊的有用資料資產。 其他小組成員可以在其分析桌面上掛接此共用雲端檔案存放區。
- 可以選擇將 Azure 檔存儲裝載到團隊的**DSVM**上，並將團隊資料資產添加到其中。
- 通過添加團隊成員並配置其許可權來設置**安全控制**。

有關詳細說明，請參閱[資料科學團隊的團隊領導任務](team-lead-tasks.md)。


## <a name="project-lead-tasks"></a>專案負責人工作

專案負責人完成以下任務以採用 TDSP：

- 在團隊專案中創建**專案存儲庫**，並從專案範本存儲庫中播種。
- 可選地創建**Azure 檔存儲**以存儲專案的資料資產。
- 可以選擇將 Azure 檔存儲裝載到**DSVM**並將專案資料資產添加到其中。
- 通過添加專案成員並配置其許可權來設置**安全控制**。

有關詳細說明，請參閱[資料科學團隊的專案主管任務](project-lead-tasks.md)。

## <a name="project-individual-contributor-tasks"></a>專案個別參與者工作

專案個人貢獻者（通常是資料科學家）使用 TDSP 執行以下任務：

- 克隆專案主管設置**的專案存儲庫**。
- 可以選擇將共用團隊和專案 Azure**檔存儲**裝載到其**資料科學虛擬機器**（DSVM） 上。
- 執行專案。

有關載入專案的詳細資訊，請參閱[資料科學團隊的專案個人貢獻者任務](project-ic-tasks.md)。

## <a name="data-science-project-execution-workflow"></a>資料科學專案執行工作流

通過遵循相關教程，資料科學家、專案主管和團隊主管可以創建工作項，從頭到尾跟蹤專案的所有任務和階段。 使用 Azure Repos 可促進資料科學家之間的協作，並確保專案執行期間生成的專案由所有專案成員控制並共用版本。 Azure DevOps 允許您將 Azure 板的工作項與 Azure 存儲庫存儲庫分支連結，並輕鬆跟蹤對工作項已完成的工作項執行哪些操作。

下圖概述了用於專案執行的 TDSP 工作流：

![典型的資料科學專案工作流](./media/roles-tasks/overview-project-execute.png)

工作流步驟可以分為三個活動：

- 專案主管進行衝刺規劃
- 資料科學家在分支上`git`開發工件以解決工作專案
- 專案主管或其他團隊成員執行代碼評審並將工作分支合併到主分支

有關專案執行工作流的詳細說明，請參閱[資料科學專案的敏捷開發](agile-development.md)。

## <a name="tdsp-project-template-repository"></a>TDSP 專案範本存儲庫

使用 Microsoft TDSP 團隊的[專案範本存儲庫](https://github.com/Azure/Azure-TDSP-ProjectTemplate)支援高效的專案執行和協作。 存儲庫為您提供了一個標準化的目錄結構和文件範本，可用於您自己的 TDSP 專案。

## <a name="next-steps"></a>後續步驟

深入探索 Team Data Science Process 定義的角色和工作描述：

- [資料科學小組的群組管理員工作](group-manager-tasks.md)
- [資料科學小組的小組負責人工作](team-lead-tasks.md)
- [資料科學小組的專案負責人工作](project-lead-tasks.md)
- [資料科學團隊的專案個人貢獻者任務](project-ic-tasks.md)
