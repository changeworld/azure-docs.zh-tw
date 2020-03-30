---
title: 使用 GitHub 和 Azure 開發人員在雲中託管移動應用程式原始程式碼
description: 瞭解使用 Microsoft 服務在雲中託管移動應用程式代碼的服務。
author: codemillmatt
ms.assetid: 12a8a079-9b3c-4faf-2222-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 66d8980ab53010af0703d789fbe791c60a32052d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240967"
---
# <a name="cloud-hosted-source-code-management-services"></a>雲託管原始程式碼管理服務
如果您有多個團隊成員在同一代碼庫上工作的開發團隊，則需要找到合適的位置來託管代碼。 將資料存儲在雲中並具有集中式存儲庫，允許每個人上載、編輯和管理代碼檔。 他們還可以與專案上的其他開發人員進行交互。 無論您身在何處，只要您有互聯網連接，代碼都可以輕鬆訪問。

雲託管比本地選項容易得多。 它要求更少的硬體設定，並允許組織以更敏捷的方式完成實現過程。

## <a name="benefits-of-hosting-source-code-in-the-cloud"></a>在雲中託管原始程式碼的好處
- **集中式雲存儲：** 隨時隨地查看和管理資料。
- **更好的協作和更簡潔的代碼：** 跟蹤團隊內的代碼並管理專案，以確保持續交付出色的軟體。
- **更容易參與：** 輕鬆為專案做出貢獻。
- **更快的發佈週期：** 在團隊中更快地工作，輕鬆為專案做出貢獻。
- **降低成本：** 不要擔心維護自己的硬體、伺服器、VPN 等。

使用以下服務在雲中託管應用程式資料。

## <a name="github"></a>GitHub
[GitHub](https://github.com/)是一個開源存儲庫託管服務，它託管各種不同程式設計語言的原始程式碼專案。 GitHub 跟蹤對每次反覆運算所做的各種更改。

**主要功能**
- 使用代碼託管將所有代碼保存在一個位置。 私有、公共和開源存儲庫都配備了工具，可説明您託管、版本和發佈代碼。
- 審核代碼並使用內置審閱工具使代碼審閱成為任何團隊流程的重要組成部分：
    - 保護分支、建議更改和請求審核。
    - 發現差異，在上下文中發表評論，並獲得明確的回饋。
- 儘早協調，保持一致，並借助 GitHub 的專案管理工具完成更多工作：
    - 查看專案的大圖景。
    - 使用 GitHub 中代碼旁邊的任務板。
    - 拖動卡片以將問題或拉取請求分配給團隊成員。
    - 設置里程碑以組織和跟蹤進度。
    - 編寫筆記以捕獲可能有用的但不屬於特定問題或拉取請求的想法。
- 通過從[GitHub 應用商店](https://github.com/marketplace)購買應用程式，輕鬆發現並選擇正確的工具，以更好地溝通和自動化工作。
- 使用： 
    - 説明組織團隊和存取權限的使用者角色。
    - 討論執行緒工具，使討論主題和團隊重點。
    - 社區指南，以快速設置新的團隊成員與帳戶。
- 流覽和明星流行的專案，以遵循他們。
- 網路，向業內其他人學習。

**引用**
- [GitHub](https://github.com/)
- [GitHub 指南](https://guides.github.com/)
- [GitHub 社區論壇](https://github.community/)
- [GitHub 市場](https://github.com/marketplace)

## <a name="azure-devops"></a>Azure DevOps
[Azure DevOps](https://azure.microsoft.com/services/devops/)支援[Azure 存儲庫](https://azure.microsoft.com/services/devops/repos/)和[團隊基礎版本控制 （TFVC）](https://docs.microsoft.com/azure/devops/repos/tfvc/index?view=azure-devops)作為原始程式碼管理選項。 它有無限的免費私人存儲庫，具有協作代碼審查、高級檔管理、代碼搜索和分支策略，以確保高品質的代碼。 Azure Repos 非常適合需要本機 Azure 活動目錄支援和高級策略的小型專案和大型組織。
    
**主要功能**
- 使用無限制的雲託管 Git 原始程式碼存儲庫進行公共和私有存儲庫：
    - 獲取對任何 Git 用戶端的支援。
    - 使用 Web 掛鉤和 API 集成。
- 從回購拉取請求啟動下一個生成：
    - 協作，通過為每個更改使用執行緒討論和持續集成來構建更好的代碼。
    - 設置連續集成/連續交付 （CI/CD），以便根據每個已完成的拉取請求自動觸發生成、測試和部署。 您可以使用 Azure 管道或工具。
    - 使用分支策略保護您的代碼品質。
- 使用團隊基礎版本控制（包括代碼評審）維護集中式版本控制。
- 使用 Xcode、Eclipse、IntelliJ、Android 工作室、視覺工作室、視覺工作室代碼等連接到您的代碼。
- 使用強大的語義代碼搜索。
- 從企業就緒功能中獲益。 Azure DevOps 與 Azure 活動目錄具有本機集成，這簡化了管理對代碼存儲庫的訪問的過程。
- 通過分支策略確保代碼品質，例如代碼評審的最小次數、成功生成的要求以及 Git 合併策略的實現。
- 連接您最喜愛的開發環境，以訪問存儲庫和管理工作。

**引用**
- [開始使用 Azure 存儲庫](https://azure.microsoft.com/services/devops/repos/) 
- [Azure 重新生成文檔](/azure/devops/repos/?view=azure-devops)