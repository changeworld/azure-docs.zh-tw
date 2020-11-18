---
title: 偵錯工具 Proxy 連接器-Azure Active Directory |Microsoft Docs
description: Azure Active Directory (Azure AD) 應用程式 Proxy 連接器的偵測問題。
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
ms.openlocfilehash: cbbb5328f2d7e814be9b5b94ec522bbb01df39e5
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658242"
---
# <a name="debug-application-proxy-connector-issues"></a>針對應用程式 Proxy 連接器問題進行偵錯 

本文可協助您針對 Azure Active Directory (Azure AD) 應用程式 Proxy 連接器的問題進行疑難排解。 如果您使用應用程式 Proxy 服務來進行內部部署 web 應用程式的遠端存取，但無法連線到應用程式，請使用此流程圖來偵測連接器問題。 

## <a name="before-you-begin"></a>開始之前

本文假設您已安裝應用程式 Proxy 連接器，而且發生問題。 針對應用程式 Proxy 問題進行疑難排解時，建議您從這個疑難排解流程開始，判斷是否已正確設定應用程式 Proxy 連接器。 如果您在連線到應用程式時仍遇到問題，請遵循「偵測 [應用程式 Proxy 應用程式」問題](application-proxy-debug-apps.md)中的疑難排解流程。  


如需應用程式 Proxy 和使用其連接器的詳細資訊，請參閱：

- [透過應用程式 Proxy 遠端存取內部部署應用程式](application-proxy.md)
- [應用程式 Proxy 連接器](application-proxy-connectors.md)
- [安裝並註冊連接器](application-proxy-add-on-premises-application.md)
- [針對應用程式 Proxy 問題和錯誤訊息進行疑難排解](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>連接器問題的流程圖

本流程圖將逐步引導您進行一些較常見的連接器問題的偵錯工具。 如需每個步驟的詳細資訊，請參閱流程圖後面的表格。

![顯示偵錯工具步驟的流程圖](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

| 步驟 | 動作 | 描述 |
|---------|---------|---------|
|1 | 尋找指派給應用程式的連接器群組 | 您可能已在多部伺服器上安裝連接器，在這種情況下，連接器應 [指派給連接器群組](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)。 若要深入了解連接器群組，請參閱[使用連接器群組在個別的網路和位置上發佈應用程式](application-proxy-connector-groups.md)。 |
|2 | 安裝連接器並指派群組 | 如果您未安裝連接器，請參閱 [安裝和註冊連接器](application-proxy-add-on-premises-application.md#install-and-register-a-connector)。<br></br> 如果您在安裝連接器時發生問題，請參閱 [安裝連接器時發生問題](application-proxy-connector-installation-problem.md)。<br></br> 如果未將連接器指派給群組，請參閱 [將連接器指派給群組](application-proxy-connector-groups.md#create-connector-groups)。<br></br>如果未將應用程式指派給連接器群組，請參閱 [將應用程式指派給連接器群組](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)。|
|3 | 在連接器伺服器上執行埠測試 | 在連接器伺服器上，使用 [telnet](/windows-server/administration/windows-commands/telnet) 或其他埠測試控管來執行埠測試，以檢查埠 [443 和80是否已開啟](application-proxy-add-on-premises-application.md#open-ports)。|
|4 | 設定網域和埠 | [請確定已正確設定您的網域和埠](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) 若要讓連接器正常運作，必須開啟某些埠，以及您的伺服器必須能夠存取的 Url。 |
|5 | 檢查後端 proxy 是否正在使用中 | 檢查連接器是否正在使用後端 proxy 伺服器，或略過它們。 如需詳細資訊，請參閱針對 [連接器 proxy 問題和服務連線能力問題進行疑難排解](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues)。 |
|6 | 更新連接器和更新程式以使用後端 proxy | 如果後端 proxy 正在使用中，您會想要確保連接器使用相同的 proxy。 如需疑難排解和設定連接器以使用 proxy 伺服器的詳細資訊，請參閱使用 [現有的內部部署 proxy 伺服器](application-proxy-configure-connectors-with-proxy-servers.md)。 |
|7 | 在連接器伺服器上載入應用程式的內部 URL | 在連接器伺服器上，載入應用程式的內部 URL。 |
|8 | 檢查內部網路連線能力 | 您的內部網路發生連線問題，表示此偵錯工具無法診斷。 您必須能夠在內部存取應用程式，連接器才能運作。 您可以啟用和查看連接器事件記錄檔，如 [應用程式 Proxy 連接器](application-proxy-connectors.md#under-the-hood)中所述。 |
|9 | 延長後端的超時值 | 在您應用程式的 **其他設定** 中，將 **後端應用程式的超時** 設定變更為 **Long**。 請參閱 [將內部部署應用程式新增至 Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。 |
|10 | 如果問題持續發生，請將目標設為特定流程問題、檢查應用程式和 SSO 的流程 | 使用「 [偵錯工具 Proxy 應用程式」問題](application-proxy-debug-apps.md) 疑難排解流程。 |

## <a name="next-steps"></a>後續步驟


* [使用連接器群組在個別的網路和位置上發佈應用程式](application-proxy-connector-groups.md)
* [使用現有的內部部署 Proxy 伺服器](application-proxy-configure-connectors-with-proxy-servers.md)
* [針對應用程式 Proxy 和連接器錯誤進行疑難排解](application-proxy-troubleshoot.md)
* [如何以無訊息方式安裝 Azure AD 應用程式 Proxy 連接器](application-proxy-register-connector-powershell.md)