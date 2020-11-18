---
title: Azure 內建角色 - Azure RBAC
description: 本文說明 Azure 角色型存取控制 (Azure RBAC) 的 Azure 內建角色。 其中列出 Actions、NotActions、DataActions 和 NotDataActions。
services: active-directory
ms.service: role-based-access-control
ms.topic: reference
ms.workload: identity
author: rolyon
ms.author: rolyon
ms.date: 11/03/2020
ms.custom: generated
ms.openlocfilehash: a48a13fa18025254ee31344868d10181ae87c65e
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685472"
---
# <a name="azure-built-in-roles"></a>Azure 內建角色

[Azure 角色型存取控制 (RBAC)](overview.md) 有數個 Azure 內建角色，可供您指派給使用者、群組、服務主體和受控身分識別。 角色指派是您控制 Azure 資源存取權的方式。 如果內建的角色無法滿足您組織的特定需求，您可以建立自己的 [Azure 自訂角色](custom-roles.md)。

本文列出的 Azure 內建角色不斷演變。 若要取得最新角色，請使用 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) 或 [az role definition list](/cli/azure/role/definition#az-role-definition-list)。 如果要尋找 Azure Active Directory (Azure AD) 的管理員角色，請參閱 [Azure Active Directory 中的管理員角色權限](../active-directory/roles/permissions-reference.md)。

下表提供每個內建角色的簡短說明和唯一識別碼。 按一下角色名稱，即可查看每個角色的 `Actions`、`NotActions`、`DataActions` 及 `NotDataActions` 清單。 如需這些動作的意義及其如何套用至管理和資料平面的相關資訊，請參閱[了解 Azure 角色定義](role-definitions.md)。

## <a name="all"></a>全部

> [!div class="mx-tableFixed"]
> | 內建角色 | 描述 | ID |
> | --- | --- | --- |
> | **一般** |  |  |
> | [參與者](#contributor) | 授與管理所有資源的完整存取權，但不允許您在 Azure RBAC 中指派角色。 | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [擁有者](#owner) | 授與管理所有資源的完整存取權，包括在 Azure RBAC 中指派角色的能力。 | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [讀取者](#reader) | 查看所有資源，但不允許您進行任何變更。 | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [使用者存取系統管理員](#user-access-administrator) | 可讓您管理 Azure 資源的使用者存取。 | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **計算** |  |  |
> | [傳統虛擬機器參與者](#classic-virtual-machine-contributor) | 可讓您管理傳統虛擬機器 (不含虛擬機器所連接的虛擬網路或儲存體帳戶)，但無法存取它們。 | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [虛擬機器系統管理員登入](#virtual-machine-administrator-login) | 在入口網站中檢視虛擬機器並以系統管理員身分登入 | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [虛擬機器參與者](#virtual-machine-contributor) | 可讓您管理虛擬機器 (不含虛擬機器所連接的虛擬網路或儲存體帳戶)，但無法存取它們。 | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [虛擬機器使用者登入](#virtual-machine-user-login) | 在入口網站中檢視虛擬機器並以一般使用者身分登入。 | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **網路功能** |  |  |
> | [CDN 端點參與者](#cdn-endpoint-contributor) | 可管理 CDN 端點，但無法將存取權授與其他使用者。 | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [CDN 端點讀者](#cdn-endpoint-reader) | 可檢視 CDN 端點，但無法變更。 | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [CDN 設定檔參與者](#cdn-profile-contributor) | 可管理 CDN 設定檔及其端點，但無法將存取權授與其他使用者。 | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [CDN 設定檔讀者](#cdn-profile-reader) | 可檢視 CDN 設定檔及其端點，但無法變更。 | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [傳統網路參與者](#classic-network-contributor) | 可讓您管理傳統網路，但無法存取它們。 | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [DNS 區域參與者](#dns-zone-contributor) | 可讓您管理 Azure DNS 中的 DNS 區域與記錄集，但無法讓您控制誰可存取它們。 | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [網路參與者](#network-contributor) | 可讓您管理網路，但無法存取它們。 | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [私人 DNS 區域參與者](#private-dns-zone-contributor) | 可讓您管理私人 DNS 區域資源，但不能管理它們所連結的虛擬網路。 | b12aa53e-6015-4669-85d0-8515ebb3ae7f |
> | [流量管理員參與者](#traffic-manager-contributor) | 可讓您管理「流量管理員」設定檔，但無法控制誰可以存取它們。 | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Storage** |  |  |
> | [Avere 參與者](#avere-contributor) | 可以建立和管理 Avere vFXT 叢集。 | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Avere 操作員](#avere-operator) | 供 Avere vFXT 叢集用來管理叢集 | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [備份參與者](#backup-contributor) | 可讓您管理備份服務，但無法建立保存庫及授與存取權給其他人 | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [備份操作員](#backup-operator) | 可讓您管理備份服務，但無法移除備份、建立保存庫及為其他人提供存取權 | 00c29273-979b-4161-815c-10b084fb9324 |
> | [備份讀取者](#backup-reader) | 可以檢視備份服務，但無法進行變更 | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [傳統儲存體帳戶參與者](#classic-storage-account-contributor) | 可讓您管理傳統儲存體帳戶，但無法存取它們。 | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [傳統儲存體帳戶金鑰操作員服務角色](#classic-storage-account-key-operator-service-role) | 「傳統儲存體帳戶金鑰操作員」可以列出及重新產生「傳統儲存體帳戶」的金鑰 | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [資料箱參與者](#data-box-contributor) | 可讓您管理資料箱服務下的所有項目，為他人賦予存取權除外。 | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [資料箱讀者](#data-box-reader) | 可讓您管理資料箱服務，建立訂單或編輯訂單詳細資料和為他人賦予存取權除外。 | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Data Lake Analytics 開發人員](#data-lake-analytics-developer) | 可讓您提交、監視及管理您自己的作業，但無法建立或刪除 Data Lake Analytics 帳戶。 | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | [讀取者及資料存取](#reader-and-data-access) | 可讓您檢視所有內容，但無法讓您刪除或建立儲存體帳戶或內含的資源。 也可透過存取儲存體帳戶金鑰，對儲存體帳戶中內含的所有資料進行讀取/寫入存取。 | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [儲存體帳戶參與者](#storage-account-contributor) | 允許管理儲存體帳戶。 支援存取帳戶金鑰，以透過共用金鑰授權來存取資料。 | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [儲存體帳戶金鑰操作員服務角色](#storage-account-key-operator-service-role) | 允許列出及重新產生儲存體帳戶存取金鑰。 | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [儲存體 Blob 資料參與者](#storage-blob-data-contributor) | 讀取、寫入和刪除 Azure 儲存體的容器和 blob。 若要了解特定資料作業所需的動作，請參閱[呼叫 blob 和佇列資料作業的權限](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [儲存體 Blob 資料擁有者](#storage-blob-data-owner) | 支援完整存取 Azure 儲存體 blob 容器和資料，包括指派 POSIX 存取控制。 若要了解特定資料作業所需的動作，請參閱[呼叫 blob 和佇列資料作業的權限](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [儲存體 Blob 資料讀者](#storage-blob-data-reader) | 讀取和列出 Azure 儲存體的容器和 blob。 若要了解特定資料作業所需的動作，請參閱[呼叫 blob 和佇列資料作業的權限](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [儲存體 Blob 委派者](#storage-blob-delegator) | 取得使用者委派金鑰，以針對使用 Azure AD 認證所簽署的容器或 blob，建立共用存取簽章。 如需詳細資訊，請參閱[建立使用者委派 SAS](/rest/api/storageservices/create-user-delegation-sas)。 | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [儲存體檔案資料 SMB 共用參與者](#storage-file-data-smb-share-contributor) | 允許讀取、寫入及刪除 Azure 檔案共用上的檔案/目錄。 此角色在 Windows 檔案伺服器上沒有內建的對等項。 | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [儲存體檔案資料 SMB 共用提升權限的參與者](#storage-file-data-smb-share-elevated-contributor) | 允許對 Azure 檔案共用上的檔案/目錄，讀取、寫入、刪除和修改 ACL。 此角色相當於 Windows 檔案伺服器上的「變更」檔案共用 ACL。 | a7264617-510b-434b-a828-9731dc254ea7 |
> | [儲存體檔案資料 SMB 共用讀者](#storage-file-data-smb-share-reader) | 允許讀取 Azure 檔案共用上的檔案/目錄。 此角色相當於 Windows 檔案伺服器上的「讀取」檔案共用 ACL。 | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [儲存體佇列資料參與者](#storage-queue-data-contributor) | 讀取、寫入及刪除 Azure 儲存體的佇列和佇列訊息。 若要了解特定資料作業所需的動作，請參閱[呼叫 blob 和佇列資料作業的權限](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [儲存體佇列資料訊息處理者](#storage-queue-data-message-processor) | 從 Azure 儲存體佇列中瞄核、擷取和刪除訊息。 若要了解特定資料作業所需的動作，請參閱[呼叫 blob 和佇列資料作業的權限](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [儲存體佇列資料訊息傳送者](#storage-queue-data-message-sender) | 將訊息新增至 Azure 儲存體佇列。 若要了解特定資料作業所需的動作，請參閱[呼叫 blob 和佇列資料作業的權限](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [儲存體佇列資料讀者](#storage-queue-data-reader) | 讀取和列出 Azure 儲存體的佇列和佇列訊息。 若要了解特定資料作業所需的動作，請參閱[呼叫 blob 和佇列資料作業的權限](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Web** |  |  |
> | [Azure 地圖服務資料讀者](#azure-maps-data-reader) | 授權從 Azure 地圖服務帳戶讀取地圖相關資料。 | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [搜尋服務參與者](#search-service-contributor) | 可讓您管理「搜尋」服務，但無法存取它們。 | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [SignalR AccessKey 讀者](#signalr-accesskey-reader) | 讀取 SignalR Service 存取金鑰 | 04165923-9d83-45d5-8227-78b77b0a687e |
> | [SignalR 應用程式伺服器 (預覽) ](#signalr-app-server-preview) | 使用 AAD 驗證選項，讓您的應用程式伺服器存取 SignalR Service。 | 420fcaa2-552c-430f-98ca-3264be4806c7 |
> | [SignalR 參與者](#signalr-contributor) | 建立、讀取、更新和刪除 SignalR 服務資源 | 8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761 |
> | [SignalR 無伺服器參與者 (預覽) ](#signalr-serverless-contributor-preview) | 使用 AAD 驗證選項，讓您的應用程式在無伺服器模式下存取服務。 | fd53cd77-2268-407a-8f46-7e7863d0f521 |
> | [SignalR Service 擁有者 (預覽) ](#signalr-service-owner-preview) | Azure SignalR Service REST Api 的完整存取權 | 7e4f1700-ea5a-4f59-8f37-079cfe29dce3 |
> | [SignalR Service 讀者 (預覽) ](#signalr-service-reader-preview) | Azure SignalR Service REST Api 的唯讀存取 | ddde6b66-c0df-4114-a159-3618637b3035 |
> | [Web 方案參與者](#web-plan-contributor) | 可讓您管理網站的 Web 方案，但無法存取它們。 | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [網站參與者](#website-contributor) | 可讓您管理網站 (非 Web 方案)，但無法存取它們。 | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **容器** |  |  |
> | [AcrDelete](#acrdelete) | acr 刪除 | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | ACR 影像簽署者 | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | acr 提取 | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | acr 推送 | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | ACR 隔離資料讀取者 | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | ACR 隔離資料寫入者 | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Azure Kubernetes Service 叢集管理員角色](#azure-kubernetes-service-cluster-admin-role) | 列出叢集管理員認證動作。 | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Azure Kubernetes Service 叢集使用者角色](#azure-kubernetes-service-cluster-user-role) | 列出叢集使用者認證動作。 | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | [Azure Kubernetes Service 參與者角色](#azure-kubernetes-service-contributor-role) | 授與讀取和寫入 Azure Kubernetes Service 叢集的存取權 | ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8 |
> | [Azure Kubernetes Service RBAC 管理員](#azure-kubernetes-service-rbac-admin) | 可讓您管理叢集/命名空間下的所有資源，但更新或刪除資源配額和命名空間除外。 | 3498e952-d568-435e-9b2c-8d77e338d7f7 |
> | [Azure Kubernetes Service RBAC 叢集管理員](#azure-kubernetes-service-rbac-cluster-admin) | 可讓您管理叢集中的所有資源。 | b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b |
> | [Azure Kubernetes Service RBAC 讀者](#azure-kubernetes-service-rbac-reader) | 允許唯讀存取，以查看命名空間中的大部分物件。 它不允許查看角色或角色系結。 此角色不允許您查看秘密，因為讀取秘密的內容可讓您存取命名空間中的 ServiceAccount 認證，這會允許 API 存取做為命名空間中的任何 ServiceAccount () 的許可權擴大形式。 在叢集範圍套用此角色可讓您存取所有命名空間。 | 7f6c6a51-bcf8-42ba-9220-52d62157d7db |
> | [Azure Kubernetes Service RBAC 寫入器](#azure-kubernetes-service-rbac-writer) | 允許對命名空間中大部分物件的讀取/寫入存取。此角色不允許查看或修改角色或角色系結。 不過，此角色可讓您存取秘密，並以命名空間中的任何 ServiceAccount 來執行 pod，讓它可以用來取得命名空間中任何 ServiceAccount 的 API 存取層級。 在叢集範圍套用此角色可讓您存取所有命名空間。 | a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb |
> | **資料庫** |  |  |
> | [Cosmos DB 帳戶讀者角色](#cosmos-db-account-reader-role) | 可以讀取 Azure Cosmos DB 帳戶資料。 請參閱 [DocumentDB 帳戶參與者](#documentdb-account-contributor)以管理 Azure Cosmos DB 帳戶。 | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Cosmos DB 操作員](#cosmos-db-operator) | 可讓您管理 Azure Cosmos DB 帳戶，但無法存取其中的資料。 防止存取帳戶金鑰和連接字串。 | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | 可為帳戶的 Cosmos DB 資料庫或容器提交還原要求 | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [DocumentDB 帳戶參與者](#documentdb-account-contributor) | 可以管理 Azure Cosmos DB 帳戶。 Azure Cosmos DB 先前稱為 DocumentDB。 | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Redis 快取參與者](#redis-cache-contributor) | 可讓您管理 Redis 快取，但無法存取它們。 | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [SQL DB 參與者](#sql-db-contributor) | 可讓您管理 SQL 資料庫，但無法存取它們。 此外，您也無法管理其安全性相關原則或其父 SQL 伺服器。 | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [SQL 受控執行個體參與者](#sql-managed-instance-contributor) | 可讓您管理 SQL 受控執行個體和必要的網路設定，但無法將存取權授與其他人。 | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [SQL 安全性管理員](#sql-security-manager) | 可讓您管理 SQL 伺服器及資料庫的安全性相關原則，但無法存取它們。 | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [SQL Server 參與者](#sql-server-contributor) | 可讓您管理 SQL 伺服器及資料庫，但無法存取這些伺服器及資料庫，也無法存取其安全性相關原則。 | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **分析** |  |  |
> | [Azure 事件中樞資料擁有者](#azure-event-hubs-data-owner) | 允許完整存取 Azure 事件中樞資源。 | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Azure 事件中樞資料接收者](#azure-event-hubs-data-receiver) | 允許接收 Azure 事件中樞資源。 | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Azure 事件中樞資料傳送者](#azure-event-hubs-data-sender) | 允許傳送 Azure 事件中樞資源。 | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Data Factory 參與者](#data-factory-contributor) | 建立和管理 Data Factory，以及其中的子資源。 | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [資料清除者](#data-purger) | 可清除分析資料 | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [HDInsight 叢集操作員](#hdinsight-cluster-operator) | 可讓您讀取和修改 HDInsight 叢集設定。 | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [HDInsight 網域服務參與者](#hdinsight-domain-services-contributor) | 可讀取、建立、修改和刪除 HDInsight 企業安全性套件所需的網域服務相關作業 | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Log Analytics 參與者](#log-analytics-contributor) | 「Log Analytics 參與者」角色可以讀取所有監視資料和編輯監視設定。 編輯監視設定包括將 VM 延伸模組新增至 VM、讀取儲存體帳戶金鑰以便能夠設定從「Azure 儲存體」收集記錄、建立及設定「自動化」帳戶、新增解決方案，以及設定所有 Azure 資源上的 Azure 診斷。 | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Log Analytics 讀者](#log-analytics-reader) | 「Log Analytics 讀者」可以檢視和搜尋所有監視資料，以及檢視監視設定，包括檢視所有 Azure 資源上的 Azure 診斷設定。 | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | [結構描述登錄參與者 (預覽)](#schema-registry-contributor-preview) | 讀取、寫入及刪除結構描述登錄群組和結構描述。 | 5dffeca3-4936-4216-b2bc-10343a5abb25 |
> | [結構描述登錄讀取器 (預覽)](#schema-registry-reader-preview) | 讀取並列出結構描述登錄群組和結構描述。 | 2c56ea50-c6b3-40a6-83c0-9d98858bc7d2 |
> | **區塊鏈** |  |  |
> | [區塊鏈成員節點存取 (預覽)](#blockchain-member-node-access-preview) | 允許存取區塊鏈成員節點 | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **AI + 機器學習** |  |  |
> | [認知服務參與者](#cognitive-services-contributor) | 可讓您建立、讀取、更新、刪除及管理認知服務的金鑰。 | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [認知服務資料讀者 (預覽)](#cognitive-services-data-reader-preview) | 可讓您讀取認知服務資料。 | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [認知服務使用者](#cognitive-services-user) | 可讓您讀取和列出認知服務的金鑰。 | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **混合實境** |  |  |
> | [遠端呈現系統管理員](#remote-rendering-administrator) | 為使用者提供轉換、管理會話、轉譯和診斷功能，以供 Azure 遠端轉譯 | 3df8b902-2a6f-47c7-8cc5-360e9b272a7e |
> | [遠端呈現用戶端](#remote-rendering-client) | 為使用者提供 Azure 遠端轉譯管理會話、轉譯及診斷功能。 | d39065c4-c120-43c9-ab0a-63eed9795f0a |
> | [空間錨點帳戶參與者](#spatial-anchors-account-contributor) | 可讓您管理帳戶中的空間錨點，但無法刪除 | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [空間錨點帳戶擁有者](#spatial-anchors-account-owner) | 可讓您管理帳戶中的空間錨點，包含刪除 | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [空間錨點帳戶讀者](#spatial-anchors-account-reader) | 可讓您尋找和讀取帳戶中空間錨點的屬性 | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **整合** |  |  |
> | [API 管理服務參與者](#api-management-service-contributor) | 可管理服務與 API | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [API 管理服務操作員角色](#api-management-service-operator-role) | 可管理服務，但無法管理 API | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [API 管理服務讀取者角色](#api-management-service-reader-role) | 具有服務與 API 的唯讀存取權 | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [應用程式組態資料擁有者](#app-configuration-data-owner) | 允許完整存取應用程式組態資料。 | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [應用程式組態資料讀者](#app-configuration-data-reader) | 允許讀取應用程式組態資料。 | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Azure 服務匯流排資料擁有者](#azure-service-bus-data-owner) | 允許完整存取 Azure 服務匯流排資源。 | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Azure 服務匯流排資料接收者](#azure-service-bus-data-receiver) | 允許接收 Azure 服務匯流排資源。 | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Azure 服務匯流排資料傳送者](#azure-service-bus-data-sender) | 允許傳送 Azure 服務匯流排資源。 | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Azure Stack 註冊擁有者](#azure-stack-registration-owner) | 可讓您管理 Azure Stack 註冊。 | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [EventGrid EventSubscription 參與者](#eventgrid-eventsubscription-contributor) | 可讓您管理 EventGrid 事件訂用帳戶作業。 | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [EventGrid EventSubscription 讀者](#eventgrid-eventsubscription-reader) | 可讓您讀取 EventGrid 事件訂用帳戶。 | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [FHIR 資料參與者](#fhir-data-contributor) | 角色可讓使用者或主體完整存取 FHIR 資料 | 5a1fc7df-4bf1-4951-a576-89034ee01acd |
> | [FHIR 資料匯出工具](#fhir-data-exporter) | 角色可讓使用者或主體讀取和匯出 FHIR 資料 | 3db33094-8700-4567-8da5-1501d4e7e843 |
> | [FHIR 資料讀取器](#fhir-data-reader) | 角色可讓使用者或主體讀取 FHIR 資料 | 4c8d0bbc-75d3-4935-991f-5f3c56d81508 |
> | [FHIR 資料寫入器外掛程式](#fhir-data-writer) | 角色可讓使用者或主體讀取和寫入 FHIR 資料 | 3f88fce4-5892-4214-ae73-ba5294559913 |
> | [整合服務環境參與者](#integration-service-environment-contributor) | 可讓您管理整合服務環境，但無法存取它們。 | a41e2c5b-bd99-4a07-88f4-9bf657a760b8 |
> | [整合服務環境開發人員](#integration-service-environment-developer) | 可讓開發人員在整合服務環境中建立和更新工作流程、整合帳戶和 API 連接。 | c7aa55d3-1abb-444a-a5ca-5e51e485d6ec |
> | [Intelligent Systems 帳戶參與者](#intelligent-systems-account-contributor) | 可讓您管理「智慧型系統」帳戶，但無法存取它們。 | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [邏輯應用程式參與者](#logic-app-contributor) | 可讓您管理邏輯應用程式，但無法變更對邏輯應用程式的存取。 | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [邏輯應用程式操作員](#logic-app-operator) | 可讓您讀取、啟用及停用邏輯應用程式，但無法編輯或更新邏輯應用程式。 | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **身分識別** |  |  |
> | [受控身分識別參與者](#managed-identity-contributor) | 建立、讀取、更新及刪除使用者指派的身分識別 | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [受控身分識別操作員](#managed-identity-operator) | 讀取及指派使用者指派的身分識別 | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **安全性** |  |  |
> | [Azure Sentinel 參與者](#azure-sentinel-contributor) | Azure Sentinel 參與者 | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Azure Sentinel 讀者](#azure-sentinel-reader) | Azure Sentinel 讀者 | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Azure Sentinel 回應者](#azure-sentinel-responder) | Azure Sentinel 回應者 | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Key Vault 系統管理員 (預覽) ](#key-vault-administrator-preview) | 在金鑰保存庫和其中的所有物件上執行所有資料平面作業，包括憑證、金鑰和秘密。 無法管理金鑰保存庫資源或管理角色指派。 僅適用于使用「Azure 角色型存取控制」許可權模型的金鑰保存庫。 | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
> | [Key Vault 憑證官員 (preview) ](#key-vault-certificates-officer-preview) | 對金鑰保存庫的憑證執行任何動作，但管理許可權除外。 僅適用于使用「Azure 角色型存取控制」許可權模型的金鑰保存庫。 | a4417e6f-fecd-4de8-b567-7b0420556985 |
> | [Key Vault 參與者](#key-vault-contributor) | 管理金鑰保存庫，但不允許您在 Azure RBAC 中指派角色，也不允許您存取秘密、金鑰或憑證。 | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Key Vault 加密長 (預覽) ](#key-vault-crypto-officer-preview) | 在金鑰保存庫的金鑰上執行任何動作，但管理許可權除外。 僅適用于使用「Azure 角色型存取控制」許可權模型的金鑰保存庫。 | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
> | [Key Vault 加密服務加密 (預覽) ](#key-vault-crypto-service-encryption-preview) | 讀取金鑰的中繼資料，並執行包裝/解除包裝作業。 僅適用于使用「Azure 角色型存取控制」許可權模型的金鑰保存庫。 | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
> | [Key Vault 加密使用者 (預覽) ](#key-vault-crypto-user-preview) | 使用金鑰執行密碼編譯作業。 僅適用于使用「Azure 角色型存取控制」許可權模型的金鑰保存庫。 | 12338af0-0e69-4776-bea7-57ae8d297424 |
> | [Key Vault 讀者 (預覽) ](#key-vault-reader-preview) | 讀取金鑰保存庫的中繼資料及其憑證、金鑰和秘密。 無法讀取敏感性值，例如秘密內容或金鑰內容。 僅適用于使用「Azure 角色型存取控制」許可權模型的金鑰保存庫。 | 21090545-7ca7-4776-b22c-e363652d74d2 |
> | [Key Vault 秘密長 (預覽) ](#key-vault-secrets-officer-preview) | 對金鑰保存庫的秘密執行任何動作，但管理許可權除外。 僅適用于使用「Azure 角色型存取控制」許可權模型的金鑰保存庫。 | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
> | [Key Vault 秘密使用者 (預覽) ](#key-vault-secrets-user-preview) | 讀取秘密內容。 僅適用于使用「Azure 角色型存取控制」許可權模型的金鑰保存庫。 | 4633458b-17de-408a-b874-0445c86b69e6 |
> | [安全性系統管理員](#security-admin) | 資訊安全中心的檢視和更新權限。 與「安全性讀者」角色的權限相同，還可以更新安全性原則及關閉警示和建議。 | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [安全性評量參與者](#security-assessment-contributor) | 可讓您將評量推送至資訊安全中心 | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [安全性管理員 (舊版)](#security-manager-legacy) | 此為舊版角色。 請改用「安全性系統管理員」。 | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [安全性讀取者](#security-reader) | 資訊安全中心的檢視權限。 可以檢視建議、警示、安全性原則和安全性狀態，但無法變更。 | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [DevTest Labs 使用者](#devtest-labs-user) | 可讓您連線、啟動、重新啟及關閉您 Azure DevTest Labs 中的虛擬機器。 | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [實驗室建立者](#lab-creator) | 可讓您在 Azure 實驗室帳戶下建立新的實驗室。 | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **監視** |  |  |
> | [Application Insights 元件參與者](#application-insights-component-contributor) | 可以管理 Application Insights 元件 | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Application Insights 快照集偵錯工具](#application-insights-snapshot-debugger) | 給予使用者權限，以便檢視及下載使用 Application Insights 快照偵錯工具所收集的偵錯快照。 請注意，[擁有者](#owner)或[參與者](#contributor)角色未包含這些權限。 將 Application Insights 快照偵錯者角色指派給使用者時，您必須直接將此角色授與使用者。 此角色若新增至自訂角色，則無法辨識。 | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [監視參與者](#monitoring-contributor) | 可以讀取所有監視資料並編輯監視設定。 請參閱[開始使用 Azure 監視器的角色、權限和安全性](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles)。 | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [監視計量發行者](#monitoring-metrics-publisher) | 針對 Azure 資源啟用發佈計量 | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [監視讀取器](#monitoring-reader) | 可以讀取所有監視資料 (計量、記錄等等)。 請參閱[開始使用 Azure 監視器的角色、權限和安全性](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles)。 | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [活頁簿參與者](#workbook-contributor) | 可以儲存共用活頁簿。 | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [活頁簿讀者](#workbook-reader) | 可以讀取活頁簿。 | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **管理和治理** |  |  |
> | [自動化作業運算子](#automation-job-operator) | 使用「自動化 Runbook」來建立及管理作業。 | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [自動化運算子](#automation-operator) | 「自動化運算子」能夠啟動、停止、暫止及繼續作業 | d3881f73-407a-4167-8283-e981cbba0404 |
> | [自動化 Runbook 運算子](#automation-runbook-operator) | 讀取 Runbook 屬性 - 以便能夠建立 Runbook 的作業。 | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Azure Connected Machine 上線](#azure-connected-machine-onboarding) | 可以讓 Azure Connected Machine 上線。 | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Azure Connected Machine 資源管理員](#azure-connected-machine-resource-administrator) | 可以讀取、寫入、刪除 Azure Connected Machine 及使之重新上線。 | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [帳單讀取器](#billing-reader) | 允許對計費資料進行讀取存取 | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [藍圖參與者](#blueprint-contributor) | 可以管理藍圖定義，但不能加以指派。 | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [藍圖操作員](#blueprint-operator) | 可以指派現有已發佈的藍圖，但無法建立新的藍圖。 請注意，只有在以使用者指派的受控識別來指派時才有效。 | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [成本管理參與者](#cost-management-contributor) | 可檢視成本和管理成本組態 (例如預算、匯出) | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [成本管理讀者](#cost-management-reader) | 可檢視成本資料和組態 (例如預算、匯出) | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [階層設定管理員](#hierarchy-settings-administrator) | 允許使用者編輯和刪除階層設定 | 350f8d15-c687-4448-8ae1-157740a3936d |
> | [Kubernetes Cluster-Azure Arc 上架](#kubernetes-cluster---azure-arc-onboarding) | 用以授權任何使用者/服務建立 connectedClusters 資源的角色定義 | 34e09817-6cbe-4d01-b1a2-e0eac5743d41 |
> | [受控應用程式參與者角色](#managed-application-contributor-role) | 允許建立受控應用程式資源。 | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [受控應用程式操作員角色](#managed-application-operator-role) | 可讓您讀取受控應用程式資源及對其執行動作 | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [受控應用程式讀者](#managed-applications-reader) | 可讓您讀取受控應用程式中的資源及要求 JIT 存取權。 | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [受控服務註冊指派刪除角色](#managed-services-registration-assignment-delete-role) | 「受控服務註冊指派刪除角色」可讓管理租用戶使用者刪除指派給其租用戶的註冊指派。 | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [管理群組參與者](#management-group-contributor) | 管理群組參與者角色 | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [管理群組讀者](#management-group-reader) | 管理群組讀者角色 | ac63b705-f282-497d-ac71-919bf39d939d |
> | [New Relic APM 帳戶參與者](#new-relic-apm-account-contributor) | 可讓您管理 New Relic Application Performance Management 帳戶及應用程式，但無法存取它們。 | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [原則深入解析資料寫入者 (預覽)](#policy-insights-data-writer-preview) | 允許讀取資源原則及寫入資源元件原則事件。 | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [資源原則參與者](#resource-policy-contributor) | 有權建立/修改資源原則、建立支援票證及讀取資源/階層的使用者。 | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Site Recovery 參與者](#site-recovery-contributor) | 可讓您管理 Site Recovery 服務，但無法建立保存庫和指派角色 | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Site Recovery 操作員](#site-recovery-operator) | 可讓您容錯移轉及容錯回復，但無法執行其他 Site Recovery 管理作業 | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Site Recovery 讀取者](#site-recovery-reader) | 可讓您檢視 Site Recovery 狀態，但無法執行其他管理作業 | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [支援要求參與者](#support-request-contributor) | 可讓您建立及管理支援要求 | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [標記參與者](#tag-contributor) | 可讓您管理實體上的標記，無需提供對實體本身的存取。 | 4a9ae827-6dc8-4573-8ac7-8239d42aa03f |
> | **其他** |  |  |
> | [Azure 數位 Twins 資料擁有者](#azure-digital-twins-data-owner) | 數位 Twins 資料平面的完整存取角色 | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
> | [Azure 數位 Twins 資料讀者](#azure-digital-twins-data-reader) | 數位 Twins 資料平面屬性的唯讀角色 | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |
> | [BizTalk 參與者](#biztalk-contributor) | 可讓您管理 BizTalk 服務，但無法存取它們。 | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [桌面虛擬化使用者](#desktop-virtualization-user) | 允許使用者使用應用程式群組中的應用程式。 | 1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63 |
> | [排程器工作集合參與者](#scheduler-job-collections-contributor) | 可讓您管理「排程器」工作集合，但無法存取它們。 | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |


## <a name="general"></a>一般


### <a name="contributor"></a>參與者

授與管理所有資源的完整存取權，但不允許您在 Azure RBAC 中指派角色。 [深入了解](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | * | 建立和管理所有類型的資源 |
> | **NotActions** |  |
> | [Microsoft. 授權](resource-provider-operations.md#microsoftauthorization)/*/delete | 刪除角色、原則指派、原則定義和原則集定義 |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/Write | 建立角色、角色指派、原則指派、原則定義和原則集定義 |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/elevateAccess/Action | 授與呼叫者租用戶範圍的使用者存取管理員存取 |
> | [Microsoft 藍圖](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/write | 建立或更新任何藍圖指派 |
> | [Microsoft 藍圖](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/delete | 刪除任何藍圖指派 |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, but does not allow you to assign roles in Azure RBAC.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="owner"></a>擁有者

授與管理所有資源的完整存取權，包括在 Azure RBAC 中指派角色的能力。 [深入了解](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | * | 建立和管理所有類型的資源 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, including the ability to assign roles in Azure RBAC.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader"></a>讀取者

查看所有資源，但不允許您進行任何變更。 [深入了解](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | */read | 讀取密碼以外的所有類型的資源。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View all resources, but does not allow you to make any changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "permissions": [
    {
      "actions": [
        "*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="user-access-administrator"></a>使用者存取系統管理員

可讓您管理 Azure 資源的使用者存取。 [深入了解](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | */read | 讀取密碼以外的所有類型的資源。 |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/* | 管理授權 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage user access to Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "User Access Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="compute"></a>計算


### <a name="classic-virtual-machine-contributor"></a>傳統虛擬機器參與者

可讓您管理傳統虛擬機器 (不含虛擬機器所連接的虛擬網路或儲存體帳戶)，但無法存取它們。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft. Microsoft.classiccompute](resource-provider-operations.md#microsoftclassiccompute)/domainNames/* | 建立和管理傳統運算網域名稱 |
> | [Microsoft. Microsoft.classiccompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/* | 建立和管理虛擬機器 |
> | [Microsoft. Microsoft.classicnetwork](resource-provider-operations.md#microsoftclassicnetwork)/networkSecurityGroups/join/action |  |
> | [Microsoft. Microsoft.classicnetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/link/action | 連結保留的 IP |
> | [Microsoft. Microsoft.classicnetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/read | 取得保留的 IP |
> | [Microsoft. Microsoft.classicnetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/join/action | 加入虛擬網路。 |
> | [Microsoft. Microsoft.classicnetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/read | 取得虛擬網路。 |
> | [Microsoft. Microsoft.classicstorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/disks/read | 傳回儲存體帳戶磁碟。 |
> | [Microsoft. Microsoft.classicstorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/images/read | 傳回儲存體帳戶映像。 (已淘汰。 使用 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | [Microsoft. Microsoft.classicstorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | 列出儲存體帳戶的存取金鑰。 |
> | [Microsoft. Microsoft.classicstorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/read | 傳回具有給定帳戶的儲存體帳戶。 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "name": "d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/domainNames/*",
        "Microsoft.ClassicCompute/virtualMachines/*",
        "Microsoft.ClassicNetwork/networkSecurityGroups/join/action",
        "Microsoft.ClassicNetwork/reservedIps/link/action",
        "Microsoft.ClassicNetwork/reservedIps/read",
        "Microsoft.ClassicNetwork/virtualNetworks/join/action",
        "Microsoft.ClassicNetwork/virtualNetworks/read",
        "Microsoft.ClassicStorage/storageAccounts/disks/read",
        "Microsoft.ClassicStorage/storageAccounts/images/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-administrator-login"></a>虛擬機器系統管理員登入

在入口網站中查看虛擬機器，並以系統管理員身分登入 [深入瞭解](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | 取得公用 IP 位址定義。 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 取得虛擬網路定義 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | 取得負載平衡器定義 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | 取得網路介面定義。  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. 計算](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/action | 以一般使用者身分登入虛擬機器 |
> | [Microsoft. 計算](resource-provider-operations.md#microsoftcompute)/virtualMachines/loginAsAdmin/action | 以 Windows 系統管理員或 Linux 根使用者權限登入虛擬機器 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as administrator",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "name": "1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action",
        "Microsoft.Compute/virtualMachines/loginAsAdmin/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Administrator Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-contributor"></a>虛擬機器參與者

可讓您管理虛擬機器 (不含虛擬機器所連接的虛擬網路或儲存體帳戶)，但無法存取它們。 [深入了解](../virtual-machines/linux/tutorial-govern-resources.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* | 建立和管理運算可用性集合 |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/locations/* | 建立和管理運算位置 |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* | 執行所有虛擬機器動作，包括建立、更新、刪除、啟動、重新開機和關閉虛擬機器的電源。 在虛擬機器上執行預先定義的腳本。 |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachineScaleSets/* | 建立和管理虛擬機器擴展集 |
> | [Microsoft. 計算](resource-provider-operations.md#microsoftcompute)/disks/write | 建立新的磁碟，或更新現有磁碟 |
> | [Microsoft. 計算](resource-provider-operations.md#microsoftcompute)/disks/read | 取得磁碟的屬性 |
> | [Microsoft. 計算](resource-provider-operations.md#microsoftcompute)/disks/delete | 刪除磁碟 |
> | [Microsoft. microsoft.devtestlab](resource-provider-operations.md#microsoftdevtestlab)/schedules/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/applicationGateways/backendAddressPools/join/action | 加入應用程式閘道後端位址集區。 不可警示。 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/action | 加入負載平衡器後端位址集區。 不可警示。 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatPools/join/action | 加入負載平衡器輸入 NAT 集區。 不可警示。 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/action | 加入負載平衡器輸入 nat 規則。 不可警示。 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/probes/join/action | 允許使用負載平衡器的探查。 例如，使用此權限，VM 擴展集的 healthProbe 屬性就可以參考探查。 不可警示。 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | 取得負載平衡器定義 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/locations/* | 建立和管理網路位置 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* | 建立和管理網路介面 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | 加入網路安全性群組。 不可警示。 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/read | 取得網路安全性群組定義 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/action | 加入公用 IP 位址。 不可警示。 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | 取得公用 IP 位址定義。 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 取得虛擬網路定義 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | 加入虛擬網路。 不可警示。 |
> | [Microsoft. az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/write | 建立備份保護用途 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | 傳回受保護項目的物件詳細資料 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/write | 建立備用的受保護項目 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | 傳回所有保護原則 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/write | 建立保護原則 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | 「取得保存庫」作業會取得物件，此物件代表 'vault' 類型的 Azure 資源 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | 傳回復原服務保存庫的使用量詳細資料。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/write | 「建立保存庫」作業會建立 'vault' 類型的 Azure 資源 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft.sqlvirtualmachine](resource-provider-operations.md#microsoftsqlvirtualmachine)/* |  |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | 傳回指定儲存體帳戶的存取金鑰。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | 傳回儲存體帳戶清單，或取得指定儲存體帳戶的屬性。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/locations/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/virtualMachineScaleSets/*",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/delete",
        "Microsoft.DevTestLab/schedules/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/loadBalancers/probes/join/action",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/locations/*",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Network/networkSecurityGroups/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/write",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.SqlVirtualMachine/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-user-login"></a>虛擬機器使用者登入

在入口網站中檢視虛擬機器並以一般使用者身分登入。 [深入了解](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | 取得公用 IP 位址定義。 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 取得虛擬網路定義 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | 取得負載平衡器定義 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | 取得網路介面定義。  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. 計算](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/action | 以一般使用者身分登入虛擬機器 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as a regular user.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb879df8-f326-4884-b1cf-06f3ad86be52",
  "name": "fb879df8-f326-4884-b1cf-06f3ad86be52",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine User Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="networking"></a>網路功能


### <a name="cdn-endpoint-contributor"></a>CDN 端點參與者

可管理 CDN 端點，但無法將存取權授與其他使用者。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft Cdn](resource-provider-operations.md#microsoftcdn)/profiles/endpoints/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "name": "426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-endpoint-reader"></a>CDN 端點讀者

可檢視 CDN 端點，但無法變更。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [/Profiles/endpoints/*](resource-provider-operations.md#microsoftcdn)/read |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "name": "871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-contributor"></a>CDN 設定檔參與者

可管理 CDN 設定檔及其端點，但無法將存取權授與其他使用者。 [深入了解](../cdn/cdn-app-dev-net.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft Cdn](resource-provider-operations.md#microsoftcdn)/profiles/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN profiles and their endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "name": "ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-reader"></a>CDN 設定檔讀者

可檢視 CDN 設定檔及其端點，但無法變更。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [/Profiles/*](resource-provider-operations.md#microsoftcdn)/read |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN profiles and their endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f96442b-4075-438f-813d-ad51ab4019af",
  "name": "8f96442b-4075-438f-813d-ad51ab4019af",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-network-contributor"></a>傳統網路參與者

可讓您管理傳統網路，但無法存取它們。 [深入了解](../virtual-network/virtual-network-manage-peering.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft.classicnetwork](resource-provider-operations.md#microsoftclassicnetwork)/* | 建立和管理傳統網路 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "name": "b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicNetwork/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="dns-zone-contributor"></a>DNS 區域參與者

可讓您管理 Azure DNS 中的 DNS 區域與記錄集，但無法讓您控制誰可存取它們。 [深入了解](../dns/dns-protect-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/dnsZones/* | 建立和管理 DNS 區域和記錄 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DNS zones and record sets in Azure DNS, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/befefa01-2a29-4197-83a8-272ff33ce314",
  "name": "befefa01-2a29-4197-83a8-272ff33ce314",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/dnsZones/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="network-contributor"></a>網路參與者

可讓您管理網路，但無法存取它們。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft 網路](resource-provider-operations.md#microsoftnetwork)/* | 建立和管理網路 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7",
  "name": "4d97b98b-1d4f-4787-a291-c67834d212e7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="private-dns-zone-contributor"></a>私人 DNS 區域參與者

可讓您管理私人 DNS 區域資源，但不能管理它們所連結的虛擬網路。 [深入了解](../dns/dns-protect-private-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/privateDnsZones/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationResults/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationStatuses/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 取得虛擬網路定義 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/join/action | 加入虛擬網路。 不可警示。 |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage private DNS zone resources, but not the virtual networks they are linked to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "name": "b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/privateDnsZones/*",
        "Microsoft.Network/privateDnsOperationResults/*",
        "Microsoft.Network/privateDnsOperationStatuses/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/join/action",
        "Microsoft.Authorization/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Private DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="traffic-manager-contributor"></a>流量管理員參與者

可讓您管理「流量管理員」設定檔，但無法控制誰可以存取它們。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/trafficManagerProfiles/* |  |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Traffic Manager profiles, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "name": "a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/trafficManagerProfiles/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Traffic Manager Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="storage"></a>儲存體


### <a name="avere-contributor"></a>Avere 參與者

可以建立和管理 Avere vFXT 叢集。 [深入了解](../avere-vfxt/avere-vfxt-deploy-plan.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/*/read |  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* |  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/proximityPlacementGroups/* |  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* |  |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/disks/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/*/read |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 取得虛擬網路定義 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/read | 取得虛擬網路子網路定義 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | 加入虛擬網路。 不可警示。 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | 將資源 (例如，儲存體帳戶或 SQL Database) 加入至子網路。 不可警示。 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | 加入網路安全性群組。 不可警示。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/*/read |  |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | 建立及管理儲存體帳戶 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/resources/read | 取得資源群組的資源。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | 傳回刪除 Blob 的結果 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | 傳回 Blob 或 Blob 清單 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | 傳回寫入 Blob 的結果 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can create and manage an Avere vFXT cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "name": "4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/proximityPlacementGroups/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/disks/*",
        "Microsoft.Network/*/read",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/*/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="avere-operator"></a>Avere 操作員

Avere vFXT 叢集用來管理叢集的 [深入瞭解](../avere-vfxt/avere-vfxt-manage-cluster.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. 計算](resource-provider-operations.md#microsoftcompute)/virtualMachines/read | 取得虛擬機器的屬性 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | 取得網路介面定義。  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/write | 建立網路介面，或更新現有的網路介面。  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 取得虛擬網路定義 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/read | 取得虛擬網路子網路定義 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | 加入虛擬網路。 不可警示。 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | 加入網路安全性群組。 不可警示。 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/delete | 傳回刪除容器的結果 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | 傳回容器的清單 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/write | 傳回放置 Blob 容器的結果 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | 傳回刪除 Blob 的結果 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | 傳回 Blob 或 Blob 清單 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | 傳回寫入 Blob 的結果 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Used by the Avere vFXT cluster to manage the cluster",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "name": "c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-contributor"></a>備份參與者

可讓您管理備份服務，但無法建立保存庫並將存取權授與其他人 [深入瞭解](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 取得虛擬網路定義 |
> | [Microsoft. az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/* | 管理備份管理上作業的結果 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/* | 在復原服務保存庫的備份網狀架構內建立和管理備份容器 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/action | 重新整理容器清單 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | 建立和管理備份作業 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | 匯出作業 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | 建立和管理備份管理作業的結果 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/* | 建立和管理備份原則 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | 建立和管理可以備份的項目 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/* | 建立和管理備份項目 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/* | 建立和管理保存備份項目的容器 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupSecurityPIN/* |  |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | 傳回復原服務之受保護項目和受保護伺服器的摘要。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/* | 建立和管理備份復原服務保存庫中與備份相關的憑證 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | 建立和管理與保存庫相關的擴充資訊 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | 取得復原服務保存庫的警示。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | 「取得保存庫」作業會取得物件，此物件代表 'vault' 類型的 Azure 資源 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | 建立和管理註冊的身分識別 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/* | 建立和管理復原服務保存庫的使用方式 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | 傳回儲存體帳戶清單，或取得指定儲存體帳戶的屬性。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/* |  |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/action | 驗證受保護項目上的作業 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/write | 「建立保存庫」作業會建立 'vault' 類型的 Azure 資源 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | 傳回復原服務保存庫的備份作業狀態。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | 傳回已向保存庫註冊的所有備份管理伺服器。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/read | 取得所有可保護的容器 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | 檢查復原服務保存庫的備份狀態 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/action |  |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | 驗證功能 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | 解決警示。 |
> | [Microsoft. az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | 作業會傳回資源提供者的作業清單 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | 取得給定作業的作業狀態 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | 列出所有的備份保護用途 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup service,but can't create vaults and give access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e467623-bb1f-42f4-a55d-6e525e11384b",
  "name": "5e467623-bb1f-42f4-a55d-6e525e11384b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupSecurityPIN/*",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/*",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/Vaults/usages/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-operator"></a>備份操作員

可讓您管理備份服務，但移除備份、建立保存庫以及為其他人提供存取權的 [詳細資訊](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 取得虛擬網路定義 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/read | 傳回作業的狀態 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/read | 取得對保護容器執行之作業的結果。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | 對受保護的項目執行備份。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | 取得對受保護項目執行之作業的結果。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | 傳回對受保護項目執行之作業的狀態。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | 傳回受保護項目的物件詳細資料 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | 為受保護的項目佈建即時項目復原 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | 取得受保護項目的復原點。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | 還原受保護項目的復原點。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | 為受保護的項目撤銷即時項目復原 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/write | 建立備用的受保護項目 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/read | 傳回所有已註冊的容器 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/action | 重新整理容器清單 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | 建立和管理備份作業 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | 匯出作業 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | 建立和管理備份管理作業的結果 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/read | 取得原則作業的結果。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | 傳回所有保護原則 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | 建立和管理可以備份的項目 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/read | 傳回所有受保護項目的清單。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/read | 傳回屬於訂用帳戶的所有容器 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | 傳回復原服務之受保護項目和受保護伺服器的摘要。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/write | 「更新資源憑證」作業會更新資源/保存庫的認證憑證。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | 「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/write | 「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | 取得復原服務保存庫的警示。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | 「取得保存庫」作業會取得物件，此物件代表 'vault' 類型的 Azure 資源 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | 「取得作業結果」作業可用來取得以非同步方式提交之作業的作業狀態和結果 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | 「取得容器」作業可用來取得為資源註冊的容器。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/write | 「註冊服務容器」作業可用來向復原服務註冊容器。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | 傳回復原服務保存庫的使用量詳細資料。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | 傳回儲存體帳戶清單，或取得指定儲存體帳戶的屬性。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/action | 驗證受保護項目上的作業 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | 傳回復原服務保存庫的備份作業狀態。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operations/read | 取得原則作業的狀態。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/write | 建立已註冊的容器 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/inquire/action | 執行容器內工作負載的查詢 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | 傳回已向保存庫註冊的所有備份管理伺服器。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/write | 建立備份保護用途 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/read | 取得備份保護用途 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/read | 取得所有可保護的容器 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/items/read | 取得容器中的所有項目 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | 檢查復原服務保存庫的備份狀態 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/action |  |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | 驗證功能 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | 解決警示。 |
> | [Microsoft. az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | 作業會傳回資源提供者的作業清單 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | 取得給定作業的作業狀態 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | 列出所有的備份保護用途 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup services, except removal of backup, vault creation and giving access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00c29273-979b-4161-815c-10b084fb9324",
  "name": "00c29273-979b-4161-815c-10b084fb9324",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/write",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/write",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-reader"></a>備份讀取者

可以查看備份服務，但無法進行變更 [深入瞭解](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp 是服務所使用的內部作業 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/read | 傳回作業的狀態 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/read | 取得對保護容器執行之作業的結果。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | 取得對受保護項目執行之作業的結果。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | 傳回對受保護項目執行之作業的狀態。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | 傳回受保護項目的物件詳細資料 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | 取得受保護項目的復原點。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/read | 傳回所有已註冊的容器 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/operationResults/read | 傳回作業的作業結果。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/read | 傳回所有作業物件 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | 匯出作業 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/read | 傳回復原服務保存庫的備份作業結果。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/read | 取得原則作業的結果。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | 傳回所有保護原則 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/read | 傳回所有受保護項目的清單。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/read | 傳回屬於訂用帳戶的所有容器 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | 傳回復原服務之受保護項目和受保護伺服器的摘要。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | 「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | 取得復原服務保存庫的警示。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | 「取得保存庫」作業會取得物件，此物件代表 'vault' 類型的 Azure 資源 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | 「取得作業結果」作業可用來取得以非同步方式提交之作業的作業狀態和結果 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | 「取得容器」作業可用來取得為資源註冊的容器。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/read | 傳回復原服務保存庫的儲存體組態。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/read | 傳回復原服務保存庫的組態。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | 傳回復原服務保存庫的備份作業狀態。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operations/read | 取得原則作業的狀態。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | 傳回已向保存庫註冊的所有備份管理伺服器。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/read | 取得備份保護用途 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/items/read | 取得容器中的所有項目 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | 檢查復原服務保存庫的備份狀態 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | 解決警示。 |
> | [Microsoft. az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | 作業會傳回資源提供者的作業清單 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | 取得給定作業的作業狀態 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | 列出所有的備份保護用途 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | 傳回復原服務保存庫的使用量詳細資料。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | 驗證功能 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view backup services, but can't make changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "name": "a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/read",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-contributor"></a>傳統儲存體帳戶參與者

可讓您管理傳統儲存體帳戶，但無法存取它們。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft. Microsoft.classicstorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/* | 建立及管理儲存體帳戶 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic storage accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "name": "86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-key-operator-service-role"></a>傳統儲存體帳戶金鑰操作員服務角色

您可以使用傳統儲存體帳戶金鑰操作員，在傳統儲存體帳戶上列出和重新產生金鑰。 [深入瞭解](../key-vault/secrets/overview-storage-keys.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. Microsoft.classicstorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listkeys/action | 列出儲存體帳戶的存取金鑰。 |
> | [Microsoft. Microsoft.classicstorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/regeneratekey/action | 重新產生儲存體帳戶的現有存取金鑰。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Classic Storage Account Key Operators are allowed to list and regenerate keys on Classic Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "name": "985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ClassicStorage/storageAccounts/listkeys/action",
        "Microsoft.ClassicStorage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-contributor"></a>資料箱參與者

可讓您管理資料箱服務下的所有項目，為他人賦予存取權除外。 [深入了解](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | [資料箱](resource-provider-operations.md#microsoftdatabox)/* |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything under Data Box Service except giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/add466c9-e687-43fc-8d98-dfcf8d720be5",
  "name": "add466c9-e687-43fc-8d98-dfcf8d720be5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Databox/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-reader"></a>資料箱讀者

可讓您管理資料箱服務，建立訂單或編輯訂單詳細資料和為他人賦予存取權除外。 [深入了解](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [資料箱](resource-provider-operations.md#microsoftdatabox)/*/read |  |
> | [Microsoft. 資料箱](resource-provider-operations.md#microsoftdatabox)/jobs/listsecrets/action |  |
> | [Microsoft. 資料箱](resource-provider-operations.md#microsoftdatabox)/jobs/listcredentials/action | 列出與訂單相關的未加密認證。 |
> | [Microsoft. 資料箱](resource-provider-operations.md#microsoftdatabox)/locations/availableSkus/action | 此方法會傳回可用的 SKU 清單。 |
> | [Microsoft. 資料箱](resource-provider-operations.md#microsoftdatabox)/locations/validateInputs/action | 此方法會執行所有類型的驗證。 |
> | [Microsoft. 資料箱](resource-provider-operations.md#microsoftdatabox)/locations/regionConfiguration/action | 此方法會傳回區域的設定。 |
> | [Microsoft. 資料箱](resource-provider-operations.md#microsoftdatabox)/locations/validateAddress/action | 驗證出貨地址，並提供備用的地址 (若有的話)。 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Data Box Service except creating order or editing order details and giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "name": "028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Databox/*/read",
        "Microsoft.Databox/jobs/listsecrets/action",
        "Microsoft.Databox/jobs/listcredentials/action",
        "Microsoft.Databox/locations/availableSkus/action",
        "Microsoft.Databox/locations/validateInputs/action",
        "Microsoft.Databox/locations/regionConfiguration/action",
        "Microsoft.Databox/locations/validateAddress/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-lake-analytics-developer"></a>Data Lake Analytics 開發人員

可讓您提交、監視及管理您自己的作業，但無法建立或刪除 Data Lake Analytics 帳戶。 [深入了解](../data-lake-analytics/data-lake-analytics-manage-use-portal.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | Microsoft.BigAnalytics/accounts/* |  |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Delete | 刪除 DataLakeAnalytics 帳戶。 |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/TakeOwnership/action | 授與權限以取消其他使用者所提交的作業。 |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Write | 建立或更新 DataLakeAnalytics 帳戶。 |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Write | 建立或更新 DataLakeAnalytics 帳戶所連結的 DataLakeStore 帳戶。 |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Delete | 取消 DataLakeStore 帳戶與 DataLakeAnalytics 帳戶的連結。 |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Write | 建立或更新 DataLakeAnalytics 帳戶所連結的儲存體帳戶。 |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Delete | 取消儲存體帳戶與 DataLakeAnalytics 帳戶的連結。 |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Write | 建立或更新防火牆規則。 |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Delete | 刪除防火牆規則。 |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Write | 建立或更新計算原則。 |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Delete | 刪除計算原則。 |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you submit, monitor, and manage your own jobs but not create or delete Data Lake Analytics accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/47b7735b-770e-4598-a7da-8b91488b4c88",
  "name": "47b7735b-770e-4598-a7da-8b91488b4c88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BigAnalytics/accounts/*",
        "Microsoft.DataLakeAnalytics/accounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.BigAnalytics/accounts/Delete",
        "Microsoft.BigAnalytics/accounts/TakeOwnership/action",
        "Microsoft.BigAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action",
        "Microsoft.DataLakeAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Write",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Write",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Lake Analytics Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader-and-data-access"></a>讀取者及資料存取

可讓您檢視所有內容，但無法讓您刪除或建立儲存體帳戶或內含的資源。 也可透過存取儲存體帳戶金鑰，對儲存體帳戶中內含的所有資料進行讀取/寫入存取。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | 傳回指定儲存體帳戶的存取金鑰。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/ListAccountSas/action | 傳回指定儲存體帳戶的帳戶 SAS 權杖。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | 傳回儲存體帳戶清單，或取得指定儲存體帳戶的屬性。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything but will not let you delete or create a storage account or contained resource. It will also allow read/write access to all data contained in a storage account via access to storage account keys.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c12c1c16-33a1-487b-954d-41c89c60f349",
  "name": "c12c1c16-33a1-487b-954d-41c89c60f349",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/ListAccountSas/action",
        "Microsoft.Storage/storageAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader and Data Access",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-contributor"></a>儲存體帳戶參與者

允許管理儲存體帳戶。 支援存取帳戶金鑰，以透過共用金鑰授權來存取資料。 [深入了解](../storage/common/storage-auth-aad.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | 建立、更新或讀取 Analysis Server 的診斷設定 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | 將資源 (例如，儲存體帳戶或 SQL Database) 加入至子網路。 不可警示。 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | 建立及管理儲存體帳戶 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage storage accounts, including accessing storage account keys which provide full access to storage account data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "name": "17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-key-operator-service-role"></a>儲存體帳戶金鑰操作員服務角色

允許列出及重新產生儲存體帳戶存取金鑰。 [深入了解](../storage/common/storage-account-keys-manage.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | 傳回指定儲存體帳戶的存取金鑰。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/regeneratekey/action | 重新產生指定儲存體帳戶的存取金鑰。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Storage Account Key Operators are allowed to list and regenerate keys on Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/81a9662b-bebf-436f-a333-f67b29880f12",
  "name": "81a9662b-bebf-436f-a333-f67b29880f12",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-contributor"></a>儲存體 Blob 資料參與者

讀取、寫入和刪除 Azure 儲存體的容器和 blob。 若要了解特定資料作業所需的動作，請參閱[呼叫 blob 和佇列資料作業的權限](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 [深入了解](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/delete | 刪除容器。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | 傳回一個容器或一份容器清單。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/write | 修改容器的中繼資料或屬性。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | 傳回 Blob 服務的使用者委派金鑰。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | 刪除 Blob。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | 傳回一個 blob 或一份 blob 清單。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/move/action | 將 blob 從一個路徑移到另一個路徑 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | 寫入 blob。 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write and delete access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "name": "ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-owner"></a>儲存體 Blob 資料擁有者

支援完整存取 Azure 儲存體 blob 容器和資料，包括指派 POSIX 存取控制。 若要了解特定資料作業所需的動作，請參閱[呼叫 blob 和佇列資料作業的權限](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 [深入了解](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/* | 容器的完整權限。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | 傳回 Blob 服務的使用者委派金鑰。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/* | Blob 的完整權限。 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "name": "b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/*",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-reader"></a>儲存體 Blob 資料讀者

讀取和列出 Azure 儲存體的容器和 blob。 若要了解特定資料作業所需的動作，請參閱[呼叫 blob 和佇列資料作業的權限](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 [深入了解](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | 傳回一個容器或一份容器清單。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | 傳回 Blob 服務的使用者委派金鑰。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | 傳回一個 blob 或一份 blob 清單。 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-delegator"></a>儲存體 Blob 委派者

取得使用者委派金鑰，以針對使用 Azure AD 認證所簽署的容器或 blob，建立共用存取簽章。 如需詳細資訊，請參閱[建立使用者委派 SAS](/rest/api/storageservices/create-user-delegation-sas)。 [深入了解](/rest/api/storageservices/get-user-delegation-key)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | 傳回 Blob 服務的使用者委派金鑰。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for generation of a user delegation key which can be used to sign SAS tokens",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "name": "db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Delegator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-contributor"></a>儲存體檔案資料 SMB 共用參與者

允許讀取、寫入及刪除 Azure 檔案共用上的檔案/目錄。 此角色在 Windows 檔案伺服器上沒有內建的對等項。 [深入了解](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | 無 |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | 傳回一個檔案/資料夾，或一份檔案/資料夾清單。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/write | 傳回寫入檔案或建立資料夾的結果。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/delete | 傳回刪除檔案/資料夾的結果。 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "name": "0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-elevated-contributor"></a>儲存體檔案資料 SMB 共用提升權限的參與者

允許對 Azure 檔案共用上的檔案/目錄，讀取、寫入、刪除和修改 ACL。 此角色相當於 Windows 檔案伺服器上的「變更」檔案共用 ACL。 [深入了解](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | 無 |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | 傳回一個檔案/資料夾，或一份檔案/資料夾清單。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/write | 傳回寫入檔案或建立資料夾的結果。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/delete | 傳回刪除檔案/資料夾的結果。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/modifypermissions/action | 傳回修改檔案/資料夾權限的結果。 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, delete and modify NTFS permission access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7264617-510b-434b-a828-9731dc254ea7",
  "name": "a7264617-510b-434b-a828-9731dc254ea7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Elevated Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-reader"></a>儲存體檔案資料 SMB 共用讀者

允許讀取 Azure 檔案共用上的檔案/目錄。 此角色相當於 Windows 檔案伺服器上的「讀取」檔案共用 ACL。 [深入了解](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | 無 |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | 傳回一個檔案/資料夾，或一份檔案/資料夾清單。 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure File Share over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aba4ae5f-2193-4029-9191-0cb91df5e314",
  "name": "aba4ae5f-2193-4029-9191-0cb91df5e314",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-contributor"></a>儲存體佇列資料參與者

讀取、寫入及刪除 Azure 儲存體的佇列和佇列訊息。 若要了解特定資料作業所需的動作，請參閱[呼叫 blob 和佇列資料作業的權限](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 [深入了解](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/delete | 刪除佇列。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/read | 傳回一個佇列或一份佇列清單。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/write | 修改佇列中繼資料或屬性。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/delete | 從佇列中刪除一或多個訊息。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | 從佇列中瞄核或取出一或多個訊息。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/write | 將訊息新增至佇列。 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "name": "974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-processor"></a>儲存體佇列資料訊息處理者

從 Azure 儲存體佇列中瞄核、擷取和刪除訊息。 若要了解特定資料作業所需的動作，請參閱[呼叫 blob 和佇列資料作業的權限](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 [深入了解](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | 無 |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | 瞄核訊息。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/process/action | 取出和刪除訊息。 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for peek, receive, and delete access to Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "name": "8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Processor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-sender"></a>儲存體佇列資料訊息傳送者

將訊息新增至 Azure 儲存體佇列。 若要了解特定資料作業所需的動作，請參閱[呼叫 blob 和佇列資料作業的權限](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 [深入了解](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | 無 |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/add/action | 將訊息新增至佇列。 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for sending of Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "name": "c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-reader"></a>儲存體佇列資料讀者

讀取和列出 Azure 儲存體的佇列和佇列訊息。 若要了解特定資料作業所需的動作，請參閱[呼叫 blob 和佇列資料作業的權限](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 [深入了解](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/read | 傳回佇列或佇列清單。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | 從佇列中瞄核或取出一或多個訊息。 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/19e7f393-937e-4f77-808e-94535e297925",
  "name": "19e7f393-937e-4f77-808e-94535e297925",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="web"></a>Web


### <a name="azure-maps-data-reader"></a>Azure 地圖服務資料讀者

授權從 Azure 地圖服務帳戶讀取地圖相關資料。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | 無 |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/read |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "name": "423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>搜尋服務參與者

可讓您管理「搜尋」服務，但無法存取它們。 [深入了解](../search/search-security-rbac.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft. Search](resource-provider-operations.md#microsoftsearch)/searchServices/* | 建立和管理搜尋服務 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Search services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "name": "7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Search/searchServices/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Search Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-accesskey-reader"></a>SignalR AccessKey 讀者

讀取 SignalR Service 存取金鑰

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft.signalrservice](resource-provider-operations.md#microsoftsignalrservice)/*/read |  |
> | [Microsoft. Microsoft.signalrservice](resource-provider-operations.md#microsoftsignalrservice)/SignalR/listkeys/action | 在管理入口網站中或透過 API 檢視 SignalR 存取金鑰 |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read SignalR Service Access Keys",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/04165923-9d83-45d5-8227-78b77b0a687e",
  "name": "04165923-9d83-45d5-8227-78b77b0a687e",
  "permissions": [
    {
      "actions": [
        "Microsoft.SignalRService/*/read",
        "Microsoft.SignalRService/SignalR/listkeys/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR AccessKey Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-app-server-preview"></a>SignalR 應用程式伺服器 (預覽) 

使用 AAD 驗證選項，讓您的應用程式伺服器存取 SignalR Service。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | 無 |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. Microsoft.signalrservice](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/accessKey/action | 產生暫時 AccessKey 來簽署 ClientTokens。 |
> | [Microsoft. Microsoft.signalrservice](resource-provider-operations.md#microsoftsignalrservice)/SignalR/serverConnection/write | 啟動伺服器連接。 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets your app server access SignalR Service with AAD auth options.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/420fcaa2-552c-430f-98ca-3264be4806c7",
  "name": "420fcaa2-552c-430f-98ca-3264be4806c7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/accessKey/action",
        "Microsoft.SignalRService/SignalR/serverConnection/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR App Server (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-contributor"></a>SignalR 參與者

建立、讀取、更新和刪除 SignalR 服務資源

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft.signalrservice](resource-provider-operations.md#microsoftsignalrservice)/* |  |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete SignalR service resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761",
  "name": "8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761",
  "permissions": [
    {
      "actions": [
        "Microsoft.SignalRService/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-serverless-contributor-preview"></a>SignalR 無伺服器參與者 (預覽) 

使用 AAD 驗證選項，讓您的應用程式在無伺服器模式下存取服務。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | 無 |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. Microsoft.signalrservice](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/clientToken/action | 產生用來啟動用戶端連接的 ClientToken。 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets your app access service in serverless mode with AAD auth options.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fd53cd77-2268-407a-8f46-7e7863d0f521",
  "name": "fd53cd77-2268-407a-8f46-7e7863d0f521",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/clientToken/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Serverless Contributor (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-service-owner-preview"></a>SignalR Service 擁有者 (預覽) 

Azure SignalR Service REST Api 的完整存取權

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | 無 |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. Microsoft.signalrservice](resource-provider-operations.md#microsoftsignalrservice)/SignalR/hub/send/action | 將訊息廣播至中樞內的所有用戶端連接。 |
> | [Microsoft. Microsoft.signalrservice](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/send/action | 將訊息廣播到群組。 |
> | [Microsoft. Microsoft.signalrservice](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/read | 檢查群組是否存在或使用者存在於群組中。 |
> | [Microsoft. Microsoft.signalrservice](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/write | 加入/離開群組。 |
> | [Microsoft. Microsoft.signalrservice](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/send/action | 將訊息直接傳送至用戶端連接。 |
> | [Microsoft. Microsoft.signalrservice](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/read | 檢查用戶端連接是否存在。 |
> | [Microsoft. Microsoft.signalrservice](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/write | 關閉用戶端連接。 |
> | [Microsoft. Microsoft.signalrservice](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/send/action | 傳送訊息給使用者，其中可能包含多個用戶端連接。 |
> | [Microsoft. Microsoft.signalrservice](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/read | 檢查使用者是否存在。 |
> | [Microsoft. Microsoft.signalrservice](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/write |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7e4f1700-ea5a-4f59-8f37-079cfe29dce3",
  "name": "7e4f1700-ea5a-4f59-8f37-079cfe29dce3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/hub/send/action",
        "Microsoft.SignalRService/SignalR/group/send/action",
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/group/write",
        "Microsoft.SignalRService/SignalR/clientConnection/send/action",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/clientConnection/write",
        "Microsoft.SignalRService/SignalR/user/send/action",
        "Microsoft.SignalRService/SignalR/user/read",
        "Microsoft.SignalRService/SignalR/user/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Service Owner (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-service-reader-preview"></a>SignalR Service 讀者 (預覽) 

Azure SignalR Service REST Api 的唯讀存取

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | 無 |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. Microsoft.signalrservice](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/read | 檢查群組是否存在或使用者存在於群組中。 |
> | [Microsoft. Microsoft.signalrservice](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/read | 檢查用戶端連接是否存在。 |
> | [Microsoft. Microsoft.signalrservice](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/read | 檢查使用者是否存在。 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ddde6b66-c0df-4114-a159-3618637b3035",
  "name": "ddde6b66-c0df-4114-a159-3618637b3035",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/user/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Service Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="web-plan-contributor"></a>Web 方案參與者

可讓您管理網站的 Web 方案，但無法存取它們。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/* | 建立和管理伺服器陣列 |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/hostingEnvironments/Join/Action | 加入 App Service 環境 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the web plans for websites, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "name": "2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/serverFarms/*",
        "Microsoft.Web/hostingEnvironments/Join/Action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Web Plan Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="website-contributor"></a>網站參與者

可讓您管理網站 (非 Web 方案)，但無法存取它們。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/components/* | 建立和管理 Insights 元件 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/certificates/* | 建立和管理網站憑證 |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/listSitesAssignedToHostName/read | 取得指派給主機名稱之網站的名稱。 |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/join/action | 加入 App Service 計畫 |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | 取得 App Service 方案的屬性 |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/sites/* | 建立和管理網站 (建立網站也需要相關聯應用程式服務方案的寫入權限) |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage websites (not web plans), but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/de139f84-1756-47ae-9be6-808fbbe84772",
  "name": "de139f84-1756-47ae-9be6-808fbbe84772",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/certificates/*",
        "Microsoft.Web/listSitesAssignedToHostName/read",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Website Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="containers"></a>容器


### <a name="acrdelete"></a>AcrDelete

acr 刪除 [深入瞭解](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. >microsoft.containerregistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/artifacts/delete | 刪除容器登錄中的成品。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr delete",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/artifacts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrDelete",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrimagesigner"></a>AcrImageSigner

acr 影像簽署者 [深入瞭解](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. >microsoft.containerregistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/sign/write | 推送/提取容器登錄的內容信任中繼資料。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr image signer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6cef56e8-d556-48e5-a04f-b8e64114680f",
  "name": "6cef56e8-d556-48e5-a04f-b8e64114680f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/sign/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrImageSigner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpull"></a>AcrPull

acr 提取 [深入瞭解](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. >microsoft.containerregistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/read | 從容器登錄中提取或取得映像。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr pull",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "name": "7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPull",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpush"></a>AcrPush

acr push [深入瞭解](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. >microsoft.containerregistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/read | 從容器登錄中提取或取得映像。 |
> | [Microsoft. >microsoft.containerregistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/push/write | 將映像推送或寫入至容器登錄。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr push",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8311e382-0749-4cb8-b61a-304f252e45ec",
  "name": "8311e382-0749-4cb8-b61a-304f252e45ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read",
        "Microsoft.ContainerRegistry/registries/push/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPush",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinereader"></a>AcrQuarantineReader

ACR 隔離資料讀取者

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. >microsoft.containerregistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/read | 從容器登錄中提取或取得隔離的映像 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cdda3590-29a3-44f6-95f2-9f980659eb04",
  "name": "cdda3590-29a3-44f6-95f2-9f980659eb04",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineReader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinewriter"></a>AcrQuarantineWriter

ACR 隔離資料寫入者

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. >microsoft.containerregistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/read | 從容器登錄中提取或取得隔離的映像 |
> | [Microsoft. >microsoft.containerregistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/write | 寫入/修改已隔離映像的隔離狀態 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data writer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "name": "c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read",
        "Microsoft.ContainerRegistry/registries/quarantine/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineWriter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Azure Kubernetes Service 叢集管理員角色

列出叢集管理員認證動作。 [深入了解](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterAdminCredential/action | 列出受控叢集的 clusterAdmin 認證 |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/accessProfiles/listCredential/action | 使用清單認證依角色名稱取得受控叢集存取設定檔 |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | 取得受控叢集 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster admin credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "name": "0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action",
        "Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster Admin Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-user-role"></a>Azure Kubernetes Service 叢集使用者角色

列出叢集使用者認證動作。 [深入了解](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | 列出受控叢集的 clusterUser 認證 |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | 取得受控叢集 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "name": "4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-contributor-role"></a>Azure Kubernetes Service 參與者角色

授與讀取和寫入 Azure Kubernetes Service 叢集的存取權 [深入瞭解](../aks/concepts-identity.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | 取得受控叢集 |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/write | 建立新的受控叢集，或更新現有的受控叢集 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read and write Azure Kubernetes Service clusters",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "name": "ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/read",
        "Microsoft.ContainerService/managedClusters/write",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-admin"></a>Azure Kubernetes Service RBAC 管理員

可讓您管理叢集/命名空間下的所有資源，但更新或刪除資源配額和命名空間除外。 [深入了解](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/write | 建立或更新部署。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 取得訂用帳戶作業結果。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 取得訂用帳戶清單。 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | 列出受控叢集的 clusterUser 認證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/write | 寫入 resourcequotas |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/delete | 刪除 resourcequotas |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/write | 寫入命名空間 |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/delete | 刪除命名空間 |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources under cluster/namespace, except update or delete resource quotas and namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3498e952-d568-435e-9b2c-8d77e338d7f7",
  "name": "3498e952-d568-435e-9b2c-8d77e338d7f7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": [
        "Microsoft.ContainerService/managedClusters/resourcequotas/write",
        "Microsoft.ContainerService/managedClusters/resourcequotas/delete",
        "Microsoft.ContainerService/managedClusters/namespaces/write",
        "Microsoft.ContainerService/managedClusters/namespaces/delete"
      ]
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-cluster-admin"></a>Azure Kubernetes Service RBAC 叢集管理員

可讓您管理叢集中的所有資源。 [深入了解](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/write | 建立或更新部署。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 取得訂用帳戶作業結果。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 取得訂用帳戶清單。 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | 列出受控叢集的 clusterUser 認證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources in the cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "name": "b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Cluster Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-reader"></a>Azure Kubernetes Service RBAC 讀者

允許唯讀存取，以查看命名空間中的大部分物件。 它不允許查看角色或角色系結。 此角色不允許您查看秘密，因為讀取秘密的內容可讓您存取命名空間中的 ServiceAccount 認證，這會允許 API 存取做為命名空間中的任何 ServiceAccount () 的許可權擴大形式。 在叢集範圍套用此角色可讓您存取所有命名空間。 [深入了解](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/write | 建立或更新部署。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 取得訂用帳戶作業結果。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 取得訂用帳戶清單。 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/controllerrevisions/read | 讀取 controllerrevisions |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/daemonsets/read | 讀取 daemonset |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/deployments/read | 讀取部署 |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/replicasets/read | 讀取 replicaset |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/statefulsets/read | 讀取 statefulset |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/autoscaling/horizontalpodautoscalers/read | 讀取 horizontalpodautoscalers |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/cronjobs/read | 讀取 cronjobs |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/jobs/read | 讀取作業 |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/read | 讀取 configmaps |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/endpoints/read | 讀取端點 |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events.k8s.io/events/read | 讀取事件 |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events/read | 讀取事件 |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/daemonsets/read | 讀取 daemonset |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/deployments/read | 讀取部署 |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/ingresses/read | 讀取 ingresses |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/networkpolicies/read | 讀取 networkpolicies |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/replicasets/read | 讀取 replicaset |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/read | 讀取 limitranges |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/read | 讀取命名空間 |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/ingresses/read | 讀取 ingresses |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/networkpolicies/read | 讀取 networkpolicies |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/read | 讀取 persistentvolumeclaims |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/read | 讀取 pod |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/policy/poddisruptionbudgets/read | 讀取 poddisruptionbudgets |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/read | 讀取 replicationcontrollers |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/read | 讀取 replicationcontrollers |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/read | 讀取 resourcequotas |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/read | 讀取 serviceaccounts |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/services/read | 讀取服務 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read-only access to see most objects in a namespace. It does not allow viewing roles or role bindings. This role does not allow viewing Secrets, since reading the contents of Secrets enables access to ServiceAccount credentials in the namespace, which would allow API access as any ServiceAccount in the namespace (a form of privilege escalation). Applying this role at cluster scope will give access across all namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "name": "7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read",
        "Microsoft.ContainerService/managedClusters/apps/daemonsets/read",
        "Microsoft.ContainerService/managedClusters/apps/deployments/read",
        "Microsoft.ContainerService/managedClusters/apps/replicasets/read",
        "Microsoft.ContainerService/managedClusters/apps/statefulsets/read",
        "Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/read",
        "Microsoft.ContainerService/managedClusters/batch/cronjobs/read",
        "Microsoft.ContainerService/managedClusters/batch/jobs/read",
        "Microsoft.ContainerService/managedClusters/configmaps/read",
        "Microsoft.ContainerService/managedClusters/endpoints/read",
        "Microsoft.ContainerService/managedClusters/events.k8s.io/events/read",
        "Microsoft.ContainerService/managedClusters/events/read",
        "Microsoft.ContainerService/managedClusters/extensions/daemonsets/read",
        "Microsoft.ContainerService/managedClusters/extensions/deployments/read",
        "Microsoft.ContainerService/managedClusters/extensions/ingresses/read",
        "Microsoft.ContainerService/managedClusters/extensions/networkpolicies/read",
        "Microsoft.ContainerService/managedClusters/extensions/replicasets/read",
        "Microsoft.ContainerService/managedClusters/limitranges/read",
        "Microsoft.ContainerService/managedClusters/namespaces/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/read",
        "Microsoft.ContainerService/managedClusters/persistentvolumeclaims/read",
        "Microsoft.ContainerService/managedClusters/pods/read",
        "Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/read",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/read",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/read",
        "Microsoft.ContainerService/managedClusters/resourcequotas/read",
        "Microsoft.ContainerService/managedClusters/serviceaccounts/read",
        "Microsoft.ContainerService/managedClusters/services/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-writer"></a>Azure Kubernetes Service RBAC 寫入器

允許對命名空間中大部分物件的讀取/寫入存取。此角色不允許查看或修改角色或角色系結。 不過，此角色可讓您存取秘密，並以命名空間中的任何 ServiceAccount 來執行 pod，讓它可以用來取得命名空間中任何 ServiceAccount 的 API 存取層級。 在叢集範圍套用此角色可讓您存取所有命名空間。 [深入了解](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/write | 建立或更新部署。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 取得訂用帳戶作業結果。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 取得訂用帳戶清單。 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/controllerrevisions/read | 讀取 controllerrevisions |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/daemonsets/* |  |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/deployments/* |  |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/replicasets/* |  |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/statefulsets/* |  |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/autoscaling/horizontalpodautoscalers/* |  |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/cronjobs/* |  |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/jobs/* |  |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/* |  |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/endpoints/* |  |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events.k8s.io/events/read | 讀取事件 |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events/read | 讀取事件 |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/daemonsets/* |  |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/deployments/* |  |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/ingresses/* |  |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/networkpolicies/* |  |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/replicasets/* |  |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/read | 讀取 limitranges |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/read | 讀取命名空間 |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/ingresses/* |  |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/networkpolicies/* |  |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/* |  |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/* |  |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/policy/poddisruptionbudgets/* |  |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/read | 讀取 resourcequotas |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/secrets/* |  |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/* |  |
> | [Microsoft. >microsoft.containerservice](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/services/* |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read/write access to most objects in a namespace.This role does not allow viewing or modifying roles or role bindings. However, this role allows accessing Secrets and running Pods as any ServiceAccount in the namespace, so it can be used to gain the API access levels of any ServiceAccount in the namespace. Applying this role at cluster scope will give access across all namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "name": "a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read",
        "Microsoft.ContainerService/managedClusters/apps/daemonsets/*",
        "Microsoft.ContainerService/managedClusters/apps/deployments/*",
        "Microsoft.ContainerService/managedClusters/apps/replicasets/*",
        "Microsoft.ContainerService/managedClusters/apps/statefulsets/*",
        "Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.ContainerService/managedClusters/batch/cronjobs/*",
        "Microsoft.ContainerService/managedClusters/batch/jobs/*",
        "Microsoft.ContainerService/managedClusters/configmaps/*",
        "Microsoft.ContainerService/managedClusters/endpoints/*",
        "Microsoft.ContainerService/managedClusters/events.k8s.io/events/read",
        "Microsoft.ContainerService/managedClusters/events/read",
        "Microsoft.ContainerService/managedClusters/extensions/daemonsets/*",
        "Microsoft.ContainerService/managedClusters/extensions/deployments/*",
        "Microsoft.ContainerService/managedClusters/extensions/ingresses/*",
        "Microsoft.ContainerService/managedClusters/extensions/networkpolicies/*",
        "Microsoft.ContainerService/managedClusters/extensions/replicasets/*",
        "Microsoft.ContainerService/managedClusters/limitranges/read",
        "Microsoft.ContainerService/managedClusters/namespaces/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.ContainerService/managedClusters/persistentvolumeclaims/*",
        "Microsoft.ContainerService/managedClusters/pods/*",
        "Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/*",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/*",
        "Microsoft.ContainerService/managedClusters/resourcequotas/read",
        "Microsoft.ContainerService/managedClusters/secrets/*",
        "Microsoft.ContainerService/managedClusters/serviceaccounts/*",
        "Microsoft.ContainerService/managedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="databases"></a>資料庫


### <a name="cosmos-db-account-reader-role"></a>Cosmos DB 帳戶讀者角色

可以讀取 Azure Cosmos DB 帳戶資料。 請參閱 [DocumentDB 帳戶參與者](#documentdb-account-contributor)以管理 Azure Cosmos DB 帳戶。 [深入了解](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/*/read | 讀取任何集合 |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlykeys/action | 讀取資料庫帳戶的唯讀金鑰。 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/read | 讀取計量定義 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/Metrics/read | 讀取計量 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read Azure Cosmos DB Accounts data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "name": "fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDB/*/read",
        "Microsoft.DocumentDB/databaseAccounts/readonlykeys/action",
        "Microsoft.Insights/MetricDefinitions/read",
        "Microsoft.Insights/Metrics/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Account Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmos-db-operator"></a>Cosmos DB 操作員

可讓您管理 Azure Cosmos DB 帳戶，但無法存取其中的資料。 防止存取帳戶金鑰和連接字串。 [深入了解](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | 將資源 (例如，儲存體帳戶或 SQL Database) 加入至子網路。 不可警示。 |
> | **NotActions** |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlyKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/regenerateKey/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listConnectionStrings/* |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Cosmos DB accounts, but not access data in them. Prevents access to account keys and connection strings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/230815da-be43-4aae-9cb4-875f7bd000aa",
  "name": "230815da-be43-4aae-9cb4-875f7bd000aa",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [
        "Microsoft.DocumentDB/databaseAccounts/readonlyKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/regenerateKey/*",
        "Microsoft.DocumentDB/databaseAccounts/listKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosbackupoperator"></a>CosmosBackupOperator

可以提交 Cosmos DB 資料庫或帳戶的容器的還原要求 [深入瞭解](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/backup/action | 提交要求以設定備份 |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/restore/action | 提交還原要求 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can submit restore request for a Cosmos DB database or a container for an account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "name": "db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/databaseAccounts/backup/action",
        "Microsoft.DocumentDB/databaseAccounts/restore/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosBackupOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="documentdb-account-contributor"></a>DocumentDB 帳戶參與者

可以管理 Azure Cosmos DB 帳戶。 Azure Cosmos DB 先前稱為 DocumentDB。 [深入了解](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* | 建立及管理 Azure Cosmos DB 帳戶 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | 將資源 (例如，儲存體帳戶或 SQL Database) 加入至子網路。 不可警示。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DocumentDB accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5bd9cd88-fe45-4216-938b-f97437e15450",
  "name": "5bd9cd88-fe45-4216-938b-f97437e15450",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DocumentDB Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="redis-cache-contributor"></a>Redis 快取參與者

可讓您管理 Redis 快取，但無法存取它們。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft. Cache](resource-provider-operations.md#microsoftcache)/register/action | 向訂用帳戶註冊 'Microsoft.Cache' 資源提供者 |
> | [Microsoft. Cache](resource-provider-operations.md#microsoftcache)/redis/* | 建立和管理 Redis 快取 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Redis caches, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e0f68234-74aa-48ed-b826-c38b57376e17",
  "name": "e0f68234-74aa-48ed-b826-c38b57376e17",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cache/register/action",
        "Microsoft.Cache/redis/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Redis Cache Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-db-contributor"></a>SQL DB 參與者

可讓您管理 SQL 資料庫，但無法存取它們。 此外，您也無法管理其安全性相關原則或其父 SQL 伺服器。 [深入了解](../data-share/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [/Locations/*](resource-provider-operations.md#microsoftsql)/read |  |
> | [/Servers/databases/*](resource-provider-operations.md#microsoftsql) | 建立和管理 SQL 資料庫 |
> | [Microsoft .sql](resource-provider-operations.md#microsoftsql)/servers/read | 傳回伺服器清單，或取得指定伺服器的屬性。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | 讀取計量 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | 讀取計量定義 |
> | **NotActions** |  |
> | [/ManagedInstances/databases/currentSensitivityLabels/*](resource-provider-operations.md#microsoftsql) |  |
> | [/ManagedInstances/databases/recommendedSensitivityLabels/*](resource-provider-operations.md#microsoftsql) |  |
> | [/ManagedInstances/databases/schemas/tables/columns/sensitivityLabels/*](resource-provider-operations.md#microsoftsql) |  |
> | [/ManagedInstances/databases/securityAlertPolicies/*](resource-provider-operations.md#microsoftsql) |  |
> | [/ManagedInstances/databases/sensitivityLabels/*](resource-provider-operations.md#microsoftsql) |  |
> | [/ManagedInstances/databases/vulnerabilityAssessments/*](resource-provider-operations.md#microsoftsql) |  |
> | [/ManagedInstances/securityAlertPolicies/*](resource-provider-operations.md#microsoftsql) |  |
> | [/ManagedInstances/vulnerabilityAssessments/*](resource-provider-operations.md#microsoftsql) |  |
> | [/Servers/databases/auditingSettings/*](resource-provider-operations.md#microsoftsql) | 編輯稽核設定 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | 擷取資料庫 Blob 稽核記錄 |
> | [/Servers/databases/currentSensitivityLabels/*](resource-provider-operations.md#microsoftsql) |  |
> | [/Servers/databases/dataMaskingPolicies/*](resource-provider-operations.md#microsoftsql) | 編輯資料遮罩原則 |
> | [/Servers/databases/extendedAuditingSettings/*](resource-provider-operations.md#microsoftsql) |  |
> | [/Servers/databases/recommendedSensitivityLabels/*](resource-provider-operations.md#microsoftsql) |  |
> | [/Servers/databases/schemas/tables/columns/sensitivityLabels/*](resource-provider-operations.md#microsoftsql) |  |
> | [/Servers/databases/securityAlertPolicies/*](resource-provider-operations.md#microsoftsql) | 編輯安全性警示原則 |
> | [/Servers/databases/securityMetrics/*](resource-provider-operations.md#microsoftsql) | 編輯安全性計量 |
> | [/Servers/databases/sensitivityLabels/*](resource-provider-operations.md#microsoftsql) |  |
> | [/Servers/databases/vulnerabilityAssessments/*](resource-provider-operations.md#microsoftsql) |  |
> | [/Servers/databases/vulnerabilityAssessmentScans/*](resource-provider-operations.md#microsoftsql) |  |
> | [/Servers/databases/vulnerabilityAssessmentSettings/*](resource-provider-operations.md#microsoftsql) |  |
> | [/Servers/vulnerabilityAssessments/*](resource-provider-operations.md#microsoftsql) |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL databases, but not access to them. Also, you can't manage their security-related policies or their parent SQL servers.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "name": "9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/databases/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL DB Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-managed-instance-contributor"></a>SQL 受控執行個體參與者

可讓您管理 SQL 受控執行個體和必要的網路設定，但無法將存取權授與其他人。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/routeTables/* |  |
> | [/Locations/*](resource-provider-operations.md#microsoftsql)/read |  |
> | [/Locations/instanceFailoverGroups/*](resource-provider-operations.md#microsoftsql) |  |
> | [/ManagedInstances/*](resource-provider-operations.md#microsoftsql) |  |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/* |  |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | 讀取計量 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | 讀取計量定義 |
> | **NotActions** |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/delete | 只 Azure Active Directory authentication 物件刪除特定的受管理伺服器 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/write | 新增或更新特定的受管理伺服器 Azure Active Directory 僅限驗證物件 |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL Managed Instances and required network configuration, but can't give access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "name": "4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Network/networkSecurityGroups/*",
        "Microsoft.Network/routeTables/*",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/locations/instanceFailoverGroups/*",
        "Microsoft.Sql/managedInstances/*",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/*",
        "Microsoft.Network/virtualNetworks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/delete",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/write"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Managed Instance Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-security-manager"></a>SQL 安全性管理員

可讓您管理 SQL 伺服器及資料庫的安全性相關原則，但無法存取它們。 [深入了解](../azure-sql/database/azure-defender-for-sql.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | 將資源 (例如，儲存體帳戶或 SQL Database) 加入至子網路。 不可警示。 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/locations/administratorAzureAsyncOperation/read | 取得受控實例的 azure async 系統管理員作業結果。 |
> | [/ManagedInstances/databases/currentSensitivityLabels/*](resource-provider-operations.md#microsoftsql) |  |
> | [/ManagedInstances/databases/recommendedSensitivityLabels/*](resource-provider-operations.md#microsoftsql) |  |
> | [/ManagedInstances/databases/schemas/tables/columns/sensitivityLabels/*](resource-provider-operations.md#microsoftsql) |  |
> | [/ManagedInstances/databases/securityAlertPolicies/*](resource-provider-operations.md#microsoftsql) |  |
> | [/ManagedInstances/databases/sensitivityLabels/*](resource-provider-operations.md#microsoftsql) |  |
> | [/ManagedInstances/databases/vulnerabilityAssessments/*](resource-provider-operations.md#microsoftsql) |  |
> | [/ManagedInstances/securityAlertPolicies/*](resource-provider-operations.md#microsoftsql) |  |
> | [/ManagedInstances/databases/transparentDataEncryption/*](resource-provider-operations.md#microsoftsql) |  |
> | [/ManagedInstances/vulnerabilityAssessments/*](resource-provider-operations.md#microsoftsql) |  |
> | [/Servers/auditingSettings/*](resource-provider-operations.md#microsoftsql) | 建立和管理 SQL Server 稽核設定 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/extendedAuditingSettings/read | 擷取指定伺服器上所設定之擴充伺服器 Blob 稽核原則的詳細資料 |
> | [/Servers/databases/auditingSettings/*](resource-provider-operations.md#microsoftsql) | 建立和管理 SQL Server 資料庫稽核設定 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | 擷取資料庫 Blob 稽核記錄 |
> | [/Servers/databases/currentSensitivityLabels/*](resource-provider-operations.md#microsoftsql) |  |
> | [/Servers/databases/dataMaskingPolicies/*](resource-provider-operations.md#microsoftsql) | 建立和管理 SQL Server 資料庫資料遮罩原則 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/read | 擷取指定資料庫上所設定之擴充 Blob 稽核原則的詳細資料 |
> | [Microsoft .sql](resource-provider-operations.md#microsoftsql)/servers/databases/read | 傳回資料庫清單，或取得指定資料庫的屬性。 |
> | [/Servers/databases/recommendedSensitivityLabels/*](resource-provider-operations.md#microsoftsql) |  |
> | [Microsoft .sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/read | 取得資料庫結構描述。 |
> | [Microsoft .sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/read | 取得資料庫資料行。 |
> | [/Servers/databases/schemas/tables/columns/sensitivityLabels/*](resource-provider-operations.md#microsoftsql) |  |
> | [Microsoft .sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/read | 取得資料庫資料表。 |
> | [/Servers/databases/securityAlertPolicies/*](resource-provider-operations.md#microsoftsql) | 建立和管理 SQL Server 資料庫安全性警示原則 |
> | [/Servers/databases/securityMetrics/*](resource-provider-operations.md#microsoftsql) | 建立和管理 SQL Server 資料庫安全性度量 |
> | [/Servers/databases/sensitivityLabels/*](resource-provider-operations.md#microsoftsql) |  |
> | [/Servers/databases/transparentDataEncryption/*](resource-provider-operations.md#microsoftsql) |  |
> | [/Servers/databases/vulnerabilityAssessments/*](resource-provider-operations.md#microsoftsql) |  |
> | [/Servers/databases/vulnerabilityAssessmentScans/*](resource-provider-operations.md#microsoftsql) |  |
> | [/Servers/databases/vulnerabilityAssessmentSettings/*](resource-provider-operations.md#microsoftsql) |  |
> | [/Servers/firewallRules/*](resource-provider-operations.md#microsoftsql) |  |
> | [Microsoft .sql](resource-provider-operations.md#microsoftsql)/servers/read | 傳回伺服器清單，或取得指定伺服器的屬性。 |
> | [/Servers/securityAlertPolicies/*](resource-provider-operations.md#microsoftsql) | 建立和管理 SQL Server 安全性警示原則 |
> | [/Servers/vulnerabilityAssessments/*](resource-provider-operations.md#microsoftsql) |  |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | [/Servers/azureADOnlyAuthentications/*](resource-provider-operations.md#microsoftsql) |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/managedInstances/read | 傳回受控執行個體的清單，或取得指定受控執行個體的屬性。 |
> | [/ManagedInstances/azureADOnlyAuthentications/*](resource-provider-operations.md#microsoftsql) |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the security-related policies of SQL servers and databases, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "name": "056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/administratorAzureAsyncOperation/read",
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/transparentDataEncryption/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/read",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/read",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/transparentDataEncryption/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/firewallRules/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Support/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/*",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Security Manager",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-server-contributor"></a>SQL Server 參與者

可讓您管理 SQL 伺服器及資料庫，但無法存取這些伺服器及資料庫，也無法存取其安全性相關原則。 [深入了解](../azure-sql/database/authentication-aad-configure.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [/Locations/*](resource-provider-operations.md#microsoftsql)/read |  |
> | [/Servers/*](resource-provider-operations.md#microsoftsql) | 建立和管理 SQL Server |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | 讀取計量 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | 讀取計量定義 |
> | **NotActions** |  |
> | [/ManagedInstances/databases/currentSensitivityLabels/*](resource-provider-operations.md#microsoftsql) |  |
> | [/ManagedInstances/databases/recommendedSensitivityLabels/*](resource-provider-operations.md#microsoftsql) |  |
> | [/ManagedInstances/databases/schemas/tables/columns/sensitivityLabels/*](resource-provider-operations.md#microsoftsql) |  |
> | [/ManagedInstances/databases/securityAlertPolicies/*](resource-provider-operations.md#microsoftsql) |  |
> | [/ManagedInstances/databases/sensitivityLabels/*](resource-provider-operations.md#microsoftsql) |  |
> | [/ManagedInstances/databases/vulnerabilityAssessments/*](resource-provider-operations.md#microsoftsql) |  |
> | [/ManagedInstances/securityAlertPolicies/*](resource-provider-operations.md#microsoftsql) |  |
> | [/ManagedInstances/vulnerabilityAssessments/*](resource-provider-operations.md#microsoftsql) |  |
> | [/Servers/auditingSettings/*](resource-provider-operations.md#microsoftsql) | 編輯 SQL Server 稽核設定 |
> | [/Servers/databases/auditingSettings/*](resource-provider-operations.md#microsoftsql) | 編輯 SQL Server 資料庫稽核設定 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | 擷取資料庫 Blob 稽核記錄 |
> | [/Servers/databases/currentSensitivityLabels/*](resource-provider-operations.md#microsoftsql) |  |
> | [/Servers/databases/dataMaskingPolicies/*](resource-provider-operations.md#microsoftsql) | 編輯 SQL Server 資料庫資料遮罩原則 |
> | [/Servers/databases/extendedAuditingSettings/*](resource-provider-operations.md#microsoftsql) |  |
> | [/Servers/databases/recommendedSensitivityLabels/*](resource-provider-operations.md#microsoftsql) |  |
> | [/Servers/databases/schemas/tables/columns/sensitivityLabels/*](resource-provider-operations.md#microsoftsql) |  |
> | [/Servers/databases/securityAlertPolicies/*](resource-provider-operations.md#microsoftsql) | 編輯 SQL Server 資料庫安全性警示原則 |
> | [/Servers/databases/securityMetrics/*](resource-provider-operations.md#microsoftsql) | 編輯 SQL Server 資料庫安全性度量 |
> | [/Servers/databases/sensitivityLabels/*](resource-provider-operations.md#microsoftsql) |  |
> | [/Servers/databases/vulnerabilityAssessments/*](resource-provider-operations.md#microsoftsql) |  |
> | [/Servers/databases/vulnerabilityAssessmentScans/*](resource-provider-operations.md#microsoftsql) |  |
> | [/Servers/databases/vulnerabilityAssessmentSettings/*](resource-provider-operations.md#microsoftsql) |  |
> | [/Servers/extendedAuditingSettings/*](resource-provider-operations.md#microsoftsql) |  |
> | [/Servers/securityAlertPolicies/*](resource-provider-operations.md#microsoftsql) | 編輯 SQL Server 安全性警示原則 |
> | [/Servers/vulnerabilityAssessments/*](resource-provider-operations.md#microsoftsql) |  |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/delete | 只 Azure Active Directory authentication 物件刪除特定伺服器 |
> | [Microsoft .Sql](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/write | 將特定伺服器新增或更新 Azure Active Directory 只有驗證物件 |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL servers and databases, but not access to them, and not their security -related policies.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "name": "6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/*",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/delete",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/write"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Server Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="analytics"></a>分析


### <a name="azure-event-hubs-data-owner"></a>Azure 事件中樞資料擁有者

允許完整存取 Azure 事件中樞資源。 [深入了解](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f526a384-b230-433a-b45c-95f59c4a2dec",
  "name": "f526a384-b230-433a-b45c-95f59c4a2dec",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-receiver"></a>Azure 事件中樞資料接收者

允許接收 Azure 事件中樞資源。 [深入了解](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/consumergroups/read |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft EventHub](resource-provider-operations.md#microsofteventhub)/*/receive/action |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows receive access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "name": "a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/consumergroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-sender"></a>Azure 事件中樞資料傳送者

允許傳送 Azure 事件中樞資源。 [深入了解](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/read |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft EventHub](resource-provider-operations.md#microsofteventhub)/*/send/action |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows send access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2b629674-e913-4c01-ae53-ef4638d8f975",
  "name": "2b629674-e913-4c01-ae53-ef4638d8f975",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-factory-contributor"></a>Data Factory 參與者

建立和管理 Data Factory，以及其中的子資源。 [深入了解](../data-factory/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft. DataFactory](resource-provider-operations.md#microsoftdatafactory)/dataFactories/* | 建立和管理 Data Factory 以及其中的子資源。 |
> | [Microsoft. DataFactory](resource-provider-operations.md#microsoftdatafactory)/factories/* | 建立和管理 Data Factory 以及其中的子資源。 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/write | 建立或更新 eventSubscription |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and manage data factories, as well as child resources within them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/673868aa-7521-48a0-acc6-0f60742d39f5",
  "name": "673868aa-7521-48a0-acc6-0f60742d39f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DataFactory/dataFactories/*",
        "Microsoft.DataFactory/factories/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.EventGrid/eventSubscriptions/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Factory Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-purger"></a>資料清除者

可以清除分析資料 [深入瞭解](../azure-monitor/platform/personal-data-mgmt.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [/Components/*](resource-provider-operations.md#microsoftinsights)/read |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/components/purge/action | 從 Application Insights 清除資料 |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | 檢視記錄分析資料 |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/purge/action | 從工作區刪除指定的資料 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can purge analytics data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "name": "150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/components/*/read",
        "Microsoft.Insights/components/purge/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/purge/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Purger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-cluster-operator"></a>HDInsight 叢集操作員

可讓您讀取和修改 HDInsight 叢集設定。 [深入了解](../hdinsight/hdinsight-migrate-granular-access-cluster-configurations.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft HDInsight](resource-provider-operations.md#microsofthdinsight)/*/read |  |
> | [Microsoft HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/getGatewaySettings/action | 取得 HDInsight 叢集的閘道設定 |
> | [Microsoft HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/updateGatewaySettings/action | 更新 HDInsight 叢集的閘道設定 |
> | [Microsoft HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/configurations/* |  |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | 取得或列出部署作業。 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and modify HDInsight cluster configurations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/61ed4efc-fab3-44fd-b111-e24485cc132a",
  "name": "61ed4efc-fab3-44fd-b111-e24485cc132a",
  "permissions": [
    {
      "actions": [
        "Microsoft.HDInsight/*/read",
        "Microsoft.HDInsight/clusters/getGatewaySettings/action",
        "Microsoft.HDInsight/clusters/updateGatewaySettings/action",
        "Microsoft.HDInsight/clusters/configurations/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Cluster Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-domain-services-contributor"></a>HDInsight 網域服務參與者

可以讀取、建立、修改和刪除 HDInsight 所需的網域服務相關作業企業安全性套件 [深入瞭解](../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [MICROSOFT AAD](resource-provider-operations.md#microsoftaad)/*/read |  |
> | [MICROSOFT AAD](resource-provider-operations.md#microsoftaad)/domainServices/*/read |  |
> | [MICROSOFT AAD](resource-provider-operations.md#microsoftaad)/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can Read, Create, Modify and Delete Domain Services related operations needed for HDInsight Enterprise Security Package",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "name": "8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "permissions": [
    {
      "actions": [
        "Microsoft.AAD/*/read",
        "Microsoft.AAD/domainServices/*/read",
        "Microsoft.AAD/domainServices/oucontainer/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Domain Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-contributor"></a>Log Analytics 參與者

「Log Analytics 參與者」角色可以讀取所有監視資料和編輯監視設定。 編輯監視設定包括將 VM 延伸模組新增至 VM、讀取儲存體帳戶金鑰以便能夠設定從「Azure 儲存體」收集記錄、建立及設定「自動化」帳戶、新增解決方案，以及設定所有 Azure 資源上的 Azure 診斷。 [深入了解](../azure-monitor/platform/manage-access.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | */read | 讀取密碼以外的所有類型的資源。 |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/* |  |
> | [Microsoft. Microsoft.classiccompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/extensions/* |  |
> | [Microsoft. Microsoft.classicstorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | 列出儲存體帳戶的存取金鑰。 |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/extensions/* |  |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/extensions/write | 安裝或更新 Azure Arc 擴充 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | 建立、更新或讀取 Analysis Server 的診斷設定 |
> | [OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/* |  |
> | [Microsoft.operationsmanagement](resource-provider-operations.md#microsoftoperationsmanagement)/* |  |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourcegroups/deployments/* |  |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | 傳回指定儲存體帳戶的存取金鑰。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Contributor can read all monitoring data and edit monitoring settings. Editing monitoring settings includes adding the VM extension to VMs; reading storage account keys to be able to configure collection of logs from Azure Storage; creating and configuring Automation accounts; adding solutions; and configuring Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "name": "92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Automation/automationAccounts/*",
        "Microsoft.ClassicCompute/virtualMachines/extensions/*",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.Compute/virtualMachines/extensions/*",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.OperationalInsights/*",
        "Microsoft.OperationsManagement/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-reader"></a>Log Analytics 讀者

「Log Analytics 讀者」可以檢視和搜尋所有監視資料，以及檢視監視設定，包括檢視所有 Azure 資源上的 Azure 診斷設定。 [深入了解](../azure-monitor/platform/manage-access.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | */read | 讀取密碼以外的所有類型的資源。 |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | 使用新的引擎進行搜尋。 |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | 執行搜尋查詢 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/read | 擷取工作區的共用金鑰。 這些金鑰可用來將 Microsoft Operational Insights 代理程式連線到工作區。 |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Reader can view and search all monitoring data as well as and view monitoring settings, including viewing the configuration of Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/73c42c96-874c-492b-b04d-ab87d138a893",
  "name": "73c42c96-874c-492b-b04d-ab87d138a893",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.OperationalInsights/workspaces/sharedKeys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="schema-registry-contributor-preview"></a>結構描述登錄參與者 (預覽)

讀取、寫入及刪除結構描述登錄群組和結構描述。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [/Namespaces/schemagroups/*](resource-provider-operations.md#microsofteventhub) |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [/Namespaces/schemas/*](resource-provider-operations.md#microsofteventhub) |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read, write, and delete Schema Registry groups and schemas.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5dffeca3-4936-4216-b2bc-10343a5abb25",
  "name": "5dffeca3-4936-4216-b2bc-10343a5abb25",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/namespaces/schemagroups/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/namespaces/schemas/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Schema Registry Contributor (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="schema-registry-reader-preview"></a>結構描述登錄讀取器 (預覽)

讀取並列出結構描述登錄群組和結構描述。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [/Namespaces/schemagroups/read](resource-provider-operations.md#microsofteventhub) | 取得 SchemaGroup 資源描述的清單 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [/Namespaces/schemas/read](resource-provider-operations.md#microsofteventhub) | 取出架構 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and list Schema Registry groups and schemas.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2c56ea50-c6b3-40a6-83c0-9d98858bc7d2",
  "name": "2c56ea50-c6b3-40a6-83c0-9d98858bc7d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/namespaces/schemagroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/namespaces/schemas/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Schema Registry Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="blockchain"></a>區塊鏈


### <a name="blockchain-member-node-access-preview"></a>區塊鏈成員節點存取 (預覽)

允許存取區塊鏈成員節點 [深入瞭解](../blockchain/service/configure-aad.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. 區塊鏈](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/read | 取得或列出現有的區塊鏈成員交易節點。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. 區塊鏈](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/connect/action | 連線至區塊鏈成員交易節點。 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for access to Blockchain Member nodes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "name": "31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Blockchain Member Node Access (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="ai--machine-learning"></a>AI + 機器學習


### <a name="cognitive-services-contributor"></a>認知服務參與者

可讓您建立、讀取、更新、刪除及管理認知服務的金鑰。 [深入了解](../cognitive-services/cognitive-services-virtual-networks.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | [Microsoft. 功能](resource-provider-operations.md#microsoftfeatures)/features/read | 取得訂用帳戶的功能。 |
> | [Microsoft. 功能](resource-provider-operations.md#microsoftfeatures)/providers/features/read | 取得給定資源提供者中某個訂用帳戶的功能。 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | 建立、更新或讀取 Analysis Server 的診斷設定 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/read | 讀取記錄定義 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/read | 讀取計量定義 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | 讀取計量 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | 取得或列出部署作業。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 取得訂用帳戶作業結果。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 取得訂用帳戶清單。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourcegroups/deployments/* |  |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, read, update, delete and manage keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "name": "25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.CognitiveServices/*",
        "Microsoft.Features/features/read",
        "Microsoft.Features/providers/features/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-data-reader-preview"></a>認知服務資料讀者 (預覽)

可讓您讀取認知服務資料。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | 無 |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read Cognitive Services data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "name": "b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-user"></a>認知服務使用者

可讓您讀取和列出認知服務的金鑰。 [深入了解](../cognitive-services/authentication.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/listkeys/action | 列出金鑰 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | 讀取傳統計量警示 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/read | 讀取資源診斷設定 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/read | 讀取記錄定義 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/read | 讀取計量定義 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | 讀取計量 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | 取得或列出部署作業。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 取得訂用帳戶作業結果。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 取得訂用帳戶清單。 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and list keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a97b65f3-24c7-4388-baec-2e87135dc908",
  "name": "a97b65f3-24c7-4388-baec-2e87135dc908",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.CognitiveServices/accounts/listkeys/action",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Insights/diagnosticSettings/read",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="mixed-reality"></a>混合實境


### <a name="remote-rendering-administrator"></a>遠端呈現系統管理員

提供使用者轉換、管理會話、轉譯和診斷功能，以 Azure 遠端轉譯 [深入瞭解](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | 無 |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/action | 開始資產轉換 |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/read | 取得資產轉換屬性 |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/delete | 停止資產轉換 |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/read | 取得會話屬性 |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/action | 開始會話 |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/delete | 停止會話 |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/read | 連接至會話 |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/diagnostic/read | 連接至遠端轉譯檢查 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with conversion, manage session, rendering and diagnostics capabilities for Azure Remote Rendering",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "name": "3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="remote-rendering-client"></a>遠端呈現用戶端

為使用者提供 Azure 遠端轉譯管理會話、轉譯及診斷功能。 [深入了解](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | 無 |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/read | 取得會話屬性 |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/action | 開始會話 |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/delete | 停止會話 |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/read | 連接至會話 |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/diagnostic/read | 連接至遠端轉譯檢查 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with manage session, rendering and diagnostics capabilities for Azure Remote Rendering.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "name": "d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Client",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-contributor"></a>空間錨點帳戶參與者

可讓您管理帳戶中的空間錨點，但不能刪除它們。 [深入瞭解](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | 無 |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/create/action | 建立空間錨點 |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | 探索附近的空間錨點 |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | 取得空間錨點的屬性 |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | 找出空間錨點 |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | 提交診斷資料，以協助改善 Azure 空間錨點服務的品質 |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/write | 更新空間錨點屬性 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, but not delete them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "name": "8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-owner"></a>空間錨點帳戶擁有者

可讓您管理帳戶中的空間錨點，包括刪除它們。 [深入瞭解](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | 無 |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/create/action | 建立空間錨點 |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/delete | 刪除空間錨點 |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | 探索附近的空間錨點 |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | 取得空間錨點的屬性 |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | 找出空間錨點 |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | 提交診斷資料，以協助改善 Azure 空間錨點服務的品質 |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/write | 更新空間錨點屬性 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, including deleting them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/70bbe301-9835-447d-afdd-19eb3167307c",
  "name": "70bbe301-9835-447d-afdd-19eb3167307c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/delete",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-reader"></a>空間錨點帳戶讀者

讓您找出並讀取帳戶中空間錨點的屬性 [瞭解更多](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | 無 |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | 探索附近的空間錨點 |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | 取得空間錨點的屬性 |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | 找出空間錨點 |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | 提交診斷資料，以協助改善 Azure 空間錨點服務的品質 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you locate and read properties of spatial anchors in your account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "name": "5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="integration"></a>整合


### <a name="api-management-service-contributor"></a>API 管理服務參與者

可以管理服務和 Api [深入瞭解](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/* | 建立和管理 API 管理服務 |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service and the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c",
  "name": "312a565d-c81f-4fd8-895a-4e21e48d571c",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-operator-role"></a>API 管理服務操作員角色

可管理服務，但 Api 無法 [深入瞭解](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/*/read | 讀取 API 管理服務執行個體 |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/backup/action | 將 API 管理服務備份到使用者所提供之儲存體帳戶中的指定容器 |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/delete | 刪除 API 管理服務執行個體 |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/managedeployments/action | 變更 SKU/單位、新增/移除 API 管理服務的區域部署 |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/read | 讀取 API 管理服務執行個體的中繼資料 |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/restore/action | 從使用者所提供之儲存體帳戶中的指定容器來還原 API 管理服務 |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/updatecertificate/action | 上傳 API 管理服務的 TLS/SSL 憑證 |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/updatehostname/action | 設定、更新或移除 API 管理服務的自訂網域名稱 |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/write | 建立或更新 API 管理服務執行個體 |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/users/keys/read | 取得與使用者相關聯的金鑰 |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service but not the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "name": "e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/backup/action",
        "Microsoft.ApiManagement/service/delete",
        "Microsoft.ApiManagement/service/managedeployments/action",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.ApiManagement/service/restore/action",
        "Microsoft.ApiManagement/service/updatecertificate/action",
        "Microsoft.ApiManagement/service/updatehostname/action",
        "Microsoft.ApiManagement/service/write",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-reader-role"></a>API 管理服務讀取者角色

服務和 Api 的唯讀存取權 [深入瞭解](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/*/read | 讀取 API 管理服務執行個體 |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/read | 讀取 API 管理服務執行個體的中繼資料 |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/users/keys/read | 取得與使用者相關聯的金鑰 |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to service and APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/71522526-b88f-4d52-b57f-d31fc3546d0d",
  "name": "71522526-b88f-4d52-b57f-d31fc3546d0d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-owner"></a>應用程式組態資料擁有者

允許完整存取應用程式組態資料。 [深入了解](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | 無 |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/read |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/write |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/delete |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows full access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "name": "5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read",
        "Microsoft.AppConfiguration/configurationStores/*/write",
        "Microsoft.AppConfiguration/configurationStores/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-reader"></a>應用程式組態資料讀者

允許讀取應用程式組態資料。 [深入了解](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | 無 |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/read |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/516239f1-63e1-4d78-a4de-a74fb236a071",
  "name": "516239f1-63e1-4d78-a4de-a74fb236a071",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-owner"></a>Azure 服務匯流排資料擁有者

允許完整存取 Azure 服務匯流排資源。 [深入了解](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft。](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft。](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/090c5cfd-751d-490a-894a-3ce6f1109419",
  "name": "090c5cfd-751d-490a-894a-3ce6f1109419",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-receiver"></a>Azure 服務匯流排資料接收者

允許接收 Azure 服務匯流排資源。 [深入了解](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft](resource-provider-operations.md#microsoftservicebus)/queues/read |  |
> | [Microsoft](resource-provider-operations.md#microsoftservicebus)/topics/read |  |
> | [Microsoft](resource-provider-operations.md#microsoftservicebus)/topics/subscriptions/read |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft](resource-provider-operations.md#microsoftservicebus)/receive/action |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for receive access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "name": "4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-sender"></a>Azure 服務匯流排資料傳送者

允許傳送 Azure 服務匯流排資源。 [深入了解](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft](resource-provider-operations.md#microsoftservicebus)/queues/read |  |
> | [Microsoft](resource-provider-operations.md#microsoftservicebus)/topics/read |  |
> | [Microsoft](resource-provider-operations.md#microsoftservicebus)/topics/subscriptions/read |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft](resource-provider-operations.md#microsoftservicebus)/send/action |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for send access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "name": "69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-stack-registration-owner"></a>Azure Stack 註冊擁有者

可讓您管理 Azure Stack 註冊。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/edgeSubscriptions/read | 取得 Azure Stack Edge 訂用帳戶的屬性 |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/products/*/action |  |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/products/read | 取得 Azure Stack Marketplace 產品的屬性 |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/read | 取得 Azure Stack 註冊的屬性 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Stack registrations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "name": "6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "permissions": [
    {
      "actions": [
        "Microsoft.AzureStack/edgeSubscriptions/read",
        "Microsoft.AzureStack/registrations/products/*/action",
        "Microsoft.AzureStack/registrations/products/read",
        "Microsoft.AzureStack/registrations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Stack Registration Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-contributor"></a>EventGrid EventSubscription 參與者

可讓您管理 EventGrid 事件訂用帳戶作業。 [深入了解](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/* |  |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/read | 依主題類型列出全域事件訂用帳戶 |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/read | 列出區域事件訂用帳戶 |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/read | 依主題類型列出區域事件訂用帳戶 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage EventGrid event subscription operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "name": "428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/*",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-reader"></a>EventGrid EventSubscription 讀者

可讓您讀取 EventGrid 事件訂用帳戶。 [深入了解](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/read | 讀取 eventSubscription |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/read | 依主題類型列出全域事件訂用帳戶 |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/read | 列出區域事件訂用帳戶 |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/read | 依主題類型列出區域事件訂用帳戶 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read EventGrid event subscriptions.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2414bbcf-6497-4faf-8c65-045460748405",
  "name": "2414bbcf-6497-4faf-8c65-045460748405",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-contributor"></a>FHIR 資料參與者

角色可讓使用者或主體完整存取 FHIR 資料。 [深入瞭解](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | 無 |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | HealthcareApis/services/fhir/resources/* |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal full access to FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "name": "5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-exporter"></a>FHIR 資料匯出工具

角色可讓使用者或主體讀取和匯出 FHIR 資料 [深入瞭解](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | 無 |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | HealthcareApis/services/fhir/resources/read | 讀取 FHIR 資源 (包括搜尋和建立版本的歷程記錄) 。  |
> | HealthcareApis/services/fhir/resources/export/action |  ($export) 的匯出作業。 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and export FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3db33094-8700-4567-8da5-1501d4e7e843",
  "name": "3db33094-8700-4567-8da5-1501d4e7e843",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read",
        "Microsoft.HealthcareApis/services/fhir/resources/export/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Exporter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-reader"></a>FHIR 資料讀取器

角色可讓使用者或主體讀取 FHIR 資料 [瞭解更多](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | 無 |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | HealthcareApis/services/fhir/resources/read | 讀取 FHIR 資源 (包括搜尋和建立版本的歷程記錄) 。  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "name": "4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-writer"></a>FHIR 資料寫入器外掛程式

角色可讓使用者或主體讀取和寫入 FHIR 資料 [深入瞭解](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | 無 |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | HealthcareApis/services/fhir/resources/* |  |
> | **NotDataActions** |  |
> | HealthcareApis/services/fhir/resources/hardDelete/action | 實刪除 (包括版本歷程記錄) 。 |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and write FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3f88fce4-5892-4214-ae73-ba5294559913",
  "name": "3f88fce4-5892-4214-ae73-ba5294559913",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/hardDelete/action"
      ]
    }
  ],
  "roleName": "FHIR Data Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-contributor"></a>整合服務環境參與者

可讓您管理整合服務環境，但無法存取它們。 [深入了解](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | [Microsoft. 邏輯](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/* |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage integration service environments, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "name": "a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-developer"></a>整合服務環境開發人員

可讓開發人員在整合服務環境中建立和更新工作流程、整合帳戶和 API 連接。 [深入了解](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | [Microsoft. 邏輯](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/read | 讀取整合服務環境。 |
> | [Microsoft. 邏輯](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/join/action | 聯結整合服務環境。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows developers to create and update workflows, integration accounts and API connections in integration service environments.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "name": "c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/read",
        "Microsoft.Logic/integrationServiceEnvironments/join/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="intelligent-systems-account-contributor"></a>Intelligent Systems 帳戶參與者

可讓您管理「智慧型系統」帳戶，但無法存取它們。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | Microsoft.IntelligentSystems/accounts/* | 建立及管理 Intelligent Systems 帳戶 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Intelligent Systems accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/03a6d094-3444-4b3d-88af-7477090a9e5e",
  "name": "03a6d094-3444-4b3d-88af-7477090a9e5e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.IntelligentSystems/accounts/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Intelligent Systems Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-contributor"></a>邏輯應用程式參與者

可讓您管理邏輯應用程式，但無法變更對邏輯應用程式的存取。 [深入了解](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft. Microsoft.classicstorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | 列出儲存體帳戶的存取金鑰。 |
> | [Microsoft. Microsoft.classicstorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/read | 傳回具有給定帳戶的儲存體帳戶。 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | 建立、更新或讀取 Analysis Server 的診斷設定 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/logdefinitions/* | 此為使用者需要透過入口網站存取活動記錄時所需的權限。 列出活動記錄檔中的記錄檔分類。 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/* | 讀取度量定義 (可用資源的度量類型清單)。 |
> | [Microsoft. 邏輯](resource-provider-operations.md#microsoftlogic)/* | 管理 Logic Apps 資源。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 取得訂用帳戶作業結果。 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | 傳回指定儲存體帳戶的存取金鑰。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | 傳回儲存體帳戶清單，或取得指定儲存體帳戶的屬性。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/connectionGateways/* | 建立及管理「連線閘道」。 |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/connections/* | 建立及管理「連線」。 |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/customApis/* | 建立及管理「自訂 API」。 |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/join/action | 加入 App Service 計畫 |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | 取得 App Service 方案的屬性 |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/sites/functions/listSecrets/action | 列出函式秘密。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage logic app, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "name": "87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logdefinitions/*",
        "Microsoft.Insights/metricDefinitions/*",
        "Microsoft.Logic/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*",
        "Microsoft.Web/connections/*",
        "Microsoft.Web/customApis/*",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/functions/listSecrets/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-operator"></a>邏輯應用程式操作員

可讓您讀取、啟用及停用邏輯應用程式，但無法編輯或更新邏輯應用程式。 [深入了解](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [/AlertRules/*](resource-provider-operations.md#microsoftinsights)/read | 讀取 Insights 警示規則 |
> | [/MetricAlerts/*](resource-provider-operations.md#microsoftinsights)/read |  |
> | [/DiagnosticSettings/*](resource-provider-operations.md#microsoftinsights)/read | 取得 Logic Apps 的診斷設定 |
> | [/MetricDefinitions/*](resource-provider-operations.md#microsoftinsights)/read | 取得 Logic Apps 的可用計量。 |
> | [Microsoft. 邏輯](resource-provider-operations.md#microsoftlogic)/*/read | 讀取 Logic Apps 資源。 |
> | [Microsoft. 邏輯](resource-provider-operations.md#microsoftlogic)/workflows/disable/action | 停用工作流程。 |
> | [Microsoft. 邏輯](resource-provider-operations.md#microsoftlogic)/workflows/enable/action | 啟用工作流程。 |
> | [Microsoft. 邏輯](resource-provider-operations.md#microsoftlogic)/workflows/validate/action | 驗證工作流程。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | 取得或列出部署作業。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 取得訂用帳戶作業結果。 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/connectionGateways/*/read | 讀取「連線閘道」。 |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/connections/*/read | 讀取「連線」。 |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/customApis/*/read | 讀取「自訂 API」。 |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | 取得 App Service 方案的屬性 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read, enable and disable logic app.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "name": "515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*/read",
        "Microsoft.Insights/metricAlerts/*/read",
        "Microsoft.Insights/diagnosticSettings/*/read",
        "Microsoft.Insights/metricDefinitions/*/read",
        "Microsoft.Logic/*/read",
        "Microsoft.Logic/workflows/disable/action",
        "Microsoft.Logic/workflows/enable/action",
        "Microsoft.Logic/workflows/validate/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*/read",
        "Microsoft.Web/connections/*/read",
        "Microsoft.Web/customApis/*/read",
        "Microsoft.Web/serverFarms/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="identity"></a>身分識別


### <a name="managed-identity-contributor"></a>受控身分識別參與者

建立、讀取、更新及刪除使用者指派的身分識別 [深入瞭解](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/read | 取得現有已指派使用者的身分識別 |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/write | 建立新的已指派使用者的身分識別，或更新與現有已指派使用者之身分識別相關聯的標記 |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/delete | 刪除現有已指派使用者的身分識別 |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "name": "e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/write",
        "Microsoft.ManagedIdentity/userAssignedIdentities/delete",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-identity-operator"></a>受控身分識別操作員

讀取及指派使用者指派的身分識別 [深入瞭解](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/read |  |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/assign/action |  |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and Assign User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f1a07417-d97a-45cb-824c-7a7467783830",
  "name": "f1a07417-d97a-45cb-824c-7a7467783830",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="security"></a>安全性


### <a name="azure-sentinel-contributor"></a>Azure Sentinel 參與者

Azure Sentinel 參與者 [深入瞭解](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/* |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | 使用新的引擎進行搜尋。 |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | 檢視記錄分析資料 |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/* |  |
> | [Microsoft. microsoft.operationsmanagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | 取得現有的 OMS 解決方案 |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | 針對工作區中的資料執行查詢 |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | 取得工作區下的資料來源。 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/workbooks/* |  |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ab8e14d6-4a74-4a29-9ba8-549422addade",
  "name": "ab8e14d6-4a74-4a29-9ba8-549422addade",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-reader"></a>Azure Sentinel 讀者

Azure Sentinel 讀者 [深入瞭解](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/read |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/action | 檢查使用者授權和使用權 |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/query/action | 查詢威脅情報指標 |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/action | 查詢威脅情報指標 |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | 使用新的引擎進行搜尋。 |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | 檢視記錄分析資料 |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/LinkedServices/read | 取得指定工作區下已連結的服務。 |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/read | 取得已儲存的搜尋查詢 |
> | [Microsoft. microsoft.operationsmanagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | 取得現有的 OMS 解決方案 |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | 針對工作區中的資料執行查詢 |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | 取得工作區下的資料來源。 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | 讀取活頁簿 |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "name": "8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/query/action",
        "Microsoft.SecurityInsights/threatIntelligence/queryIndicators/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/LinkedServices/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-responder"></a>Azure Sentinel 回應者

Azure Sentinel 回應者 [深入瞭解](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/read |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/action | 檢查使用者授權和使用權 |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/cases/* |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/* |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/appendTags/action | 將標記附加至威脅情報指標 |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/query/action | 查詢威脅情報指標 |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/bulkTag/action | 大量標記威脅情報 |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/appendTags/action | 將標記附加至威脅情報指標 |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/replaceTags/action | 取代威脅情報指標的標記 |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/action | 查詢威脅情報指標 |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | 使用新的引擎進行搜尋。 |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | 檢視記錄分析資料 |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | 取得工作區下的資料來源。 |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/read | 取得已儲存的搜尋查詢 |
> | [Microsoft. microsoft.operationsmanagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | 取得現有的 OMS 解決方案 |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | 針對工作區中的資料執行查詢 |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | 取得工作區下的資料來源。 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | 讀取活頁簿 |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Responder",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "name": "3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/cases/*",
        "Microsoft.SecurityInsights/incidents/*",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/appendTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/query/action",
        "Microsoft.SecurityInsights/threatIntelligence/bulkTag/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/appendTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/replaceTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/queryIndicators/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Responder",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-administrator-preview"></a>Key Vault 系統管理員 (預覽) 

在金鑰保存庫和其中的所有物件上執行所有資料平面作業，包括憑證、金鑰和秘密。 無法管理金鑰保存庫資源或管理角色指派。 僅適用于使用「Azure 角色型存取控制」許可權模型的金鑰保存庫。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | 確認 Key Vault 名稱有效，且並非使用中 |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | 檢視虛刪除之 Key Vault 的屬性 |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | 列出可以對 Microsoft.KeyVault 資源提供者執行的作業 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/* |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform all data plane operations on a key vault and all objects in it, including certificates, keys, and secrets. Cannot manage key vault resources or manage role assignments. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "name": "00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Administrator (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-certificates-officer-preview"></a>Key Vault 憑證官員 (preview) 

對金鑰保存庫的憑證執行任何動作，但管理許可權除外。 僅適用于使用「Azure 角色型存取控制」許可權模型的金鑰保存庫。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | 確認 Key Vault 名稱有效，且並非使用中 |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | 檢視虛刪除之 Key Vault 的屬性 |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | 列出可以對 Microsoft.KeyVault 資源提供者執行的作業 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/certificatecas/* |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/certificates/* |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the certificates of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4417e6f-fecd-4de8-b567-7b0420556985",
  "name": "a4417e6f-fecd-4de8-b567-7b0420556985",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/certificatecas/*",
        "Microsoft.KeyVault/vaults/certificates/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Certificates Officer (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-contributor"></a>Key Vault 參與者

管理金鑰保存庫，但不允許您在 Azure RBAC 中指派角色，也不允許您存取秘密、金鑰或憑證。 [深入了解](../key-vault/general/secure-your-key-vault.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [KeyVault](resource-provider-operations.md#microsoftkeyvault)/* |  |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/deletedVaults/purge/action | 清除虛刪除的 Key Vault |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/hsmPools/* |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/managedHsms/* |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage key vaults, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f25e0fa2-a7c8-4377-a976-54943a77a395",
  "name": "f25e0fa2-a7c8-4377-a976-54943a77a395",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.KeyVault/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.KeyVault/locations/deletedVaults/purge/action",
        "Microsoft.KeyVault/hsmPools/*",
        "Microsoft.KeyVault/managedHsms/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-officer-preview"></a>Key Vault 加密長 (預覽) 

在金鑰保存庫的金鑰上執行任何動作，但管理許可權除外。 僅適用于使用「Azure 角色型存取控制」許可權模型的金鑰保存庫。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | 確認 Key Vault 名稱有效，且並非使用中 |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | 檢視虛刪除之 Key Vault 的屬性 |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | 列出可以對 Microsoft.KeyVault 資源提供者執行的作業 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/* |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the keys of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "name": "14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Officer (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-service-encryption-preview"></a>Key Vault 加密服務加密 (預覽) 

讀取金鑰的中繼資料，並執行包裝/解除包裝作業。 僅適用于使用「Azure 角色型存取控制」許可權模型的金鑰保存庫。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | 無 |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/read | 列出指定保存庫中的金鑰，或讀取索引鍵的屬性和公開內容。 若為非對稱金鑰，這項作業會公開公開金鑰，並包含執行公開金鑰演算法的能力，例如加密和驗證簽章。 私用金鑰和對稱金鑰永遠不會公開。 |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/wrap/action | 使用 Key Vault 金鑰包裝對稱金鑰。 請注意，如果 Key Vault 的金鑰是非對稱的，則可以使用讀取存取來執行此作業。 |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/unwrap/action | 使用 Key Vault 金鑰解除包裝對稱金鑰。 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of keys and perform wrap/unwrap operations. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "name": "e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Service Encryption (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-user-preview"></a>Key Vault 加密使用者 (預覽) 

使用金鑰執行密碼編譯作業。 僅適用于使用「Azure 角色型存取控制」許可權模型的金鑰保存庫。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | 無 |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/read | 列出指定保存庫中的金鑰，或讀取索引鍵的屬性和公開內容。 若為非對稱金鑰，這項作業會公開公開金鑰，並包含執行公開金鑰演算法的能力，例如加密和驗證簽章。 私用金鑰和對稱金鑰永遠不會公開。 |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/update/action | 更新與指定索引鍵相關聯的指定屬性。 |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/backup/action | 建立金鑰的備份檔案。 檔案可以用來還原相同訂用帳戶 Key Vault 中的金鑰。 可能適用限制。 |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/encrypt/action | 使用金鑰加密純文字。 請注意，如果金鑰為非對稱金鑰，則可透過具有讀取存取權的主體來執行此作業。 |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/decrypt/action | 使用金鑰來解密加密文字。 |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/wrap/action | 使用 Key Vault 金鑰包裝對稱金鑰。 請注意，如果 Key Vault 的金鑰是非對稱的，則可以使用讀取存取來執行此作業。 |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/unwrap/action | 使用 Key Vault 金鑰解除包裝對稱金鑰。 |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/sign/action | 使用金鑰簽署雜湊。 |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/verify/action | 確認雜湊。 請注意，如果金鑰為非對稱金鑰，則可透過具有讀取存取權的主體來執行此作業。 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform cryptographic operations using keys. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/12338af0-0e69-4776-bea7-57ae8d297424",
  "name": "12338af0-0e69-4776-bea7-57ae8d297424",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/update/action",
        "Microsoft.KeyVault/vaults/keys/backup/action",
        "Microsoft.KeyVault/vaults/keys/encrypt/action",
        "Microsoft.KeyVault/vaults/keys/decrypt/action",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action",
        "Microsoft.KeyVault/vaults/keys/sign/action",
        "Microsoft.KeyVault/vaults/keys/verify/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto User (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-reader-preview"></a>Key Vault 讀者 (預覽) 

讀取金鑰保存庫的中繼資料及其憑證、金鑰和秘密。 無法讀取敏感性值，例如秘密內容或金鑰內容。 僅適用于使用「Azure 角色型存取控制」許可權模型的金鑰保存庫。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | 確認 Key Vault 名稱有效，且並非使用中 |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | 檢視虛刪除之 Key Vault 的屬性 |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | 列出可以對 Microsoft.KeyVault 資源提供者執行的作業 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/readMetadata/action | 列出或查看秘密的屬性，而不是其值。 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of key vaults and its certificates, keys, and secrets. Cannot read sensitive values such as secret contents or key material. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/21090545-7ca7-4776-b22c-e363652d74d2",
  "name": "21090545-7ca7-4776-b22c-e363652d74d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Reader (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-officer-preview"></a>Key Vault 秘密長 (預覽) 

對金鑰保存庫的秘密執行任何動作，但管理許可權除外。 僅適用于使用「Azure 角色型存取控制」許可權模型的金鑰保存庫。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | 確認 Key Vault 名稱有效，且並非使用中 |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | 檢視虛刪除之 Key Vault 的屬性 |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | 列出可以對 Microsoft.KeyVault 資源提供者執行的作業 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/* |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the secrets of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "name": "b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets Officer (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-user-preview"></a>Key Vault 秘密使用者 (預覽) 

讀取秘密內容。 僅適用于使用「Azure 角色型存取控制」許可權模型的金鑰保存庫。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | 無 |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/getSecret/action | 取得秘密的值。 |
> | [Microsoft. KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/readMetadata/action | 列出或查看秘密的屬性，而不是其值。 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read secret contents. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4633458b-17de-408a-b874-0445c86b69e6",
  "name": "4633458b-17de-408a-b874-0445c86b69e6",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/getSecret/action",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets User (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-admin"></a>安全性系統管理員

資訊安全中心的檢視和更新權限。 與「安全性讀者」角色的權限相同，還可以更新安全性原則及關閉警示和建議。 [深入了解](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/policyAssignments/* | 建立及管理原則指派 |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/policyDefinitions/* | 建立及管理原則定義 |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/policyExemptions/* |  |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/policySetDefinitions/* | 建立及管理原則集合 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft. 管理](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | 列出已驗證之使用者的管理群組。 |
> | [Microsoft. operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | 檢視記錄分析資料 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 安全性](resource-provider-operations.md#microsoftsecurity)/* | 建立和管理安全性元件和原則 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Admin Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "name": "fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Authorization/policyAssignments/*",
        "Microsoft.Authorization/policyDefinitions/*",
        "Microsoft.Authorization/policyExemptions/*",
        "Microsoft.Authorization/policySetDefinitions/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-assessment-contributor"></a>安全性評量參與者

可讓您將評量推送至資訊安全中心

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. 安全性](resource-provider-operations.md#microsoftsecurity)/assessments/write | 在您的訂用帳戶上建立或更新安全性評量 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you push assessments to Security Center",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "name": "612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Security/assessments/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Assessment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-manager-legacy"></a>安全性管理員 (舊版)

此為舊版角色。 請改用「安全性系統管理員」。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft.classiccompute](resource-provider-operations.md#microsoftclassiccompute)/*/read | 讀取傳統虛擬機器的設定資訊 |
> | [Microsoft. Microsoft.classiccompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/*/write | 撰寫傳統虛擬機器的設定 |
> | [Microsoft.classicnetwork](resource-provider-operations.md#microsoftclassicnetwork)/*/read | 讀取傳統網路的組態資訊 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 安全性](resource-provider-operations.md#microsoftsecurity)/* | 建立和管理安全性元件和原則 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "This is a legacy role. Please use Security Administrator instead",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "name": "e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/*/read",
        "Microsoft.ClassicCompute/virtualMachines/*/write",
        "Microsoft.ClassicNetwork/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Manager (Legacy)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-reader"></a>安全性讀取者

資訊安全中心的檢視權限。 可以檢視建議、警示、安全性原則和安全性狀態，但無法變更。 [深入了解](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | 讀取傳統計量警示 |
> | [Microsoft. operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | 檢視記錄分析資料 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/*/read |  |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/*/read | 讀取安全性元件和原則 |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/*/read |  |
> | [Microsoft. 管理](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | 列出已驗證之使用者的管理群組。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "name": "39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*/read",
        "Microsoft.Support/*/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="devops"></a>DevOps


### <a name="devtest-labs-user"></a>DevTest Labs 使用者

可讓您連線、啟動、重新啟及關閉您 Azure DevTest Labs 中的虛擬機器。 [深入了解](../devtest-labs/devtest-lab-add-devtest-user.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft. 計算](resource-provider-operations.md#microsoftcompute)/availabilitySets/read | 取得可用性設定組的屬性 |
> | [Microsoft. Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read | 讀取虛擬機器的屬性 (VM 大小、執行階段狀態、VM 擴充功能等) |
> | [Microsoft. 計算](resource-provider-operations.md#microsoftcompute)/virtualMachines/deallocate/action | 關閉虛擬機器的電源，並將計算資源釋出 |
> | [Microsoft. 計算](resource-provider-operations.md#microsoftcompute)/virtualMachines/read | 取得虛擬機器的屬性 |
> | [Microsoft. 計算](resource-provider-operations.md#microsoftcompute)/virtualMachines/restart/action | 重新啟動虛擬機器 |
> | [Microsoft. 計算](resource-provider-operations.md#microsoftcompute)/virtualMachines/start/action | 啟動虛擬機器 |
> | [Microsoft.devtestlab](resource-provider-operations.md#microsoftdevtestlab)/*/read | 讀取實驗室的屬性 |
> | [Microsoft. Microsoft.devtestlab](resource-provider-operations.md#microsoftdevtestlab)/labs/claimAnyVm/action | 在實驗室中宣告隨機的可宣告虛擬機器。 |
> | [Microsoft. Microsoft.devtestlab](resource-provider-operations.md#microsoftdevtestlab)/labs/createEnvironment/action | 在實驗室中建立虛擬機器。 |
> | [Microsoft. Microsoft.devtestlab](resource-provider-operations.md#microsoftdevtestlab)/labs/ensureCurrentUserProfile/action | 請確認目前的使用者在實驗室中具備有效的設定檔。 |
> | [Microsoft. microsoft.devtestlab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/delete | 刪除公式。 |
> | [Microsoft. microsoft.devtestlab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/read | 讀取公式。 |
> | [Microsoft. microsoft.devtestlab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/write | 新增或修改公式。 |
> | [Microsoft. Microsoft.devtestlab](resource-provider-operations.md#microsoftdevtestlab)/labs/policySets/evaluatePolicies/action | 評估實驗室原則。 |
> | [Microsoft. Microsoft.devtestlab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualMachines/claim/action | 取得現有虛擬機器的擁有權 |
> | [Microsoft. Microsoft.devtestlab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualmachines/listApplicableSchedules/action | 列出適用的啟動/停止排程 (若有的話)。 |
> | [Microsoft. Microsoft.devtestlab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualMachines/getRdpFileContents/action | 取得代表虛擬機器 RDP 檔案內容的字串 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/action | 加入負載平衡器後端位址集區。 不可警示。 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/action | 加入負載平衡器輸入 nat 規則。 不可警示。 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/*/read | 讀取網路介面的屬性 (例如網路介面所屬的所有負載平衡器) |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/join/action | 將虛擬機器加入網路介面。 不可警示。 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | 取得網路介面定義。  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/write | 建立網路介面，或更新現有的網路介面。  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/*/read | 讀取公用 IP 位址的屬性 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/action | 加入公用 IP 位址。 不可警示。 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | 取得公用 IP 位址定義。 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | 加入虛擬網路。 不可警示。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | 取得或列出部署作業。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/read | 取得或列出部署。 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | 傳回指定儲存體帳戶的存取金鑰。 |
> | **NotActions** |  |
> | [Microsoft. 計算](resource-provider-operations.md#microsoftcompute)/virtualMachines/vmSizes/read | 列出虛擬機器所能更新成的大小 |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you connect, start, restart, and shutdown your virtual machines in your Azure DevTest Labs.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/76283e04-6283-4c54-8f91-bcf1374a3c64",
  "name": "76283e04-6283-4c54-8f91-bcf1374a3c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/read",
        "Microsoft.Compute/virtualMachines/*/read",
        "Microsoft.Compute/virtualMachines/deallocate/action",
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Compute/virtualMachines/restart/action",
        "Microsoft.Compute/virtualMachines/start/action",
        "Microsoft.DevTestLab/*/read",
        "Microsoft.DevTestLab/labs/claimAnyVm/action",
        "Microsoft.DevTestLab/labs/createEnvironment/action",
        "Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action",
        "Microsoft.DevTestLab/labs/formulas/delete",
        "Microsoft.DevTestLab/labs/formulas/read",
        "Microsoft.DevTestLab/labs/formulas/write",
        "Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action",
        "Microsoft.DevTestLab/labs/virtualMachines/claim/action",
        "Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action",
        "Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/networkInterfaces/*/read",
        "Microsoft.Network/networkInterfaces/join/action",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/publicIPAddresses/*/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listKeys/action"
      ],
      "notActions": [
        "Microsoft.Compute/virtualMachines/vmSizes/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DevTest Labs User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="lab-creator"></a>實驗室建立者

可讓您在 Azure 實驗室帳戶下建立新的實驗室。 [深入了解](../lab-services/add-lab-creator.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/*/read |  |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/createLab/action | 在實驗室帳戶中建立實驗室。 |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getPricingAndAvailability/action | 依大小、地理位置和作業系統的各種組合，取得實驗室帳戶的價格和可用性。 |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getRestrictionsAndUsage/action | 取得此訂用帳戶的核心限制及使用量 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create new labs under your Azure Lab Accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "name": "b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.LabServices/labAccounts/*/read",
        "Microsoft.LabServices/labAccounts/createLab/action",
        "Microsoft.LabServices/labAccounts/getPricingAndAvailability/action",
        "Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Lab Creator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="monitor"></a>監視


### <a name="application-insights-component-contributor"></a>Application Insights 元件參與者

可以管理 Application Insights 元件 [深入瞭解](../azure-monitor/app/resources-roles-access-control.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統警示規則 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* | 建立和管理新的警示規則 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/components/* | 建立和管理 Insights 元件 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | 建立和管理 Insights web 測試 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage Application Insights components",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e",
  "name": "ae349356-3a1b-4a5e-921d-050484c6347e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Component Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="application-insights-snapshot-debugger"></a>Application Insights 快照集偵錯工具

給予使用者權限，以便檢視及下載使用 Application Insights 快照偵錯工具所收集的偵錯快照。 請注意，[擁有者](#owner)或[參與者](#contributor)角色未包含這些權限。 將 Application Insights 快照偵錯者角色指派給使用者時，您必須直接將此角色授與使用者。 此角色若新增至自訂角色，則無法辨識。 [深入了解](../azure-monitor/app/snapshot-debugger.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [/Components/*](resource-provider-operations.md#microsoftinsights)/read |  |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives user permission to use Application Insights Snapshot Debugger features",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "name": "08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Snapshot Debugger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-contributor"></a>監視參與者

可以讀取所有監視資料並編輯監視設定。 請參閱[開始使用 Azure 監視器的角色、權限和安全性](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles)。 [深入了解](../azure-monitor/platform/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | */read | 讀取密碼以外的所有類型的資源。 |
> | [Microsoft. microsoft.alertsmanagement](resource-provider-operations.md#microsoftalertsmanagement)/alerts/* |  |
> | [Microsoft. Microsoft.alertsmanagement](resource-provider-operations.md#microsoftalertsmanagement)/alertsSummary/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/actiongroups/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/activityLogAlerts/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/AlertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/components/* | 建立和管理 Insights 元件 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRules/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRuleAssociations/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/DiagnosticSettings/* | 建立、更新或讀取 Analysis Server 的診斷設定 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/eventtypes/* | 列出訂用帳戶中的活動記錄檔事件 (管理事件)。 此權限適用於以程式設計方式存取和入口網站存取活動記錄檔。 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/LogDefinitions/* | 此為使用者需要透過入口網站存取活動記錄時所需的權限。 列出活動記錄檔中的記錄檔分類。 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/metricalerts/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/* | 讀取度量定義 (可用資源的度量類型清單)。 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/Metrics/* | 讀取資源的度量。 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | 註冊 Microsoft Insights 提供者 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | 建立和管理 Insights web 測試 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/workbooks/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopes/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopeOperationStatuses/* |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/write | 建立新的工作區，或藉由提供來自現有工作區的客戶識別碼來連結至現有工作區。 |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/intelligencepacks/* | 讀取/寫入/刪除記錄分析解決方案套件。 |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/* | 讀取/寫入/刪除記錄分析已儲存的搜尋。 |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | 執行搜尋查詢 |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/action | 擷取工作區的共用金鑰。 這些金鑰可用來將 Microsoft Operational Insights 代理程式連線到工作區。 |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/storageinsightconfigs/* | 讀取/寫入/刪除記錄分析儲存體深入解析設定。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | [Microsoft. WorkloadMonitor](resource-provider-operations.md#microsoftworkloadmonitor)/monitors/* | 取得來賓 VM 健康情況監視的相關資訊。  |
> | [Microsoft. Microsoft.alertsmanagement](resource-provider-operations.md#microsoftalertsmanagement)/smartDetectorAlertRules/* |  |
> | [Microsoft. Microsoft.alertsmanagement](resource-provider-operations.md#microsoftalertsmanagement)/actionRules/* |  |
> | [Microsoft. Microsoft.alertsmanagement](resource-provider-operations.md#microsoftalertsmanagement)/smartGroups/* |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data and update monitoring settings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "name": "749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.AlertsManagement/alerts/*",
        "Microsoft.AlertsManagement/alertsSummary/*",
        "Microsoft.Insights/actiongroups/*",
        "Microsoft.Insights/activityLogAlerts/*",
        "Microsoft.Insights/AlertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/dataCollectionRules/*",
        "Microsoft.Insights/dataCollectionRuleAssociations/*",
        "Microsoft.Insights/DiagnosticSettings/*",
        "Microsoft.Insights/eventtypes/*",
        "Microsoft.Insights/LogDefinitions/*",
        "Microsoft.Insights/metricalerts/*",
        "Microsoft.Insights/MetricDefinitions/*",
        "Microsoft.Insights/Metrics/*",
        "Microsoft.Insights/Register/Action",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Insights/privateLinkScopes/*",
        "Microsoft.Insights/privateLinkScopeOperationStatuses/*",
        "Microsoft.OperationalInsights/workspaces/write",
        "Microsoft.OperationalInsights/workspaces/intelligencepacks/*",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.OperationalInsights/workspaces/sharedKeys/action",
        "Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*",
        "Microsoft.Support/*",
        "Microsoft.WorkloadMonitor/monitors/*",
        "Microsoft.AlertsManagement/smartDetectorAlertRules/*",
        "Microsoft.AlertsManagement/actionRules/*",
        "Microsoft.AlertsManagement/smartGroups/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-metrics-publisher"></a>監視計量發行者

針對 Azure 資源啟用發佈計量 [深入瞭解](../azure-monitor/insights/container-insights-update-metrics.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | 註冊 Microsoft Insights 提供者 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Write | 寫入計量 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Enables publishing metrics against Azure resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c390055eb",
  "name": "3913510d-42f4-4e42-8a64-420c390055eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/Register/Action",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Insights/Metrics/Write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Metrics Publisher",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-reader"></a>監視讀取器

可以讀取所有監視資料 (計量、記錄等等)。 請參閱[開始使用 Azure 監視器的角色、權限和安全性](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles)。 [深入了解](../azure-monitor/platform/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | */read | 讀取密碼以外的所有類型的資源。 |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | 執行搜尋查詢 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "name": "43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-contributor"></a>活頁簿參與者

可以儲存共用活頁簿。 [深入了解](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/workbooks/write | 建立或更新活頁簿 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/workbooks/delete | 刪除活頁簿 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | 讀取活頁簿 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can save shared workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "name": "e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/workbooks/write",
        "Microsoft.Insights/workbooks/delete",
        "Microsoft.Insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-reader"></a>活頁簿讀者

可以讀取活頁簿。 [深入了解](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [microsoft insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | 讀取活頁簿 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "name": "b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "permissions": [
    {
      "actions": [
        "microsoft.insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management--governance"></a>管理和治理


### <a name="automation-job-operator"></a>自動化作業運算子

使用「自動化 Runbook」來建立及管理作業。 [深入了解](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/read | 讀取混合式 Runbook 背景工作角色資源 |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/read | 取得 Azure 自動化作業 |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/resume/action | 繼續 Azure 自動化作業 |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/stop/action | 停止 Azure 自動化作業 |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/streams/read | 取得 Azure 自動化作業串流 |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/suspend/action | 暫止 Azure 自動化作業 |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/write | 建立 Azure 自動化作業 |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/output/read | 取得作業的輸出 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and Manage Jobs using Automation Runbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "name": "4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Job Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-operator"></a>自動化運算子

自動化操作員能夠啟動、停止、暫停及繼續作業， [深入瞭解](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/read | 讀取混合式 Runbook 背景工作角色資源 |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/read | 取得 Azure 自動化作業 |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/resume/action | 繼續 Azure 自動化作業 |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/stop/action | 停止 Azure 自動化作業 |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/streams/read | 取得 Azure 自動化作業串流 |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/suspend/action | 暫止 Azure 自動化作業 |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/write | 建立 Azure 自動化作業 |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/read | 取得 Azure 自動化作業排程 |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/write | 建立 Azure 自動化作業排程 |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/linkedWorkspace/read | 取得連結至自動化帳戶的工作區 |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/read | 取得 Azure 自動化帳戶 |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/read | 取得 Azure 自動化 Runbook |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/schedules/read | 取得 Azure 自動化排程資產 |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/schedules/write | 建立或更新 Azure 自動化排程資產 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/output/read | 取得作業的輸出 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Automation Operators are able to start, stop, suspend, and resume jobs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d3881f73-407a-4167-8283-e981cbba0404",
  "name": "d3881f73-407a-4167-8283-e981cbba0404",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobSchedules/read",
        "Microsoft.Automation/automationAccounts/jobSchedules/write",
        "Microsoft.Automation/automationAccounts/linkedWorkspace/read",
        "Microsoft.Automation/automationAccounts/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Automation/automationAccounts/schedules/read",
        "Microsoft.Automation/automationAccounts/schedules/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-runbook-operator"></a>自動化 Runbook 運算子

讀取 Runbook 屬性 - 以便能夠建立 Runbook 的作業。 [深入了解](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/read | 取得 Azure 自動化 Runbook |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read Runbook properties - to be able to create Jobs of the runbook.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "name": "5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Runbook Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-onboarding"></a>Azure Connected Machine 上線

可以讓 Azure Connected Machine 上線。 [深入了解](../azure-arc/servers/onboard-service-principal.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/read | 讀取任何 Azure Arc 機器 |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/write | 寫入 Azure Arc 機器 |
> | [Microsoft. >microsoft.guestconfiguration](resource-provider-operations.md#microsoftguestconfiguration)/guestConfigurationAssignments/read | 取得來賓組態指派。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "name": "b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.GuestConfiguration/guestConfigurationAssignments/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-resource-administrator"></a>Azure Connected Machine 資源管理員

可以讀取、寫入、刪除 Azure Connected Machine 及使之重新上線。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/read | 讀取任何 Azure Arc 機器 |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/write | 寫入 Azure Arc 機器 |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/delete | 刪除 Azure Arc 機器 |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/reconnect/action |  |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/extensions/write | 安裝或更新 Azure Arc 擴充 |
> | [HybridCompute](resource-provider-operations.md#microsofthybridcompute)/*/read |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read, write, delete and re-onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cd570a14-e51a-42ad-bac8-bafd67325302",
  "name": "cd570a14-e51a-42ad-bac8-bafd67325302",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.HybridCompute/machines/delete",
        "Microsoft.HybridCompute/machines/reconnect/action",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.HybridCompute/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Resource Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="billing-reader"></a>帳單讀取器

允許帳單資料的讀取存取權 [深入瞭解](../cost-management-billing/manage/manage-billing-access.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft 帳單](resource-provider-operations.md#microsoftbilling)/*/read | 讀取帳單資訊 |
> | [Microsoft Commerce](resource-provider-operations.md#microsoftcommerce)/*/read |  |
> | [Microsoft. 耗用量](resource-provider-operations.md#microsoftconsumption)/*/read |  |
> | [Microsoft. 管理](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | 列出已驗證之使用者的管理群組。 |
> | [CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/read |  |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to billing data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "name": "fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Billing/*/read",
        "Microsoft.Commerce/*/read",
        "Microsoft.Consumption/*/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Billing Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-contributor"></a>藍圖參與者

可以管理藍圖定義，但不能加以指派。 [深入了解](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft 藍圖](resource-provider-operations.md#microsoftblueprint)/blueprints/* | 建立和管理藍圖定義或藍圖成品。 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage blueprint definitions, but not assign them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/41077137-e803-4205-871c-5a86e6a753b4",
  "name": "41077137-e803-4205-871c-5a86e6a753b4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprints/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-operator"></a>藍圖操作員

可以指派現有已發佈的藍圖，但無法建立新的藍圖。 請注意，只有在以使用者指派的受控識別來指派時才有效。 [深入了解](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft 藍圖](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/* | 建立和管理藍圖指派。 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can assign existing published blueprints, but cannot create new blueprints. NOTE: this only works if the assignment is done with a user-assigned managed identity.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/437d2ced-4a38-4302-8479-ed2bcb43d090",
  "name": "437d2ced-4a38-4302-8479-ed2bcb43d090",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprintAssignments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-contributor"></a>成本管理參與者

可以查看成本及管理成本設定 (例如預算、匯出) [深入瞭解](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 耗用量](resource-provider-operations.md#microsoftconsumption)/* |  |
> | [CostManagement](resource-provider-operations.md#microsoftcostmanagement)/* |  |
> | [Microsoft 帳單](resource-provider-operations.md#microsoftbilling)/billingPeriods/read |  |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 取得訂用帳戶清單。 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | [Microsoft Advisor](resource-provider-operations.md#microsoftadvisor)/configurations/read | 取得組態 |
> | [Microsoft Advisor](resource-provider-operations.md#microsoftadvisor)/recommendations/read | 讀取建議 |
> | [Microsoft. 管理](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | 列出已驗證之使用者的管理群組。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view costs and manage cost configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/434105ed-43f6-45c7-a02f-909b2ba83430",
  "name": "434105ed-43f6-45c7-a02f-909b2ba83430",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*",
        "Microsoft.CostManagement/*",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-reader"></a>成本管理讀者

可以查看成本資料和設定 (例如預算、匯出) [深入瞭解](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. 耗用量](resource-provider-operations.md#microsoftconsumption)/*/read |  |
> | [CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/read |  |
> | [Microsoft 帳單](resource-provider-operations.md#microsoftbilling)/billingPeriods/read |  |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 取得訂用帳戶清單。 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | [Microsoft Advisor](resource-provider-operations.md#microsoftadvisor)/configurations/read | 取得組態 |
> | [Microsoft Advisor](resource-provider-operations.md#microsoftadvisor)/recommendations/read | 讀取建議 |
> | [Microsoft. 管理](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | 列出已驗證之使用者的管理群組。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view cost data and configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/72fafb9e-0641-4937-9268-a91bfd8191a3",
  "name": "72fafb9e-0641-4937-9268-a91bfd8191a3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hierarchy-settings-administrator"></a>階層設定管理員

允許使用者編輯和刪除階層設定

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. 管理](resource-provider-operations.md#microsoftmanagement)/managementGroups/settings/write | 建立或更新管理群組階層設定。 |
> | [Microsoft. 管理](resource-provider-operations.md#microsoftmanagement)/managementGroups/settings/delete | 刪除管理群組階層設定。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows users to edit and delete Hierarchy Settings",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/350f8d15-c687-4448-8ae1-157740a3936d",
  "name": "350f8d15-c687-4448-8ae1-157740a3936d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/settings/write",
        "Microsoft.Management/managementGroups/settings/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Hierarchy Settings Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="kubernetes-cluster---azure-arc-onboarding"></a>Kubernetes Cluster-Azure Arc 上架

角色定義，用以授權任何使用者/服務建立 connectedClusters 資源， [深入瞭解](../azure-arc/kubernetes/connect-cluster.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/write | 建立或更新部署。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 取得訂用帳戶作業結果。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 取得訂用帳戶清單。 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Write | 寫入 connectedClusters |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/read | 讀取 connectedClusters |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role definition to authorize any user/service to create connectedClusters resource",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "name": "34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Kubernetes/connectedClusters/Write",
        "Microsoft.Kubernetes/connectedClusters/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Kubernetes Cluster - Azure Arc Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-contributor-role"></a>受控應用程式參與者角色

允許建立受控應用程式資源。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | */read | 讀取密碼以外的所有類型的資源。 |
> | [Microsoft 方案](resource-provider-operations.md#microsoftsolutions)/applications/* |  |
> | [Microsoft 方案](resource-provider-operations.md#microsoftsolutions)/register/action | 向 Solutions 註冊。 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/* |  |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for creating managed application resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/641177b8-a67a-45b9-a033-47bc880bb21e",
  "name": "641177b8-a67a-45b9-a033-47bc880bb21e",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/*",
        "Microsoft.Solutions/register/action",
        "Microsoft.Resources/subscriptions/resourceGroups/*",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-operator-role"></a>受控應用程式操作員角色

可讓您讀取受控應用程式資源及對其執行動作

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | */read | 讀取密碼以外的所有類型的資源。 |
> | [Microsoft 方案](resource-provider-operations.md#microsoftsolutions)/applications/read | 擷取應用程式清單。 |
> | [Microsoft 解決方案](resource-provider-operations.md#microsoftsolutions)/*/action |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and perform actions on Managed Application resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "name": "c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/read",
        "Microsoft.Solutions/*/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-applications-reader"></a>受控應用程式讀者

可讓您讀取受控應用程式中的資源及要求 JIT 存取權。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | */read | 讀取密碼以外的所有類型的資源。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft 方案](resource-provider-operations.md#microsoftsolutions)/jitRequests/* |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read resources in a managed app and request JIT access.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "name": "b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Solutions/jitRequests/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Applications Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-services-registration-assignment-delete-role"></a>受控服務註冊指派刪除角色

「受控服務註冊指派刪除角色」可讓管理租用戶使用者刪除指派給其租用戶的註冊指派。 [深入了解](../lighthouse/how-to/remove-delegation.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. >microsoft.managedservices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/read | 取出受控服務註冊指派的清單。 |
> | [Microsoft. >microsoft.managedservices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/delete | 移除受控服務註冊指派。 |
> | [Microsoft. >microsoft.managedservices](resource-provider-operations.md#microsoftmanagedservices)/operationStatuses/read | 讀取資源的作業狀態。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Managed Services Registration Assignment Delete Role allows the managing tenant users to delete the registration assignment assigned to their tenant.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/91c1777a-f3dc-4fae-b103-61d183457e46",
  "name": "91c1777a-f3dc-4fae-b103-61d183457e46",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedServices/registrationAssignments/read",
        "Microsoft.ManagedServices/registrationAssignments/delete",
        "Microsoft.ManagedServices/operationStatuses/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Services Registration assignment Delete Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-contributor"></a>管理群組參與者

管理群組參與者角色 [深入瞭解](../governance/management-groups/overview.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. 管理](resource-provider-operations.md#microsoftmanagement)/managementGroups/delete | 刪除管理群組。 |
> | [Microsoft. 管理](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | 列出已驗證之使用者的管理群組。 |
> | [Microsoft. 管理](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/delete | 從管理群組中取消訂用帳戶的關聯。 |
> | [Microsoft. 管理](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/write | 將現有的訂用帳戶關聯至管理群組。 |
> | [Microsoft. 管理](resource-provider-operations.md#microsoftmanagement)/managementGroups/write | 建立或更新管理群組。 |
> | [Microsoft. 管理](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/read | 列出指定管理群組底下的訂用帳戶。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Contributor Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "name": "5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/delete",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/delete",
        "Microsoft.Management/managementGroups/subscriptions/write",
        "Microsoft.Management/managementGroups/write",
        "Microsoft.Management/managementGroups/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-reader"></a>管理群組讀者

管理群組讀者角色

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft. 管理](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | 列出已驗證之使用者的管理群組。 |
> | [Microsoft. 管理](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/read | 列出指定管理群組底下的訂用帳戶。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ac63b705-f282-497d-ac71-919bf39d939d",
  "name": "ac63b705-f282-497d-ac71-919bf39d939d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="new-relic-apm-account-contributor"></a>New Relic APM 帳戶參與者

可讓您管理 New Relic Application Performance Management 帳戶及應用程式，但無法存取它們。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | NewRelic.APM/accounts/* |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage New Relic Application Performance Management accounts and applications, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d28c62d-5b37-4476-8438-e587778df237",
  "name": "5d28c62d-5b37-4476-8438-e587778df237",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "NewRelic.APM/accounts/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "New Relic APM Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="policy-insights-data-writer-preview"></a>原則深入解析資料寫入者 (預覽)

允許讀取資源原則及寫入資源元件原則事件。 [深入了解](../governance/policy/concepts/policy-for-kubernetes.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/policyassignments/read | 取得關於原則指派的資訊。 |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/policydefinitions/read | 取得關於原則定義的資訊。 |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/policyexemptions/read | 取得原則豁免的相關資訊。 |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/read | 取得原則集合定義的相關資訊。 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. Microsoft.policyinsights](resource-provider-operations.md#microsoftpolicyinsights)/checkDataPolicyCompliance/action | 根據資料原則檢查給定元件的合規性狀態。 |
> | [Microsoft. Microsoft.policyinsights](resource-provider-operations.md#microsoftpolicyinsights)/policyEvents/logDataEvents/action | 記錄資源元件原則事件。 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to resource policies and write access to resource component policy events.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "name": "66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/policyassignments/read",
        "Microsoft.Authorization/policydefinitions/read",
        "Microsoft.Authorization/policyexemptions/read",
        "Microsoft.Authorization/policysetdefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.PolicyInsights/checkDataPolicyCompliance/action",
        "Microsoft.PolicyInsights/policyEvents/logDataEvents/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Policy Insights Data Writer (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="resource-policy-contributor"></a>資源原則參與者

有權建立/修改資源原則、建立支援票證及讀取資源/階層的使用者。 [深入了解](../governance/policy/overview.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | */read | 讀取密碼以外的所有類型的資源。 |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/policyassignments/* | 建立及管理原則指派 |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/policydefinitions/* | 建立及管理原則定義 |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/policyexemptions/* |  |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/* | 建立及管理原則集合 |
> | [Microsoft.policyinsights](resource-provider-operations.md#microsoftpolicyinsights)/* |  |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Users with rights to create/modify resource policy, create support ticket and read resources/hierarchy.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/36243c78-bf99-498c-9df9-86d9f8d28608",
  "name": "36243c78-bf99-498c-9df9-86d9f8d28608",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/policyassignments/*",
        "Microsoft.Authorization/policydefinitions/*",
        "Microsoft.Authorization/policyexemptions/*",
        "Microsoft.Authorization/policysetdefinitions/*",
        "Microsoft.PolicyInsights/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Resource Policy Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-contributor"></a>Site Recovery 參與者

可讓您管理 Site Recovery 服務，但保存庫建立和角色指派 [更深入瞭解](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 取得虛擬網路定義 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp 是服務所使用的內部作業 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/action | AllocateStamp 是服務所使用的內部作業 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/write | 「更新資源憑證」作業會更新資源/保存庫的認證憑證。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | 建立和管理與保存庫相關的擴充資訊 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | 「取得保存庫」作業會取得物件，此物件代表 'vault' 類型的 Azure 資源 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | 建立和管理註冊的身分識別 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/* | 建立或更新複寫警示設定 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | 讀取任何事件 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/* | 建立和管理複寫網狀架構 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/* | 建立和管理複寫作業 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/* | 建立和管理複寫原則 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/* | 建立和管理復原計劃 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/* | 建立和管理復原服務保存庫的儲存體設定 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | 傳回復原服務保存庫的使用量詳細資料。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | 「保存庫權杖」作業可用來取得保存庫層級後端作業的保存庫權杖。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | 讀取復原服務保存庫的警示 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | 傳回儲存體帳戶清單，或取得指定儲存體帳戶的屬性。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationOperationStatus/read | 讀取任何保存庫複寫作業狀態 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Site Recovery service except vault creation and role assignment",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "name": "6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/*",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/*",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/*",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/*",
        "Microsoft.RecoveryServices/Vaults/storageConfig/*",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/vaults/replicationOperationStatus/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-operator"></a>Site Recovery 操作員

可讓您容錯移轉和容錯回復，但無法執行其他 Site Recovery 管理作業 [深入瞭解](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 取得虛擬網路定義 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp 是服務所使用的內部作業 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/action | AllocateStamp 是服務所使用的內部作業 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | 「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | 「取得保存庫」作業會取得物件，此物件代表 'vault' 類型的 Azure 資源 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | 「取得作業結果」作業可用來取得以非同步方式提交之作業的作業狀態和結果 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | 「取得容器」作業可用來取得為資源註冊的容器。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/read | 讀取任何警示設定 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | 讀取任何事件 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/checkConsistency/action | 檢查網狀架構的一致性 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/read | 讀取任何網狀架構 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/reassociateGateway/action | 重新關聯閘道 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/renewcertificate/action | 更新網狀架構的憑證 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/read | 讀取任何網路 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | 讀取任何網路對應 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/read | 讀取任何保護容器 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | 讀取任何可保護的項目 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | 套用復原點 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | 容錯移轉認可 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | 計劃性容錯移轉 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | 讀取任何受保護的項目 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | 讀取任何複寫復原點 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | 修復複寫 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | 重新保護受保護的項目 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | 切換保護容器 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Test Failover |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | 測試容錯移轉清理 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | 容錯移轉 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | 更新行動服務 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | 讀取任何保護容器對應 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | 讀取任何復原服務提供者 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | 重新整理提供者 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/read | 讀取任何存放裝置分類 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | 讀取任何存放裝置分類對應 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationvCenters/read | 讀取任何 vCenter |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/* | 建立和管理複寫作業 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/read | 讀取任何原則 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/failoverCommit/action | 容錯移轉認可復原方案 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/plannedFailover/action | 計劃性容錯移轉復原方案 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/read | 讀取任何復原方案 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/reProtect/action | 重新保護復原方案 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/testFailover/action | 測試容錯移轉復原方案 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/testFailoverCleanup/action | 測試容錯移轉清理復原方案 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/unplannedFailover/action | 容錯移轉復原方案 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | 讀取復原服務保存庫的警示 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/read |  |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | 傳回復原服務保存庫的使用量詳細資料。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | 「保存庫權杖」作業可用來取得保存庫層級後端作業的保存庫權杖。 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft. 儲存體](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | 傳回儲存體帳戶清單，或取得指定儲存體帳戶的屬性。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you failover and failback but not perform other Site Recovery management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/494ae006-db33-4328-bf46-533a6560a3ca",
  "name": "494ae006-db33-4328-bf46-533a6560a3ca",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-reader"></a>Site Recovery 讀取者

可讓您查看 Site Recovery 狀態，但不能執行其他管理作業 [深入瞭解](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp 是服務所使用的內部作業 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | 「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | 取得復原服務保存庫的警示。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | 「取得保存庫」作業會取得物件，此物件代表 'vault' 類型的 Azure 資源 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | 「取得作業結果」作業可用來取得以非同步方式提交之作業的作業狀態和結果 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | 「取得容器」作業可用來取得為資源註冊的容器。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/read | 讀取任何警示設定 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | 讀取任何事件 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/read | 讀取任何網狀架構 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/read | 讀取任何網路 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | 讀取任何網路對應 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/read | 讀取任何保護容器 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | 讀取任何可保護的項目 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | 讀取任何受保護的項目 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | 讀取任何複寫復原點 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | 讀取任何保護容器對應 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | 讀取任何復原服務提供者 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/read | 讀取任何存放裝置分類 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | 讀取任何存放裝置分類對應 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationvCenters/read | 讀取任何 vCenter |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/read | 讀取任何作業 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/read | 讀取任何原則 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/read | 讀取任何復原方案 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/read |  |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | 傳回復原服務保存庫的使用量詳細資料。 |
> | [Microsoft. Az.recoveryservices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | 「保存庫權杖」作業可用來取得保存庫層級後端作業的保存庫權杖。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view Site Recovery status but not perform other management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "name": "dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/read",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="support-request-contributor"></a>支援要求參與者

可讓您建立及管理支援要求 [深入瞭解](../azure-portal/supportability/how-to-create-azure-support-request.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create and manage Support requests",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "name": "cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Support Request Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="tag-contributor"></a>標記參與者

可讓您管理實體上的標記，無需提供對實體本身的存取。 [深入了解](../azure-resource-manager/management/tag-resources.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/resources/read | 取得資源群組的資源。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/subscriptions/resources/read | 取得訂用帳戶的資源。 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/tags/* |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage tags on entities, without providing access to the entities themselves.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "name": "4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
        "Microsoft.Resources/subscriptions/resources/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/tags/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Tag Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="other"></a>其他


### <a name="azure-digital-twins-data-owner"></a>Azure 數位 Twins 資料擁有者

數位 Twins 資料平面的完整存取角色 [學習更多](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | 無 |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/* | 讀取、刪除、建立或更新任何事件路由 |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/* | 讀取、建立、更新或刪除任何數位對應項 |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/commands/* | 在數位對應項上叫用任何命令 |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/relationships/* | 讀取、建立、更新或刪除任何數位對應項關聯性 |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/models/* | 讀取、建立、更新或刪除任何模型 |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/query/* | 查詢任何數位 Twins 圖形 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access role for Digital Twins data-plane",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/bcd981a7-7f74-457b-83e1-cceb9e632ffe",
  "name": "bcd981a7-7f74-457b-83e1-cceb9e632ffe",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DigitalTwins/eventroutes/*",
        "Microsoft.DigitalTwins/digitaltwins/*",
        "Microsoft.DigitalTwins/digitaltwins/commands/*",
        "Microsoft.DigitalTwins/digitaltwins/relationships/*",
        "Microsoft.DigitalTwins/models/*",
        "Microsoft.DigitalTwins/query/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Digital Twins Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-digital-twins-data-reader"></a>Azure 數位 Twins 資料讀者

數位 Twins 資料平面屬性的唯讀角色 [深入瞭解](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | 無 |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/read | 讀取任何數位對應項 |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/relationships/read | 讀取任何數位對應項關聯性 |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/read | 讀取任何事件路由 |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/models/read | 讀取任何模型 |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/query/action | 查詢任何數位 Twins 圖形 |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only role for Digital Twins data-plane properties",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d57506d4-4c8d-48b1-8587-93c323f6a5a3",
  "name": "d57506d4-4c8d-48b1-8587-93c323f6a5a3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DigitalTwins/digitaltwins/read",
        "Microsoft.DigitalTwins/digitaltwins/relationships/read",
        "Microsoft.DigitalTwins/eventroutes/read",
        "Microsoft.DigitalTwins/models/read",
        "Microsoft.DigitalTwins/query/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Digital Twins Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="biztalk-contributor"></a>BizTalk 參與者

可讓您管理 BizTalk 服務，但無法存取它們。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | Microsoft.BizTalkServices/BizTalk/* | 建立和管理 BizTalk 服務 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage BizTalk services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "name": "5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BizTalkServices/BizTalk/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "BizTalk Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-user"></a>桌面虛擬化使用者

允許使用者使用應用程式群組中的應用程式。 [深入了解](../virtual-desktop/delegated-access-virtual-desktop.md)

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | 無 |  |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | DesktopVirtualization/applicationGroups/useApplications/action | 使用 ApplicationGroup |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows user to use the applications in an application group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "name": "1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DesktopVirtualization/applicationGroups/useApplications/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="scheduler-job-collections-contributor"></a>排程器工作集合參與者

可讓您管理「排程器」工作集合，但無法存取它們。

> [!div class="mx-tableFixed"]
> | 動作 | 描述 |
> | --- | --- |
> | [Microsoft 授權](resource-provider-operations.md#microsoftauthorization)/*/read | 讀取角色和角色指派 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 建立和管理傳統計量警示 |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 取得指定範圍中所有資源的可用性狀態 |
> | [Microsoft .resources](resource-provider-operations.md#microsoftresources)/deployments/* | 建立和管理部署 |
> | [Microsoft .Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 取得或列出資源群組。 |
> | [Microsoft.](resource-provider-operations.md#microsoftscheduler)排程器/jobcollections/* | 建立和管理工作集合 |
> | [Microsoft 支援](resource-provider-operations.md#microsoftsupport)/* | 建立和更新支援票證 |
> | **NotActions** |  |
> | 無 |  |
> | **DataActions** |  |
> | 無 |  |
> | **NotDataActions** |  |
> | 無 |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Scheduler job collections, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "name": "188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Scheduler/jobcollections/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Scheduler Job Collections Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="next-steps"></a>後續步驟

- [將資源提供者與服務配對](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Azure 自訂角色](custom-roles.md)
- [Azure 資訊安全中心的權限](../security-center/security-center-permissions.md)
