---
title: 具有條件式存取的應用程式保護原則-Azure Active Directory
description: 瞭解如何在 Azure Active Directory 中使用條件式存取來要求應用程式保護原則以進行雲端應用程式存取。
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
ms.openlocfilehash: 3c853ef3a5a40381aba4e1c13eaf9ad7d8653170
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186626"
---
# <a name="require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>需要使用條件式存取的應用程式保護原則進行雲端應用程式存取（預覽）

您的員工使用行動裝置處理公私事務。 在維護員工的生產力時，您也希望能預防資料遺失。 透過 Azure Active Directory （Azure AD）條件式存取，您可以藉由限制對雲端應用程式的存取，來保護您的公司資料。 請先使用具有應用程式保護原則的用戶端應用程式。

本文說明如何設定條件式存取原則，在授與資料存取權之前，可以要求應用程式保護原則。

## <a name="overview"></a>概觀

透過[Azure AD 條件式存取](overview.md)，您可以微調授權使用者如何存取您的資源。 例如，您可以將雲端應用程式的存取限制為受信任的裝置。

您可以使用 [Intune 應用程式保護原則](https://docs.microsoft.com/intune/app-protection-policy)來協助保護公司資料。 Intune 應用程式保護原則不需要使用行動裝置管理（MDM）解決方案。 不論是否在裝置管理解決方案中註冊裝置，都可以保護公司的資料。

Azure Active Directory 條件式存取會將雲端應用程式的存取限制為 Intune 已回報要 Azure AD 為接收應用程式保護原則的用戶端應用程式。 例如，您可以將 Exchange Online 的存取限制為具有 Intune 應用程式保護原則的 Outlook 應用程式。

在條件式存取術語中，這些用戶端應用程式已知受到*應用程式保護原則*保護的原則。  

![條件式存取](./media/app-protection-based-conditional-access/05.png)

如需受原則保護的用戶端應用程式清單，請參閱[應用程式保護原則需求](concept-conditional-access-grant.md)。

您可以結合以應用程式保護為基礎的條件式存取原則與其他原則，例如[裝置型條件式存取原則](require-managed-devices.md)。 如此一來，您就可以在保護個人和公司裝置的資料時，提供彈性。

> [!NOTE]
> 條件式存取應用程式保護原則無法套用至 B2B 使用者，因為邀請的組織無法看到 B2B 使用者的主要組織。

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>以應用程式保護為基礎的條件式存取需求的優點

與受管理裝置的 iOS 和 Android Intune 所報告的相容性類似，Intune 現在會報告應用程式保護原則是否已套用 Azure AD。 條件式存取可以使用此原則做為存取檢查。 這個新的條件式存取原則（應用程式保護原則）會提高安全性。 它可防止系統管理員錯誤，例如：

- 沒有 Intune 授權的使用者。
- 無法接收 Intune 應用程式保護原則的使用者。
- 未設定為接收原則的 Intune 應用程式保護原則應用程式。

## <a name="before-you-begin"></a>開始之前

本文假設您已熟悉以下各項：

- [應用程式保護原則需求](concept-conditional-access-grant.md)。
- [已核准的用戶端應用程式需求](concept-conditional-access-grant.md)。
- [Azure Active Directory 中條件式存取](overview.md)的基本概念。
- 如何[設定條件式存取原則](app-based-mfa.md)。

## <a name="prerequisites"></a>必要條件

若要建立以應用程式保護為基礎的條件式存取原則，您必須：

- 擁有 Enterprise Mobility + Security 或 Azure Active Directory premium 訂用帳戶 + Intune。
- 請確定使用者已獲得 Enterprise Mobility + Security 或 Azure AD + Intune 的授權。
- 請確定已在 Intune 中設定用戶端應用程式，以接收應用程式保護原則。
- 請確定已在 Intune 中設定使用者，以接收 Intune 應用程式保護原則。

## <a name="app-protection-based-policy-for-exchange-online"></a>適用于 Exchange Online 的以應用程式保護為基礎的原則

此案例包含以應用程式保護為基礎的條件式存取原則，可存取 Exchange Online。

### <a name="scenario-playbook"></a>情節腳本

此情節假設使用者：

- 使用 iOS 或 Android 上的原生郵件應用程式來設定電子郵件，以連接到 Exchange。
- 接收電子郵件，指出只有使用 Outlook 應用程式才能存取。
- 使用連結下載應用程式。
- 開啟 Outlook 應用程式，並使用 Azure AD 認證登入。
- 系統會提示您安裝**Microsoft Authenticator 應用程式**或**Intune 公司入口網站**以繼續。
- 會安裝應用程式，並返回 Outlook 應用程式以繼續。
- 系統會提示您註冊裝置。
- 可以接收 Intune 應用程式保護原則。
- 可以存取電子郵件。

所有 Intune 應用程式保護原則都必須在應用程式上，才能存取公司資料。 這些原則可能會提示使用者重新開機應用程式，或使用額外的 PIN。 如果已針對應用程式和平臺設定原則，就會發生這種情況。

### <a name="configuration"></a>組態

**步驟1：設定 Exchange Online 的 Azure AD 條件式存取原則**

針對此步驟中的條件式存取原則，請設定下列元件：

![條件式存取](./media/app-protection-based-conditional-access/01.png)

1. 輸入條件式存取原則的名稱。
1. 在 [**指派**] 底下的 [**使用者和群組**] 中，為每個條件式存取原則至少選取一個使用者或群組。
1. 在 [**雲端應用程式**] 中，選取 [ **Office 365 Exchange Online**]。

   ![條件式存取](./media/app-protection-based-conditional-access/07.png)

1. 在 [**條件**] 中，設定**裝置平臺**和**用戶端應用程式（預覽）** ：
   1. 在 [**裝置平臺**] 中，選取 [ **Android**和**iOS**]。

      ![條件式存取](./media/app-protection-based-conditional-access/03.png)

   1. 在**用戶端應用程式（預覽）** 中，選取 行動裝置**應用程式和桌面用戶端**和**新式驗證用戶端**

      ![條件式存取](./media/app-protection-based-conditional-access/91.png)

1. 在 [**存取控制**] 底下，選取 **[需要應用程式保護原則（預覽）** ]。

   ![條件式存取](./media/app-protection-based-conditional-access/05.png)

**步驟2：使用 ActiveSync （EAS）設定適用于 Exchange Online 的 Azure AD 條件式存取原則**

針對此步驟中的條件式存取原則，請設定下列元件：

![條件式存取](./media/app-protection-based-conditional-access/06.png)

1. 輸入條件式存取原則的名稱。
1. 在 [**指派**] 底下的 [**使用者和群組**] 中，為每個條件式存取原則至少選取一個使用者或群組。
1. 在 [**雲端應用程式**] 中，選取 [ **Office 365 Exchange Online**]。

   ![條件式存取](./media/app-protection-based-conditional-access/07.png)

1. 在 [**條件**] 中，設定**用戶端應用程式（預覽）** 。 

   1. 在**用戶端應用程式（預覽）** 中，選取 行動裝置**應用程式和桌面用戶端**和**Exchange ActiveSync 用戶端**

      ![條件式存取](./media/app-protection-based-conditional-access/92.png)

   1. 在 [**存取控制**] 底下，選取 **[需要應用程式保護原則（預覽）** ]。

      ![條件式存取](./media/app-protection-based-conditional-access/05.png)

**步驟3：設定適用于 iOS 和 Android 用戶端應用程式的 Intune 應用程式保護原則**

![條件式存取](./media/app-protection-based-conditional-access/09.png)

如需詳細資訊，請參閱[使用 Microsoft Intune 保護應用程式和資料](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。

## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>適用于 Exchange Online 的以應用程式保護為基礎或相容的裝置原則

此案例包含以應用程式保護為基礎或相容的裝置條件式存取原則，以存取 Exchange Online。

### <a name="scenario-playbook"></a>情節腳本

此情節假設：
 
- 使用者已註冊，且有或沒有公司裝置。
- 未使用受保護應用程式來註冊並向 Azure AD 註冊的使用者，必須註冊裝置才能存取資源。
- 使用受保護應用程式的已註冊使用者不需要重新註冊裝置。
- 使用者可以接收 Intune 應用程式保護原則（若未註冊）。
- 如果未註冊，使用者就可以使用 Outlook 和 Intune 應用程式保護原則來存取電子郵件。
- 如果裝置已註冊，使用者就可以使用 Outlook 來存取電子郵件。

### <a name="configuration"></a>組態

**步驟1：設定 Exchange Online 的 Azure AD 條件式存取原則**

針對此步驟中的條件式存取原則，請設定下列元件：

![條件式存取](./media/app-protection-based-conditional-access/62.png)

1. 輸入條件式存取原則的名稱。
1. 在 [**指派**] 底下的 [**使用者和群組**] 中，為每個條件式存取原則至少選取一個使用者或群組。
1. 在 [**雲端應用程式**] 中，選取 [ **Office 365 Exchange Online**]。 

   ![條件式存取](./media/app-protection-based-conditional-access/07.png)

1. 在 [**條件**] 中，設定**裝置平臺**和**用戶端應用程式（預覽）** 。 
 
   1. 在 [**裝置平臺**] 中，選取 [ **Android**和**iOS**]。

      ![條件式存取](./media/app-protection-based-conditional-access/03.png)

   1. 在**用戶端應用程式（預覽）** 中，選取 行動裝置**應用程式和桌面用戶端**和**新式驗證用戶端**

      ![條件式存取](./media/app-protection-based-conditional-access/91.png)

5. 在 [**存取控制**] 底下，選取下列選項：
   - [裝置需要標記為合規]
   - **需要應用程式保護原則（預覽）**
   - [需要其中一個選取的控制項]   
 
      ![條件式存取](./media/app-protection-based-conditional-access/11.png)

**步驟2：使用 ActiveSync 設定 Exchange Online 的 Azure AD 條件式存取原則**

針對此步驟中的條件式存取原則，請設定下列元件：

![條件式存取](./media/app-protection-based-conditional-access/06.png)

1. 輸入條件式存取原則的名稱。
1. 在 [**指派**] 底下的 [**使用者和群組**] 中，為每個條件式存取原則至少選取一個使用者或群組。
1. 在 [**雲端應用程式**] 中，選取 [ **Office 365 Exchange Online**]。 

   ![條件式存取](./media/app-protection-based-conditional-access/07.png)

1. 在 [**條件**] 中，設定**用戶端應用程式（預覽）** 。 

   在**用戶端應用程式（預覽）** 中，選取 行動裝置**應用程式和桌面用戶端**和**Exchange ActiveSync 用戶端**

   ![條件式存取](./media/app-protection-based-conditional-access/92.png)

1. 在 [**存取控制**] 底下，選取下列選項：
   - [裝置需要標記為合規]
   - **需要應用程式保護原則（預覽）**
   - [需要其中一個選取的控制項]

      ![條件式存取](./media/app-protection-based-conditional-access/11.png)

**步驟3：設定適用于 iOS 和 Android 用戶端應用程式的 Intune 應用程式保護原則**

![條件式存取](./media/app-protection-based-conditional-access/09.png)

如需詳細資訊，請參閱[使用 Microsoft Intune 保護應用程式和資料](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。

## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>適用于 Exchange Online 的應用程式保護型和相容裝置原則

此案例包含以應用程式保護為基礎且相容的裝置條件式存取原則，可存取 Exchange Online。

### <a name="scenario-playbook"></a>情節腳本

此情節假設使用者：
 
- 使用 iOS 或 Android 上的原生郵件應用程式來設定電子郵件，以連接到 Exchange。
- 收到電子郵件，指出存取需要註冊其裝置。
- 下載 Intune 公司入口網站並登入入口網站。
- 會檢查 mail，並要求使用 Outlook 應用程式。
- 下載 Outlook 應用程式。
- 開啟 Outlook 應用程式，並輸入註冊時所使用的認證。
- 可以接收 Intune 應用程式保護原則。
- 可以使用 Outlook 和 Intune 應用程式保護原則來存取電子郵件。

在授與公司資料的存取權之前，會啟用任何 Intune 應用程式保護原則。 這些原則可能會提示使用者重新開機應用程式，或使用額外的 PIN。 如果已針對應用程式和平臺設定原則，就會發生這種情況。

### <a name="configuration"></a>組態

**步驟1：設定 Exchange Online 的 Azure AD 條件式存取原則**

針對此步驟中的條件式存取原則，請設定下列元件：

![條件式存取](./media/app-protection-based-conditional-access/01.png)

1. 輸入條件式存取原則的名稱。
1. 在 [**指派**] 底下的 [**使用者和群組**] 中，為每個條件式存取原則至少選取一個使用者或群組。
1. 在 [**雲端應用程式**] 中，選取 [ **Office 365 Exchange Online**]。 

   ![條件式存取](./media/app-protection-based-conditional-access/07.png)

1. 在 [**條件**] 中，設定**裝置平臺**和**用戶端應用程式（預覽）** 。 
   1. 在 [**裝置平臺**] 中，選取 [ **Android**和**iOS**]。

      ![條件式存取](./media/app-protection-based-conditional-access/03.png)

   1. 在**用戶端應用程式（預覽）** 中，選取 行動裝置**應用程式和桌面用戶端**和**新式驗證用戶端**

      ![條件式存取](./media/app-protection-based-conditional-access/91.png)

1. 在 [**存取控制**] 底下，選取下列選項：
   - [裝置需要標記為合規]
   - **需要應用程式保護原則（預覽）**
   - [需要所有選取的控制項]   
 
      ![條件式存取](./media/app-protection-based-conditional-access/13.png)

**步驟2：使用 ActiveSync 設定 Exchange Online 的 Azure AD 條件式存取原則**

針對此步驟中的條件式存取原則，請設定下列元件：

![條件式存取](./media/app-protection-based-conditional-access/06.png)

1. 輸入條件式存取原則的名稱。
1. 在 [**指派**] 底下的 [**使用者和群組**] 中，為每個條件式存取原則至少選取一個使用者或群組。
1. 在 [**雲端應用程式**] 中，選取 [ **Office 365 Exchange Online**]。 

   ![條件式存取](./media/app-protection-based-conditional-access/07.png)

1. 在 [**條件**] 中，設定**用戶端應用程式（預覽）** 。 

   在**用戶端應用程式（預覽）** 中，選取 行動裝置**應用程式和桌面用戶端**和**Exchange ActiveSync 用戶端**

   ![條件式存取](./media/app-protection-based-conditional-access/92.png)

1. 在 [**存取控制**] 底下，選取下列選項：
   - [裝置需要標記為合規]
   - **需要應用程式保護原則（預覽）**
   - [需要所有選取的控制項]   
 
      ![條件式存取](./media/app-protection-based-conditional-access/13.png)

**步驟3：設定適用于 iOS 和 Android 用戶端應用程式的 Intune 應用程式保護原則**

![條件式存取](./media/app-protection-based-conditional-access/09.png)

如需詳細資訊，請參閱[使用 Microsoft Intune 保護應用程式和資料](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。

## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>適用于 Exchange Online 和 SharePoint Online 的應用程式保護型或以應用程式為基礎的原則

此案例包含以應用程式保護為基礎或已核准的應用程式原則，可存取 Exchange Online 和 SharePoint Online。

### <a name="scenario-playbook"></a>情節腳本

此情節假設使用者：

- 設定用戶端應用程式，其位於支援應用程式保護原則需求或已核准應用程式需求的應用程式清單上。  
- 會使用符合應用程式保護原則需求的用戶端應用程式，並可接收 Intune 應用程式保護原則。
- 使用符合支援 Intune 應用程式保護原則之已核准應用程式原則需求的用戶端應用程式。
- 開啟應用程式，以存取電子郵件或檔。
- 開啟 Outlook 應用程式，並使用 Azure AD 認證登入。
- 系統會提示您安裝適用于 iOS 的 Microsoft Authenticator，或 Intune 公司入口網站適用于 Android 使用（如果尚未安裝）。
- 會安裝應用程式，並可返回 Outlook 應用程式繼續進行。
- 系統會提示您註冊裝置。
- 可以接收 Intune 應用程式保護原則。
- 可以使用 Outlook 和 Intune 應用程式保護原則來存取電子郵件。
- 可以使用應用程式來存取網站和檔，而不是應用程式保護原則需求，但在已核准的應用程式需求中列出。

在授與公司資料的存取權之前，所有 Intune 應用程式保護原則都是必要的。 這些原則可能會提示使用者重新開機應用程式，或使用額外的 PIN。 如果已針對應用程式和平臺設定原則，就會發生這種情況。

**備註**

- 如果您想要同時支援以應用程式保護為基礎和以應用程式為基礎的條件式存取原則，您可以使用此案例。
- 在此*原則*中，必須先評估具有應用程式保護原則需求的應用程式，以取得核准的用戶端應用程式需求之前的存取權。

### <a name="configuration"></a>組態

**步驟1：設定 Exchange Online 和 SharePoint Online 的 Azure AD 條件式存取原則**

針對此步驟中的條件式存取原則，請設定下列元件：

![條件式存取](./media/app-protection-based-conditional-access/62.png)

1. 輸入條件式存取原則的名稱。
1. 在 [**指派**] 底下的 [**使用者和群組**] 中，為每個條件式存取原則至少選取一個使用者或群組。
1. 在 [**雲端應用程式**] 中，選取 [ **Office 365 Exchange online**和**office 365 SharePoint online**]。 

   ![條件式存取](./media/app-protection-based-conditional-access/02.png)

1. 在 [**條件**] 中，設定**裝置平臺**和**用戶端應用程式（預覽）** 。 
   1. 在 [**裝置平臺**] 中，選取 [ **Android**和**iOS**]。

      ![條件式存取](./media/app-protection-based-conditional-access/03.png)

   1. 在**用戶端應用程式（預覽）** 中，選取 行動裝置**應用程式和桌面用戶端**和**新式驗證用戶端**

      ![條件式存取](./media/app-protection-based-conditional-access/91.png)

1. 在 [**存取控制**] 底下，選取下列選項：
   - **需要核准的用戶端應用程式**
   - **需要應用程式保護原則（預覽）**
   - [需要其中一個選取的控制項]
 
      ![條件式存取](./media/app-protection-based-conditional-access/12.png)

**步驟2：設定適用于 iOS 和 Android 用戶端應用程式的 Intune 應用程式保護原則**

![條件式存取](./media/app-protection-based-conditional-access/09.png)

如需詳細資訊，請參閱[使用 Microsoft Intune 保護應用程式和資料](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。

## <a name="next-steps"></a>後續步驟

- 如果您想要知道如何設定條件式存取原則，請參閱[利用 Azure Active Directory 條件式存取來取得特定應用程式的 MFA](app-based-mfa.md)。
- 如果您已經準備好設定環境的條件式存取原則，請參閱[Azure Active Directory 中條件式存取的最佳做法](best-practices.md)。
