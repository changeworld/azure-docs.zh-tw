---
title: 使用 Azure Migrate 準備實體伺服器以進行評量/移轉
description: 了解如何使用 Azure Migrate 準備進行實體伺服器的評量/移轉。
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: aba1608c9219e7e8dffe66344b04fa3f085b06f3
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88927369"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>準備評估實體伺服器並將其移轉至 Azure

本文將說明如何使用 [Azure Migrate](migrate-services-overview.md) 來準備評估內部部署實體伺服器。

[Azure Migrate](./migrate-services-overview.md) 會提供工具中樞，協助您探索和評估應用程式、基礎結構和工作負載，並且將這些項目遷移至 Microsoft Azure。 此中樞包含 Azure Migrate 工具和第三方獨立軟體廠商 (ISV) 供應項目。 

本教學課程是一個系列中的第一篇，說明如何使用 Azure Migrate 評估實體伺服器。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 準備 Azure。 設定 Azure 帳戶和資源的權限，以搭配使用 Azure Migrate。
> * 準備要進行伺服器評量的內部部署實體伺服器。


> [!NOTE]
> 教學課程將會針對案例示範最簡單的部署路徑，讓您可以快速設定概念證明。 教學課程在情況允許時都會使用預設選項，且不會顯示所有可能的設定與路徑。 如需詳細指示，請參閱實體伺服器評量的操作說明。


如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="prepare-azure-for-server-assessment"></a>準備 Azure 進行伺服器評定

捨訂 Azure 以使用 Azure Migrate。 

**Task** | **詳細資料** 
--- | --- 
**建立 Azure Migrate 專案** | 您的 Azure 帳戶需要參與者或擁有者權限，才能建立專案。 
**註冊資源提供者 (僅限評定)** | Azure Migrate 會使用輕量型 Azure Migrate 設備搭配「Azure Migrate：伺服器評定」來探索和評定電腦。<br/><br/> 在設備註冊期間，資源提供者會使用設備中選擇的訂用帳戶進行註冊。 [深入了解](migrate-appliance-architecture.md#appliance-registration)。<br/><br/> 若要註冊資源提供者，您必須具有訂用帳戶的「參與者」或「擁有者」角色。
**建立 Azure AD 應用程式 (僅限評定)** | 註冊設備時，Azure Migrate 會建立一個 Azure Active Directory (Azure AD) 應用程式，用於溝通設備上執行的代理程式，使其各自的服務能在 Azure 上執行。 [深入了解](migrate-appliance-architecture.md#appliance-registration)。<br/><br/> 您需要建立 Azure AD 應用程式 (可在應用程式開發人員中取得) 角色的權限。
**建立 Key Vault** | 該 Key Vault 會建立為設備註冊的一部分，並用於管理在設定設備期間下載的憑證。<br/><br/>若要允許 Azure Migrate 建立 Key Vault，您的 Azure 帳戶必須在 Azure Migrate 專案所在的資源群組上擁有參與者權限。


### <a name="assign-permissions-to-create-project"></a>指派建立專案的權限 

檢查您是否有建立 Azure Migrate 專案的權限。

1. 在 Azure 入口網站中開啟訂用帳戶，然後選取 [存取控制 (IAM)]****。
2. 在 [檢查存取權]**** 中，尋找相關的帳戶，然後按一下以查看權限。
3. 您應該會具有「參與者」**** 或「擁有者」**** 權限。
    - 如果您剛建立免費的 Azure 帳戶，您就是訂用帳戶的擁有者。
    - 如果您不是訂用帳戶擁有者，請與擁有者合作以指派角色。


### <a name="assign-permissions-to-register-the-appliance"></a>指派權限以註冊設備 

您可以使用下列其中一種方法來指派 Azure Migrate 的權限，以在設備註冊期間建立 Azure AD 應用程式：

- 租用戶/全域管理員可將權限授與租用戶中的使用者，以建立及註冊 Azure AD 應用程式。
- 租用戶/全域管理員可為帳戶指派應用程式開發人員角色 (具有權限)。

> [!NOTE]
> - 除了前述權限外，應用程式在訂用帳戶上沒有任何其他存取權限。
> - 只有當您註冊新的設備時，才需要這些權限。 完成設備的設定後，您可以移除權限。


#### <a name="grant-account-permissions"></a>授與帳戶權限

租用戶/全域管理員可以授與權限，如下所示：

1. 在 Azure AD 中，租用戶/全域管理員應該瀏覽至 [Azure Active Directory] > [使用者] > [使用者設定]。
2. 管理員應將 [應用程式註冊] 設定為 [是]。

    ![Azure AD 權限](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> 這是一個不敏感的預設設定。 [深入了解](../active-directory/develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance)。

#### <a name="assign-application-developer-role"></a>指派應用程式開發人員角色

租用戶/全域管理員可為帳戶指派應用程式開發人員角色。 [深入了解](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)。


## <a name="prepare-azure-for-physical-server-migration"></a>準備 Azure 以便進行實體伺服器移轉

準備 Azure 以使用伺服器移轉來遷移實體伺服器。

**Task** | **詳細資料**
--- | ---
**建立 Azure Migrate 專案** | 您的 Azure 帳戶需要參與者或擁有者權限，才能建立專案。
**驗證您 Azure 帳戶的權限** | 您的 Azure 帳戶必須有建立 VM 以及寫入至 Azure 受控磁碟的權限。
**建立 Azure 網路** | 在 Azure 中設定網路。


### <a name="assign-permissions-to-create-project"></a>指派建立專案的權限

1. 在 Azure 入口網站中開啟訂用帳戶，然後選取 [存取控制 (IAM)]****。
2. 在 [檢查存取權]**** 中，尋找相關的帳戶，然後按一下以查看權限。
3. 您應該會具有「參與者」**** 或「擁有者」**** 權限。
    - 如果您剛建立免費的 Azure 帳戶，您就是訂用帳戶的擁有者。
    - 如果您不是訂用帳戶擁有者，請與擁有者合作以指派角色。


### <a name="assign-azure-account-permissions"></a>指派 Azure 帳戶權限

將「虛擬機器參與者」角色指派給 Azure 帳戶。 這會提供權限給：
  - 在所選的資源群組中建立 VM。
  - 在所選的虛擬網路中建立 VM。
  - 寫入至 Azure 受控磁碟。 

### <a name="create-an-azure-network"></a>建立 Azure 網路

[設定](../virtual-network/manage-virtual-network.md#create-a-virtual-network) Azure 虛擬網路 (VNet)。 當您複寫至 Azure 時，系統會建立 Azure VM 並將其加入至您設定移轉時所指定的 Azure VNet。


## <a name="prepare-for-physical-server-assessment"></a>準備進行實體伺服器評量

若要準備進行實體伺服器評量，您必須確認實體伺服器的設定，並確認設備部署的設定：

### <a name="verify-physical-server-settings"></a>確認實體伺服器設定

1. 確認伺服器評量的[實體伺服器需求](migrate-support-matrix-physical.md#physical-server-requirements)。
2. 確定已在實體伺服器上開啟[必要的連接埠](migrate-support-matrix-physical.md#port-access)。


### <a name="verify-appliance-settings"></a>確認設備設定

在下一個教學課程中設定 Azure Migrate 設備並開始進行評量之前，請先準備設備部署。

1. [確認](migrate-appliance.md#appliance---physical)實體的設備需求。
2. 檢閱[公用](migrate-appliance.md#public-cloud-urls)和[政府](migrate-appliance.md#government-cloud-urls)雲端中設備需要存取的 Azure URL。
3. [檢閱](migrate-appliance.md#collected-data---vmware)設備將在探索和評定期間收集的資料。
4. [注意](migrate-support-matrix-physical.md#port-access)實體伺服器評量的連接埠存取需求。


### <a name="set-up-an-account-for-physical-server-discovery"></a>設定用於實體伺服器探索的帳戶

Azure Migrate 需要探索內部部署伺服器的權限。

- **Windows：** 您需要網域帳戶來探索已加入網域的伺服器，並使用本機帳戶來探索未加入網域的電腦。 使用者帳戶應新增至下列群組：遠端管理使用者、效能監視器使用者，以及效能記錄使用者。
- **Linux：** 您在要探索的 Linux 伺服器上必須要有根帳戶。

## <a name="prepare-for-physical-server-migration"></a>準備進行實體伺服器移轉

檢閱實體伺服器的移轉需求。

> [!NOTE]
> 遷移實體機器時，「Azure Migrate：伺服器移轉」會使用與 Azure Site Recovery 服務中的代理程式型災害復原功能相同的複寫架構，而部分元件會共用相同的程式碼基底。 有些內容可能會連結至 Site Recovery 文件。

1. [檢閱](migrate-support-matrix-physical-migration.md#physical-server-requirements)用於移轉的實體伺服器需求。
2. 「Azure Migrate：伺服器移轉」會使用複寫伺服器來進行實體伺服器的移轉：
    - [檢閱](migrate-replication-appliance.md#appliance-requirements)複寫設備的部署需求，以及在設備上安裝 MySQL 的[選項](migrate-replication-appliance.md#mysql-installation)。
    - 檢閱複寫設備存取公用和政府雲端所需的 [Azure URL](migrate-appliance.md#url-access)。
    - 檢閱複寫設備的 [連接埠] (migrate-replication-appliance.md#port-access) 存取需求。
3. 在您將 VM 遷移至 Azure 之前，VM 需要進行一些變更。
    - 請務必先進行這些變更再開始移轉。 如果您先遷移 VM 再進行變更，VM 可能無法在 Azure 中啟動。
    - 請參閱您所需進行的 [Windows](prepare-for-migration.md#windows-machines) 和 [Linux](prepare-for-migration.md#linux-machines) 變更。



## <a name="next-steps"></a>後續步驟

在本教學課程中，您：

> [!div class="checklist"]
> * 設定 Azure 帳戶權限。
> * 準備好要進行評估的實體伺服器。

請繼續進行下一個教學課程，以建立 Azure Migrate 專案，並評估要移轉至 Azure 的實體伺服器

> [!div class="nextstepaction"]
> [評估實體伺服器](./tutorial-assess-physical.md)
