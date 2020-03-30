---
title: 調試應用程式代理連接器 - Azure 活動目錄 |微軟文檔
description: Azure 活動目錄 （Azure AD） 應用程式代理連接器的調試問題。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: c041578932bd33eb0a2d3afc18a35c2c0458dc8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72311841"
---
# <a name="debug-application-proxy-connector-issues"></a>針對應用程式 Proxy 連接器問題進行偵錯 

本文可説明您解決 Azure 活動目錄 （Azure AD） 應用程式代理連接器的問題。 如果您使用應用程式代理服務遠端存取本地 Web 應用程式，但連接到應用程式時遇到問題，請使用此流程圖調試連接器問題。 

## <a name="before-you-begin"></a>開始之前

本文假定您已安裝應用程式代理連接器，並且出現問題。 在排除應用程式代理問題時，我們建議您從此故障排除流開始，以確定應用程式代理連接器是否配置正確。 如果連接到應用程式時仍有問題，請按照[調試應用程式代理應用程式中](application-proxy-debug-apps.md)的故障排除流操作。  


有關應用程式代理及其連接器的詳細資訊，請參閱：

- [通過應用程式代理遠端存取本地應用程式](application-proxy.md)
- [應用程式代理連接器](application-proxy-connectors.md)
- [安裝並註冊連接器](application-proxy-add-on-premises-application.md)
- [針對應用程式 Proxy 問題和錯誤訊息進行疑難排解](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>連接器問題的流程圖

此流程圖將引導您完成調試一些更常見連接器問題的步驟。 有關每個步驟的詳細資訊，請參閱流程圖下面的表。

![顯示調試連接器步驟的流程圖](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

|  | 動作 | 描述 | 
|---------|---------|---------|
|1 | 查找分配給應用的連接器組 | 您可能在多個伺服器上安裝了連接器，在這種情況下，連接器應[分配給連接器組](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)。 若要深入了解連接器群組，請參閱[使用連接器群組在個別的網路和位置上發佈應用程式](application-proxy-connector-groups.md)。 |
|2 | 安裝連接器並分配組 | 如果未安裝連接器，請參閱[安裝和註冊連接器](application-proxy-add-on-premises-application.md#install-and-register-a-connector)。<br></br> 如果您在安裝連接器時遇到問題，請參閱[安裝連接器時出現問題](application-proxy-connector-installation-problem.md)。<br></br> 如果連接器未分配給組，請參閱[將連接器分配給組](application-proxy-connector-groups.md#create-connector-groups)。<br></br>如果應用程式未分配給連接器組，請參閱[將應用程式分配給連接器組](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)。|
|3 | 在連接器伺服器上運行埠測試 | 在連接器伺服器上，使用[telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet)或其他埠測試控管運行埠測試，以檢查埠 443 和 80 是否打開。|
|4 | 配置域和埠 | [確保域和埠配置正確](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)要使連接器正常工作，某些埠必須打開，並且伺服器必須能夠訪問 URL。 |
|5 | 檢查後端代理是否正在使用中 | 檢查連接器是否使用後端代理伺服器或繞過它們。 有關詳細資訊，請參閱[排除連接器代理問題和服務連接問題](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues)。 |
|6 | 更新連接器和更新程式以使用後端代理 | 如果後端代理正在使用中，則需要確保連接器使用相同的代理。 有關故障排除和配置連接器以使用代理伺服器的詳細資訊，請參閱[使用現有的本地代理伺服器](application-proxy-configure-connectors-with-proxy-servers.md)。 |
|7 | 在連接器伺服器上載入應用的內部 URL | 在連接器伺服器上，載入應用的內部 URL。 |
|8 | 檢查內部網路連接 | 內部網路中存在此調試流無法診斷的連接問題。 連接器必須在內部訪問應用程式才能正常工作。 您可以啟用和查看連接器事件日誌，如[應用程式代理連接器](application-proxy-connectors.md#under-the-hood)中所述。 |
|9 | 延長後端的超時值 | 在應用程式的 **"其他設置"** 中，將**後端應用程式超時**設置更改為 **"長**"。 請參閱[將本地應用添加到 Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。 |
|10 | 如果問題仍然存在，則針對特定流問題，查看應用和 SSO 調試流 | 使用[調試應用程式代理應用程式問題](application-proxy-debug-apps.md)故障排除流。 |

## <a name="next-steps"></a>後續步驟


* [使用連接器群組在個別的網路和位置上發佈應用程式](application-proxy-connector-groups.md)
* [使用現有的內部部署 Proxy 伺服器](application-proxy-configure-connectors-with-proxy-servers.md)
* [針對應用程式 Proxy 和連接器錯誤進行疑難排解](application-proxy-troubleshoot.md)
* [如何靜默安裝 Azure AD 應用程式代理連接器](application-proxy-register-connector-powershell.md)
