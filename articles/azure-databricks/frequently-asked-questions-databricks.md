---
title: Azure Databricks：常見問題與說明
description: 取得有關 Azure Databricks 的常見問題解答和疑難排解資訊。
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 8d7aab43641c6c594ff60368ccb3810e0c060dd7
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671569"
---
# <a name="frequently-asked-questions-about-azure-databricks"></a>關於 Azure Databricks 的常見問題

本文列出關於 Azure Databricks 的最常見問題。 其中也會列出您在使用 Databricks 時可能遇到的一些常見問題。 如需詳細資訊，請參閱[何謂 Azure Databricks](what-is-azure-databricks.md)。 

## <a name="can-i-use-azure-key-vault-to-store-keyssecrets-to-be-used-in-azure-databricks"></a>可以使用 Azure Key Vault 來儲存金鑰/祕密以使用於 Azure Databricks 嗎？
是的。 您可以使用 Azure Key Vault 來儲存金鑰/祕密，以便搭配 Azure Databricks 使用。 如需詳細資訊，請參閱 [Azure Key Vault 支援的範圍](/azure/databricks/security/secrets/secret-scopes)。


## <a name="can-i-use-azure-virtual-networks-with-databricks"></a>我可以將 Azure 虛擬網路與 Databricks 搭配使用嗎？
是的。 您可以將 Azure 虛擬網路 (VNET) 與 Azure Databricks 搭配使用。 如需詳細資訊，請參閱[在 Azure 虛擬網路中部署 Azure Databricks](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)。

## <a name="how-do-i-access-azure-data-lake-storage-from-a-notebook"></a>如何? 從筆記本存取 Azure Data Lake Storage 嗎？ 

請遵循下列步驟：
1. 在 Azure Active Directory (Azure AD) 中，佈建服務主體並記錄其金鑰。
1. 在 Data Lake Storage 中，將必要的許可權指派給服務主體。
1. 若要存取 Data Lake Storage 中的檔案，請在筆記本中使用服務主體認證。

如需詳細資訊，請參閱搭配[Azure Databricks 使用 Azure Data Lake Storage](/azure/databricks/data/data-sources/azure/azure-datalake)。

## <a name="fix-common-problems"></a>修正常見問題

以下是您使用 Databricks 時可能遇到的一些問題。

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>問題：此訂用帳戶未註冊為使用命名空間 ' Databricks '

#### <a name="error-message"></a>錯誤訊息

「此訂用帳戶未註冊為使用命名空間 ' Databricks '。 請參閱 https://aka.ms/rps-not-found 以了解如何註冊訂用帳戶。 (錯誤碼：MissingSubscriptionRegistration)」

#### <a name="solution"></a>解決方案

1. 移至 [Azure 入口網站](https://portal.azure.com)。
1. 依序選取 [訂用帳戶]、您所使用的訂用帳戶及 [資源提供者]。 
1. 在資源提供者清單中，針對 **Microsoft.Databricks** 選取 [註冊]。 您在訂用帳戶中必須具有參與者或擁有者角色，才能註冊資源提供者。


### <a name="issue-your-account-email-does-not-have-the-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>問題：您的帳戶 {email} 在 Azure 入口網站的 Databricks 工作區資源中沒有擁有者或參與者角色

#### <a name="error-message"></a>錯誤訊息

「您的帳戶 {email} 在 Azure 入口網站的 Databricks 工作區資源中沒有「擁有者」或「參與者」角色。 如果您是租用戶中的來賓使用者，也會發生此錯誤。 請向系統管理員申請授予存取權，或將您直接新增為 Databricks 工作區中的使用者。」 

#### <a name="solution"></a>解決方案

以下是此問題的幾種解決方案：

* 若要將租用戶初始化，您必須以租用戶的一般使用者身分登入，而不是以來賓使用者身分登入。 您在 Databricks 工作區資源上也必須具有參與者角色。 您可以在 Azure 入口網站的 Databricks 工作區中，從 [存取控制 (IAM)] 索引標籤來授與使用者存取權。

* 如果您的電子郵件網域名稱指派給 Azure AD 中的多個目錄，也可能發生此錯誤。 若要避開此問題，請使用 Databricks 工作區，在包含訂用帳戶的目錄中建立新的使用者。

    a. 在 Azure 入口網站中，移至 Azure AD。 選取 [使用者與群組] > [新增使用者]。

    b. 以 `@<tenant_name>.onmicrosoft.com` 電子郵件 (而非 `@<your_domain>` 電子郵件) 來新增使用者。 您可以在 Azure 入口網站中 Azure AD 下方的[自訂網域] 中找到此選項。
    
    c. 在 Databricks 工作區資源上，為這位新的使用者授與**參與者**角色。
    
    d. 以新的使用者身分登入 Azure 入口網站，並尋找 Databricks 工作區。
    
    e. 以此使用者的身分啟動 Databricks 工作區。


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>問題：您的帳戶 {email} 未在 Databricks 中登錄 

#### <a name="solution"></a>解決方案

如果您並未建立工作區，但已新增為使用者，請連絡建立該工作區的人員。 請該人員使用 Azure Databricks 管理主控台來將您新增。 如需指示，請參閱[新增和管理使用者](/azure/databricks/administration-guide/users-groups/users)。 如果您建立了工作區，但仍然收到此錯誤，請再次嘗試從 Azure 入口網站選取 [初始化工作區]。

### <a name="issue-cloud-provider-launch-failure-while-setting-up-the-cluster-publicipcountlimitreached"></a>問題：設定叢集時雲端提供者啟動失敗 (PublicIPCountLimitReached)

#### <a name="error-message"></a>錯誤訊息

「雲端提供者啟動失敗：設定叢集時發生雲端提供者錯誤。 如需詳細資訊，請參閱 Databricks 指南。 Azure 錯誤碼：PublicIPCountLimitReached。 Azure 錯誤訊息：無法為此區域中的這個訂用帳戶建立超過10個公用 IP 位址。」

#### <a name="background"></a>背景

Databricks 叢集會在每個節點（包括驅動程式節點）上使用一個公用 IP 位址。 Azure 訂用帳戶在每個區域都有[公用 IP 位址限制](/azure/azure-resource-manager/management/azure-subscription-service-limits#publicip-address)。 因此，如果叢集建立和相應增加作業會導致該區域中配置給該訂用帳戶的公用 IP 位址數目超過限制，則可能會失敗。 此限制也包含配置給非 Databricks 使用的公用 IP 位址，例如自訂使用者定義的 Vm。

一般而言，叢集只會在作用中時使用公用 IP 位址。 不過，即使在其他叢集終止之後，`PublicIPCountLimitReached` 錯誤仍可能會在短時間內繼續發生。 這是因為在終止叢集時，Databricks 會暫時快取 Azure 資源。 資源快取是根據設計，因為它可大幅降低叢集啟動的延遲，以及在許多常見案例中自動調整。

#### <a name="solution"></a>解決方案

如果您的訂用帳戶已達到給定區域的公用 IP 位址限制，則您應該執行下列其中一項或其他動作。

- 在不同的 Databricks 工作區中建立新的叢集。 另一個工作區必須位於尚未到達訂用帳戶的公用 IP 位址限制的區域中。
- [要求增加您的公用 IP 位址限制](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)。 選擇 [配額] 作為 [問題類型]，並選擇 [網路：ARM] 作為 [配額類型]。 在 [詳細資料] 中，申請提高公用 IP 位址配額。 例如，如果您目前的限制是 60，而您想要建立具有 100 個節點的叢集，請申請將限制提高到 160。

### <a name="issue-a-second-type-of-cloud-provider-launch-failure-while-setting-up-the-cluster-missingsubscriptionregistration"></a>問題：設定叢集時發生第二種類型的雲端提供者啟動失敗 (MissingSubscriptionRegistration)

#### <a name="error-message"></a>錯誤訊息

「雲端提供者啟動失敗：設定叢集時發生雲端提供者錯誤。 如需詳細資訊，請參閱 Databricks 指南。
Azure 錯誤碼：MissingSubscriptionRegistration Azure 錯誤訊息：訂用帳戶未註冊為使用命名空間 'Microsoft.Compute'。 請參閱 https://aka.ms/rps-not-found 以了解如何註冊訂用帳戶。」

#### <a name="solution"></a>解決方案

1. 移至 [Azure 入口網站](https://portal.azure.com)。
1. 依序選取 [訂用帳戶]、您所使用的訂用帳戶及 [資源提供者]。 
1. 在資源提供者清單中，針對 **Microsoft.Compute** 選取 [註冊]。 您在訂用帳戶中必須具有參與者或擁有者角色，才能註冊資源提供者。

如需更多詳細指示，請參閱[資源提供者和類型](../azure-resource-manager/management/resource-providers-and-types.md)。

### <a name="issue-azure-databricks-needs-permissions-to-access-resources-in-your-organization-that-only-an-admin-can-grant"></a>問題：Azure Databricks 需要只有管理員才能授與的權限來存取您組織中的資源。

#### <a name="background"></a>背景

Azure Databricks 會與 Azure Active Directory 整合。 您可藉由指定 Azure AD 的使用者，在 Azure Databricks 內設定權限 (例如，在筆記型電腦或叢集上)。 若要讓 Azure Databricks 能夠列出來自您 Azure AD 的使用者名稱，它需要讀取該資訊的權限以及給予同意。 如果尚未取得同意，您就會看到錯誤。

#### <a name="solution"></a>解決方案

以全域管理員身分登入 Azure 入口網站。 針對 Azure Active Directory，移至 [使用者設定] 索引標籤，並確定已將 [使用者可同意應用程式代表自己存取公司資料] 設為 [是]。

## <a name="next-steps"></a>後續步驟

- [快速入門：開始使用 Azure Databricks](quickstart-create-databricks-workspace-portal.md)
- [何謂 Azure Databricks？](what-is-azure-databricks.md)
