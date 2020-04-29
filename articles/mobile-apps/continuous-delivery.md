---
title: 使用 Visual Studio App Center 和 Azure 服務，將行動應用程式的部署和發行自動化
description: 深入瞭解可協助為您的行動應用程式設定持續傳遞管線的服務（例如 App Center）。
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: ccfd0fc0450a6fbd34192dce7e375fe2de7f47c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80235346"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>使用持續傳遞服務自動部署和發行您的行動應用程式

身為開發人員，您可以撰寫程式碼，並將它簽入程式碼存放庫，但簽入儲存機制的認可哥能不一定一致。 當多位開發人員處理相同的專案時，可能會有整合的問題。 小組可能會遇到無法運作的情況、bug 堆積，以及專案開發會延遲。 開發人員必須等到整個軟體程式碼建立並經過測試後，才能檢查是否有錯誤，讓程式變慢且更不反復。

透過持續傳遞，您可以將行動應用程式的部署和發行作業自動化。 無論您是將應用程式散發給測試人員或公司員工群組（適用于搶鮮版（Beta）測試）還是 app store （用於生產環境），都不重要。 持續傳遞讓部署變得更具風險，並鼓勵快速反復執行。 您也可以透過持續的方式，將新的變更發行至您的客戶。

## <a name="distribute-application-binaries-to-beta-testers"></a>將應用程式二進位檔散發給 Beta 測試人員
搶鮮版（Beta）測試您的行動應用程式是應用程式開發過程中的其中一個重要步驟。 它有助於及早在您的應用程式中尋找 bug 和問題。 當您準備好用於生產環境時，意見反應可改善您的應用程式品質。

使用下列服務，在您的行動應用程式中啟用持續傳遞管線。

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center 散發](/appcenter/distribution/)是一種工具，可讓開發人員快速將組建發行至裝置。 有了完整的安裝入口網站體驗，App Center 散發是適用于 Beta 應用程式測試者散發的強大解決方案。 這也是透過公用應用程式存放區散發的便利替代方案。 開發人員可以使用 App Center 組建和公用應用程式存放區整合，更進一步地自動化其散發工作流程。

**主要功能**
- 將您的應用程式散發給搶鮮版（Beta）測試人員和使用者，並確保所有測試人員都是在最新版的應用程式上。
- 向測試人員通知新版本，而不讓測試人員再次執行下載流程。
- 管理不同應用程式版本的通訊群組。
- 散發至存放區： 
    - [Apple](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Intune](/appcenter/distribution/stores/intune)
- 取得適用于 iOS、Android、macOS、tvOS、Xamarin、React Native、Unity 和 Cordova 的平臺支援。
- 自動將 iOS 裝置註冊到您的布建設定檔。

**參考**
- [使用 Visual Studio App Center 註冊](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [開始使用 App Center 散發](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)是功能完整的持續整合（CI）和持續傳遞（CD）服務，可與您慣用的 Git 提供者搭配運作。 Azure Pipelines 可以部署到最主要的雲端服務，例如 Azure 服務。 您可以從 GitHub、GitHub Enterprise Server、GitLab、Bitbucket Cloud 或 Azure Repos 上的程式碼開始。 接著，您可以將程式碼的組建、測試和部署自動化，以 Microsoft Azure、Google Cloud Platform 或 Amazon Web Services （AWS）。

**主要功能**
- **簡化設定 CI 伺服器的工作體驗：** 設定適用于原生（Android、iOS 和 Windows）和跨平臺（Xamarin、Cordova 和 React Native）行動應用程式的 CI 伺服器。
- **任何語言、平臺和雲端：** 建立、測試及部署 node.js、Python、JAVA、PHP、Ruby、Go、C/c + +、c #、Android 和 iOS 應用程式。 在 Linux、macOS 和 Windows 上以平行方式執行。 部署至雲端提供者，例如 Azure、AWS 和 Google Cloud Platform。 透過 Beta 頻道和應用程式商店散發行動應用程式。
- **原生容器支援：** 輕鬆建立新的容器，並將其推送到任何登錄。 將容器部署至獨立主機或 Kubernetes。
- **先進的工作流程和功能：** 輕鬆建立組建鏈和 multiphased 組建。 取得 YAML、測試整合、發行閘道、報告等的支援。
- **可擴充：** 使用由「社區」建立的一系列組建、測試和部署工作，其中包括數百個從時差到 SonarCloud 的延伸模組。 您甚至可以從其他 CI 系統（例如 Jenkins）進行部署。 Web 勾點和 REST Api 可協助您進行整合。
- **免費的雲端託管組建：** 這些組建適用于公用和私人存放庫。
- **支援部署至其他雲端廠商：** 廠商包含 AWS 和 Google Cloud Platform。

**參考**
- [Azure Pipelines 指南入門](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [開始使用 Azure DevOps](https://app.vsaex.visualstudio.com/signup/)
  
## <a name="distribute-your-application-directly-to-app-stores"></a>將您的應用程式直接散發至 App store
當您的應用程式準備好供生產環境使用，且您想要公開使用它之後，必須將它提交到可由客戶下載的應用程式商店。 有多種方式可將您的應用程式直接散發至應用程式存放區。 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
使用[App Center 散發](/appcenter/distribution/stores/)，您可以將行動應用程式直接發佈至應用程式商店。 當您的應用程式準備好供使用者下載之後，您就可以直接從 Visual Studio App Center 入口網站發佈應用程式二進位檔。 

您可以直接散發至：
- [Apple App Store](/appcenter/distribution/stores/apple)
- [Google Play 商店](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
在 Apple 開發和維護的 app store 中，使用者可以流覽及下載針對 iOS、MacOS、WatchOS 和 tvOS 裝置所開發的應用程式。 開發人員必須將其 iOS 應用程式提交至 Apple App Store 以供公開使用。

### <a name="google-play"></a>Google Play

Google Play 是 Android OS 的官方 app store，使用者可以在其中流覽及下載針對透過 Google 發佈之 Android 裝置所開發的應用程式。

### <a name="intune"></a>Intune

[Microsoft Intune](/intune/app-management)是企業行動管理空間中的雲端式服務，可協助讓您的工作人員提高生產力，同時確保公司資料受到保護。 使用 Intune，您可以︰
- 管理您員工用來存取使用公司數據資料的行動裝置和電腦。
- 管理您的員工使用的行動應用程式。
- 藉由控制您的員工存取及共用公司資訊的方式來保護您的公司資訊。
- 確保裝置和應用程式符合公司的安全性需求。
    
## <a name="deploy-updates-directly-to-users-devices"></a>將更新直接部署到使用者的裝置

### <a name="codepush"></a>CodePush
使用 App Center 中的[CodePush](/appcenter/distribution/codepush/) ，Apache Cordova 和 React Native 開發人員可以直接將行動應用程式更新部署到使用者的裝置。 它可做為中央存放庫，開發人員可以將特定更新發佈至，例如 JavaScript、HTML、CSS 和影像變更。 然後，應用程式可以使用提供的用戶端 Sdk 來查詢存放庫中的更新。 如此一來，當您解決 bug 或加入小型功能時，您可以與使用者擁有更具決定性且直接的互動模型。 您不需要重建二進位檔，或透過任何公用應用程式存放區重新散發。

**主要功能**
- Cordova 和 React Native 開發人員可以直接將行動應用程式更新部署到使用者的裝置，而不需在商店上發行。
- 適用于修正錯誤，或新增和移除不需要您重建二進位檔，並透過個別存放區重新發佈的小型功能。

**參考**
- [使用 Visual Studio App Center 註冊](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [在 App Center 中開始使用 CodePush](/appcenter/distribution/codepush/)
- [CodePush CLI](/appcenter/distribution/codepush/cli)