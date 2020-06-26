---
title: 使用 Azure Migrate 準備 VMware VM 以進行評量/移轉
description: 了解如何使用 Azure Migrate 準備進行 VMware VM 的評量/移轉。
ms.topic: tutorial
ms.date: 06/08/2020
ms.custom: mvc
ms.openlocfilehash: f3bfaf7c2396e0f1091299375aae4bfaa1d7e8ff
ms.sourcegitcommit: 99d016949595c818fdee920754618d22ffa1cd49
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/15/2020
ms.locfileid: "84771184"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>準備 VMware VM 以進行評量並移轉至 Azure

本文將協助您使用 [Azure Migrate](migrate-services-overview.md) 來準備內部部署 VMware VM 的評量並將其移轉至 Azure。

本教學課程是一個系列中的第一篇，說明如何評估及遷移 VMware VM。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 準備 Azure 以使用 Azure Migrate。
> * 使用「Azure Migrate：伺服器評量」工具準備評估 VMware VM。
> * 使用「Azure Migrate：伺服器移轉」工具準備遷移 VMware VM。 

> [!NOTE]
> 這些教學課程示範案例的最簡單部署路徑。 其非常適合作為快速的概念證明。 教學課程在情況允許時都會使用預設選項，且不會顯示所有可能的設定與路徑。 

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="prepare-azure"></a>準備 Azure

此表格摘要說明您需要在 Azure 中完成的工作。 表格後面有每項工作的指示。

**Task** | **詳細資料** | **權限**
--- | --- | ---
**建立 Azure Migrate 專案** | Azure Migrate 專案提供一個集中位置，讓您使用 Azure Migrate 工具、Microsoft 工具和第三方產品來協調和管理評量和移轉。 | 您的 Azure 帳戶需要專案所在資源群組中的「參與者」或「擁有者」權限。
**註冊設備** | Azure Migrate 會使用輕量型 Azure Migrate 設備來探索 VM、使用伺服器評量工具來評估 VM，以及使用無代理程式移轉搭配伺服器移轉工具來遷移 VM。 [深入了解](migrate-appliance-architecture.md#appliance-registration)註冊。 | 若要註冊設備，您的 Azure 帳戶需要 Azure 訂用帳戶的「參與者」或「擁有者」權限。
**建立 Azure AD 應用程式** | 在註冊設備時，Azure Migrate 會建立 Azure Active Directory (Azure AD) 應用程式。 <br/><br/> - 第一個應用程式可供在設備上執行的代理程式與 Azure Migrate 之間進行通訊。 <br/><br/> - 第二個應用程式專門用來存取在使用者的訂用帳戶中建立的 KeyVault，以進行無代理程式 VMware VM 移轉。   | 您的 Azure 帳戶必須建立 Azure AD 應用程式的權限。
**建立 Key Vault** | 若要使用無代理程式移轉來遷移 VMware VM，Azure Migrate 必須建立 Key Vault 來管理訂用帳戶中的複寫帳戶的存取金鑰。 | 若要允許 Azure Migrate 建立 Key Vault，您可以在 Azure Migrate 專案所在的資源群組上設定權限 (擁有者、參與者及使用者存取管理員)。


### <a name="assign-permissions-to-create-project"></a>指派建立專案的權限

1. 在 Azure 入口網站中開啟訂用帳戶，然後選取 [存取控制 (IAM)]。
2. 在 [檢查存取權] 中，尋找相關的帳戶，然後按一下以查看權限。
3. 您應該會具有「參與者」或「擁有者」權限。
    - 如果您剛建立免費的 Azure 帳戶，您就是訂用帳戶的擁有者。
    - 如果您不是訂用帳戶擁有者，請與擁有者合作以指派角色。

### <a name="assign-permissions-to-create-azure-ad-apps"></a>指派建立 Azure AD 應用程式的權限

若要註冊設備，您的 Azure 帳戶需要建立 Azure AD 應用程式的權限。 使用下列其中一種方法來指派權限：

- **方法 1：授與權限給帳戶**：租用戶/全域管理員可將權限授與租用戶中的使用者帳戶，以建立及註冊 Azure AD 應用程式。
- **方法 2：將具有權限的角色指派給使用者帳戶**：租用戶/全域管理員可以將應用程式開發人員角色 (具有必要權限) 指派給使用者帳戶。

> [!NOTE]
> 只有當您註冊新的設備時，才需要這些權限。 完成設備的設定後，您可以移除權限。


#### <a name="method-1-grant-permissions-to-the-account"></a>方法 1：授與權限給帳戶

授與權限給帳戶，如下所示：

1. 確定您是租用戶或全域管理員。然後在 Azure AD 中，瀏覽至 [Azure Active Directory] > [使用者] > [使用者設定]。
2. 將 [應用程式註冊] 設定為 [是]。 這是一個不敏感的預設設定。 [深入了解](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)。

    ![Azure AD 權限](./media/tutorial-prepare-vmware/aad.png)



#### <a name="method-2-assign-application-developer-role"></a>方法 2：指派應用程式開發人員角色

或者，租用戶/全域管理員可為帳戶指派應用程式開發人員角色。 [深入了解](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)如何指派角色。

### <a name="assign-permissions-to-create-a-key-vault"></a>指派建立金鑰保存庫的權限

若要讓 Azure Migrate 建立 Key Vault，請依照下列方式指派權限：

1. 在 Azure 入口網站的資源群組中，選取 [存取控制 (IAM)]。
2. 在 [檢查存取權] 中，尋找相關的帳戶，然後按一下以查看權限。

    - 若要執行伺服器評量，「參與者」權限就已足夠。
    - 若要執行無代理程式的伺服器移轉，您應具有「擁有者」(或「參與者」及「使用者存取系統管理員」) 權限。

3. 如果您沒有必要權限，請向資源群組擁有者提出要求。

## <a name="prepare-for-assessment"></a>準備進行評估

若要準備 VMware VM 評量，您必須：

1. **確認 VMware 設定**。 確定您想要遷移的 vCenter Server 和 VM 符合需求。
2. **設定評估的權限**。 Azure Migrate 會使用 vCenter 帳戶來存取 vCenter Server，以探索和評估 VM。
3. **確認設備需求**。 在下一個教學課程中部署 Azure Migrate 設備之前，請確認其部署需求。

### <a name="verify-vmware-settings"></a>確認 VMware 設定

1. [檢查 VMware 需求](migrate-support-matrix-vmware.md#vmware-requirements)以供評估。
2. [確認](migrate-support-matrix-vmware.md#port-access-requirements) vCenter 伺服器上已開啟您需要的連接埠。
3. 在 vCenter Server 上，確認您的帳戶有權使用 OVA 檔案建立 VM。 當您使用 OVA 檔案，將 Azure Migrate 設備部署為 VMware VM 時，這是必要條件。
4. 在您將 VM 遷移至 Azure 之前，VM 需要進行一些變更。

    - 在某些作業系統上，Azure Migrate 會自動進行這些變更。 
    - 請務必先進行這些變更再開始移轉。 如果您先遷移 VM 再進行變更，VM 可能無法在 Azure 中啟動。
    - 請參閱您所需進行的 [Windows](prepare-for-migration.md#windows-machines) 和 [Linux](prepare-for-migration.md#linux-machines) 變更。


### <a name="set-up-permissions-for-assessment"></a>設定評估的權限

Azure Migrate 需要存取 vCenter Server，Azure Migrate 設備才能探索用於評量和無代理程式移轉的 VM。 設定帳戶，如下所示：

1. 在 vSphere Web 用戶端中，開啟 [系統管理] > [存取] > [SSO 使用者和群組]。
2. 在 [使用者] 中，按一下 [新增使用者] 圖示。
3. 輸入新的使用者詳細資料。
4. 根據表格值來選取權限。

    **功能** | **帳戶權限**
    --- | ---
    [評估 VM](tutorial-assess-vmware.md) | 對於評量，帳戶需要唯讀存取權。
    [探索 VM 應用程式、角色和功能](how-to-discover-applications.md) | 如果您想要使用應用程式探索，用於評估的唯讀帳戶需要針對 [虛擬機器] > [來賓作業] 啟用的權限。
    [分析 VM 的相依性 (無代理程式)](how-to-create-group-machine-dependencies-agentless.md) | 如果您想要分析相依性，用於評量的唯讀帳戶需要針對 [虛擬機器] > [來賓作業] 啟用的權限。
    
> [!NOTE]
> 如果您想要將評估的 VM 探索限制為特定的範圍，請檢閱[這篇文章](set-discovery-scope.md#assign-a-role-for-assessment)。

### <a name="verify-appliance-settings-for-assessment"></a>確認用於評量的設備設定

在[下一個教學課程中](tutorial-assess-vmware.md)，您會設定 Azure Migrate 設備，並開始進行評量。 執行此動作之前，請先檢閱設備需求，如下所示： 

1. [檢閱](migrate-appliance.md#appliance---vmware)部署 Azure Migrate 設備的需求。
2. 檢閱[公用](migrate-appliance.md#public-cloud-urls)和[政府](migrate-appliance.md#government-cloud-urls)雲端中設備需要存取的 Azure URL。
3. [請記下](migrate-support-matrix-vmware.md#port-access-requirements)設備所使用的連接埠。
4. [檢閱設備在探索和評量期間收集的資料](migrate-appliance.md#collected-data---vmware)。

## <a name="prepare-for-agentless-vmware-migration"></a>準備無代理程式的 VMware 移轉

您可使用[無代理程式或代理程式型移轉](server-migrate-overview.md)來遷移 VMware VM。 這一節摘要說明無代理程式移轉的需求。

1. [決定](server-migrate-overview.md#compare-migration-methods)您是否要使用無代理程式移轉。
2. 針對您要遷移的機器，[檢閱](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless) Hypervisor 需求。
3. 針對您要使用無代理程式移轉來遷移 VMWare VM，[檢閱](migrate-support-matrix-vmware-migration.md#vm-requirements-agentless)其需求。
4. [檢閱](migrate-support-matrix-vmware-migration.md#appliance-requirements-agentless) Azure Migrate 設備進行無代理程式移轉的需求。
5. 請注意，[公用](migrate-appliance.md#public-cloud-urls)和[政府](migrate-appliance.md#government-cloud-urls)雲端需要 URL 存取權。
6. 檢閱[連接埠存取](migrate-support-matrix-vmware-migration.md#port-requirements-agentless)需求。
7. 設定無代理程式移轉的權限，如下一個程序所述。


### <a name="assign-permissions-to-an-account"></a>將權限指派給帳戶

Azure Migrate 設備會連線到 vCenter Server，以透過無代理程式移轉來探索和遷移 VM。 您可將設備所需的權限指派給使用者帳戶，或建立具有權限的角色，並將該角色指派給使用者帳戶。

1. 在 vSphere Web 用戶端中，開啟 [系統管理] > [存取] > [SSO 使用者和群組]。
2. 在 [使用者] 中，按一下 [新增使用者] 圖示。
3. 輸入新的使用者詳細資料。
4. 指派 [這些權限](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)

#### <a name="create-a-role-and-assign-to-an-account"></a>建立角色並指派給帳戶

或者，您可建立免費帳戶。 然後建立角色，並將其指派給帳戶，如下所示：

1. 以 vCenter Server 系統管理員身分登入 vSphere Web 用戶端
2. 選取 vCenter Server 執行個體 > [建立角色]。
3. 指定角色名稱，例如 <em>Azure_Migrate</em>，並將[必要權限](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)指派給角色。

    ![vCenter Server 帳戶權限](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

4. 現在建立帳戶，並將角色指派給帳戶。

> [!NOTE]
> 如果您想要將無代理程式移轉的 VM 探索限制為特定的範圍，請檢閱[這篇文章](set-discovery-scope.md#assign-a-role-for-agentless-migration)。



## <a name="prepare-for-agent-based-vmware-migration"></a>準備代理程式型 VMware 移轉

您可使用[無代理程式或代理程式型移轉](server-migrate-overview.md)來遷移 VMware VM。 這一節摘要說明代理程式型移轉的需求。

1. [決定](server-migrate-overview.md#compare-migration-methods)您是否要使用代理程式型移轉。
1. 針對您要遷移的機器，[檢閱](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) Hypervisor 需求。
2. [檢閱](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) VMware VM 需求，包括您要遷移的每個 VM 上的行動服務安裝。
3. 代理程式型移轉會使用複寫設備：
    - [檢閱](migrate-replication-appliance.md#appliance-requirements)複寫設備的部署需求。
    - [檢閱在設備上安裝 MySQL 的選項](migrate-replication-appliance.md#mysql-installation)。
    - 請注意，[公用](migrate-replication-appliance.md#url-access)和[政府](migrate-replication-appliance.md#azure-government-url-access)雲端需要 URL 存取權。
    - 檢閱複寫設備的[連接埠存取](migrate-replication-appliance.md#port-access)需求。
4. 在您將 VM 遷移至 Azure 之前，VM 需要進行一些變更。 請參閱您所需進行的 [Windows](prepare-for-migration.md#windows-machines) 和 [Linux](prepare-for-migration.md#linux-machines) 變更。



## <a name="next-steps"></a>後續步驟

在本教學課程中，您：

> [!div class="checklist"]
> * 設定 Azure 權限。
> * 準備VMware 以進行評估及移轉。


請繼續進行第二個教學課程，以設定 Azure Migrate 專案，並評估要遷移至 Azure 的 VMware VM。

> [!div class="nextstepaction"]
> [評估 VMware VM](./tutorial-assess-vmware.md)
