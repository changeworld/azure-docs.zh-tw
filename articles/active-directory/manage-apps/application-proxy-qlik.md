---
title: Azure AD 應用程式 Proxy 和 Qlik Sense | Microsoft Docs
description: 在 Azure 入口網站中開啟應用程式 Proxy，並安裝反向 Proxy 的連接器。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 09/06/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2edf63da0fd09f829f936b54eb088c34dfe029a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79037003"
---
# <a name="application-proxy-and-qlik-sense"></a>應用程式 Proxy 和 Qlik Sense 
Azure Active Directory 應用程式 Proxy 和 Qlik Sense 已搭配運作，可確保您能夠輕鬆使用「應用程式 Proxy」來為 Qlik Sense 部署提供遠端存取。  

## <a name="prerequisites"></a>先決條件 
本案例的其餘部分假設您已完成下列操作：
 
- 已設定 [Qlik Sense](https://community.qlik.com/docs/DOC-19822)。 
- [已安裝應用程式 Proxy 連接器](application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>在 Azure 中發佈應用程式 
若要發佈 QlikSense，您將需要在 Azure 中發佈兩個應用程式。  

### <a name="application-1"></a>應用程式 #1： 
請遵循下列步驟來發佈您的應用程式。 如需步驟 1-8 的更詳細逐步解說，請參閱[使用 Azure AD Application Proxy 發佈應用程式](application-proxy-add-on-premises-application.md)。 


1. 以系統管理員身分登入 Azure 入口網站。 
2. 選取 [ **Azure Active Directory** > **企業應用程式**]。 
3. 在刀鋒視窗頂端選取 [新增]****。 
4. 選取**內部部署應用程式**。 
5. 使用新應用程式的相關資訊填寫必要的欄位。 使用下列指導方針設定︰ 
   - **內部 URL**：此應用程式應該具有就是 QlikSense URL 本身的內部 URL。 例如，**https&#58;//demo.qlikemm.com:4244** 
   - **預先驗證方法**： Azure Active Directory （建議使用，但非必要） 
1. 選取刀鋒視窗底部的 [新增]****。 已新增您的應用程式，快速入門功能表隨即開啟。 
2. 在 [快速啟動] 功能表中，選取 [指派測試使用者]****，並將至少一個使用者新增至應用程式。 請確定此測試帳戶可存取內部部署應用程式。 
3. 選取**指派**以儲存測試使用者指派。 
4. (選擇性) 在 [應用程式管理] 刀鋒視窗中，選取 [單一登入]。 從下拉式功能表中選擇 [Kerberos 限制委派]****，然後根據您的 Qlik 設定填寫必要的欄位。 選取 [儲存]  。 

### <a name="application-2"></a>應用程式 #2： 
請依照用於第 1 個應用程式的相同步驟進行操作，但下列除外： 

**步驟 #5**：「內部 URL」現在應該是 QlikSense URL 搭配應用程式所使用的驗證連接埠。 HTTPS 的預設值是**4244** ， **4248**則適用于4月2018之前的 QlikSense 版本。 2018年4月後的 QlikSense 版本預設為**443** ，適用于 HTTP 的 HTTPS 和**80** 。  例如：**https&#58;//demo.qlik.com:4244**</br></br>
**步驟 #10：** 請勿設定 SSO，讓**單一登入保持停用**
 
 
## <a name="testing"></a>測試 
您的應用程式現已完成測試準備。 請存取您在應用程式 #1 中用來發佈 QlikSense 的外部 URL，然後以已指派給這兩個應用程式的使用者身分登入。  

## <a name="additional-references"></a>其他參考資料
如需有關使用應用程式 Proxy 發佈 Qlik sense 意義的詳細資訊，請參閱下列 Qlik sense 的社區文章： 
- [使用具有 Qlik sense 意義之 Kerberos 限制委派的整合式 Windows 驗證 Azure AD](https://community.qlik.com/docs/DOC-20183)
- [與 Azure AD 應用程式 Proxy 的 qlik sense 意義整合](https://community.qlik.com/t5/Technology-Partners-Ecosystem/Azure-AD-Application-Proxy/ta-p/1528396)

## <a name="next-steps"></a>後續步驟

- [使用應用程式 Proxy 發行應用程式](application-proxy-add-on-premises-application.md)
- [使用應用程式 Proxy 連接器](application-proxy-connector-groups.md)

