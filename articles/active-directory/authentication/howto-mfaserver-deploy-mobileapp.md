---
title: Azure MFA Server Mobile App Web 服務-Azure Active Directory
description: 設定 MFA 伺服器以將推播通知傳送給具有 Microsoft Authenticator App 的使用者。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 785c4ccf591ce6a7a50a3d144fb1740121c73ce6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80653197"
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>使用 Azure Multi-Factor Authentication Server 來啟用行動應用程式驗證

Microsoft Authenticator 應用程式提供額外的頻外驗證選項。 Azure 多因素驗證會將通知推送至使用者的智慧型手機或平板電腦上的 Microsoft Authenticator 應用程式，而不是在登入期間將自動通話或 SMS 放到使用者。 使用者只需要在應用程式中按 [**驗證**] （或輸入 PIN，然後按 [驗證]），即可完成其登入。

當手機收訊不可靠時，建議使用行動應用程式進行兩步驟驗證。 如果您使用此應用程式作為 OATH 權杖產生器，它並不需要任何網路或網際網路連線。

> [!IMPORTANT]
> 從2019年7月1日起，Microsoft 將不再為新的部署提供 MFA 伺服器。 新客戶若想要從他們的使用者要求多重要素驗證，應該使用雲端式 Azure 多重要素驗證。 在7月1日前啟動 MFA Server 的現有客戶將能夠下載最新版本、未來的更新，並如往常般產生啟用認證。

> [!IMPORTANT]
> 若您已安裝 Azure Multi-Factor Authentication Server v8.x 或更新版本，則不需要執行以下大部分的步驟都。 您可以依照[設定行動應用程式](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server)下的步驟，設定行動應用程式驗證。

## <a name="requirements"></a>需求

若要使用 Microsoft Authenticator 應用程式，您必須執行 Multi-Factor Authentication Server v8.x 或更高版本

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>在 Azure Multi-Factor Authentication Server 中配置行動應用程式設定

1. 在 Multi-Factor Authentication Server 主控台中，按一下 [使用者入口網站] 圖示。 如果您允許使用者控制其驗證方法，請在 [設定] 索引標籤之 [允許使用者選取方法]**** 下方選取 [行動應用程式]****。 若未啟用這個功能，使用者就必須連絡技術支援人員來完成行動裝置應用程式啟用。
2. 勾選 [**允許使用者啟用行動裝置應用程式**] 方塊。
3. 勾選 [**允許使用者註冊**] 方塊。
4. 按一下 [行動裝置**應用程式**] 圖示。
5. 以要顯示在此帳戶的行動裝置應用程式中的公司或組織名稱填入 [帳戶名稱]**** 欄位。
   ![MFA Server 組態行動裝置應用程式設定](./media/howto-mfaserver-deploy-mobileapp/mobile.png)

## <a name="next-steps"></a>後續步驟

- [使用 Azure Multi-Factor Authentication 與協力廠商 VPN 的進階案例](howto-mfaserver-nps-vpn.md)。
