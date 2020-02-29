---
title: Azure AD Connect 雲端布建代理程式：自動升級 |Microsoft Docs
description: 本文說明 Azure AD Connect 雲端布建代理程式中的內建自動升級功能。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f09b2fc685881aa8a7bd87b6a855c657af9ef43d
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190308"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect 雲端布建代理程式：自動升級

透過自動升級功能，確保您的 Azure Active Directory （Azure AD）連接雲端布建代理程式安裝一律是最新的。

代理程式安裝于此處：「Program files\Azure AD Connect 布建 Agent\AADConnectProvisioningAgent.exe」

若要驗證您的版本，請以滑鼠右鍵按一下可執行檔，然後選取 [屬性] 和 [詳細資料]

![代理程式檔案版本](media/how-to-automatic-upgrade/agent1.png)

代理程式更新程式安裝于此處：「Program files\Azure AD Connect 布建代理程式 Updater\AzureADConnectAgentUpdater.exe」

若要驗證您的版本，請以滑鼠右鍵按一下可執行檔，然後選取 [屬性] 和 [詳細資料]

![代理程式更新程式版本](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>解除安裝代理程式
若要移除代理程式，請移至**卸載或變更程式**並卸載下列專案：

- **Microsoft Azure AD 連接代理程式更新程式**
- **Microsoft Azure AD 連接布建代理程式**
- **Microsoft Azure AD 連接布建代理程式套件**

![代理程式移除](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>後續步驟 

- [什麼是佈建？](what-is-provisioning.md)
- [什麼是 Azure AD Connect 雲端佈建？](what-is-cloud-provisioning.md)

