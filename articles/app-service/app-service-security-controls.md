---
title: 安全性控制
description: 查找用於為組織評估 Azure 應用服務的安全控制措施清單。
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 2586821c4c48f809efb5408c3cdae5e42e3b3fcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671441"
---
# <a name="security-controls-for-azure-app-service"></a>Azure 應用服務的安全控制

本文將記錄 Azure 應用服務中內置的安全控制項。

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>網路

| 安全控制 | 是/否 | 注意 | 文件
|---|---|--|
| 服務終結點支援| 是 | 可用於應用服務。| [Azure 應用服務訪問限制](app-service-ip-restrictions.md)
| VNet 注入支援| 是 | 應用服務環境是應用服務的專用實現，專用於注入客戶虛擬網路的單個客戶。 | [App Service 環境簡介](environment/intro.md)
| 網路隔離和防火牆支援| 是 | 對於應用服務的公共多租戶變體，客戶可以配置網路 ACL（IP 限制）以鎖定允許的入站流量。  應用服務環境直接部署到虛擬網路中，因此可以使用 NSG 進行保護。 | [Azure 應用服務訪問限制](app-service-ip-restrictions.md)
| 強制隧道支援| 是 | 應用服務環境可以部署到配置強制隧道的客戶虛擬網路中。 | [設定 App Service 環境搭配強制通道](environment/forced-tunnel-support.md)

## <a name="monitoring--logging"></a>監視&日誌記錄

| 安全控制 | 是/否 | 注意 | 文件
|---|---|--|
| Azure 監視支援（日誌分析、應用見解等）| 是 | 應用服務與應用程式見解集成，適用于支援應用程式見解的語言（完整 .NET 框架、.NET 核心、JAVA 和 Node.JS）。  請參閱[監視 Azure 應用服務性能](../azure-monitor/app/azure-web-apps.md)。 應用服務還會將應用程式指標發送到 Azure 監視器。 | [監視 Azure 應用服務中的應用](web-sites-monitor.md)
| 控制和管理平面日誌記錄和審核| 是 | 對應用服務物件執行的所有管理操作都通過[Azure 資源管理器](../azure-resource-manager/index.yml)進行。 這些操作的歷史日誌在門戶中或通過 CLI 都可用。 | [Azure 資源管理器資源管理器提供程式操作](../role-based-access-control/resource-provider-operations.md#microsoftweb) [，az 監視器活動日誌](/cli/azure/monitor/activity-log)
| 資料平面日誌記錄和審核 | 否 | 應用服務的資料平面是包含客戶部署的網站內容的遠端檔共用。  沒有審核遠端檔共用。 |

## <a name="identity"></a>身分識別

| 安全控制 | 是/否 | 注意 |  文件
|---|---|--|
| 驗證| 是 | 客戶可以在應用服務上構建自動與 Azure[活動目錄 （Azure AD）](../active-directory/index.yml)以及其他 OAuth 相容標識提供程式集成的應用程式 為了管理訪問應用服務資產，所有訪問都由 Azure AD 身份驗證的主體和 Azure 資源管理器 RBAC 角色的組合控制。 | [Azure App Service 中的驗證與授權](overview-authentication-authorization.md)
| 授權| 是 | 對於對應用服務資產的管理訪問，所有訪問都由 Azure AD 身份驗證的主體和 Azure 資源管理器 RBAC 角色的組合控制。  | [Azure App Service 中的驗證與授權](overview-authentication-authorization.md)

## <a name="data-protection"></a>資料保護

| 安全控制 | 是/否 | 注意 | 文件
|---|---|--|
| 伺服器端靜態加密：微軟管理的金鑰 | 是 | 網站檔內容存儲在 Azure 存儲中，它會自動加密靜態內容。 <br><br>客戶提供的秘密在靜態時進行加密。 機密在靜態時加密，同時存儲在應用服務設定資料庫中。<br><br>本地連接的磁片可以選擇用作網站的臨時存儲（D：\本地和 %TMP%）。 本地連接的磁片在靜態時未加密。 | [靜態資料的 Azure 存儲加密](../storage/common/storage-service-encryption.md)
| 伺服器端靜態加密：客戶管理的金鑰 （BYOK） | 是 | 客戶可以選擇在金鑰保存庫中存儲應用程式機密，並在運行時檢索它們。 | [使用 App Service 和 Azure Functions 的 Key Vault 參考 (預覽)](app-service-key-vault-references.md)
| 列級加密（Azure 資料服務）| N/A | |
| 傳輸中的加密（如 ExpressRoute 加密、VNet 加密和 VNet-VNet 加密）| 是 | 客戶可以將網站配置為需要並使用 HTTPS 來訪問入站流量。  | [如何僅製作 Azure 應用服務 HTTPS（](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/)博客文章）
| API 呼叫加密| 是 | 通過[HTTPS](../azure-resource-manager/index.yml)通過 Azure 資源管理器調用來配置應用服務的管理調用。 |

## <a name="configuration-management"></a>設定管理

| 安全控制 | 是/否 | 注意 | 文件
|---|---|--|
| 建構管理支援（配置版本控制等）| 是 | 對於管理操作，應用服務配置的狀態可以匯出為 Azure 資源管理器範本，並隨著時間的推移進行版本控制。 對於運行時操作，客戶可以使用應用服務部署槽功能維護應用程式的多個不同即時版本。 | 

## <a name="next-steps"></a>後續步驟

- 詳細瞭解 Azure[服務中的內置安全控制項](../security/fundamentals/security-controls.md)。
