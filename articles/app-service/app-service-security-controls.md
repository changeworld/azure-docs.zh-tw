---
title: Azure App Service 的安全性控制
description: 找出安全性控制項的檢查清單，以評估貴組織的 Azure App Service。
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 93e5123e5b61c9013177f7f3c908578b68da52d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88962922"
---
# <a name="security-controls-for-azure-app-service"></a>Azure App Service 的安全性控制

本文記錄 Azure App Service 內建的安全性控制項。

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 注意 | 文件
|---|---|--|
| 服務端點支援| 是 | 適用于 App Service。| [Azure App Service 存取限制](app-service-ip-restrictions.md)
| VNet 插入支援| 是 | App Service 環境是專用於單一客戶（插入客戶的虛擬網路）的私用 App Service。 | [App Service 環境簡介](environment/intro.md)
| 網路隔離和防火牆支援| 是 | 針對 App Service 的公用多租使用者變化，客戶可以設定網路 Acl (IP 限制) 以鎖定允許的輸入流量。  App Service 環境會直接部署到虛擬網路中，因此可以使用 Nsg 進行保護。 | [Azure App Service 存取限制](app-service-ip-restrictions.md)
| 強制通道支援| 是 | App Service 環境可以部署到客戶的虛擬網路中，並在其中設定強制通道。 | [設定 App Service 環境搭配強制通道](environment/forced-tunnel-support.md)

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 注意 | 文件
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等 ) | 是 | App Service 會針對支援 Application Insights (完整 .NET Framework、.NET Core、JAVA 和 Node.JS) 的語言，與 Application Insights 整合。  請參閱 [監視 Azure App Service 效能](../azure-monitor/app/azure-web-apps.md)。 App Service 也會將應用程式計量傳送至 Azure 監視器。 | [監視 Azure App Service 中的應用程式](web-sites-monitor.md)
| 控制和管理平面記錄和審核| 是 | 在 App Service 物件上執行的所有管理作業都是透過 [Azure Resource Manager](../azure-resource-manager/index.yml)進行。 您可以在入口網站中和透過 CLI 取得這些作業的歷程記錄。 | [Azure Resource Manager 資源提供者作業](../role-based-access-control/resource-provider-operations.md#microsoftweb)， [az 監視器活動-記錄](/cli/azure/monitor/activity-log)
| 資料平面記錄和審核 | 否 | App Service 的資料平面是遠端檔案共用，其中包含客戶的已部署網站內容。  遠端檔案共用不會進行任何審核。 |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 注意 |  文件
|---|---|--|
| 驗證| 是 | 客戶可以在 App Service 上建立應用程式，這些應用程式會自動與 [Azure Active Directory (Azure AD) ](../active-directory/index.yml) 以及其他 OAuth 相容身分識別提供者，以管理 App Service 資產的存取權，所有存取都是透過 Azure AD 已驗證的主體和 Azure RBAC 的組合來控制。 | [Azure App Service 的驗證和授權](overview-authentication-authorization.md)
| 授權| 是 | 針對 App Service 資產的管理存取權，所有存取都是透過 Azure AD 驗證的主體和 Azure RBAC 的組合來控制。  | [Azure App Service 的驗證和授權](overview-authentication-authorization.md)

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 注意 | 文件
|---|---|--|
| 靜止的伺服器端加密： Microsoft 管理的金鑰 | 是 | 網站檔案內容會儲存在 Azure 儲存體中，這會自動加密待用內容。 <br><br>客戶提供的秘密會在靜止時加密。 密碼會在儲存于 App Service 設定資料庫時待用加密。<br><br>網站 (D:\local 和% TMP% ) 可選擇性地將本機連接的磁片用作暫存儲存體。 本機連接的磁片不會在待用時加密。 | [待用資料的 Azure 儲存體加密](../storage/common/storage-service-encryption.md)
| 靜止的伺服器端加密：客戶管理的金鑰 (BYOK)  | 是 | 客戶可以選擇將應用程式秘密儲存在 Key Vault 中，並在執行時間加以取出。 | [使用 App Service 和 Azure Functions 的 Key Vault 參考 (預覽)](app-service-key-vault-references.md)
| Azure 資料服務) 的資料行層級加密 (| N/A | |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密，以及 VNet-VNet 加密 ) | 是 | 客戶可以將網站設定為要求和使用 HTTPS 來輸入流量。  | [如何將 AZURE APP SERVICE HTTPS (的](/archive/blogs/benjaminperkins/how-to-make-an-azure-app-service-https-only) blog 文章) 
| API 呼叫加密| 是 | 設定 App Service 的管理呼叫會透過 HTTPS 的 [Azure Resource Manager](../azure-resource-manager/index.yml) 呼叫進行。 |

## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 注意 | 文件
|---|---|--|
| 設定管理支援 (設定版本等 ) | 是 | 針對管理作業，App Service 設定的狀態可以匯出為 Azure Resource Manager 範本，並在一段時間內進行版本設定。 針對執行時間作業，客戶可以使用 App Service 部署位置功能來維護應用程式的多個不同的即時版本。 | 

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Azure 服務內建的安全性控制項](../security/fundamentals/security-controls.md)。