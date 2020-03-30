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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471493"
---
條件訪問允許基於每個應用程式的細細微性存取控制。 為了使用條件訪問，應將 Azure AD 高級 1 或更高許可應用於受條件訪問規則約束的使用者。

1. 導航到**企業應用程式 - 所有應用程式**頁面，然後按一下**Azure VPN**。

   - 按一下 **"條件訪問**"。
   - 按一下 [新增原則]**** 以開啟 [新增]**** 窗格。
2. 在 **"新建"** 窗格中，導航到 **"分配 -> 使用者和組**。 在 **"使用者和組 ->** **包括"** 選項卡上：

   - 按一下 [選取使用者和群組]****。
   - 檢查**使用者和組**。
   - 按一下"**選擇"** 以選擇要受 MFA 影響的使用者組或組。
   - 按一下 [完成]****。

   ![指派](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. 在 **"新建"** 窗格中，導航到 **"訪問"控制項 ->授予**窗格：

   - 按一下 **"授予存取權限**"。
   - 按一下 **"需要多重要素驗證**"。
   - 按一下 **"要求所有選定的控制項**"。
   - 按一下 **"選擇**"。
   
   ![授予存取權限 - MFA](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. 在 **"啟用策略**"部分中：

   - 選擇 **"打開**"。
   - 按一下 **[建立]**。

   ![啟用策略](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)