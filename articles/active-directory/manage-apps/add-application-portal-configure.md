---
title: 快速入門：針對您 Azure Active Directory (Azure AD) 租用戶中的應用程式設定屬性
description: 本快速入門會使用 Azure 入口網站來設定已向您 Azure Active Directory (Azure AD) 租用戶註冊的應用程式。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82a3310f6fc2169a515b4b13d81c88d187bd0f9c
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85956147"
---
# <a name="quickstart-configure-properties-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>快速入門：針對您 Azure Active Directory (Azure AD) 租用戶中的應用程式設定屬性

在先前的快速入門中，您已將應用程式新增至 Azure AD 租用戶。 當您新增應用程式時，您就會讓 Azure AD 租用戶知道這是應用程式的識別提供者。 現在您將為應用程式設定一些屬性。
 
## <a name="prerequisites"></a>必要條件

若要在您的 Azure AD 租用戶中設定應用程式的屬性，您需要：

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 下列其中一個角色：全域管理員、雲端應用程式管理員、應用程式管理員或服務主體的擁有者。
- (選擇性：完成[檢視您的應用程式](view-applications-portal.md))。
- (選擇性：完成[新增應用程式](add-application-portal.md))。

>[!IMPORTANT]
>若要測試本教學課程中的步驟，我們建議您使用非實際執行環境。

## <a name="configure-app-properties"></a>設定應用程式屬性

當您將應用程式新增至 Azure AD 租用戶後，您會立即看到其概觀頁面。 如果您要設定已新增的應用程式，請查看第一個快速入門，其會逐步引導您檢視已新增至您租用戶的應用程式。 

若要編輯應用程式屬性：

1. 在 Azure AD 入口網站中選取 [企業應用程式]，然後尋找並選取您要設定的應用程式。
2. 在 [管理] 區段中，選取 [屬性] 來開啟屬性窗格以進行編輯。
    ![顯示 [屬性] 畫面和可編輯的應用程式屬性](media/add-application-portal/edit-properties.png)
3. 請花點時間了解可供設定的選項。
    - [為使用者啟用登入？] 可決定指派給應用程式的使用者是否可以登入。
    - [需要使用者指派？] 可決定未指派給應用程式的使用者是否可以登入。
    - [使用者看得到嗎？] 可決定指派給應用程式的使用者是否可以在存取面板 (https://myapps.microsoft.com) 和 O365 應用程式啟動器 (Office 365 或 Microsoft 365 網站左上方的鬆餅選單) 中看到應用程式。
4. 您可以使用下表來協助您選擇最符合需求的選項。

   - **已指派**的使用者行為：

       | 應用程式屬性 | 應用程式屬性 | 應用程式屬性 | 已指派的使用者體驗 | 已指派的使用者體驗 |
       |---|---|---|---|---|
       | 為使用者啟用登入？ | 需要使用者指派？ | 可讓使用者看見？ | 已指派的使用者可以登入？ | 已指派的使用者可以看見應用程式？* |
       | 是 | 是 | 是 | 是 | 是  |
       | 是 | 是 | 否  | 是 | 否   |
       | 是 | 否  | 是 | 是 | 是  |
       | 是 | 否  | 否  | 是 | 否   |
       | 否  | 是 | 是 | 否  | 否   |
       | 否  | 是 | 否  | 否  | 否   |
       | 否  | 否  | 是 | 否  | 否   |
       | 否  | 否  | 否  | 否  | 否   |

   - **未指派**的使用者行為：

       | 應用程式屬性 | 應用程式屬性 | 應用程式屬性 | 未指派的使用者體驗 | 未指派的使用者體驗 |
       |---|---|---|---|---|
       | 為使用者啟用登入？ | 需要使用者指派？ | 可讓使用者看見？ | 未指派的使用者可以登入？ | 未指派的使用者可以看見應用程式？* |
       | 是 | 是 | 是 | 否  | 否   |
       | 是 | 是 | 否  | 否  | 否   |
       | 是 | 否  | 是 | 是 | 否   |
       | 是 | 否  | 否  | 是 | 否   |
       | 否  | 是 | 是 | 否  | 否   |
       | 否  | 是 | 否  | 否  | 否   |
       | 否  | 否  | 是 | 否  | 否   |
       | 否  | 否  | 否  | 否  | 否   |

     *使用者是否可以在存取面板和 Office 365 應用程式啟動器中看到應用程式？

## <a name="use-a-custom-logo"></a>使用自訂標誌

使用自訂標誌：

1. 建立 215 x 215 像素的標誌，並將它儲存為 PNG 格式。
2. 在 Azure AD 入口網站中選取 [企業應用程式]，然後尋找並選取您要設定的應用程式。
3. 在 [管理] 區段中，選取 [屬性] 來開啟屬性窗格以進行編輯。 
4. 選取圖示以上傳標誌。
5. 完成之後，選取 [儲存]。 
    ![說明如何從應用程式的 [屬性] 頁面變更標誌](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > 這個 [屬性] 窗格上顯示的縮圖不會立即更新。 您可以關閉屬性再重新加以開啟，以查看更新的圖示。

## <a name="next-steps"></a>後續步驟

現在，您已設定了應用程式的屬性，接下來您可以設定單一登入。

- [設定單一登入](add-application-portal-setup-sso.md)
- [刪除應用程式](delete-application-portal.md)