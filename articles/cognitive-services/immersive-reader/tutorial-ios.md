---
title: 教學課程：使用 Swift iOS 程式碼範例啟動沈浸式閱讀程式
titleSuffix: Azure Cognitive Services
description: 在本教學課程中，您將設定及執行可啟動沈浸式閱讀程式的範例 Swift 應用程式。
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.openlocfilehash: d9bb3a5fb41215f2e839efc7989ea7854f254f16
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537930"
---
# <a name="tutorial-start-the-immersive-reader-using-the-swift-ios-code-sample"></a>教學課程：使用 Swift iOS 程式碼範例啟動沈浸式閱讀程式

在[概觀](./overview.md)中，您會了解什麼是沈浸式閱讀程式，及其如何實作經實證的技術，針對語言學習者、沈浸式讀者及存在學習差異的學生改善其閱讀理解程度。 本教學課程說明如何建立可啟動沈浸式閱讀程式的 iOS 應用程式。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 使用範例專案設定及執行適用於 iOS 的 Swift 應用程式。
> * 取得存取權杖。
> * 使用範例內容啟動沈浸式閱讀程式。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必要條件

* 為 Azure Active Directory 驗證所設定的沈浸式閱讀程式資源。 遵循[這些指引](./how-to-create-immersive-reader.md)來設定。 設定環境屬性時，您需要用到在這裡建立的一些值。 將工作階段的輸出儲存到文字檔中，以供日後參考。
* [macOS](https://www.apple.com/macos)。
* [Git](https://git-scm.com/)。
* [沈浸式閱讀程式 SDK](https://github.com/microsoft/immersive-reader-sdk)。
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)。

## <a name="configure-authentication-credentials"></a>設定驗證認證

1. 開啟 Xcode，然後開啟 **immersive-reader-sdk/js/samples/ios/quickstart-swift/quickstart-swift.xcodeproj**。
1. 在頂端功能表上，選取 [產品] > [配置] > [編輯結構描述]。
1. 在 [執行] 檢視中，選取 [引數] 索引標籤。
1. 在 [環境變數] 區段中，新增下列名稱和值。 請使用您建立沈浸式閱讀程式資源時提供的值。

    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

請勿將此變更認可到原始檔控制，因為其中包含不應公開的機密資料。

## <a name="start-the-immersive-reader-with-sample-content"></a>使用範例內容啟動沈浸式閱讀程式

在 Xcode 中，選取 **Ctrl+R** 執行專案。

## <a name="next-steps"></a>後續步驟

* 探索[沈浸式閱讀程式 SDK](https://github.com/microsoft/immersive-reader-sdk) 和[沈浸式閱讀程式 SDK 參考](./reference.md)。
* 檢視 [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/) 上的程式碼範例。
