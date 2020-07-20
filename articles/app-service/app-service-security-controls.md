---
title: Azure App Service 的安全性控制項
description: 尋找安全性控制的檢查清單，以評估貴組織的 Azure App Service。
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 9b0c336d9d9c463a7ed7acf42eaf292ecce4f1bb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85831143"
---
# <a name="security-controls-for-azure-app-service"></a>Azure App Service 的安全性控制項

本文記載內建在 Azure App Service 中的安全性控制項。

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 備註 | 文件
|---|---|--|
| 服務端點支援| Yes | 適用于 App Service。| [Azure App Service 存取限制](app-service-ip-restrictions.md)
| VNet 插入支援| Yes | App Service 環境是專用於單一客戶的 App Service 私用，並插入客戶的虛擬網路中。 | [App Service 環境簡介](environment/intro.md)
| 網路隔離和防火牆支援| Yes | 針對 App Service 的公用多租使用者變化，客戶可以設定網路 Acl （IP 限制）來鎖定允許的輸入流量。  App Service 環境會直接部署到虛擬網路中，因此可以使用 Nsg 來保護。 | [Azure App Service 存取限制](app-service-ip-restrictions.md)
| 強制通道支援| Yes | App Service 環境可以部署到客戶的虛擬網路，其中已設定強制通道。 | [設定 App Service 環境搭配強制通道](environment/forced-tunnel-support.md)

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 備註 | 文件
|---|---|--|
| Azure 監視支援（Log analytics、App insights 等）| Yes | App Service 與支援 Application Insights 的語言 Application Insights 整合（完整 .NET Framework、.NET Core、JAVA 和 Node.JS）。  請參閱[監視 Azure App Service 效能](../azure-monitor/app/azure-web-apps.md)。 App Service 也會將應用程式計量傳送至 Azure 監視器。 | [監視 Azure App Service 中的應用程式](web-sites-monitor.md)
| 控制和管理平面記錄和審核| Yes | 在 App Service 物件上執行的所有管理作業都是透過[Azure Resource Manager](../azure-resource-manager/index.yml)進行。 這些作業的歷程記錄可以在入口網站中和透過 CLI 取得。 | [Azure Resource Manager 資源提供者作業](../role-based-access-control/resource-provider-operations.md#microsoftweb)， [az monitor activity-log](/cli/azure/monitor/activity-log)
| 資料平面記錄和審核 | No | App Service 的資料平面是遠端檔案共用，其中包含客戶已部署的網站內容。  不會對遠端檔案共用進行任何審核。 |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 備註 |  文件
|---|---|--|
| 驗證| Yes | 客戶可以在自動整合[Azure Active Directory （Azure AD）](../active-directory/index.yml)的 App Service 和其他與 OAuth 相容的識別提供者之間建立應用程式，以進行 App Service 資產的管理存取，所有存取都是由 Azure AD 已驗證的主體和 Azure RBAC 的組合控制。 | [Azure App Service 的驗證和授權](overview-authentication-authorization.md)
| 授權| Yes | 對於 App Service 資產的管理存取權，所有存取都是由 Azure AD 已驗證的主體和 Azure RBAC 的組合來控制。  | [Azure App Service 的驗證和授權](overview-authentication-authorization.md)

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 備註 | 文件
|---|---|--|
| 待用的伺服器端加密： Microsoft 管理的金鑰 | Yes | 網站檔案內容會儲存在 Azure 儲存體中，這會自動將待用內容加密。 <br><br>客戶提供的秘密會在待用時加密。 秘密會在待用時加密，並儲存在 App Service 設定資料庫中。<br><br>網站（D:\local 和% TMP%）可以選擇性地使用本機連接的磁片做為暫時儲存。 本機連接的磁片不會在待用時加密。 | [待用資料的 Azure 儲存體加密](../storage/common/storage-service-encryption.md)
| 待用的伺服器端加密：客戶管理的金鑰（BYOK） | Yes | 客戶可以選擇將應用程式秘密儲存在 Key Vault 中，並在執行時間取得它們。 | [使用 App Service 和 Azure Functions 的 Key Vault 參考 (預覽)](app-service-key-vault-references.md)
| 資料行層級加密（Azure 資料服務）| N/A | |
| 傳輸中的加密（例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密）| Yes | 客戶可以將網站設定為要求並使用 HTTPS 來輸入流量。  | [如何建立僅 AZURE APP SERVICE HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/) （blog 文章）
| API 呼叫加密| Yes | 透過 HTTPS [Azure Resource Manager](../azure-resource-manager/index.yml)呼叫來設定 App Service 進行管理呼叫。 |

## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 備註 | 文件
|---|---|--|
| 設定管理支援（設定的版本設定等）| Yes | 針對管理作業，App Service 設定的狀態可以匯出為 Azure Resource Manager 範本，並在一段時間後進行版本設定。 針對執行時間作業，客戶可以使用 App Service 部署位置] 功能來維護應用程式的多個不同即時版本。 | 

## <a name="next-steps"></a>後續步驟

- 深入瞭解[跨 Azure 服務的內建安全性控制](../security/fundamentals/security-controls.md)。
