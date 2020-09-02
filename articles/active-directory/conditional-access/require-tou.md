---
title: 條件式存取需要使用規定 - Azure Active Directory
description: 在本快速入門中，您將了解如何要求必須接受使用規定，才可存取由 Azure Active Directory 條件式存取授予的指定雲端應用程式。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53f99456449e778ff7c1fd3ab096e1afd3562c1e
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88948856"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>快速入門：必須接受使用規定才可存取雲端應用程式

在使用者存取您的環境中特定的雲端應用程式之前，您可以先取得使用者經由接受您的使用規定 (ToU) 所表示的同意。 Azure Active Directory (Azure AD) 條件式存取可為您提供：

- 設定 ToU 的簡易方法
- 要求透過條件式存取原則接受使用規定的選項  

本快速入門說明如何設定 [Azure AD 條件式存取原則](./overview.md)，以要求必須對您環境中選定的雲端應用程式接受 ToU。

![建立原則](./media/require-tou/5555.png)

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必要條件

若要完成本快速入門中的案例，您需要：

- **Azure AD Premium 版的存取權** - Azure AD 條件式存取是 Azure AD Premium 中的功能。
- **稱為 Isabella Simonsen 的測試帳戶** - 如果您不知道如何建立測試帳戶，請參閱[新增雲端式使用者](../fundamentals/add-users-azure-active-directory.md#add-a-new-user)。

## <a name="test-your-sign-in"></a>測試您的登入

此步驟的目標是要取得登入體驗的印象，而不需要條件式存取原則。

**若要測試您的登入：**

1. 以 Isabella Simonsen 的身分登入 [Azure 入口網站](https://portal.azure.com/)。
1. 登出。

## <a name="create-your-terms-of-use"></a>建立使用規定

本節提供建立範例 ToU 的步驟。 當您建立 ToU 時，您會選取 [強制使用條件式存取原則範本] 的值。 選取 [自訂原則]，會開啟在您的 ToU 建立後隨即新建條件式存取原則的對話方塊。

**若要建立使用規定：**

1. 在 Microsoft Word 中，建立新的文件。
1. 輸入「我的使用規定」，然後以 **mytou.pdf** 作為檔名將文件儲存在電腦上。
1. 以全域管理員、安全性系統管理員或條件式存取系統管理員的身分，登入 [Azure 入口網站](https://portal.azure.com)。
1. 在 Azure 入口網站的左側導覽列上，按一下 [Azure Active Directory]。

   ![Azure Active Directory](./media/require-tou/02.png)

1. 在 [Azure Active Directory] 頁面的 [安全性] 區段中，按一下 [條件式存取]。

   ![條件式存取](./media/require-tou/03.png)

1. 在 [管理] 區段中，按一下 [使用規定]。

   ![使用規定](./media/require-tou/04.png)

1. 在頂端的功能表中，按一下 [新增條款]。

   ![使用規定](./media/require-tou/05.png)

1. 在 [新增使用規定] 頁面上：

   ![使用規定](./media/require-tou/112.png)

   1. 在 [名稱] 文字方塊中，輸入「我的 TOU」。
   1. 在 [顯示名稱] 文字方塊中，輸入「我的 TOU」。
   1. 上傳您的使用規定 PDF 檔案。
   1. 選取 [英文] 作為 [語言]。
   1. 針對 [要求使用者展開使用規定]，選取 [開啟]。
   1. 針對 [強制使用條件式存取原則範本]，選取 [自訂原則]。
   1. 按一下 [建立]。

## <a name="create-your-conditional-access-policy"></a>建立條件式存取原則

本節說明如何建立必要的條件式存取原則。 本快速入門中的案例會使用：

- Azure 入口網站；作為要求接受 ToU 的雲端應用程式的預留位置。 
- 您的範例使用者；用來測試條件式存取原則。  

為您的原則進行下列設定：

| 設定 | 值 |
| --- | --- |
| 使用者和群組 | Isabella Simonsen |
| 雲端應用程式 | Microsoft Azure 管理 |
| 授與存取權 | 我的 TOU |

![建立原則](./media/require-tou/1234.png)

**若要設定條件式存取原則：**

1. 在 [新增] 頁面的 [名稱] 文字方塊中，輸入「Isabella 必須接受 TOU」。

   ![名稱](./media/require-tou/71.png)

1. 在 [指派] 區段中，按一下 [使用者和群組]。

   ![使用者和群組](./media/require-tou/06.png)

1. 在 [使用者和群組] 頁面上：

   ![使用者和群組](./media/require-tou/24.png)

   1. 按一下 [選取使用者和群組]，然後選取 [群組設定]。
   1. 按一下 [選取]。
   1. 在 [選取] 頁面上，選取 [Isabella Simonsen]，然後按一下 [選取]。
   1. 在 [使用者和群組] 頁面上，按一下 [完成]。
1. 按一下 [雲端應用程式]。

   ![雲端應用程式](./media/require-tou/08.png)

1. 在 [雲端應用程式] 頁面上：

   ![選取雲端應用程式](./media/require-tou/26.png)

   1. 按一下 [選取應用程式]。
   1. 按一下 [選取]。
   1. 在 [選取] 頁面中，選取 [Microsoft Azure 管理]，然後按一下 [選取]。
   1. 在 [雲端應用程式] 頁面上，按一下 [完成]。
1. 在 [存取控制] 區段中，按一下 [授與]。

   ![存取控制](./media/require-tou/10.png)

1. 在 [授與] 頁面上：

   ![授與](./media/require-tou/111.png)

   1. 選取 [授與存取權]。
   1. 選取 [我的 TOU]。
   1. 按一下 [選取]。
1. 在 [啟用原則] 區段中，按一下 [開啟]。

   ![啟用原則](./media/require-tou/18.png)

1. 按一下 [建立]。

## <a name="evaluate-a-simulated-sign-in"></a>評估模擬的登入狀況

現在您已設定條件式存取原則，建議您查看它是否如預期般運作。 第一個步驟是使用條件式存取 What If 原則工具，模擬您測試使用者的登入情況。 該模擬可評估此登入對原則所造成的影響，並產生模擬報告。  

若要初始化 **What If** 原則評估工具，請設定下列項目：

- 將 **Isabella Simonsen** 設為使用者
- 將 [Microsoft Azure 管理] 設為雲端應用程式

按一下 [What If] 建立模擬報表，即會顯示：

- [會套用的原則] 下方的 [Isabella 必須接受 TOU]
- 作為 [授與控制] 的 [我的 TOU]。

![What If 原則工具](./media/require-tou/79.png)

**若要評估條件式存取原則：**

1. 在[條件式存取 - 原則](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)頁面頂端的功能表中，按一下 [What If]。  

   ![What If](./media/require-tou/14.png)

1. 按一下 **使用者**，選取 **Isabella Simonsen**，然後按一下 **選取**。

   ![User](./media/require-tou/15.png)

1. 若要選取雲端應用程式：

   ![雲端應用程式](./media/require-tou/16.png)

   1. 按一下 [雲端應用程式]。
   1. 在 [雲端應用程式] 頁面上，按一下 [選取應用程式]。
   1. 按一下 [選取]。
   1. 在 [選取] 頁面中，選取 [Microsoft Azure 管理]，然後按一下 [選取]。
   1. 在 [雲端應用程式] 頁面上，按一下 [完成]。
1. 按一下 [What If]。

## <a name="test-your-conditional-access-policy"></a>測試條件式存取原則

在上一節中，您已經學會如何評估模擬的登入狀況。 除了模擬，您也應該測試條件式存取原則，以確保它如預期般運作。

若要測試原則，請再次使用 **Isabella Simonsen** 測試帳戶來嘗試登入 [Azure 入口網站](https://portal.azure.com)。 您應該會看到一個對話方塊要求您接受使用規定。

![使用規定](./media/require-tou/57.png)

## <a name="clean-up-resources"></a>清除資源

當您不再需要測試使用者與條件式存取原則時，即可予以刪除：

- 如果您不知道如何刪除 Azure AD 使用者，請參閱[從 Azure AD 刪除使用者](../fundamentals/add-users-azure-active-directory.md#delete-a-user)。
- 若要刪除原則，請選取您的原則，然後按一下快速存取工具列的 [刪除]。

    ![Multi-Factor Authentication](./media/require-tou/33.png)

- 若要刪除使用規定，請加以選取，然後按一下頂端工具列中的 [刪除條款]。

    ![Multi-Factor Authentication](./media/require-tou/29.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [特定應用程式需要 MFA](../authentication/tutorial-enable-azure-mfa.md)