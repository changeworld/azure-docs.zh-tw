---
title: 使用視覺化工作室應用中心和 Azure 服務自動實現應用的生命週期
description: 瞭解説明為移動應用程式設定連續構建和集成的應用中心等服務。
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 0560f47b832ec2965d9b567e1aeff78baa9c247c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240932"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>通過持續構建和集成實現應用生命週期的自動化

作為開發人員，您編寫代碼並將其簽入代碼存儲庫，但簽入存儲庫中的提交可能並不總是一致的。 當多個開發人員處理同一個專案時，可能會遇到集成問題。 團隊可能會遇到事情不工作、Bug 堆積和專案開發延遲的情況。 開發人員必須等到構建和測試整個軟體代碼以檢查錯誤，這使得過程變慢，反覆運算更少。 

通過持續構建和集成，開發人員可以通過將更改提交到原始程式碼存儲庫並將測試和驗證放入生成環境來簡化生成並測試其代碼。 這樣，他們總是針對代碼運行測試。 每當向存儲庫提交時，對原始程式碼所做的所有更改都會持續生成。 每次簽入時，持續集成 （CI） 伺服器都會驗證並執行開發人員創建的任何測試。 如果測試未通過，代碼將發送回以進行進一步更改。 這樣，開發人員就不會破壞創建的生成。 他們也不必在其電腦上本地運行所有測試，從而提高了開發人員的工作效率。 

## <a name="key-benefits"></a>主要權益
- 自動執行管道的生成、測試和部署。
- 及早檢測 Bug 並修復問題，以確保更快的發佈速度。
- 更頻繁地提交代碼並快速生成應用程式。
- 靈活快速更改代碼，無需任何問題。
- 獲得更快的上市時間，以便只有高品質的代碼才能一直貫穿其中。
- 由於一次集成了小塊代碼，因此可以更高效地進行小代碼更改。
- 提高團隊透明度和問責制，以便不斷獲得客戶和團隊的回饋。

使用以下服務在移動應用中啟用連續集成管道。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[應用中心構建](/appcenter/build/)可説明您使用安全的雲基礎結構構建您的團隊正在處理的本機和跨平臺應用程式。 您可以在 Visual Studio 應用中心輕鬆連接您的回購，並在每次提交時開始在雲中構建應用。 您不必擔心在本地配置生成伺服器、複雜的配置和基於同事的電腦而不是您的代碼的代碼。

借助 Visual Studio 應用中心服務的強大功能，您可以進一步自動化您的工作流程。 您可以使用應用中心分發自動向測試人員和公共應用商店發佈生成。 您還可以通過應用中心測試在雲中的數千個真實設備和作業系統配置上運行自動 UI 測試。

**主要功能**
- 在幾分鐘內設置持續集成，並更頻繁、更快速地構建應用程式。
- 與 GitHub、位存儲桶、Azure 開發人員和 GitLab 集成。
- 在託管的雲託管電腦上創建快速安全的構建。
- 使生成能夠啟動測試，並驗證應用是否在真實 iOS 和 Android 設備中生成。
- 獲得針對 iOS、Android、macOS、Windows、Xamarin 和反應本機的本機和跨平臺支援。
- 通過添加克隆後、預生成和生成後腳本自訂生成。

**引用**
- [使用視覺化工作室應用中心註冊](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [開始使用應用中心生成](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 [Azure 管道](https://azure.microsoft.com/services/devops/pipelines/)是 Azure DevOps 中的一項服務，是一項功能齊全的連續集成和連續傳遞 （CD） 服務，可與首選 Git 提供程式配合使用。 它可以部署到大多數主要雲服務，其中包括 Azure。 您可以在 GitHub、GitHub 企業伺服器、GitLab、Bitbucket 雲或 Azure 存儲庫上使用代碼開始。 然後，您可以將代碼的生成、測試和部署自動化到 Microsoft Azure、Google 雲平臺或亞馬遜 Web 服務 （AWS）。

**主要功能**
- **簡化基於任務的 CI 伺服器設置體驗：** 除了微軟和非微軟（Node.js，JAVA）的伺服器技術外，為本機（安卓、iOS 和 Windows）和跨平臺（Xamarin、Cordova 和 React Native）移動應用程式設定 CI 伺服器。
- **任何語言、平臺和雲：** 構建、測試和部署 Node.js、Python、JAVA、PHP、Ruby、Go、C/C++、C#、Android 和 iOS 應用程式。 在 Linux、macOS 和 Windows 上並行運行。 部署到 Azure、AWS 和 Google 雲平臺等雲供應商。 通過測試版管道和應用商店分發移動應用程式。
- **本機容器支援：** 輕鬆創建新容器，並將它們推送到任何註冊表。 將容器部署到獨立的主機或庫伯奈斯。
- **高級工作流：** 輕鬆創建生成鏈和多階段生成。 獲得對 YAML、測試集成、釋放門、報告等的支援。
- **可擴展：** 使用社區構建的一系列生成、測試和部署任務，其中包括從 Slack 到聲納雲的數百個擴展。 您甚至可以從其他 CI 系統（如 Jenkins）進行部署。 網路掛鉤和 REST API 可以説明您進行集成。
- **免費雲託管版本：** 這些生成可用於公共和私有存儲庫。
- **支援部署到其他雲供應商：** 供應商包括 AWS 和 Google 雲平臺。

**引用**
- [開始使用 Azure 管道指南](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [開始使用 Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
- [快速入門](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

為了説明為應用程式生成選擇正確的服務，請參閱將[應用中心生成與 Azure 管道](/appcenter/build/choose-between-services)進行比較的文章。
