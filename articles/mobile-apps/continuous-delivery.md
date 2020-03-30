---
title: 使用 Visual Studio 應用中心和 Azure 服務自動部署和發佈移動應用程式
description: 瞭解説明為移動應用程式設定連續交付管道的應用中心等服務。
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: ccfd0fc0450a6fbd34192dce7e375fe2de7f47c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235346"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>通過連續交付服務自動部署和發佈移動應用程式

作為開發人員，您編寫代碼並將其簽入代碼存儲庫，但簽入存儲庫中的提交可能並不總是一致的。 當多個開發人員處理同一個專案時，可能會遇到集成問題。 團隊可能會遇到事情不工作、Bug 堆積和專案開發延遲的情況。 開發人員必須等到構建和測試整個軟體代碼以檢查錯誤，這使得過程變慢，反覆運算更少。

通過持續交付，您可以自動部署和釋放移動應用程式。 無論您是將應用程式分發給一組測試人員還是公司員工（用於 Beta 測試）還是分發到應用商店（用於生產）並不重要。 持續交付使部署風險降低，並鼓勵快速反覆運算。 您還可以持續向客戶發佈新的更改。

## <a name="distribute-application-binaries-to-beta-testers"></a>將應用程式二進位檔案分發給 Beta 測試人員
Beta 測試移動應用程式是應用程式開發過程中的關鍵步驟之一。 它有助於在早期查找應用程式中的 Bug 和問題。 當您準備好用於生產時，回饋會提高應用程式品質。

使用以下服務在移動應用中啟用連續交付管道。

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[應用中心分發](/appcenter/distribution/)是開發人員快速向設備發佈生成的工具。 憑藉完整的安裝門戶體驗，應用中心分發是測試版應用測試器分發的強大解決方案。 這也是通過公共應用商店分發的便捷替代方案。 開發人員可以通過應用中心構建和公共應用程式存儲集成進一步自動化其分發工作流。

**主要功能**
- 將應用分發給 Beta 測試人員和使用者，並確保所有測試人員都位於應用程式的最新版本上。
- 通知測試人員新版本，無需測試人員再次遍通下載流程。
- 管理應用程式不同版本的通訊組。
- 分銷到商店： 
    - [Apple](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Intune](/appcenter/distribution/stores/intune)
- 獲得對 iOS、Android、macOS、tvOS、Xamarin、反應原生、團結和科爾多瓦的平臺支援。
- 自動將 iOS 設備註冊到預配設定檔。

**引用**
- [使用視覺化工作室應用中心註冊](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [開始應用中心分發](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

[Azure 管道](https://azure.microsoft.com/services/devops/pipelines/)是一項功能齊全的連續集成 （CI） 和連續交付 （CD） 服務，可與首選 Git 供應商配合使用。 Azure 管道可以部署到大多數主要雲服務，如 Azure 服務。 您可以在 GitHub、GitHub 企業伺服器、GitLab、Bitbucket 雲或 Azure 存儲庫上使用代碼開始。 然後，您可以將代碼的生成、測試和部署自動化到 Microsoft Azure、Google 雲平臺或亞馬遜 Web 服務 （AWS）。

**主要功能**
- **簡化基於任務的 CI 伺服器設置體驗：** 為本機（安卓、iOS 和 Windows）和跨平臺（Xamarin、Cordova 和 React 本機）移動應用程式設定 CI 伺服器。
- **任何語言、平臺和雲：** 生成、測試和部署 Node.js、Python、JAVA、PHP、Ruby、Go、C/C++、C#、Android 和 iOS 應用。 在 Linux、macOS 和 Windows 上並行運行。 部署到 Azure、AWS 和 Google 雲平臺等雲供應商。 通過測試版管道和應用商店分發移動應用程式。
- **本機容器支援：** 輕鬆創建新容器，並將它們推送到任何註冊表。 將容器部署到獨立的主機或庫伯奈斯。
- **高級工作流和功能：** 輕鬆創建生成鏈和多階段生成。 獲得對 YAML、測試集成、釋放門、報告等的支援。
- **可擴展：** 使用社區構建的一系列生成、測試和部署任務，其中包括從 Slack 到聲納雲的數百個擴展。 您甚至可以從其他 CI 系統（如 Jenkins）進行部署。 網路掛鉤和 REST API 可以説明您進行集成。
- **免費雲託管版本：** 這些生成可用於公共和私有存儲庫。
- **支援部署到其他雲供應商：** 供應商包括 AWS 和 Google 雲平臺。

**引用**
- [開始使用 Azure 管道指南](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [開始使用 Azure DevOps](https://app.vsaex.visualstudio.com/signup/)
  
## <a name="distribute-your-application-directly-to-app-stores"></a>將應用程式直接分發到應用商店
應用程式已準備好用於生產，並且希望公開使用後，需要將其提交到客戶可以下載的應用商店。 有多種方法可以將應用程式直接分發到應用商店。 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
使用[應用中心分發](/appcenter/distribution/stores/)，您可以將移動應用程式直接發佈到應用商店。 應用程式準備好由使用者下載後，可以直接從視覺化 Studio 應用中心門戶發佈應用程式二進位檔案。 

您可以直接分發到：
- [Apple App Store](/appcenter/distribution/stores/apple)
- [谷歌遊戲商店](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
在 Apple 開發和維護的應用商店中，使用者可以流覽和下載為 iOS、MacOS、WatchOS 和 tvOS 設備開發的應用程式。 開發人員需要將其 iOS 應用程式提交到 Apple 應用商店供公眾使用。

### <a name="google-play"></a>Google Play

Google Play 是 Android 作業系統的官方應用商店，使用者可以在這裡流覽和下載為通過 Google 發佈的 Android 設備開發的應用程式。

### <a name="intune"></a>Intune

[Microsoft Intune](/intune/app-management)是企業移動管理領域的基於雲的服務，可説明您的員工提高工作效率，同時保護公司資料。 使用 Intune，您可以︰
- 管理您員工用來存取使用公司數據資料的行動裝置和電腦。
- 管理員工使用的移動應用程式。
- 通過控制員工訪問和共用公司資訊的方式來保護公司資訊。
- 確保設備和應用程式符合公司安全要求。
    
## <a name="deploy-updates-directly-to-users-devices"></a>將更新直接部署到使用者的設備

### <a name="codepush"></a>代碼推送
借助 AppCenter 中的[CodePush，Apache](/appcenter/distribution/codepush/) Cordova 和 React 原生開發人員可以直接將移動應用程式更新部署到其使用者的設備上。 它充當開發人員可以發佈某些更新（如 JavaScript、HTML、CSS 和映射更改）的中央存儲庫。 然後，應用程式可以使用提供的用戶端 SDK 查詢存儲庫中的更新。 通過這種方式，您可以在解決 Bug 或添加小功能時，與使用者建立更具確定性和直接參與模型。 您不需要重建二進位檔案或通過任何公共應用商店重新分發它。

**主要功能**
- Cordova 和 React Native 開發人員可以直接將移動應用程式更新部署到使用者的設備上，而無需在存儲中發佈。
- 可用於修復 Bug 或添加和刪除不需要您重新生成二進位檔案並通過相應存儲重新分發的小功能。

**引用**
- [使用視覺化工作室應用中心註冊](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [在應用中心開始使用代碼推送](/appcenter/distribution/codepush/)
- [代碼推送 CLI](/appcenter/distribution/codepush/cli)