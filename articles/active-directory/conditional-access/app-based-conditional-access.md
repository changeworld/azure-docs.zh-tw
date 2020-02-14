---
title: 具有條件式存取的已核准用戶端應用程式-Azure Active Directory
description: 瞭解如何透過 Azure Active Directory 中的條件式存取，要求已核准的用戶端應用程式存取雲端應用程式。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a8832234978a2c8b2db25d88b5dd6c211b634b7
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186462"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>如何：透過條件式存取要求使用已核准的用戶端應用程式存取雲端應用程式 

您的員工使用行動裝置處理公私事務。 在維護員工的生產力時，您也希望能預防資料遺失。 透過 Azure Active Directory （Azure AD）條件式存取，您可以將雲端應用程式的存取限制為可保護公司資料的已核准用戶端應用程式。  

本主題說明如何設定需要已核准用戶端應用程式的條件式存取原則。

## <a name="overview"></a>概觀

透過[Azure AD 條件式存取](overview.md)，您可以微調授權使用者如何存取您的資源。 例如，您可以將雲端應用程式的存取限制為受信任的裝置。

您可以使用 [Intune 應用程式保護原則](https://docs.microsoft.com/intune/app-protection-policy)來協助保護公司資料。 Intune 應用程式保護原則不需要行動裝置管理 (MDM) 解決方案，因此不論您是否在裝置管理解決方案中註冊裝置，都可以保護公司的資料。

Azure Active Directory 條件式存取可讓您將雲端應用程式的存取限制為支援 Intune 應用程式保護原則的用戶端應用程式。 例如，您可以限制唯有 Outlook 應用程式能存取 Exchange Online。

在條件式存取術語中，這些用戶端應用程式稱為**已核准的用戶端應用程式**。  

![條件式存取](./media/app-based-conditional-access/05.png)

如需核准的用戶端應用程式清單，請參閱[核准的用戶端應用程式需求](concept-conditional-access-grant.md)。

您可以將以應用程式為基礎的條件式存取原則與其他原則（例如[裝置型條件式存取原則](require-managed-devices.md)）結合，以提供如何保護個人和公司裝置資料的彈性。

## <a name="before-you-begin"></a>開始之前

本主題假設您已熟悉：

- [已核准的用戶端應用程式需求](concept-conditional-access-grant.md)。
- [Azure Active Directory 中條件式存取](overview.md)的基本概念。
- 如何[設定條件式存取原則](app-based-mfa.md)。
- [條件式存取原則的遷移](best-practices.md#policy-migration)。

## <a name="prerequisites"></a>必要條件

若要建立以應用程式為基礎的條件式存取原則，您必須擁有 Enterprise Mobility + Security 或 Azure Active Directory premium 訂用帳戶，而且使用者必須獲得 EMS 或 Azure AD 的授權。 

## <a name="exchange-online-policy"></a>Exchange Online 原則 

此案例是由以應用程式為基礎的條件式存取原則所組成，可存取 Exchange Online。

### <a name="scenario-playbook"></a>情節腳本

此情節假設使用者：

- 將使用 iOS 或 Android 原生郵件應用程式的電子郵件設定為連線到 Exchange
- 收到一封電子郵件，郵件顯示僅能使用 Outlook 應用程式存取
- 透過連結下載應用程式
- 開啟 Outlook 應用程式並使用 Azure AD 認證登入
- 系統提示安裝 Authenticator (iOS) 或公司入口網站 (Android) 以繼續進行
- 安裝應用程式，而且可以返回 Outlook 應用程式繼續進行
- 系統提示註冊裝置
- 能夠存取電子郵件

所有 Intune 應用程式保護原則都會在存取公司資料時啟動，而且可能會提示使用者重新開機應用程式、使用額外的 PIN 等等（如果已針對應用程式和平臺設定）。

### <a name="configuration"></a>組態 

**步驟 1-設定 Exchange Online 的 Azure AD 條件式存取原則**

針對此步驟中的條件式存取原則，您必須設定下列元件：

1. 條件式存取原則的**名稱**。
1. **使用者和群組**：每個條件式存取原則必須至少選取一個使用者或群組。
1. **雲端應用程式：** 您必須選取 [Office 365 Exchange Online] 做為雲端應用程式。
1. **條件：** [條件] 需要設定 [裝置平台] 和 [用戶端應用程式]：
   1. [裝置平台] 請選取 [Android] 和 [iOS]。
   1. [用戶端應用程式 (預覽)] 需選取 [行動裝置應用程式與傳統型應用程式] 和 [新式驗證用戶端]。
1. [存取控制項] 必須選取 [需要核准的用戶端應用程式 \(預覽\)\]。

   ![條件式存取](./media/app-based-conditional-access/05.png)

**步驟 2-使用 Active Sync （EAS）為 Exchange Online 設定 Azure AD 條件式存取原則**

針對此步驟中的條件式存取原則，您必須設定下列元件：

1. 條件式存取原則的**名稱**。
1. **使用者和群組**：每個條件式存取原則必須至少選取一個使用者或群組。
1. **雲端應用程式：** 您必須選取 [Office 365 Exchange Online] 做為雲端應用程式。
1. **條件：** [條件] 需要設定 [用戶端應用程式 (預覽)]。 
   1. [用戶端應用程式 (預覽)] 需選取 [行動裝置應用程式和桌面用戶端] 和 [Exchange ActiveSync 用戶端]。
   1. [存取控制項] 必須選取 [需要核准的用戶端應用程式 \(預覽\)\]。

      ![條件式存取](./media/app-based-conditional-access/05.png)

**步驟 3 - 設定 iOS 和 Android 用戶端應用程式的 Intune 應用程式防護原則**

如需詳細資訊，請參閱[使用 Microsoft Intune 保護應用程式和資料](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。

## <a name="exchange-online-and-sharepoint-online-policy"></a>Exchange Online 和 SharePoint Online 原則

此案例包含使用行動應用程式管理原則的條件式存取，可讓您透過已核准的應用程式存取 Exchange Online 和 SharePoint Online。

### <a name="scenario-playbook"></a>情節腳本

此情節假設使用者：

- 嘗試使用 SharePoint 應用程式連接還有檢視其公司網站
- 嘗試使用與 Outlook 應用程式認證相同的認證登入
- 不需要重新註冊，即可取得資源的存取權

### <a name="configuration"></a>組態

**步驟 1-設定 Exchange Online 和 SharePoint Online 的 Azure AD 條件式存取原則**

針對此步驟中的條件式存取原則，您必須設定下列元件：

1. 條件式存取原則的**名稱**。
1. **使用者和群組**：每個條件式存取原則必須至少選取一個使用者或群組。
1. **雲端應用程式：** 您必須選取 [Office 365 Exchange Online] 和 [Office 365 SharePoint Online] 做為雲端應用程式。 
1. **條件：** [條件] 需要設定 [裝置平台] 和 [用戶端應用程式]：
   1. [裝置平台] 請選取 [Android] 和 [iOS]。
   1. [用戶端應用程式 (預覽)] 需選取 [行動裝置應用程式和桌面用戶端] 和 [新式驗證用戶端]。
1. [存取控制項] 必須選取 [需要核准的用戶端應用程式 \(預覽\)\]。

   ![條件式存取](./media/app-based-conditional-access/05.png)

**步驟 2-使用 Active Sync （EAS）為 Exchange Online 設定 Azure AD 條件式存取原則**

針對此步驟中的條件式存取原則，您必須設定下列元件：

1. 條件式存取原則的**名稱**。
1. **使用者和群組**：每個條件式存取原則必須至少選取一個使用者或群組。
1. **雲端應用程式：** 您必須選取 [Office 365 Exchange Online] 做為雲端應用程式。 線上 
1. **條件：** [條件] 需要設定 [用戶端應用程式]：
   1. [用戶端應用程式 (預覽)] 需選取 [行動裝置應用程式和桌面用戶端] 和 [Exchange ActiveSync 用戶端]。
   1. [存取控制項] 必須選取 [需要核准的用戶端應用程式 \(預覽\)\]。

      ![條件式存取](./media/app-based-conditional-access/05.png)

**步驟 3 - 設定 iOS 和 Android 用戶端應用程式的 Intune 應用程式防護原則**

![條件式存取](./media/app-based-conditional-access/09.png)

如需詳細資訊，請參閱[使用 Microsoft Intune 保護應用程式和資料](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。

## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>適用於 Exchange Online 和 SharePoint Online 的應用程式型或相容裝置原則

此案例是由應用程式或相容的裝置條件式存取原則所組成，可存取 Exchange Online。

### <a name="scenario-playbook"></a>情節腳本

此情節假設：
 
- 某些使用者已註冊（不論是否有公司裝置）
- 未使用應用程式保護的應用程式向 Azure AD 申請和註冊的使用者需要註冊裝置，才能存取資源
- 已使用應用程式保護的應用程式完成申請的使用者不需要重新註冊裝置

### <a name="configuration"></a>組態

**步驟 1-設定 Exchange Online 和 SharePoint Online 的 Azure AD 條件式存取原則**

針對此步驟中的條件式存取原則，您必須設定下列元件：

1. 條件式存取原則的**名稱**。
1. **使用者和群組**：每個條件式存取原則必須至少選取一個使用者或群組。
1. **雲端應用程式：** 您必須選取 [Office 365 Exchange Online] 和 [Office 365 SharePoint Online] 做為雲端應用程式。 
1. **條件：** [條件] 需要設定 [裝置平台] 和 [用戶端應用程式]。 
   1. [裝置平台] 請選取 [Android] 和 [iOS]。
   1. [用戶端應用程式 (預覽)] 需選取 [行動裝置應用程式和桌面用戶端] 和 [新式驗證用戶端]。
1. [存取控制項] 需要選取下列項目：
   - [裝置需要標記為合規]
   - [需要經過核准的用戶端應用程式 \(預覽\)\]
   - [需要其中一個選取的控制項]   
 
      ![條件式存取](./media/app-based-conditional-access/11.png)

**步驟 2-使用 Active Sync （EAS）為 Exchange Online 設定 Azure AD 條件式存取原則**

針對此步驟中的條件式存取原則，您必須設定下列元件：

1. 條件式存取原則的**名稱**。
1. **使用者和群組**：每個條件式存取原則必須至少選取一個使用者或群組。
1. **雲端應用程式：** 您必須選取 [Office 365 Exchange Online] 做為雲端應用程式。 
1. **條件：** [條件] 需要設定 [用戶端應用程式]。 
   1. [用戶端應用程式 (預覽)] 需選取 [行動裝置應用程式和桌面用戶端] 和 [Exchange ActiveSync 用戶端]。
1. [存取控制項] 必須選取 [需要核准的用戶端應用程式 \(預覽\)\]。
 
   ![條件式存取](./media/app-based-conditional-access/11.png)

**步驟 3 - 設定 iOS 和 Android 用戶端應用程式的 Intune 應用程式防護原則**

![條件式存取](./media/app-based-conditional-access/09.png)

如需詳細資訊，請參閱[使用 Microsoft Intune 保護應用程式和資料](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。

## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>適用於 Exchange Online 和 SharePoint Online 的應用程式型和相容裝置原則

此案例包含以應用程式為基礎且相容的裝置條件式存取原則，可存取 Exchange Online。

### <a name="scenario-playbook"></a>情節腳本

此情節假設使用者：
 
- 將使用 iOS 或 Android 原生郵件應用程式的電子郵件設定為連線到 Exchange
- 接收指出存取權要求註冊您裝置的電子郵件
- 下載公司入口網站並登入公司入口網站
- 檢查電子郵件，並且被要求使用 Outlook 應用程式
- 下載 Outlook 應用程式
- 開啟 Outlook 應用程式，並輸入申請時所用的認證
- 使用者即可存取電子郵件

任何 Intune 應用程式防護原則都會在存取公司資料時啟動，而且可能會提示使用者重新啟動應用程式、使用其他 PIN 碼等 (若有針對應用程式和平台設定)。

### <a name="configuration"></a>組態

**步驟 1-設定 Exchange Online 和 SharePoint Online 的 Azure AD 條件式存取原則**

針對此步驟中的條件式存取原則，您必須設定下列元件：

1. 條件式存取原則的**名稱**。
1. **使用者和群組**：每個條件式存取原則必須至少選取一個使用者或群組。
1. **雲端應用程式：** 您必須選取 [Office 365 Exchange Online] 和 [Office 365 SharePoint Online] 做為雲端應用程式。 
1. **條件：** [條件] 需要設定 [裝置平台] 和 [用戶端應用程式]。 
   1. [裝置平台] 請選取 [Android] 和 [iOS]。
   1. [用戶端應用程式 (預覽)] 需選取 [行動裝置應用程式與傳統型應用程式] 和 [新式驗證用戶端]。
1. [存取控制項] 需要選取下列項目：
   - [裝置需要標記為合規]
   - [需要經過核准的用戶端應用程式 \(預覽\)\]
   - [需要所有選取的控制項]   
 
      ![條件式存取](./media/app-based-conditional-access/13.png)

**步驟 2-使用 Active Sync （EAS）為 Exchange Online 設定 Azure AD 條件式存取原則**

針對此步驟中的條件式存取原則，您必須設定下列元件：

1. 條件式存取原則的**名稱**。
1. **使用者和群組**：每個條件式存取原則必須至少選取一個使用者或群組。
1. **雲端應用程式：** 您必須選取 [Office 365 Exchange Online] 做為雲端應用程式。 
1. **條件：** [條件] 需要設定 [用戶端應用程式 (預覽)]。 
   1. [用戶端應用程式 (預覽)] 需選取 [行動裝置應用程式和桌面用戶端] 和 [Exchange ActiveSync 用戶端]。

   ![條件式存取](./media/app-based-conditional-access/92.png)

1. [存取控制項] 需要選取下列項目：
   - [裝置需要標記為合規]
   - [需要經過核准的用戶端應用程式 \(預覽\)\]
   - [需要所有選取的控制項]   

**步驟 3 - 設定 iOS 和 Android 用戶端應用程式的 Intune 應用程式防護原則**

![條件式存取](./media/app-based-conditional-access/09.png)

如需詳細資訊，請參閱[使用 Microsoft Intune 保護應用程式和資料](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。

## <a name="next-steps"></a>後續步驟

如果您想要知道如何設定條件式存取原則，請參閱[利用 Azure Active Directory 條件式存取來取得特定應用程式的 MFA](app-based-mfa.md)。

如果您已準備好設定您環境的條件式存取原則，請參閱 [Azure Active Directory 中條件式存取的最佳做法](best-practices.md)。 
