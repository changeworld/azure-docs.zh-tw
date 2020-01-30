---
title: 內部部署資源的 SSO 如何在加入 Azure AD 的裝置上運作 | Microsoft Docs
description: 了解如何設定混合式 Azure Active Directory 已加入的裝置。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 083433d31f088eae1e138dd9cbd5ac05bbe8a304
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773314"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>內部部署資源的 SSO 如何在加入 Azure AD 的裝置上運作

加入 Azure Active Directory (Azure AD) 的裝置可對租用戶雲端應用程式提供單一登入 (SSO) 體驗可能已不稀奇。 但如果您的環境具有內部部署 Active Directory (AD)，也可以將這些裝置上的 SSO 體驗延伸至此。

本文將說明此作業的運作方式。

## <a name="prerequisites"></a>必要條件

 如果 Azure AD 聯結的電腦未連線到您組織的網路，則需要 VPN 或其他網路基礎結構。 內部部署 SSO 需要與內部部署 AD DS 網域控制站之間的通訊。

## <a name="how-it-works"></a>運作方式 

因為您只需要記住一個單一使用者名稱和密碼，因此 SSO 會簡化資源的存取，並提升環境的安全性。 透過已加入 Azure AD 的裝置，您的使用者已將 SSO 體驗帶入環境中的雲端應用程式。 如果您的環境具有 Azure AD 和內部部署 AD，您可能會想將 SSO 體驗的範圍延伸至內部部署企業營運 (LOB) 應用程式、檔案共用和印表機。

加入 Azure AD 的裝置不了解您的內部部署 AD 環境，因為這些裝置未加入該環境。 不過，您可以透過 Azure AD Connect 將其他有關內部部署 AD 的資訊提供給這些裝置。

同時具有 Azure AD 和內部部署 AD 的環境也稱為混合式環境。 如果您有混合式環境，您很可能已經部署了 Azure AD Connect，可將內部部署身分識別資訊同步至雲端。 做為同步處理常式的一部分，Azure AD Connect 會將內部部署使用者資訊同步到 Azure AD。 當使用者登入混合式環境中已加入 Azure AD 的裝置時：

1. Azure AD 會將使用者所屬的內部部署網域名稱傳回到裝置。
1. 本機安全性授權 (LSA) 服務可讓您在裝置上啟用 Kerberos 驗證。

在嘗試對使用者的內部部署環境中要求 Kerberos 的資源進行存取時，裝置會：

1. 將內部網域資訊和使用者認證傳送給找到的 DC，以對使用者進行驗證。
1. 接收 Kerberos 的[票證授權票證 (TGT)](https://docs.microsoft.com/windows/desktop/secauthn/ticket-granting-tickets)，這會用來存取已加入 AD 的資源。 如果嘗試取得 AAD connect 網域的 TGT 失敗（相關的 DCLocator timeout 可能會造成延遲），則會嘗試認證管理員專案，或使用者可能會收到驗證快顯視窗，要求目標資源的認證。

只要應用程式已設定 **Windows 整合式驗證**，當使用者嘗試存取這些應用程式時，就可順利地取得 SSO。

Windows Hello 企業版需要其他設定，才能從已加入 Azure AD 的裝置中啟用內部部署 SSO。 如需詳細資訊，請參閱[使用 Windows Hello 企業版為加入 Azure AD 的裝置設定內部部署單一登入](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base)。 

## <a name="what-you-get"></a>您的權益

藉由 SSO，您可以在加入 Azure AD 的裝置上執行下列作業： 

- 存取 AD 成員伺服器上的 UNC 路徑
- 存取已設定 Windows 整合式安全性的 AD 成員 Web 伺服器 

如果您想要從 Windows 裝置管理內部部署 AD，請安裝[適用於 Windows 10 的遠端伺服器管理工具](https://www.microsoft.com/download/details.aspx?id=45520)。

您可以使用：

- 用來管理所有 AD 物件的 Active Directory 使用者和電腦 (ADUC) 嵌入式管理單元。 不過，您必須指定您想要以手動方式連線的網域。
- DHCP 嵌入式管理單元，用來管理已加入 AD 的 DHCP 伺服器。 不過，您可能需要指定 DHCP 伺服器名稱或位址。
 
## <a name="what-you-should-know"></a>您應該知道的事情

您可能必須調整 Azure AD Connect 中的[網域型篩選](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering)，以確保必要網域的相關資料會保持同步。

相依於 Active Directory 機器驗證的應用程式和資源不會運作，因為加入 Azure AD 的裝置沒有 AD 中的電腦物件。 

您不能與其他加入 Azure AD 的裝置使用者共用檔案。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱[什麼是 Azure Active Directory 中的裝置管理？](overview.md) 
