---
title: 針對 Azure Spring Cloud 應用程式啟用系統指派的受控識別
description: 如何為應用程式啟用系統指派的受控識別。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/13/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 2fbdd46b872c4c70999355d457045f4aac8aa34c
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92742802"
---
# <a name="how-to-enable-system-assigned-managed-identity-for-azure-spring-cloud-application"></a>如何針對 Azure Spring Cloud 應用程式啟用系統指派的受控識別

**本文適用於：** ✔️ Java ✔️ C#

適用于 Azure 資源的受控識別會在 azure 資源（例如您的 Azure 春季雲端應用程式） Azure Active Directory 中提供自動管理的身分識別。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。

本文說明如何使用 0.2.4) 版本所提供的 Azure 入口網站和 CLI (，為 Azure 春季雲端應用程式啟用和停用系統指派的受控識別。

## <a name="prerequisites"></a>必要條件
如果您不熟悉 Azure 資源的受控識別，請參閱 [總覽一節](../active-directory/managed-identities-azure-resources/overview.md)。
您將需要已部署的 Azure 春季 Cloud 實例。 遵循 [快速入門，使用 Azure CLI 進行部署](spring-cloud-quickstart.md)。

## <a name="add-a-system-assigned-identity"></a>新增系統指派的身分識別
使用系統指派的身分識別來建立應用程式，需要在應用程式上設定額外的屬性。

### <a name="using-azure-portal"></a>使用 Azure 入口網站
若要在 [Azure 入口網站](https://portal.azure.com/)中設定受控識別，請先建立應用程式，然後再啟用此功能。

1. 像平常一樣在入口網站中建立應用程式。 在入口網站中瀏覽至該應用程式。
2. 向下滾動至左方流覽窗格中的 [ **設定** ] 群組。
3. 選取 [身分識別]。
4. 在 [系統指派] 索引標籤內，將 [狀態] 切換為 [開啟]。 按一下 [檔案] 。

 ![入口網站中的受控識別](./media/spring-cloud-managed-identity/identity-1.png)

### <a name="using-azure-cli"></a>使用 Azure CLI
您可以在應用程式建立期間，或在現有的應用程式中啟用系統指派的受控識別。

**在應用程式建立期間啟用系統指派的受控識別**

下列範例會使用系統指派的受控識別，建立名為 *app_name* 的應用程式，如 `--assign-identity` 參數要求。

```azurecli
az spring-cloud app create -n app_name -s service_name -g resource_group_name --assign-identity
```

**在現有的應用程式上啟用系統指派的受控識別** 使用 `az spring-cloud app identity assign` 命令，在現有的應用程式上啟用系統指派的身分識別。

```azurecli
az spring-cloud app identity assign -n app_name -s service_name -g resource_group_name
```

## <a name="obtain-tokens-for-azure-resources"></a>取得 Azure 資源的權杖
應用程式可以使用其受控識別來取得權杖，以存取受 Azure Active Directory 保護的其他資源，例如 Azure Key Vault。 這些權杖代表存取資源的應用程式，而不是應用程式的任何特定使用者。

您可能需要設定 [目標資源，以允許從您的應用程式存取](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)。 例如，如果您要求權杖以存取 Key Vault，請確定您已新增存取原則，其中包含應用程式的身分識別。 否則即使呼叫含有權杖，依然會遭到拒絕。 若要深入了解哪些資源支援 Azure Active Directory 權杖，請參閱[支援 Azure AD 驗證的 Azure 服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

Azure 春季 Cloud 會與 Azure 虛擬機器共用相同的端點以取得權杖。 建議您使用 JAVA SDK 或春季 boot starter 來取得權杖。  瞭解如何針對各種程式碼和腳本範例 [使用 VM 權杖](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) ，以及有關處理權杖到期和 HTTP 錯誤等重要主題的指引。

建議：使用 JAVA SDK 或春季 boot starter 來取得權杖。  請參閱 [後續步驟](#next-steps)中的範例。

## <a name="disable-system-assigned-identity-from-an-app"></a>從應用程式停用系統指派的身分識別
移除系統指派的身分識別也會將它從 Azure AD 中刪除。 刪除應用程式資源會自動從 Azure AD 移除系統指派的身分識別。

### <a name="using-azure-portal"></a>使用 Azure 入口網站
若要從不再需要的應用程式中移除系統指派的受控識別：

1. 使用與包含 Azure 春天雲端實例的 Azure 訂用帳戶相關聯的帳戶登入 [Azure 入口網站](https://portal.azure.com/) 。
1. 瀏覽至所需的虛擬機器，然後選取 [身分識別]  。
1. 在 [ **系統指派** / 的 **狀態** ] 底下，選取 [ **關閉** ]，然後按一下 [ **儲存** ]：

 ![受控識別](./media/spring-cloud-managed-identity/remove-identity.png)

### <a name="using-azure-cli"></a>使用 Azure CLI
若要從不再需要的應用程式中移除系統指派的受控識別，請使用下列命令：
```azurecli
az spring-cloud app identity remove -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>後續步驟

* [在春季 boot starter 中使用受控識別存取 Azure Key Vault](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets/README.md#use-msi--managed-identities)
* [深入了解 Azure 資源受控識別](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [如何搭配使用受控識別與 JAVA SDK](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples)
