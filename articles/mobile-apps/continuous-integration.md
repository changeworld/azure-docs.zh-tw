---
title: 使用 Visual Studio App Center 和 Azure 服務將應用程式的生命週期自動化
description: 深入瞭解可協助為您的行動應用程式設定持續組建和整合的服務（例如 App Center）。
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 0560f47b832ec2965d9b567e1aeff78baa9c247c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80240932"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>使用持續的組建和整合將應用程式的生命週期自動化

身為開發人員，您可以撰寫程式碼，並將它簽入程式碼存放庫，但簽入儲存機制的認可哥能不一定一致。 當多位開發人員處理相同的專案時，可能會有整合的問題。 小組可能會遇到無法運作的情況、bug 堆積，以及專案開發會延遲。 開發人員必須等到整個軟體程式碼建立並經過測試後，才能檢查是否有錯誤，讓程式變慢且更不反復。 

藉由持續組建和整合，開發人員可以藉由認可原始程式碼存放庫的變更，並將測試和驗證放入組建環境中，來簡化組建並測試程式碼。 如此一來，他們一律會針對其程式碼執行測試。 每當對存放庫進行認可時，就會持續建立對原始程式碼所做的所有變更。 在每次簽入時，持續整合（CI）伺服器會驗證並執行開發人員所建立的任何測試。 如果測試未通過，則會傳回程序代碼進行進一步變更。 如此一來，開發人員就不會中斷所建立的組建。 它們也不需要在本機電腦上執行所有測試，因而提高開發人員的生產力。 

## <a name="key-benefits"></a>主要權益
- 自動化管線的組建、測試和部署。
- 及早偵測錯誤並修正問題，以確保更快的發行速度。
- 更頻繁地認可程式碼並快速建立應用程式。
- 取得快速變更程式碼的彈性，而不會有任何問題。
- 取得更快速的上市時間，讓只有良好的品質程式碼能夠順利完成。
- 使小型程式碼變更更有效率，因為一次只會整合一小段程式碼。
- 提高小組透明度和責任，讓您可以從客戶和小組取得持續的意見反應。

使用下列服務，在您的行動應用程式中啟用持續整合管線。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center build](/appcenter/build/)可協助您使用安全的雲端基礎結構，建立您的小組所使用的原生和跨平臺應用程式。 您可以輕鬆地在 Visual Studio App Center 中連接您的存放庫，並在每次認可時開始在雲端中建立您的應用程式。 您不必擔心是在本機設定組建伺服器、複雜的設定，以及在同事電腦上建立的程式碼，而不是您想要的。

有了 Visual Studio App Center 服務的增強功能，您就可以進一步將工作流程自動化。 您可以使用 App Center 散發，自動將組建發行至測試人員和公用應用程式存放區。 您也可以使用 App Center 測試，在雲端中數以千計的實際裝置和 OS 設定上執行自動化的 UI 測試。

**主要功能**
- 幾分鐘內即可設定持續整合，並更頻繁且更快速地建立應用程式。
- 與 GitHub、BitBucket、Azure DevOps 和 GitLab 整合。
- 在受管理的雲端託管機器上建立快速且安全的組建。
- 讓您的組建啟動測試，並確認應用程式是否在實際的 iOS 和 Android 裝置上建立。
- 取得適用于 iOS、Android、macOS、Windows、Xamarin 和 React Native 的原生和跨平臺支援。
- 藉由新增複製後、預先組建和後置組建腳本來自訂您的組建。

**參考**
- [使用 Visual Studio App Center 註冊](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [開始使用 App Center 組建](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)（Azure DevOps 中的服務）是功能完整的持續整合與持續傳遞（CD）服務，可與您慣用的 Git 提供者搭配運作。 它可以部署到最主要的雲端服務，包括 Azure。 您可以從 GitHub、GitHub Enterprise Server、GitLab、Bitbucket Cloud 或 Azure Repos 上的程式碼開始。 接著，您可以將程式碼的組建、測試和部署自動化，以 Microsoft Azure、Google Cloud Platform 或 Amazon Web Services （AWS）。

**主要功能**
- **簡化設定 CI 伺服器的工作體驗：** 針對原生（Android、iOS 和 Windows）和跨平臺（Xamarin、Cordova 和 React Native）行動應用程式（除了 Microsoft 和非 Microsoft （node.js、JAVA）架構的伺服器技術），設定 CI 伺服器。
- **任何語言、平臺和雲端：** 建立、測試及部署 node.js、Python、JAVA、PHP、Ruby、Go、C/c + +、c #、Android 和 iOS 應用程式。 在 Linux、macOS 和 Windows 上以平行方式執行。 部署至雲端提供者，例如 Azure、AWS 和 Google Cloud Platform。 透過 Beta 頻道和應用程式商店散發行動應用程式。
- **原生容器支援：** 輕鬆建立新的容器，並將其推送到任何登錄。 將容器部署至獨立主機或 Kubernetes。
- **先進的工作流程：** 輕鬆建立組建鏈和 multiphased 組建。 取得 YAML、測試整合、發行閘道、報告等的支援。
- **可擴充：** 使用由「社區」建立的一系列組建、測試和部署工作，其中包括數百個從時差到 SonarCloud 的延伸模組。 您甚至可以從其他 CI 系統（例如 Jenkins）進行部署。 Web 勾點和 REST Api 可協助您進行整合。
- **免費的雲端託管組建：** 這些組建適用于公用和私人存放庫。
- **支援部署至其他雲端廠商：** 廠商包含 AWS 和 Google Cloud Platform。

**參考**
- [Azure Pipelines 指南入門](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [開始使用 Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
- [快速入門](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

若要協助您為應用程式組建選擇正確的服務，請參閱比較[App Center 組建與 Azure Pipelines](/appcenter/build/choose-between-services)的相關文章。
