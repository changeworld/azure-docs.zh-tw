---
title: Debug 應用程式 Proxy 連接器-Azure Active Directory |Microsoft Docs
description: Azure Active Directory (Azure AD) 應用程式 Proxy 連接器的 Debug 問題。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/21/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 7e7a1de24e5032b2dade2f325560fd6964c892d5
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86145689"
---
# <a name="debug-application-proxy-connector-issues"></a>針對應用程式 Proxy 連接器問題進行偵錯 

本文可協助您針對 Azure Active Directory (Azure AD) 應用程式 Proxy 連接器的問題進行疑難排解。 如果您要使用應用程式 Proxy 服務來遠端存取內部部署 web 應用程式，但無法連線到應用程式，請使用此流程圖來偵測連接器問題。 

## <a name="before-you-begin"></a>開始之前

本文假設您已安裝應用程式 Proxy 連接器，而且發生問題。 針對應用程式 Proxy 問題進行疑難排解時，建議您從這個疑難排解流程開始，以判斷是否已正確設定應用程式 Proxy 連接器。 如果您在連接至應用程式時仍然遇到問題，請遵循[Debug Application Proxy 應用程式問題](application-proxy-debug-apps.md)中的疑難排解流程。  


如需有關應用程式 Proxy 和使用其連接器的詳細資訊，請參閱：

- [透過應用程式 Proxy 遠端存取內部部署應用程式](application-proxy.md)
- [應用程式 Proxy 連接器](application-proxy-connectors.md)
- [安裝並註冊連接器](application-proxy-add-on-premises-application.md)
- [針對應用程式 Proxy 問題和錯誤訊息進行疑難排解](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>連接器問題的流程圖

此流程圖會逐步引導您進行一些常見的連接器問題的偵錯工具。 如需每個步驟的詳細資訊，請參閱流程圖後面的表格。

![顯示偵錯工具步驟的流程圖](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

| 步驟 | 動作 | 描述 |
|---------|---------|---------|
|1 | 尋找指派給應用程式的連接器群組 | 您可能會在多部伺服器上安裝連接器，在此情況下，連接器應[指派給連接器群組](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)。 若要深入了解連接器群組，請參閱[使用連接器群組在個別的網路和位置上發佈應用程式](application-proxy-connector-groups.md)。 |
|2 | 安裝連接器並指派群組 | 如果您未安裝連接器，請參閱[安裝並註冊連接器](application-proxy-add-on-premises-application.md#install-and-register-a-connector)。<br></br> 如果您在安裝連接器時遇到問題，請參閱[安裝連接器時發生問題](application-proxy-connector-installation-problem.md)。<br></br> 如果未將連接器指派給群組，請參閱[將連接器指派給群組](application-proxy-connector-groups.md#create-connector-groups)。<br></br>如果未將應用程式指派給連接器群組，請參閱[將應用程式指派給連接器群組](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)。|
|3 | 在連接器伺服器上執行埠測試 | 在連接器伺服器上，使用[telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet)或其他埠測試控管執行埠測試，以檢查埠[443 和80是否已開啟](application-proxy-add-on-premises-application.md#open-ports)。|
|4 | 設定網域和埠 | [請確定已正確設定您的網域和埠](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)為了讓連接器能夠正常運作，有某些必須開啟的埠，以及您的伺服器必須能夠存取的 Url。 |
|5 | 檢查後端 proxy 是否正在使用中 | 查看連接器是否使用後端 proxy 伺服器或略過它們。 如需詳細資訊，請參閱針對[連接器 proxy 問題和服務連線問題進行疑難排解](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues)。 |
|6 | 更新連接器和更新程式，以使用後端 proxy | 如果正在使用後端 proxy，您會想要確保連接器使用相同的 proxy。 如需疑難排解和設定連接器以使用 proxy 伺服器的詳細資訊，請參閱使用[現有的內部部署 proxy 伺服器](application-proxy-configure-connectors-with-proxy-servers.md)。 |
|7 | 在連接器伺服器上載入應用程式的內部 URL | 在連接器伺服器上，載入應用程式的內部 URL。 |
|8 | 檢查內部網路連線能力 | 您的內部網路發生連線問題，這是因為這項調試流程無法診斷。 應用程式必須在內部存取，連接器才能正常執行。 您可以依照[應用程式 Proxy 連接器](application-proxy-connectors.md#under-the-hood)中的說明，啟用及查看連接器事件記錄。 |
|9 | 延長後端的超時值 | 在應用程式的**其他設定**中，將 [**後端應用程式超時**] 設定變更為 [**長**]。 請參閱[將內部部署應用程式新增至 Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。 |
|10 | 如果問題持續發生，請鎖定特定流程問題，並查看應用程式和 SSO 的偵測流程 | 使用[Debug Application Proxy 應用程式問題](application-proxy-debug-apps.md)疑難排解流程。 |

## <a name="next-steps"></a>後續步驟


* [使用連接器群組在個別的網路和位置上發佈應用程式](application-proxy-connector-groups.md)
* [使用現有的內部部署 Proxy 伺服器](application-proxy-configure-connectors-with-proxy-servers.md)
* [針對應用程式 Proxy 和連接器錯誤進行疑難排解](application-proxy-troubleshoot.md)
* [如何以無訊息方式安裝 Azure AD 應用程式 Proxy 連接器](application-proxy-register-connector-powershell.md)
