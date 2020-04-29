---
title: 使用 GitHub 和 Azure DevOps 在雲端裝載您的行動應用程式原始程式碼
description: 瞭解使用 Microsoft 服務在雲端中裝載行動應用程式程式碼的服務。
author: codemillmatt
ms.assetid: 12a8a079-9b3c-4faf-2222-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 66d8980ab53010af0703d789fbe791c60a32052d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80240967"
---
# <a name="cloud-hosted-source-code-management-services"></a>雲端託管的原始程式碼管理服務
如果您有多個小組成員的開發團隊在相同的程式碼基底上工作，您需要尋找適當的位置來裝載您的程式碼。 將資料儲存在雲端並擁有集中式存放庫，可讓每個人都能上傳、編輯和管理程式碼檔案。 他們也可以與其他開發人員互動專案。 無論您身在何處，都可以隨時存取程式碼，只要您有網際網路連線即可。

雲端裝載比內部部署選項簡單許多。 它需要較少的硬體設定，並可讓組織以更靈活的方式完成執行程式。

## <a name="benefits-of-hosting-source-code-in-the-cloud"></a>在雲端裝載原始程式碼的優點
- **集中式雲端儲存體：** 從任何地方查看及管理您的資料。
- **更好的共同作業和更清楚的程式碼：** 追蹤小組內的程式碼並管理專案，以確保能持續提供絕佳的軟體。
- **更容易參與：** 輕鬆地參與您的專案。
- **更快速的發行週期：** 更快速地在您的小組中工作，並輕鬆參與您的專案。
- **降低成本：** 別擔心如何維護自己的硬體、伺服器、VPN 等等。

使用下列服務在雲端裝載您的應用程式資料。

## <a name="github"></a>GitHub
[GitHub](https://github.com/)是一個開放原始碼存放庫裝載服務，其裝載各種不同程式設計語言的原始程式碼專案。 GitHub 會持續追蹤對每個反復專案所做的各種變更。

**主要功能**
- 使用程式碼裝載，將您的所有程式碼都放在同一個位置。 私用、公用和開放原始碼存放庫全都具備工具，可協助您裝載、版本和發行程式碼。
- 請參閱程式碼，並使用內建的審核工具，讓程式碼在任何小組的流程中都能進行檢查：
    - 保護分支、建議變更和要求審查。
    - 找出差異、內容中的批註，以及取得清楚的意見反應。
- 使用 GitHub 的專案管理工具，及早協調、保持一致，並完成更多工作：
    - 請參閱專案的大圖片。
    - 使用 GitHub 內您的程式碼旁邊的工作面板。
    - 拖曳卡片來指派問題或提取要求給小組成員。
    - 設定里程碑來組織和追蹤進度。
    - 撰寫記事來捕捉可能有用但不屬於特定問題或提取要求的想法。
- 藉由從[GitHub Marketplace](https://github.com/marketplace)購買應用程式，輕鬆探索及選擇適當的工具以獲得更好的溝通和自動化工作。
- 使用下列方式來管理和拓展小組： 
    - 使用者角色可協助組織小組和存取權限。
    - 討論執行緒工具，可將交談持續放在主題和小組。
    - 使用帳戶快速設定新小組成員的社區指導方針。
- 流覽和星狀熱門專案以遵循。
- 網路，並向產業中的其他人學習。

**參考**
- [GitHub](https://github.com/)
- [GitHub 指南](https://guides.github.com/)
- [GitHub 社區論壇](https://github.community/)
- [GitHub Marketplace](https://github.com/marketplace)

## <a name="azure-devops"></a>Azure DevOps
[Azure DevOps](https://azure.microsoft.com/services/devops/)支援[Azure Repos](https://azure.microsoft.com/services/devops/repos/)和[Team Foundation 版本控制（TFVC）](https://docs.microsoft.com/azure/devops/repos/tfvc/index?view=azure-devops)做為原始檔控制選項。 它有無限制的免費私人存放庫，具有共同作業程式碼審查、先進的檔案管理、程式碼搜尋和分支原則，以確保高品質的程式碼。 Azure Repos 非常適合需要原生 Azure Active Directory 支援和 advanced 原則的小型專案和大型組織。
    
**主要功能**
- 針對您的公用和私用存放庫使用無限制的雲端裝載 Git 原始程式碼存放庫：
    - 取得任何 Git 用戶端的支援。
    - 使用 web 勾點和 API 整合。
- 從存放庫提取要求啟動下一個組建：
    - 使用執行緒討論和每項變更的持續整合，共同作業以建立更好的程式碼。
    - 設定持續整合/持續傳遞（CI/CD），以使用每個完成的提取要求來自動觸發組建、測試和部署。 您可以使用 Azure Pipelines 或您的工具。
    - 使用分支原則來保護您的程式碼品質。
- 使用包含程式碼審查的 Team Foundation 版本控制維護集中式版本控制。
- 使用 Xcode、Eclipse、IntelliJ、Android Studio、Visual Studio、Visual Studio Code 等連接到您的程式碼。
- 使用強大的語義程式碼搜尋。
- 從符合企業需求的功能中獲得好處。 Azure DevOps 與 Azure Active Directory 的原生整合，可簡化管理程式碼存放庫之存取的流程。
- 使用分支原則來確保程式碼品質，例如最少的程式碼審核次數、成功組建的需求，以及強制執行 Git 合併策略。
- 連接您最愛的開發環境，以存取存放庫和管理工作。

**參考**
- [開始使用 Azure Repos](https://azure.microsoft.com/services/devops/repos/) 
- [Azure Repos 檔](/azure/devops/repos/?view=azure-devops)