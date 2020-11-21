---
title: 使用回應建立適用于 Android 的註冊應用程式
titleSuffix: Azure Cognitive Services
description: 瞭解如何設定您的開發環境，並部署臉部註冊應用程式以取得客戶的同意。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: bd2032d565f5bd1fb430449be8b8c08e222f531d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025727"
---
# <a name="build-an-enrollment-app-for-android-with-react"></a>使用回應建立適用于 Android 的註冊應用程式

本指南將說明如何開始使用範例臉部註冊應用程式。 應用程式會示範取得有意義的同意，以將使用者註冊至臉部辨識服務並取得高精確度臉部資料的最佳做法。 整合式系統可以使用像這樣的註冊應用程式，根據其臉部資料提供 touchless 存取控制、身分識別驗證、出席追蹤、個人化 kiosk 或身分識別驗證。

當啟動時，應用程式會向使用者顯示詳細的同意畫面。 如果使用者同意，則應用程式會提示您輸入使用者名稱和密碼，然後使用裝置的相機來捕捉高品質的臉部影像。

範例註冊應用程式是使用 JavaScript 和 React Native framework 撰寫的。 它目前可以部署在 Android 裝置上;未來將推出更多部署選項。

## <a name="prerequisites"></a>必要條件 

* Azure 訂用帳戶– [免費建立一個](https://azure.microsoft.com/free/cognitive-services/)訂用帳戶。  
* 擁有 Azure 訂用帳戶之後，請在 Azure 入口網站中 [建立臉部資源](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) ，以取得您的金鑰和端點。 在其部署後，選取 [前往資源]。  
  * 您將需要您所建立資源的金鑰和端點，以將應用程式連線至臉部 API。  
  * 針對本機開發和測試，您可以將 API 金鑰和端點貼到設定檔中。 針對最終部署，將 API 金鑰儲存在安全的位置，而且絕不會在程式碼中。  

> [!IMPORTANT]
> 這些訂用帳戶金鑰可用來存取您的認知服務 API。 請勿共用您的金鑰。 以安全的方式儲存它們，例如，使用 Azure Key Vault。 我們也建議您定期重新產生這些金鑰。 進行 API 呼叫時，只需要一個金鑰。 重新產生第一個金鑰時，您可以使用第二個金鑰繼續存取服務。

## <a name="set-up-the-development-environment"></a>設定開發環境

1. 複製 [範例註冊應用程式](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample)的 git 存放庫。
1. 若要設定您的開發環境，請依照 <a href="https://reactnative.dev/docs/environment-setup"  title=" React Native 檔 "  target="_blank"> React Native 檔 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 。 選取 **REACT NATIVE CLI 快速入門** 作為開發作業系統，然後選取 [ **Android** ] 作為目標 OS。 完成 **安裝** 相依性和 **Android 開發環境** 的各節。
1. 在您慣用的文字編輯器（例如 [Visual Studio Code](https://code.visualstudio.com/)）中開啟 env.json 檔案，然後新增您的端點和金鑰。 您可以在資源的 [ **總覽** ] 索引標籤下，取得 Azure 入口網站中的端點和金鑰。 此步驟僅供本機測試之用，不會將 &mdash; 您的臉部 API 金鑰簽入遠端存放庫。
1. 從 Android Studio 或您自己的 Android 裝置，使用 Android 虛擬裝置模擬器來執行應用程式。 若要在實體裝置上測試您的應用程式，請遵循相關的 <a href="https://reactnative.dev/docs/running-on-device"  title=" React Native 檔 "  target="_blank"> React Native 檔 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 。  


## <a name="create-an-enrollment-experience"></a>建立註冊體驗  

現在您已設定好範例註冊應用程式，您可以根據自己的註冊體驗需求進行調整。

例如，您可能會想要在您的同意頁面上新增特定情況的資訊：

> [!div class="mx-imgBorder"]
> ![應用程式同意頁面](./media/enrollment-app/1-consent-1.jpg)

此服務會提供影像品質檢查，以協助您選擇影像的品質是否足夠，以註冊客戶或嘗試臉部辨識。 此應用程式示範如何從裝置相機存取畫面、選取最高品質的畫面格，並將偵測到的臉部註冊到臉部 API 服務中。 

許多臉部辨識問題都是由低品質的參考影像所造成。 可能會降低模型效能的一些因素如下：
* 臉部尺寸 (距離相機的臉部) 
* 臉部方向 (臉部，或離開相機) 
* 燈光不良狀況不良 (可能是低光源或背光) 影像可能不佳或有過多的雜訊
* 遮蔽 (部分隱藏或阻擋的臉部) 包含像是頭銜或框指示燈眼鏡) 的配件
* 模糊 (，例如) 拍攝相片時的快速臉部移動。 

> [!div class="mx-imgBorder"]
> ![應用程式映射捕獲指示頁面](./media/enrollment-app/4-instruction.jpg)

請注意，此應用程式也提供刪除使用者註冊和重新註冊選項的功能。

> [!div class="mx-imgBorder"]
> ![設定檔管理頁面](./media/enrollment-app/10-manage-2.jpg)

若要延伸應用程式的功能以涵蓋完整的註冊體驗，請參閱 [總覽](enrollment-overview.md) ，以瞭解要執行的其他功能和最佳作法。

## <a name="deploy-the-enrollment-app"></a>部署註冊應用程式

### <a name="android"></a>Android

首先，請確定您的應用程式已準備好進行生產環境部署：請從應用程式程式碼中移除任何金鑰或秘密，並確定您已遵循 [安全性最佳作法](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security?tabs=command-line%2Ccsharp)。

當您準備好要發行應用程式以進行生產時，您將會產生可供發行的 APK 檔案，這是適用于 Android 應用程式的套件檔案格式。 這個 APK 檔必須以私密金鑰簽署。 在此版本組建中，您可以開始將應用程式直接散發至您的裝置。 

遵循「準備發行的準備」 <a href="https://developer.android.com/studio/publish/preparing#publishing-build"  title=" "  target="_blank"> 檔， <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 瞭解如何產生私密金鑰、簽署您的應用程式，以及產生發行 APK。  

一旦您建立了已簽署的 APK，請參閱發行您的應用程式 <a href="https://developer.android.com/studio/publish"  title=" "  target="_blank"> 發佈應用程式 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 檔，以深入瞭解如何發行您的應用程式。

## <a name="next-steps"></a>下一步  

在本指南中，您已瞭解如何設定開發環境，並開始使用範例註冊應用程式。 如果您不熟悉 React Native，可以 [閱讀他們的「開始使用](https://reactnative.dev/docs/getting-started) 」檔，以深入瞭解背景資訊。 熟悉 [臉部 API](Overview.md)也可能很有説明。 開始開發之前，請閱讀註冊應用程式檔的其他章節。
