---
title: 整合內部部署應用程式與 Cloud App Security Azure AD
description: 設定 Azure Active Directory 中的內部部署應用程式，以便與 Microsoft Cloud App Security (MCAS) 搭配運作。 使用 MCAS 條件式存取應用程式控制，根據條件式存取原則即時監視和控制會話。 您可以將這些原則套用至內部部署應用程式，以在 Azure Active Directory (Azure AD) 中使用應用程式 Proxy。
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb97f9dd87277215a5d4708d3a6f49564c490204
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74275502"
---
# <a name="configure-real-time-application-access-monitoring-with-microsoft-cloud-app-security-and-azure-active-directory"></a>使用 Microsoft Cloud App Security 與 Azure Active Directory 設定即時應用程式存取監視
設定 Azure Active Directory (Azure AD) 中的內部部署應用程式，以使用 Microsoft Cloud App Security (MCAS) 進行即時監視。 MCAS 會使用條件式存取應用程式控制，根據條件式存取原則即時監視和控制會話。 您可以將這些原則套用至內部部署應用程式，以在 Azure Active Directory (Azure AD) 中使用應用程式 Proxy。

以下一些範例是您可以使用 MCAS 建立的原則類型：

- 封鎖或保護非受控裝置上的敏感性文件下載。
- 監視高風險使用者何時登入應用程式，然後在工作階段中記錄其動作。 透過此資訊，您可以分析使用者行為來判斷如何套用工作階段原則。
- 使用用戶端憑證或裝置相容性，來封鎖非受控裝置對特定應用程式的存取。
- 限制來自非公司網路的使用者工作階段。 您可以為從公司網路外部存取應用程式之使用者提供受限制的存取權。 例如，這個受限制的存取權會封鎖使用者下載敏感性文件的動作。

如需詳細資訊，請參閱[使用 Microsoft Cloud App Security 條件式存取應用程式控制來保護應用程式](/cloud-app-security/proxy-intro-aad)。

## <a name="requirements"></a>需求

授權：

- EMS E5 授權，或 
- Azure Active Directory Premium P1 和 MCAS 獨立式授權。

內部部署應用程式：

- 內部部署應用程式必須使用 Kerberos 限制委派 (KCD)

設定應用程式 Proxy：

- 設定 Azure AD 以使用應用程式 Proxy，包括準備您的環境，以及安裝應用程式 Proxy 連接器。 如需教學課程，請參閱[新增內部部署應用程式以便透過 Azure AD 中的應用程式 Proxy 進行遠端存取](application-proxy-add-on-premises-application.md)。 

## <a name="add-on-premises-application-to-azure-ad"></a>將內部部署應用程式新增至 Azure AD

將內部部署應用程式新增至 Azure AD。 如需快速入門，請參閱[將內部部署應用程式新增至 Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。 新增應用程式時，請務必在 [新增內部部署應用程式]**** 刀鋒視窗中設定下列兩項設定：

- **預先驗證**：輸入**Azure Active Directory**。
- **轉譯應用程式主體中的 url**：選擇 **[是]**。

應用程式若要與 MCAS 搭配運作，就需要這兩個設定。

## <a name="test-the-on-premises-application"></a>測試內部部署應用程式

將應用程式新增至 Azure AD 之後，請使用[測試應用程式](application-proxy-add-on-premises-application.md#test-the-application)中的步驟，新增使用者以進行測試，然後測試登入。 

## <a name="deploy-conditional-access-app-control"></a>部署條件式存取應用程式控制

若要使用條件式存取應用程式控制來設定您的應用程式，請遵循[為 Azure AD 應用程式部署條件式存取應用程式控制](/cloud-app-security/proxy-deployment-aad)中的指示。


## <a name="test-conditional-access-app-control"></a>測試條件式存取應用程式控制

若要測試使用條件式存取應用程式的 Azure AD 應用程式部署，請遵循[測試 Azure AD 應用程式部署](/cloud-app-security/proxy-deployment-aad)中的指示。





