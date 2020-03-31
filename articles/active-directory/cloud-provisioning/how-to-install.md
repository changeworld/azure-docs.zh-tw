---
title: 安裝 Azure AD Connect 雲端佈建代理程式
description: 本文介紹如何安裝 Azure AD 連接雲預配代理。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4581ab89f74425682f569425f62714ead2c27c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263344"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>安裝 Azure AD Connect 雲端佈建代理程式
本文檔將引導您完成 Azure 活動目錄 （Azure AD） 連接預配代理的安裝過程以及如何在 Azure 門戶中最初配置它。

>[!IMPORTANT]
>以下安裝說明假定已滿足所有[先決條件](how-to-prerequisites.md)。

安裝和配置 Azure AD 連接預配在以下步驟中完成：
    
- [安裝代理程式](#install-the-agent)
- [驗證代理程式安裝](#verify-agent-installation)


## <a name="install-the-agent"></a>安裝代理程式
要安裝代理，請按照以下步驟操作。

1. 登錄到您將使用企業管理員許可權的伺服器。
1. 移至 Azure 入口網站。 在左側，選擇**Azure 活動目錄**。
1. 選擇 **"管理預配（預覽）"** > **查看所有代理**。
1. 從 Azure 門戶下載 Azure AD 連接預配代理。

   ![下載本地代理](media/how-to-install/install9.png)</br>
1. 運行 Azure AD 連接預配安裝程式（AADConnect 預配代理.安裝程式）。
1. 在**Microsoft Azure AD 連接預配代理包螢幕上**，接受授權條款並選擇 **"安裝**"。

   ![微軟 Azure AD 連接預配代理包螢幕](media/how-to-install/install1.png)</br>

1. 此操作完成後，設定精靈將啟動。 以 Azure AD 全域管理員帳戶登入。
1. 在 **"連接活動目錄"** 螢幕上，選擇 **"添加目錄**"。 然後使用活動目錄管理員帳戶登錄。 此操作將添加本地目錄。 選取 [下一步]****。

   ![連接活動目錄螢幕](media/how-to-install/install3.png)</br>

1. 在 **"配置完整**"螢幕上，選擇 **"確認**"。 此操作註冊並重新啟動代理。

   ![設定完成畫面](media/how-to-install/install4.png)</br>

1. 此操作完成後，您應該會看到已**成功驗證代理配置的通知。** 選擇**退出**。

   ![結束按鈕](media/how-to-install/install5.png)</br>
1. 如果仍然看到初始的**Microsoft Azure AD 連接預配代理包**螢幕，請選擇 **"關閉**"。

## <a name="verify-agent-installation"></a>驗證代理程式安裝
代理驗證發生在 Azure 門戶和運行代理的本機伺服器上。

### <a name="azure-portal-agent-verification"></a>Azure 入口網站代理程式驗證
要驗證 Azure 正在看到代理，請按照以下步驟操作。

1. 登入 Azure 入口網站。
1. 在左側，選擇**Azure 活動目錄** > **Azure AD 連接**。 在中心中，選擇 **"管理預配（預覽）"。**

   ![Azure 入口網站](media/how-to-install/install6.png)</br>

1.  在**Azure AD 預配（預覽）** 螢幕上，選擇 **"查看所有代理**"。

    ![查看所有代理選項](media/how-to-install/install7.png)</br>
 
1. 在**本地預配代理**螢幕上，可以看到您安裝的代理。 確認相關代理程式位於該處，且已標示為 [作用中]**。

   ![本地預配代理螢幕](media/how-to-install/verify1.png)</br>

### <a name="verify-the-port"></a>驗證埠
要驗證 Azure 正在偵聽埠 443 以及代理是否可以與它通信，請按照以下步驟操作。

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

此測試驗證代理是否可以通過埠 443 與 Azure 通信。 打開瀏覽器，然後從安裝代理的伺服器轉到以前的 URL。

![埠可到達性驗證](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>在本機伺服器上
要驗證代理是否正在運行，請按照以下步驟操作。

1.  使用管理員帳戶登錄到伺服器。
1.  通過導航到服務或訪問**啟動** > **運行** > **服務.msc**來打開**服務**。
1.  在 **"服務**"下，請確保**Microsoft Azure AD 連接代理更新程式**以及**Microsoft Azure AD 連接預配代理**已存在，並且其狀態正在*運行*。

    ![服務螢幕](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>代理已安裝，但必須先配置並啟用它才能開始同步使用者。 要配置新代理，請參閱[為 Azure AD 連接基於雲的預配創建新配置](how-to-configure.md)。



## <a name="next-steps"></a>後續步驟 

- [什麼是佈建？](what-is-provisioning.md)
- [什麼是 Azure AD Connect 雲端佈建？](what-is-cloud-provisioning.md)
 
