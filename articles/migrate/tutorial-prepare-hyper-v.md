---
title: 使用 Azure Migrate 準備 Hyper-V VM 以進行評量/移轉
description: 了解如何使用 Azure Migrate 準備進行 VMware VM 的評量/移轉。
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: ea6290eae187ed4ead39f7307a84a54a23c19c29
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641642"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>準備 Hyper-V VM 的評量並將其移轉至 Azure

本文說明如何使用 [Azure Migrate：伺服器評量](migrate-services-overview.md#azure-migrate-server-assessment-tool)來準備內部部署 Hyper-V VM 的評量，並透過 [Azure Migrate：伺服器移轉](migrate-services-overview.md#azure-migrate-server-migration-tool)來遷移 Hyper-V VM。


本教學課程是一個系列中的第一篇，說明如何評估 Hyper-V VM 並將其遷移至 Azure。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 準備 Azure。 設定 Azure 帳戶和資源的權限，以搭配使用 Azure Migrate。
> * 準備內部部署 Hyper-V 主機和 VM 以進行伺服器評量。 您可以使用設定指令碼或以手動方式進行準備。
> * 準備 Azure Migrate 設備的部署。 設備可用來探索及評估內部部署 VM。
> * 準備內部部署 Hyper-V 主機和 VM 以進行伺服器移轉。


> [!NOTE]
> 教學課程將會針對案例示範最簡單的部署路徑，讓您可以快速設定概念證明。 教學課程在情況允許時都會使用預設選項，且不會顯示所有可能的設定與路徑。 如需詳細指示，請參閱 Hyper-V 評量和移轉的操作說明。


如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="prepare-azure"></a>準備 Azure

### <a name="azure-permissions"></a>Azure 權限

您需要 Azure Migrate 部署的設定權限。

**Task** | **詳細資料** 
--- | --- 
**建立 Azure Migrate 專案** | 您的 Azure 帳戶需要參與者或擁有者權限，才能建立專案。 | 
**註冊資源提供者** | Azure Migrate 會使用輕量的 Azure Migrate 設備搭配 Azure Migrate 伺服器評量，來探索和評估 Hyper-V VM。<br/><br/> 在設備註冊期間，資源提供者會使用設備中選擇的訂用帳戶進行註冊。 [深入了解](migrate-appliance-architecture.md#appliance-registration)。<br/><br/> 若要註冊資源提供者，您必須具有訂用帳戶的「參與者」或「擁有者」角色。
**建立 Azure AD 應用程式** | 註冊設備時，Azure Migrate 會建立一個 Azure Active Directory (Azure AD) 應用程式，用於溝通設備上執行的代理程式，使其各自的服務能在 Azure 上執行。 [深入了解](migrate-appliance-architecture.md#appliance-registration)。<br/><br/> 您需要建立 Azure AD 應用程式 (可在應用程式開發人員中取得) 角色的權限。



### <a name="assign-permissions-to-create-project"></a>指派建立專案的權限

檢查您是否有建立 Azure Migrate 專案的權限。

1. 在 Azure 入口網站中開啟訂用帳戶，然後選取 [存取控制 (IAM)]  。
2. 在 [檢查存取權]  中，尋找相關的帳戶，然後按一下以查看權限。
3. 您應該會具有「參與者」  或「擁有者」  權限。
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

1. 在 Azure AD 中，租用戶/全域管理員應該瀏覽至 [Azure Active Directory]   > [使用者]   > [使用者設定]  。
2. 管理員應將 [應用程式註冊]  設定為 [是]  。

    ![Azure AD 權限](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> 這是一個不敏感的預設設定。 [深入了解](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)。



#### <a name="assign-application-developer-role"></a>指派應用程式開發人員角色

租用戶/全域管理員可為帳戶指派應用程式開發人員角色。 [深入了解](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)。


## <a name="prepare-hyper-v-for-assessment"></a>準備 Hyper-V以進行評量

您可以透過手動方式或使用設定指令碼來準備 Hyper-V 以進行 VM 評量。 準備步驟如下所示：
- [驗證](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements)Hyper-V 主機設定，並確定已在 Hyper-V 主機上開啟[所需的連接埠](migrate-support-matrix-hyper-v.md#port-access)。
- 在每部主機上設定 PowerShell 遠端功能，讓 Azure Migrate 設備可以透過 WinRM 連線在主機上執行 PowerShell 命令。
- 如果 VM 磁碟位於遠端 SMB 共用上，則委派認證。
- 設定帳戶，以便設備用來探索 Hyper-V 主機上的 VM。
- 在您想要探索和評估的每部 VM 上設定 Hyper-V Integration Services。 當您啟用 Integration Services 時，預設設定就足以供 Azure Migrate 使用。

    ![啟用 Integration Services](./media/tutorial-prepare-hyper-v/integrated-services.png)


## <a name="prepare-with-a-script"></a>準備指令碼

指令碼會執行下列作業：

- 檢查您是否在支援的 PowerShell 版本上執行指令碼。
- 驗證您 (執行指令碼的使用者) 是否有 Hyper-V 主機上的系統管理權限。
- 可讓您建立本機使用者帳戶 (非系統管理員)，以便 Azure Migrate 服務與 Hyper-V 主機通訊。 此使用者帳戶會新增至主機上的這些群組：
    - 遠端管理使用者
    - Hyper-V 系統管理員
    - 效能監視器使用者
- 檢查主機是否正在執行支援的 Hyper-V 版本和 Hyper-V 角色。
- 啟用 WinRM 服務，並在主機上開啟埠 5985 (HTTP) 和 5986 (HTTPS) (用於收集中繼資料)。
- 在主機上啟用 PowerShell 遠端功能。
- 檢查由主機管理的所有 VM 是否都已啟用 Hyper-V 整合服務。
- 視需要在主機上啟用 CredSSP。

執行指令碼，如下所示：

1. 請確定您已在 Hyper-V 主機上安裝 PowerShell 4.0 版或更新版本。
2. 從 [Microsoft 下載中心](https://aka.ms/migrate/script/hyperv)下載指令碼。 此指令碼會由 Microsoft 以密碼編譯方式進行簽署。
3. 使用 MD5 或 SHA256 雜湊檔案來驗證指令碼完整性。 主題標籤值如下。 執行此命令以產生指令碼的雜湊：
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    使用方式範例：
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1
    SHA256
    ```

4.    驗證指令碼完整性之後，請使用此 PowerShell 命令在每部 Hyper-V 主機上執行指令碼：
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

### <a name="hashtag-values"></a>主題標籤值

雜湊值為：

| **雜湊** | **ReplTest1** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |


## <a name="prepare-manually"></a>手動準備

遵循本節中的程序，以手動方式準備 Hyper-V (不是使用指令碼)。

### <a name="verify-powershell-version"></a>確認 PowerShell 版本

請確定您已在 Hyper-V 主機上安裝 PowerShell 4.0 版或更新版本。



### <a name="set-up-an-account-for-vm-discovery"></a>設定用於 VM 探索的帳戶

Azure Migrate 需要可探索內部部署 VM 的權限。

- 在 Hyper-V 主機/叢集上設定具有系統管理員權限的網域或本機使用者帳戶。

    - 您需要單一帳戶來使用要包含在探索中的所有主機和叢集。
    - 此帳戶可以是本機或網域帳戶。 我們建議您在 Hyper-V 主機或叢集上具有系統管理員權限。
    - 或者，如果您不想要指派系統管理員權限，則需要下列權限：
        - 遠端管理使用者
        - Hyper-V 系統管理員
        - 效能監視器使用者

### <a name="verify-hyper-v-host-settings"></a>確認 Hyper-V 主機設定

1. 確認伺服器評量的 [Hyper-V 主機需求](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements)。
2. 確認 Hyper-V 主機上已開啟[必要的連接埠](migrate-support-matrix-hyper-v.md#port-access)。

### <a name="enable-powershell-remoting-on-hosts"></a>在主機上啟用 PowerShell 遠端功能

在每部主機上設定 PowerShell 遠端功能，如下所示：

1. 以系統管理員身分在每個主機上開啟 PowerShell 主控台。
2. 請執行這個命令：

    ```
    Enable-PSRemoting -force
    ```
### <a name="enable-integration-services-on-vms"></a>在 VM 上啟用 Integration Services

每部 VM 上都應啟用 Integration Services，才能讓 Azure Migrate 在 VM 上擷取作業系統資訊。

在您想要探索和評估的 VM 上，對每部 VM 啟用 [Hyper-V Integration Services](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services)。


### <a name="enable-credssp-on-hosts"></a>在主機上啟用 CredSSP

如果主機具有 VM，且磁碟都位於 SMB 共用上，請在主機上完成此步驟。

- 您可以從遠端對所有 Hyper-V 主機執行此命令。
- 如果您在叢集上新增主機節點，系統會自動加入這些節點以進行探索，但您需要在新節點上手動啟用 CredSSP (如有需要)。

以下列方式啟用：

1. 識別 Hyper-V 主機是否執行在 SMB 共用上有磁碟的 Hyper-V VM。
2. 在每個識別到的 Hyper-V 主機上執行下列命令：

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

當您設定設備時，您可以藉由[在設備上啟用 CredSSP](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds) 來完成 CredSSP 的設定。 這會在本系列的下一個教學課程中加以說明。


## <a name="prepare-for-appliance-deployment"></a>準備設備部署

在下一個教學課程中設定 Azure Migrate 設備並開始進行評量之前，請先準備設備部署。

1. [確認](migrate-appliance.md#appliance---hyper-v)設備需求。
2. 檢閱[公用](migrate-appliance.md#public-cloud-urls)和[政府](migrate-appliance.md#government-cloud-urls)雲端中設備需要存取的 Azure URL。 如果您使用以 URL 為基礎的防火牆或 Proxy，請務必允許必要 URL 的存取。
3. 檢閱設備將在探索和評量期間收集的資料。
4. [檢閱](migrate-appliance.md#collected-data---hyper-v)設備的連接埠存取需求。




## <a name="prepare-for-hyper-v-migration"></a>準備 Hyper-V 移轉

1. [檢閱](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) 移轉時的 Hyper-V 主機需求，以及進行 VM 移轉時 Hyper-V 主機和叢集需要存取的 Azure URL。
2. [檢閱](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms)您要遷移至 Azure 的 Hyper-V VM 有何需求。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您：

> [!div class="checklist"]
> * 設定 Azure 帳戶權限。
> * 準備 Hyper-V 主機和 VM 以進行評量和移轉。
> * 準備 Azure Migrate 設備的部署。

請繼續進行下一個教學課程，以建立 Azure Migrate 專案、部署設備，以及探索和評估要遷移至 Azure 的 Hyper-V VM。

> [!div class="nextstepaction"]
> [評估 Hyper-V VMs](./tutorial-assess-hyper-v.md)
