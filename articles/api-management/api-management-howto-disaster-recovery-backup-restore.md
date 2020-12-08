---
title: 使用 API 管理中的備份和還原來執行嚴重損壞修復
titleSuffix: Azure API Management
description: 了解如何在 Azure API 管理中使用備份和還原來執行災難復原。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/05/2020
ms.author: apimpm
ms.openlocfilehash: 25356e7101293fc27d4107b3a618cfc481aee969
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779578"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>如何在 Azure API 管理中使用服務備份和還原實作災害復原

透過 Azure API 管理來發佈及管理 API，您將能夠利用非 Azure API 管理使用者要另行手動設計、實作及管理的容錯和基礎結構功能。 Azure 平台可緩和絕大部分可能的失敗後果，且成本低廉。

若要從影響裝載您「API 管理」服務的區域可用性問題復原，請隨時準備好在另一個區域中重新建構您的服務。 根據您的復原時間目標，您可能會想要在一或多個區域中保留待命服務。 您也可以嘗試根據復原點目標，將其設定和內容與使用中的服務保持同步。 服務備份和還原功能提供必要的組建區塊，以實行災難修復策略。

備份和還原作業也可用來在作業環境之間複寫 API 管理服務設定，例如開發和預備環境。 請注意，系統也會複製使用者和訂用帳戶之類的執行時間資料，而這可能不是最理想的做法。

本指南說明如何自動執行備份和還原作業，以及如何確保 Azure Resource Manager 成功驗證備份和還原要求。

> [!IMPORTANT]
> 還原作業不會變更目標服務的自訂主機名稱設定。 建議您針對作用中和待命服務使用相同的自訂主機名稱和 TLS 憑證，如此一來，在還原作業完成之後，就可以透過簡單的 DNS CNAME 變更，將流量重新導向待命實例。
>
> 備份作業不會在 Azure 入口網站中的分析分頁所顯示的報表中，捕捉所使用的預先匯總記錄資料。

> [!WARNING]
> 每個備份會在 30 天後到期。 如果您在過了 30 天的到期時間後嘗試還原備份，還原會失敗並傳回 `Cannot restore: backup expired` 訊息。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>驗證 Azure 資源管理員要求

> [!IMPORTANT]
> 備份和還原的 REST API 會使用 Azure 資源管理員，並有不同的驗證機制的 REST API 來管理您的 API 管理實體。 本節中的步驟描述如何驗證 Azure 資源管理員的要求。 如需詳細資訊，請參閱 [驗證 Azure Resource Manager 要求](/rest/api/index)。

所有您使用 Azure Resource Manager 對資源執行的工作，都必須使用下列步驟向 Azure Active Directory 進行驗證：

-   新增應用程式到 Azure Active Directory 租用戶。
-   設定您所新增之應用程式的權限。
-   取得向 Azure 資源管理員驗證要求的權杖。

### <a name="create-an-azure-active-directory-application"></a>建立 Azure Active Directory 應用程式

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 使用含 API 管理服務執行個體的訂用帳戶，瀏覽至 **Azure Active Directory** 的 [應用程式註冊] 索引標籤 (Azure Active Directory > 管理/應用程式註冊)。

    > [!NOTE]
    > 如果您的帳戶看不到 Azure Active Directory 預設目錄，請連絡 Azure 訂用帳戶的系統管理員，以授與您的帳戶必要權限。

3. 按一下 [新增應用程式註冊]。

    [建立] 視窗會出現在右邊。 您可以在這裡輸入 AAD 應用程式的相關資訊。

4. 輸入應用程式的名稱。
5. 針對應用程式類型，選取 [原生]。
6. 輸入 [重新導向 URI] 的預留位置 URL，例如 `http://resources`，因為它是必要的欄位，但稍後不會使用這個值。 按一下核取方塊以儲存應用程式。
7. 按一下 [建立]  。

### <a name="add-an-application"></a>新增應用程式

1. 建立應用程式之後，按一下 [ **API 許可權**]。
2. 按一下 [+ 新增權限]  。
4. 按下 [ **選取 Microsoft api**]。
5. 選擇 **Azure 服務管理**。
6. 按 [選取]。

    ![新增權限](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. 按一下剛新增之應用程式旁邊的 [委派的權限]，選取 [存取 Azure 服務管理 (預覽)] 方塊。
8. 按 [選取]。
9. 按一下 [授與權限]。

### <a name="configuring-your-app"></a>設定應用程式

在呼叫產生備份並將其還原的 API 之前，您必須先取得權杖。 下列範例會使用 [Microsoft.identitymodel](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet 封裝來取得權杖。

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireTokenAsync("https://management.azure.com/", "{application id}", new Uri("{redirect uri}"), new PlatformParameters(PromptBehavior.Auto)).Result;

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

請使用下列指示來取代 `{tenant id}`、`{application id}` 和 `{redirect uri}`：

1. 使用您所建立 Azure Active Directory 應用程式的租用戶識別碼來取代 `{tenant id}`。 您可以按一下 **應用程式註冊**  ->  **端點** 來存取識別碼。

    ![端點][api-management-endpoint]

2. 使用您瀏覽至 [設定] 頁面取得的值來取代 `{application id}`。
3. 以來自您 Azure Active Directory 應用程式 [重新導向 URI] 索引標籤的值取代 `{redirect uri}`。

    指定值之後，程式碼範例應該會傳回類似以下範例的權杖：

    ![權杖][api-management-arm-token]

    > [!NOTE]
    > 權杖可能會在一段時間之後過期。 再次執行程式碼範例即可產生新的權杖。

## <a name="calling-the-backup-and-restore-operations"></a>呼叫備份與還原作業

REST API 是 [API 管理服務 - 備份](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup)和 [API 管理服務 - 還原](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)。

在呼叫接下來小節中所述的「備份與還原」作業之前，請先為您的 REST 呼叫設定授權要求標頭。

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="back-up-an-api-management-service"></a><a name="step1"> </a>備份 API 管理服務

若要備份 API 管理服務，請發出以下 HTTP 要求：

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

其中：

-   `subscriptionId` - 訂用帳戶的識別碼，此訂用帳戶會保留您嘗試備份的「API 管理」服務
-   `resourceGroupName` - 您 Azure API 管理服務的資源群組名稱
-   `serviceName` - 所要備份 API 管理服務的名稱，該名稱是在服務建立時所指定
-   `api-version` -取代為 `2019-12-01`

在要求的本文中指定目標 Azure 儲存體帳戶名稱、存取金鑰、Blob 容器名稱和備份名稱：

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

將 `Content-Type` 要求標頭的值設定為 `application/json`。

備份作業的執行時間較長，因此可能需要數分鐘的時間才能完成。 如果要求成功並已開始備份程序，您就會收到含有 `Location` 標頭的 `202 Accepted` 回應狀態碼。 請向 `Location` 標頭中的 URL 發出 'GET' 要求，以查明作業的狀態。 在備份進行時，您會持續收到「202 已接受」狀態碼。 回應碼 `200 OK` 代表備份作業已成功完成。

### <a name="restore-an-api-management-service"></a><a name="step2"> </a>還原 API 管理服務

若要從先前建立的備份還原 API 管理服務，請發出以下 HTTP 要求：

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

其中：

-   `subscriptionId` - 訂用帳戶的識別碼，此訂用帳戶會保留您要還原備份的目標 API 管理服務
-   `resourceGroupName` - 資源群組的名稱，該資源群組會保留您要還原備份的目標 Azure API 管理服務
-   `serviceName` - 所要還原目標 API 管理服務的名稱，該名稱是在服務建立時所指定
-   `api-version` -取代為 `api-version=2019-12-01`

在要求的本文中，指定備份檔案位置。 也就是新增 Azure 儲存體帳戶名稱、存取金鑰、Blob 容器名稱和備份名稱：

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

將 `Content-Type` 要求標頭的值設定為 `application/json`。

還原作業的執行時間較長，因此可能需要 30 分鐘以上的時間才能完成。 如果要求成功並已開始還原程序，您就會收到含有 `Location` 標頭的 `202 Accepted` 回應狀態碼。 請向 `Location` 標頭中的 URL 發出 'GET' 要求，以查明作業的狀態。 在還原進行時，您會持續收到「202 已接受」狀態碼。 回應碼 `200 OK` 代表還原作業已成功完成。

> [!IMPORTANT]
> 作為還原目的地之服務的 **SKU****必須符合** 所要還原之已備份服務的 SKU。
>
> 在還原作業進行時針對服務組態 (例如 API、原則、開發人員入口網站外觀) 所做的 **變更****可能會遭到覆寫**。

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> 您也可以分別使用 PowerShell [_backup->set-azapimanagement_](/powershell/module/az.apimanagement/backup-azapimanagement) 和 [_restore->set-azapimanagement_](/powershell/module/az.apimanagement/restore-azapimanagement) 命令來執行備份和還原作業。

## <a name="constraints-when-making-backup-or-restore-request"></a>進行備份或還原要求時的條件約束

-   在要求本文中指定的 **容器****必須存在**。
-   當備份正在進行時，請 **避免在服務中進行管理變更** ，例如 SKU 升級或降級、功能變數名稱變更等等。
-   備份還原的 **保證僅限建立後的 30 天內**。
-   對服務設定所做的 **變更** (例如，api、原則和開發人員入口網站外觀) 進行中的備份作業 **可能會從備份中排除，而且將會遺失**。
-   如果 Azure 儲存體帳戶已啟用 [防火牆][azure-storage-ip-firewall] ，則客戶必須 **允許** 在其儲存體帳戶上使用一組 [AZURE API 管理控制平面 IP 位址][control-plane-ip-address] ，以進行備份或還原。 Azure 儲存體帳戶可以位於 API 管理服務所在的任何 Azure 區域中。 例如，如果「API 管理」服務位於美國西部，則 Azure 儲存體帳戶可以是美國西部2，而客戶必須開啟「控制平面 IP 13.64.39.16， (「美國西部的 API 管理控制平面 IP」) 在防火牆中。 這是因為 Azure 儲存體的要求不會從 Azure Api 管理控制平面) 在相同 Azure 區域中的計算 (Snat 轉譯至公用 IP。 跨區域儲存體要求將會 Snat 轉譯至公用 IP 位址。
-   Azure 儲存體帳戶中的 Blob 服務 **不** 應啟用 [跨原始來源資源分享 (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) 。
-   作為還原目的地之服務的 **SKU****必須符合** 所要還原之已備份服務的 SKU。

## <a name="what-is-not-backed-up"></a>未備份的內容
-   備份 **不包含** 用來建立分析報告的 **使用量資料**。 請使用 [Azure API 管理 REST API][azure api management rest api] 來定期擷取分析報告，以利妥善保存。
-   [自訂網域 TLS/SSL](configure-custom-domain.md) 憑證
-   [自訂 CA 憑證](api-management-howto-ca-certificates.md)，其中包含客戶上傳的中繼或根憑證
-   [虛擬網路](api-management-using-with-vnet.md) 整合設定。
-   [受控識別](api-management-howto-use-managed-service-identity.md) 設定。
-   [Azure 監視器診斷](api-management-howto-use-azure-monitor.md) 配置。
-   [通訊協定和密碼](api-management-howto-manage-protocols-ciphers.md) 設定。
-   [開發人員入口網站](api-management-howto-developer-portal.md#is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management) 內容。

執行服務備份的頻率會影響您的復原點目標。 為了盡可能縮小，建議您實作定期備份，並在針對 API 管理服務進行變更後執行隨選備份。

## <a name="next-steps"></a>後續步驟

請參閱下列資源，以取得不同的備份/還原程序逐步解說。

-   [複寫 Azure API 管理帳戶 (英文)](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
-   [使用 Logic Apps 將 API 管理備份與還原自動化](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps) \(英文\)
-   [AZURE API 管理：備份和還原設定](/archive/blogs/stuartleeks/azure-api-management-backing-up-and-restoring-configuration) 
    _Stuart 詳述的方法與官方指導方針並不相符，但這是很有趣的。_

[backup an api management service]: #step1
[restore an api management service]: #step2
[azure api management rest api]: /rest/api/apimanagement/apimanagementrest/api-management-rest
[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png
[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
[control-plane-ip-address]: api-management-using-with-vnet.md#control-plane-ips
[azure-storage-ip-firewall]: ../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range
