---
title: 將應用移動到其他區域
description: 瞭解如何將應用服務資源從一個區域移動到另一個區域。
ms.topic: how-to
ms.date: 02/27/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 7e68f12ce062831ad361c88345188aca61922c4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925705"
---
# <a name="move-an-app-service-app-to-another-region"></a>將應用服務應用移動到其他區域

本文介紹如何將應用服務資源移動到其他 Azure 區域。 出於多種原因，您可以將資源移動到另一個區域。 例如，利用新的 Azure 區域，部署僅在特定區域可用的功能或服務，以滿足內部策略和治理要求，或回應容量規劃要求。

應用服務資源特定于區域，不能跨區域移動。 您必須在目的地區域中創建現有應用服務資源的副本，將內容移動到新應用。 如果源應用使用自訂域，則可以在完成自訂域後[將其遷移到目的地區域中的新應用](manage-custom-dns-migrate-domain.md)。

為了簡化應用的複製，您可以將[單個應用服務應用克隆](app-service-web-app-cloning.md)到另一個區域中的應用服務方案中，但它確實有[限制](app-service-web-app-cloning.md#current-restrictions)，尤其是它不支援 Linux 應用。

## <a name="prerequisites"></a>Prerequisites

- 確保應用服務應用位於要從其移動的 Azure 區域中。
- 確保目的地區域支援應用服務和任何相關服務，您希望移動其資源。
<!-- - Domain bindings, certificates, and managed identities can't replicated using the **Export template** method. You must create them manually. -->

## <a name="prepare"></a>準備

標識當前使用的所有應用服務資源。 例如：

- App Service 應用程式
- [應用服務方案](overview-hosting-plans.md)
- [部署插槽](deploy-staging-slots.md)
- [在 Azure 中購買的自訂域](manage-custom-dns-buy-domain.md)
- [SSL 憑證](configure-ssl-certificate.md)
- [Azure 虛擬網路集成](web-sites-integrate-with-vnet.md)
- [混合連接](app-service-hybrid-connections.md)。
- [託管標識](overview-managed-identity.md)
- [備份設置](manage-backup.md)

某些資源（如導入的證書或混合連接）包含與其他 Azure 服務的集成。 有關如何跨區域移動這些資源的資訊，請參閱相應服務的文檔。

## <a name="move"></a>移動

1. [創建源應用的備份](manage-backup.md)。
1. [在目的地區域中創建新的應用服務方案中](app-service-plan-manage.md#create-an-app-service-plan)的應用。
2. [在目標應用中還原備份](web-sites-restore.md)
2. 如果使用自訂域，[則先先將其綁定到目標應用](manage-custom-dns-migrate-domain.md#bind-the-domain-name-preemptively)，`awverify.`並在[目標應用中啟用域](manage-custom-dns-migrate-domain.md#enable-the-domain-for-your-app)。
3. 將目標應用中的其他所有內容配置為與源應用相同，並驗證您的配置。
4. 當您準備好自訂域指向目標應用時，[重新映射功能變數名稱](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name)。

<!-- 1. Login to the [Azure portal](https://portal.azure.com) > **Resource Groups**.
2. Locate the Resource Group that contains the source App Service resources and click on it.
3. Select > **Settings** > **Export template**.
4. Choose **Deploy** in the **Export template** blade.
5. Click **TEMPLATE** > **Edit template** to open the template in the online editor.
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"identity": {` to find any managed identity definition. The following is an example if you have a user-assigned managed identity.
    ```json
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>": {
                "principalId": "00000000-0000-0000-0000-000000000000",
                "clientId": "00000000-0000-0000-0000-000000000000"
            }
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/sites/hostNameBindings` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/sites/hostNameBindings",
        "apiVersion": "2018-11-01",
        "name": "[concat(parameters('sites_webapp_name'), '/', parameters('sites_webapp_name'), '.azurewebsites.net')]",
        "location": "West Europe",
        "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('sites_webapp_name'))]"
        ],
        "properties": {
            "siteName": "<app-name>",
            "hostNameType": "Verified"
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/certificates` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/certificates",
        "apiVersion": "2018-11-01",
        "name": "[parameters('certificates_test2_cephaslin_com_name')]",
        "location": "West Europe",
        "properties": {
            "hostNames": [
                "[parameters('certificates_test2_cephaslin_com_name')]"
            ],
            "password": "[parameters('certificates_test2_cephaslin_com_password')]"
        }
    },
    ```
7. Delete the entire JSON block. Click **Save** in the online editor.
8. Click **BASICS** > **Create new** to create a new resource group. Type the group name and click **OK**.
9. In **BASICS** > **Location**, select the region you want.   -->

## <a name="clean-up-source-resources"></a>清理源資源

刪除源應用和應用服務方案。 [非免費層中的應用服務方案會收取費用，即使其中沒有應用運行。](app-service-plan-manage.md#delete-an-app-service-plan)

## <a name="next-steps"></a>後續步驟

[使用 PowerShell 複製 Azure App Service App](app-service-web-app-cloning.md)