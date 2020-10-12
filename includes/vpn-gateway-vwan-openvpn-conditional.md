---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 410570302eec418f1e4bcb75d6413936a96b5171
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "77471493"
---
條件式存取可讓您以每個應用程式為基礎，進行更細微的存取控制。 若要使用條件式存取，您應該將 Azure AD Premium 1 或更高的授權套用至將受條件式存取規則所規範的使用者。

1. 流覽至 [ **企業應用程式-所有應用程式** ] 頁面，然後按一下 [ **Azure VPN**]。

   - 按一下 [ **條件式存取**]。
   - 按一下 [新增原則] 以開啟 [新增] 窗格。
2. 在 **新** 的窗格中，流覽至 [ **指派-> 使用者和群組**]。 在 [ **使用者和群組->** **包含** ] 索引標籤上：

   - 按一下 [選取使用者和群組]****。
   - 檢查 **使用者和群組**。
   - 按一下 [ **選取** ]，以選取要受 MFA 影響的群組或一組使用者。
   - 按一下 [完成]。

   ![作業](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. 在 **新** 的窗格中，流覽至 [ **存取控制-> 授** 與] 窗格：

   - 按一下 **[授與存取權**]。
   - 按一下 [ **需要多重要素驗證**]。
   - 按一下 **[需要所有選取的控制項**]。
   - 按一下 [選取]。
   
   ![授與存取權-MFA](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. 在 [ **啟用原則** ] 區段中：

   - 選取 [ **開啟**]。
   - 按一下 [建立]。

   ![啟用原則](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)