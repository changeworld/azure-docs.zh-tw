---
title: 條件訪問需要託管設備 - Azure 活動目錄
description: 瞭解如何配置 Azure 活動目錄 （Azure AD） 基於設備的條件訪問策略，這些策略需要託管設備才能訪問雲應用。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/22/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a3c71534febc3cdb6429d3092225ebc73f6cbe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481478"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>如何：需要託管設備才能通過條件訪問進行雲應用訪問

在行動第一、雲端第一的世界中，Azure Active Directory (Azure AD) 可讓您從任何地方單一登入至應用程式和服務。 授權使用者可以從多種裝置 (包括行動裝置和個人裝置) 存取您的雲端應用程式。 不過，許多環境都至少會有些許應用程式只應由符合您的安全性與相容性標準的裝置存取。 這些裝置也稱為受控裝置。 

本文介紹如何配置需要託管設備訪問環境中某些雲應用的條件訪問策略。 

## <a name="prerequisites"></a>Prerequisites

需要託管設備進行雲應用訪問將**Azure AD 條件訪問**和**Azure AD 裝置管理**聯繫在一起。 如果您還不熟悉上述其中一種領域，您應該先閱讀下列主題：

- **[Azure 活動目錄中的條件訪問](../active-directory-conditional-access-azure-portal.md)**- 本文為您提供了條件訪問和相關術語的概念概述。
- **[Azure Active Directory 中的裝置管理簡介](../devices/overview.md)** - 本文概略說明在組織的控制下可用來連接裝置的各種選項。 
- 有關 Windows **10 建立者更新（版本 1703）** 或更高版本的 Chrome 支援，請安裝[Windows 10 帳戶副檔名](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)。 當條件訪問策略需要特定于設備的詳細資訊時，需要此擴展。

>[!NOTE] 
> 我們建議使用基於 Azure AD 設備的條件訪問策略，在初始設備身份驗證後獲得最佳實施。 這包括如果設備不符合合規性和設備代碼流，則結束會話。


## <a name="scenario-description"></a>案例描述

要兼顧安全性與生產力並不容易。 在可存取雲端資源的裝置種類激增的情況下，使用者的生產力得以提升。 而另一方面，您應該不希望環境中的特定資源在保護層級不確定的情況下受到裝置存取。 對於受影響的資源，您應要求使用者只能使用受控裝置加以存取。 

使用 Azure AD 條件訪問，可以使用授予存取權限的單個策略來滿足此要求：

- 將存取權授與所選的雲端應用程式
- 將存取權授與所選使用者和群組
- 必須使用受控裝置

## <a name="managed-devices"></a>受控裝置  

簡單來說，受控裝置是受到*某種*組織性控制的裝置。 在 Azure AD 中，受控裝置的必要條件是已向 Azure AD 註冊。 註冊裝置時，系統會以裝置物件的形式為裝置建立身分識別。 Azure 使用此物件來追蹤裝置的狀態資訊。 身為 Azure AD 管理員的您已經可以使用此物件來切換 (啟用/停用) 裝置的狀態。
  
![裝置型條件](./media/require-managed-devices/32.png)

若要向 Azure AD 註冊裝置，您有三個選項： 

- **Azure AD 註冊裝置** - 向 Azure AD 註冊個人裝置
- **已加入 Azure AD 的裝置** - 向 Azure AD 註冊未加入內部部署 AD 的組織 Windows 10 裝置。 
- **已加入混合式 Azure AD 的裝置** - 取得 Windows 10 裝置或支援的下層裝置，該裝置會加入已向 Azure AD 註冊的內部部署 AD。

這三個選項在本文中討論了[什麼是設備標識？](../devices/overview.md)

若要成為受控裝置，已註冊的裝置必須是**已加入混合式 Azure AD 的裝置**，或是**標示為符合規範的裝置**。  

![裝置型條件](./media/require-managed-devices/47.png)
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>需要已加入混合式 Azure AD 的裝置

在條件訪問策略中，可以選擇 **"需要混合 Azure AD 聯接設備**"以聲明只能使用託管設備訪問選定的雲應用。 

![裝置型條件](./media/require-managed-devices/10.png)

此設定僅適用於要加入內部部署 AD 的 Windows 10 或下層裝置 (例如 Windows 7 或 Windows 8)。 您只可以使用「加入混合式 Azure AD」向 Azure AD 註冊這些裝置，而這是註冊 Windows 10 裝置的[自動化程序](../devices/hybrid-azuread-join-plan.md)。 

![裝置型條件](./media/require-managed-devices/45.png)

如何讓已加入混合式 Azure AD 的裝置成為受控裝置？  對於已加入本地 AD 的設備，假定使用管理解決方案（如**組態管理員**或**群組原則 （GP））** 來強制執行對這些設備的控制。這些解決方案用於管理這些設備。 因為 Azure AD 沒有任何方法可判斷裝置是否已套用任何方法，所以需要已加入混合式 Azure AD 的裝置是需要受控裝置的相對較弱機制。 如果這類裝置也是已加入混合式 Azure AD 的裝置，則由系統管理員判斷已加入網域的內部部署裝置所套用的方法是否足以造就受控裝置。

## <a name="require-device-to-be-marked-as-compliant"></a>裝置需要標記為符合規範

「裝置需要標記為符合規範」** 選項是要求受控裝置的最強形式。

![裝置型條件](./media/require-managed-devices/11.png)

此選項要求向 Azure AD 註冊裝置，而且裝置要由下列項目標示為符合規範：
         
- Intune
- 一個第三方行動裝置管理 (MDM) 系統，可透過 Azure AD 整合來管理 Windows 10 裝置。 不支援針對 Windows 10 以外的裝置 OS 類型使用的第三方 MDM 系統。
 
![裝置型條件](./media/require-managed-devices/46.png)

對於標示為符合規範的裝置，您可以假設： 

- 您的員工用來存取公司資料的行動裝置受到控管
- 您的員工使用的行動裝置應用程式受到控管
- 協助控制您的員工存取及共用公司資訊的方式，進而保護公司資訊
- 裝置與其應用程式都符合公司安全性需求的規範

### <a name="known-behavior"></a>已知行為

在 Windows 7 上，iOS、Android、macOS 和某些協力廠商 Web 瀏覽器 Azure AD 使用在設備註冊 Azure AD 時預配的用戶端憑證標識設備。 當使用者首次通過瀏覽器登錄時，系統會提示使用者選擇證書。 最終使用者必須先選擇此證書，然後才能繼續使用瀏覽器。

## <a name="next-steps"></a>後續步驟

在環境中配置基於設備的條件訪問策略之前，應瞭解[Azure 活動目錄中的條件訪問最佳做法](best-practices.md)。
