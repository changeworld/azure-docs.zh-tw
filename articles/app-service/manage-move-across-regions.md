---
title: 將應用程式移至另一個區域
description: 瞭解如何將 App Service 資源從一個區域移至另一個區域。
ms.topic: how-to
ms.date: 02/27/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 7e68f12ce062831ad361c88345188aca61922c4c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77925705"
---
# <a name="move-an-app-service-app-to-another-region"></a>將 App Service 應用程式移至另一個區域

本文說明如何將 App Service 資源移至不同的 Azure 區域。 您可能會因為許多原因而將您的資源移至另一個區域。 例如，若要利用新的 Azure 區域，只部署特定區域中可用的功能或服務，以符合內部原則和治理需求，或是為了回應容量規劃需求。

App Service 資源是區域特定的，無法跨區域移動。 您必須在目的地區域中建立現有 App Service 資源的複本，將您的內容移到新的應用程式。 如果您的來源應用程式使用自訂網域，當您完成時，可以將[它遷移至目的地區域中的新應用程式](manage-custom-dns-migrate-domain.md)。

為了讓複製應用程式更容易，您可以將[個別 App Service 應用程式](app-service-web-app-cloning.md)複製到另一個區域中的 App Service 方案，但它有[一些限制](app-service-web-app-cloning.md#current-restrictions)，特別是它不支援 Linux 應用程式。

## <a name="prerequisites"></a>先決條件

- 請確定 App Service 應用程式位於您要移動的 Azure 區域中。
- 請確定目的地區域支援 App Service 和任何相關的服務，而您想要移動其資源。
<!-- - Domain bindings, certificates, and managed identities can't replicated using the **Export template** method. You must create them manually. -->

## <a name="prepare"></a>準備

識別您目前正在使用的所有 App Service 資源。 例如：

- App Service 應用程式
- [App Service 方案](overview-hosting-plans.md)
- [部署位置](deploy-staging-slots.md)
- [在 Azure 中購買的自訂網域](manage-custom-dns-buy-domain.md)
- [SSL 憑證](configure-ssl-certificate.md)
- [Azure 虛擬網路整合](web-sites-integrate-with-vnet.md)
- [混合式連接](app-service-hybrid-connections.md)。
- [受控身分識別](overview-managed-identity.md)
- [備份設定](manage-backup.md)

某些資源（例如匯入的憑證或混合式連線）包含與其他 Azure 服務的整合。 如需如何跨區域移動這些資源的詳細資訊，請參閱個別服務的檔。

## <a name="move"></a>移動

1. [建立來源應用程式的備份](manage-backup.md)。
1. 在[目的地區域中，于新的 App Service 方案中建立應用程式](app-service-plan-manage.md#create-an-app-service-plan)。
2. [還原目標應用程式中的備份](web-sites-restore.md)
2. 如果您使用自訂網域，請使用將[它事先至目標應用程式](manage-custom-dns-migrate-domain.md#bind-the-domain-name-preemptively)， `awverify.`並[在目標應用程式中啟用網域](manage-custom-dns-migrate-domain.md#enable-the-domain-for-your-app)。
3. 設定目標應用程式中的其他專案，使其與來源應用程式相同，並驗證您的設定。
4. 當您準備好要讓自訂網域指向目標應用程式時，會重新對應[功能變數名稱](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name)。

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

## <a name="clean-up-source-resources"></a>清除來源資源

刪除來源應用程式並 App Service 方案。 [即使沒有任何應用程式正在執行，在非免費層中的 App Service 方案也會產生費用。](app-service-plan-manage.md#delete-an-app-service-plan)

## <a name="next-steps"></a>後續步驟

[使用 PowerShell 複製 Azure App Service App](app-service-web-app-cloning.md)