---
title: 內部部署資源的 SSO 如何在加入 Azure AD 的裝置上運作 | Microsoft Docs
description: 瞭解如何藉由設定已加入混合式 Azure Active Directory 的裝置來擴充 SSO 體驗。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: da22a4e5e9ab13ec18347e58bea6cfc5f45333de
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630695"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>內部部署資源的 SSO 如何在加入 Azure AD 的裝置上運作

加入 Azure Active Directory (Azure AD) 的裝置可對租用戶雲端應用程式提供單一登入 (SSO) 體驗可能已不稀奇。 如果您的環境具有內部部署 Active Directory (AD) ，您也可以在與內部部署 AD 相依的資源和應用程式上，取得 Azure AD 加入裝置的 SSO 體驗。 

本文將說明此作業的運作方式。

## <a name="prerequisites"></a>必要條件

內部部署 SSO 需要與內部部署 AD DS 網域控制站之間的連線能力。 如果 Azure AD 加入的裝置未連線到您組織的網路，則需要 VPN 或其他網路基礎結構。 

## <a name="how-it-works"></a>運作方式 

透過已加入 Azure AD 的裝置，您的使用者已將 SSO 體驗帶入環境中的雲端應用程式。 如果您的環境有 Azure AD 和內部部署 AD，您可能會想要將 SSO 體驗的範圍延伸至內部部署企業營運 (LOB) 應用程式、檔案共用和印表機。

加入 Azure AD 的裝置不了解您的內部部署 AD 環境，因為這些裝置未加入該環境。 不過，您可以透過 Azure AD Connect 將其他有關內部部署 AD 的資訊提供給這些裝置。

如果您有混合式環境，同時具備 Azure AD 和內部部署 AD，您可能已部署 Azure AD Connect，以便將內部部署身分識別資訊同步處理至雲端。 在同步處理過程中，Azure AD Connect 會將內部部署使用者和網域資訊同步處理到 Azure AD。 當使用者登入混合式環境中已加入 Azure AD 的裝置時：

1. Azure AD 將使用者內部部署網域的詳細資料，連同主要重新整理[權杖](concept-primary-refresh-token.md)傳送回裝置
1. 本地安全機構 (LSA) 服務會在裝置上啟用 Kerberos 和 NTLM 驗證。

>[!NOTE]
> Windows Hello 企業版需要其他設定，才能從已加入 Azure AD 的裝置中啟用內部部署 SSO。 如需詳細資訊，請參閱[使用 Windows Hello 企業版為加入 Azure AD 的裝置設定內部部署單一登入](/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base)。 

在嘗試存取在使用者的內部部署環境中要求 Kerberos 或 NTLM 的資源期間，裝置：

1. 將內部網域資訊和使用者認證傳送給找到的 DC，以對使用者進行驗證。
1. 根據內部部署資源或應用程式所支援的通訊協定，收到 Kerberos [票證授權票證 (TGT) ](/windows/desktop/secauthn/ticket-granting-tickets) 或 NTLM 權杖。 如果嘗試取得網域的 Kerberos TGT 或 NTLM 權杖失敗 (相關的 DCLocator timeout 可能會造成延遲) 、嘗試認證管理員的專案，或使用者可能會收到要求目標資源認證的驗證快顯視窗。

只要應用程式已設定 **Windows 整合式驗證**，當使用者嘗試存取這些應用程式時，就可順利地取得 SSO。

## <a name="what-you-get"></a>您會獲得的服務

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

## <a name="next-steps"></a>下一步

如需詳細資訊，請參閱[什麼是 Azure Active Directory 中的裝置管理？](overview.md) 
