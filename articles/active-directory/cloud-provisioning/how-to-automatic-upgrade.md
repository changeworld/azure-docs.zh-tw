---
title: Azure AD 連接雲預配代理：自動升級 |微軟文檔
description: 本文介紹了 Azure AD Connect 雲預配代理中的內置自動升級功能。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190308"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD 連接雲預配代理：自動升級

通過自動升級功能，請確保 Azure 活動目錄 （Azure AD） 連接雲預配代理安裝始終保持最新。

代理安裝在此處："程式檔_Azure AD 連接預配代理_AADConnect配置代理.exe"

要驗證版本，請按右鍵可執行檔並選擇屬性，然後選擇詳細資訊。

![代理檔版本](media/how-to-automatic-upgrade/agent1.png)

代理更新程式安裝在此處："程式檔_Azure 連接預配代理更新程式_AzureADConnectAgentUpdater.exe"

要驗證版本，請按右鍵可執行檔並選擇屬性，然後選擇詳細資訊。

![代理更新程式版本](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>解除安裝代理程式
要刪除代理，請轉到**卸載或更改程式**並卸載以下內容：

- **Microsoft Azure AD Connect 代理程式更新程式**
- **Microsoft Azure AD Connect 佈建代理程式**
- **Microsoft Azure AD Connect 佈建代理程式套件**

![代理刪除](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>後續步驟 

- [什麼是佈建？](what-is-provisioning.md)
- [什麼是 Azure AD Connect 雲端佈建？](what-is-cloud-provisioning.md)

