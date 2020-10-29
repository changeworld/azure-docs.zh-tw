---
title: 備份適用於 PostgreSQL 的 Azure 資料庫
description: '深入瞭解使用長期保留 (預覽版適用於 PostgreSQL 的 Azure 資料庫備份) '
ms.topic: conceptual
ms.date: 09/08/2020
ms.custom: references_regions
ms.openlocfilehash: edbfdb6ea741cdb344a121acdbee3b8bd4bc743c
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927884"
---
# <a name="azure-database-for-postgresql-backup-with-long-term-retention-preview"></a>使用長期保留的適用於 PostgreSQL 的 Azure 資料庫備份 (預覽) 

Azure 備份與 Azure 資料庫服務共同建立了企業級的備份解決方案，適用于將備份保留最多10年的適用於 PostgreSQL 的 Azure 資料庫伺服器。

除了長期保留，此解決方案也有許多其他功能，如下所示：

- Azure 角色型存取控制 (Azure RBAC) 使用 Azure Active Directory 和受控服務識別 (MSI) 驗證的資料庫。
- 在個別資料庫層級進行客戶控制的排程和隨選備份。
- 資料庫層級還原至任何 Postgres 伺服器，或直接還原至 blob 儲存體。
- 長期保留。
- 所有作業和工作的集中監視。
- 備份會儲存在不同的安全性和容錯網域中。 因此，即使來源伺服器遭到入侵或甚至被終止，備份仍會在 [備份保存庫](backup-vault-overview.md)中保持安全。
- 使用 **pg_dump** 可讓您在還原時有更大的彈性，讓您可以跨資料庫版本還原，甚至只還原一部分的備份。

您可以單獨使用此解決方案，或除了 Azure 于 postgresql 所提供的原生備份解決方案，以提供最多35天的保留。 原生方案適用于作業復原，例如當您想要從最新的備份復原時。 Azure 備份的解決方案可協助您符合合規性需求，以及更細微且更有彈性的備份與還原。

## <a name="support-matrix"></a>支援矩陣

|支援  |詳細資料  |
|---------|---------|
|支援的部署   |  適用於 PostgreSQL 的 Azure 資料庫獨立單一伺服器     |
|支援的 Azure 區域 |  美國東部、美國東部2、美國中部、美國中南部、美國西部、美國西部2、美國中西部、巴西南部、加拿大中部、歐洲北部、歐洲西部、英國南部、英國西部、德國中西部、瑞士北部、瑞士西部、東亞、南部東亞、日本東部、日本西部、韓國中部、南韓南部、印度中部、澳大利亞東部、澳大利亞中部、澳大利亞中部2、阿拉伯聯合大公國北部  |
|支援的 Azure 于 postgresql 版本    |   9.5、9.6、10、11      |

## <a name="feature-considerations-and-limitations"></a>功能考慮和限制

- 只有 Azure 入口網站支援所有作業。
- 建議的最大資料庫大小上限為 400 GB。
- 不支援跨區域備份。 這表示您無法將 Azure 于 postgresql server 備份到另一個區域中的保存庫。 同樣地，您只能將備份還原至與保存庫位於相同區域內的伺服器。
- 還原時只會復原資料。 「角色」不會還原。
- 在預覽中，我們建議您只在測試環境中執行解決方案。

## <a name="backup-process"></a>備份程序

1. 此解決方案會使用 **pg_dump** 來取得 Azure 于 postgresql 資料庫的備份。

2. 當您指定要備份的 Azure 于 postgresql 資料庫之後，服務會驗證它是否有正確的許可權集和存取權，可在伺服器和資料庫上執行備份操作。

3. Azure 備份會啟動已安裝備份延伸模組的背景工作角色。 此延伸模組會與 Postgres 伺服器通訊。

4. 此延伸模組是由協調器和 Azure Postgres 外掛程式所組成。 協調器會負責觸發各種作業（例如設定備份、備份和還原）的工作流程，而外掛程式則負責實際的資料流程。
  
5. 當您在選取的資料庫上觸發設定保護之後，備份服務會使用備份排程和其他原則詳細資料來設定協調器。

6. 在排程的時間，協調器會與外掛程式進行通訊，並使用 **pg_dump** 開始從 Postgres 伺服器串流處理備份資料。

7. 外掛程式會將資料直接傳送至備份保存庫，而不需要預備位置。 資料會使用 Microsoft 管理的金鑰進行加密，並由儲存體帳戶中的 Azure 備份服務儲存。

8. 當資料傳輸完成時，協調器會向備份服務確認認可。

    ![備份程序](./media/backup-azure-database-postgresql/backup-process.png)

## <a name="configure-backup-on-azure-postgresql-databases"></a>在 Azure 于 postgresql 資料庫上設定備份

您可以跨多個 Azure 于 postgresql 伺服器設定多個資料庫的備份。 確定已設定服務用來備份 Postgres 伺服器所需的 [必要許可權](#prerequisite-permissions-for-configure-backup-and-restore) 。

下列指示是使用 Azure 備份在 Azure 于 postgresql 資料庫上設定備份的逐步指南：

1. 有兩種方式可以開始進行程式：

    1. 移至 [備份中心](backup-center-overview.md)  ->  **總覽**  ->  **備份** 。

        ![移至備份中心](./media/backup-azure-database-postgresql/backup-center.png)

        在 [ **起始：設定備份** ] 下，選取 [ **資料來源類型** ] **適用於 PostgreSQL 的 Azure 資料庫** 。

        ![在 [起始：設定備份] 中，選取 [資料來源類型]](./media/backup-azure-database-postgresql/initiate-configure-backup.png)

    1. 或者，您可以直接移至 [備份保存庫](backup-vault-overview.md)  ->  **備份** 。

        ![移至備份保存庫](./media/backup-azure-database-postgresql/backup-vaults.png)

        ![選取備份保存庫中的備份](./media/backup-azure-database-postgresql/backup-backup-vault.png)

1. 在 [ **設定備份** ] 底下，選取您要備份 Postgres 資料庫的 **備份保存庫** 。 如果您已經在保存庫內容中，則會預先填入此資訊。

    ![在設定備份中選取備份保存庫](./media/backup-azure-database-postgresql/configure-backup.png)

1. 選取或建立 **備份原則** 。

    ![選擇備份原則](./media/backup-azure-database-postgresql/backup-policy.png)

1. 選取要備份的資源或 Postgres 資料庫。

    ![選取要備份的資源](./media/backup-azure-database-postgresql/select-resources.png)

1. 如果訂用帳戶位於與保存庫相同的區域，您可以在訂用帳戶的所有 Azure 于 postgresql 伺服器清單中進行選擇。 展開箭號以查看伺服器內的資料庫清單。

    ![選擇伺服器](./media/backup-azure-database-postgresql/choose-servers.png)

1. 服務會在選取的資料庫上執行這些檢查，以驗證保存庫是否有權備份選取的 Postgres 伺服器和資料庫。
    1. 所有資料庫的 **備份準備就緒** 都應讀取 **成功** ，才能繼續進行。
    1. 如果發生錯誤，請 **修正** 錯誤，然後重新 **驗證** 或從選取專案中移除資料庫。

    ![要修正的驗證錯誤](./media/backup-azure-database-postgresql/validation-errors.png)

1. 確認 [審核] 下的所有詳細資料 **，並設定** 並選取 [ **設定備份** ] 以提交作業。

    ![確認檢查和設定中的詳細資料](./media/backup-azure-database-postgresql/review-and-configure.png)

1. 一旦觸發， **設定備份** 作業就會建立備份實例。 您可以在備份中心或保存庫視圖的 [ [備份實例](backup-center-monitor-operate.md#backup-instances) ] 窗格下，追蹤作業的狀態。

    ![備份實例](./media/backup-azure-database-postgresql/backup-instances.png)

1. 系統會根據原則中定義的備份排程來觸發備份。 您可以在 [備份作業](backup-center-monitor-operate.md#backup-jobs)下追蹤作業。 目前，您可以在過去七天內查看工作。

    ![備份作業](./media/backup-azure-database-postgresql/backup-jobs.png)

## <a name="create-backup-policy"></a>建立備份原則

1. 移至 **備份中心**  ->  **備份原則**  ->  **新增** 。 或者，您可以移至 **備份保存庫**  ->  **備份原則**  ->  **新增** 。

    ![新增備份原則](./media/backup-azure-database-postgresql/add-backup-policy.png)

1. 輸入新原則的 **名稱** 。

    ![輸入原則名稱](./media/backup-azure-database-postgresql/enter-policy-name.png)

1. 定義備份排程。 我們目前支援 **每週** 備份。 您可以將備份排程在一周的一或多天。

    ![定義備份排程](./media/backup-azure-database-postgresql/define-backup-schedule.png)

1. 定義 **保留** 設定。 您可以新增一或多個保留規則。 每個保留規則都會假設特定備份的輸入，以及這些備份的資料存放區和保留期間。

1. 您可以選擇將備份儲存在兩個數據存放區的其中一個 (或層) ： **備份資料存放區** (經常性存取層) 或封存 **資料存放區** (在預覽) 中。 您可以選擇 **兩個階層選項** 來定義在兩個數據存放區之間分層備份的時間：

    - 如果您想要同時在備份和封存資料存放區中同時具有備份副本，請選擇 **立即** 複製。
    - 如果您想要在備份資料存放區中將備份移至封存資料存放區，請選擇移至 **到期日** 。

1. **預設保留規則** 適用于缺少任何其他保留規則，且預設值為三個月。

    - **備份資料存放區** 中的保留持續時間範圍從七天到10年。
    - 封存 **資料存放區** 中的保留持續時間範圍為6個月到10年。

    ![編輯保留期間](./media/backup-azure-database-postgresql/edit-retention.png)

>[!NOTE]
>保留規則會以預先決定的優先順序來評估。 **每年** 規則的優先順序最高，後面接著 **每月** 和 **每週** 的規則。 當沒有其他規則符合資格時，會套用預設保留設定。 例如，相同的復原點可能是每週第一次成功的備份，以及每個月第一次成功的備份。 不過，由於每月規則優先順序高於每週規則的優先順序，因此每個月所進行的第一次成功備份的保留期都會套用。

## <a name="restore"></a>還原

如果服務在目標伺服器上具有適當的許可權集，則您可以將資料庫還原至相同訂用帳戶內的任何 Azure 于 postgresql server。 確定已設定服務用來備份 Postgres 伺服器所需的必要 [許可權](#prerequisite-permissions-for-configure-backup-and-restore) 。

遵循本逐步指南來觸發還原：

1. 有兩種方式可以啟動還原程式：
    1. 移至 [備份中心](backup-center-overview.md)的  ->  **[**  ->  **還原** ]。

    ![選取備份中心的還原](./media/backup-azure-database-postgresql/backup-center-restore.png)

    在 [ **起始：還原** ] 底下，選取 [ **資料來源類型** ] **適用於 PostgreSQL 的 Azure 資料庫** 。 選取 **備份實例** 。

    ![選取起始：還原中的資料來源類型](./media/backup-azure-database-postgresql/initiate-restore.png)

    1. 或者，您可以直接移至 **備份保存庫**  ->  **備份實例** 。 選取對應至您想要還原之資料庫的 [ **備份實例** ]。

    ![還原的備份實例](./media/backup-azure-database-postgresql/backup-instances-restore.png)

    ![備份實例的清單](./media/backup-azure-database-postgresql/list-backup-instances.png)

    ![選取 [還原]](./media/backup-azure-database-postgresql/select-restore.png)

1. 從選取的備份實例可用的所有完整備份清單中 **選取復原點** 。 預設會選取最新的復原點。

    ![選取復原點](./media/backup-azure-database-postgresql/select-recovery-point.png)

    ![復原點清單](./media/backup-azure-database-postgresql/list-recovery-points.png)

1. 輸入 **還原參數** 。 此時，您可以從兩種還原中選取： [ **還原為資料庫** ] 和 [ **還原為** 檔案]。

1. **還原為資料庫** ：還原備份資料，在目標于 postgresql 伺服器中建立新的資料庫。

    - 目標伺服器可以與來源伺服器相同。 但是，不支援覆寫原始資料庫。
    - 您可以從伺服器選擇所有訂用帳戶，但在與保存庫相同的區域中。
    - 選取 [ **審核 + 還原** ]。 這將會觸發驗證，以檢查服務是否在目標伺服器上具有適當的還原許可權。

    ![還原為資料庫](./media/backup-azure-database-postgresql/restore-as-database.png)

1. **還原為** 檔案：將備份檔案傾印到 (blob) 的目標儲存體帳戶。

    - 您可以從儲存體帳戶中選擇所有訂用帳戶，但在與保存庫相同的區域中。
    - 從針對選取的儲存體帳戶篩選的容器清單中選取目標容器。
    - 選取 [ **審核 + 還原** ]。 這將會觸發驗證，以檢查服務是否在目標伺服器上具有適當的還原許可權。

    ![還原為檔案](./media/backup-azure-database-postgresql/restore-as-files.png)

1. 檢查資訊，然後選取 [ **還原** ]。 這會觸發可在 **備份作業** 下追蹤的對應還原作業。

## <a name="prerequisite-permissions-for-configure-backup-and-restore"></a>設定備份和還原的先決條件許可權

Azure 備份遵循嚴格的安全性指導方針。 雖然它是原生的 Azure 服務，但不會假設資源的許可權，而且必須由使用者明確指定。  同樣地，不會儲存連接到資料庫的認證。 這一點很重要，可以保護您的資料。 相反地，我們會使用 Azure Active Directory 驗證。

[下載本檔](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) 以取得自動化腳本和相關指示。 它會將適當的許可權集授與 Azure 于 postgresql 伺服器，以進行備份和還原。

## <a name="manage-the-backed-up-azure-postgresql-databases"></a>管理已備份的 Azure 于 postgresql 資料庫

以下是您可以在 **備份實例** 上執行的管理作業：

### <a name="on-demand-backup"></a>隨選備份

若要以原則中指定的排程來觸發備份，請移至 [ **備份實例**  ->  **立即備份** ]。
從已在相關聯的備份原則中定義的保留規則清單中選擇。

![立即觸發備份](./media/backup-azure-database-postgresql/backup-now.png)

![從保留規則清單中選擇](./media/backup-azure-database-postgresql/retention-rules.png)

### <a name="stop-protection"></a>停止保護

您可以停止保護備份專案。 這也會刪除該備份專案的相關聯復原點。 我們尚未提供停止保護的選項，同時保留現有的復原點。

![停止保護](./media/backup-azure-database-postgresql/stop-protection.png)

### <a name="change-policy"></a>變更原則

您可以使用備份實例來變更相關聯的原則。

1. 選取 **備份實例**  ->  **變更原則** 。

    ![變更原則](./media/backup-azure-database-postgresql/change-policy.png)

1. 選取您要套用至資料庫的新原則。

    ![重新指派原則](./media/backup-azure-database-postgresql/reassign-policy.png)

## <a name="troubleshooting"></a>疑難排解

本節提供使用 Azure 備份備份 Azure 于 postgresql 資料庫的疑難排解資訊。

### <a name="usererrormsimissingpermissions"></a>UserErrorMSIMissingPermissions

在您想要備份或還原的 PG 伺服器上，授與備份保存庫 MSI **讀取** 許可權：

若要建立與于 postgresql 資料庫的安全連線，Azure 備份使用 [受控服務識別 (MSI) ](../active-directory/managed-identities-azure-resources/overview.md) 驗證模型。 這表示備份保存庫只會存取使用者已明確授與許可權的資源。

系統會在建立時自動將系統 MSI 指派給保存庫。 您必須將您想要備份資料庫之于 postgresql 伺服器的存取權授與此保存庫 MSI。

步驟：

1. 在 Postgres 伺服器中，移至 [ **存取控制] (IAM)** 窗格。

    ![存取控制窗格](./media/backup-azure-database-postgresql/access-control-pane.png)

1. 選取 [ **新增角色指派** ]。

    ![新增角色指派](./media/backup-azure-database-postgresql/add-role-assignment.png)

1. 在開啟的右側內容窗格中，輸入下列內容：<br>

    **角色：** 讀者<br>
    **將存取權指派給：** 選擇 **備份保存庫**<br>
    如果您在下拉式清單中找不到 **備份保存庫** 選項，請選擇 [ **Azure AD 使用者、群組或服務主體] 選項**<br>

    ![選取職責](./media/backup-azure-database-postgresql/select-role.png)

    **選取：** 輸入您想要備份此伺服器及其資料庫的備份保存庫名稱。<br>

    ![輸入備份保存庫名稱](./media/backup-azure-database-postgresql/enter-backup-vault-name.png)

### <a name="usererrorbackupuserauthfailed"></a>UserErrorBackupUserAuthFailed

建立可使用 Azure Active Directory 進行驗證的資料庫備份使用者：

此錯誤可能是因為于 postgresql 伺服器沒有 Azure Active Directory 系統管理員，或是沒有可使用 Azure Active Directory 進行驗證的備份使用者。

步驟：

將 Active Directory 系統管理員新增至 OSS 伺服器：

您必須執行此步驟，才能透過可使用 Azure Active Directory （而非密碼）進行驗證的使用者來連接到資料庫。 適用於 PostgreSQL 的 Azure 資料庫中的 Azure AD 管理使用者將會有 **azure_ad_admin** 的角色。 只有 **azure_ad_admin** 角色可以建立可使用 Azure AD 進行驗證的新資料庫使用者。

1. 移至 [伺服器] 視圖左側導覽窗格中的 [Active Directory 系統管理員] 索引標籤，並將自己的 (或其他人) 做為 Active Directory 系統管理員。

    ![設定 Active Directory 管理員](./media/backup-azure-database-postgresql/set-admin.png)

1. 請務必 **儲存** AD 系統管理使用者設定。

    ![儲存 Active Directory 系統管理使用者設定](./media/backup-azure-database-postgresql/save-admin-setting.png)

請參閱 [這份檔](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) ，以取得完成許可權授與步驟所需執行的步驟清單。

### <a name="usererrormissingnetworksecuritypermissions"></a>UserErrorMissingNetworkSecurityPermissions

藉由啟用伺服器視圖中的 [ **允許存取 Azure 服務** ] 旗標，建立網路連線。 在 [伺服器] 視圖的 [連線 **安全性** ] 窗格下，將 [ **允許存取 Azure 服務** ] 旗標設定為 **[是]** 。

![允許存取 Azure 服務](./media/backup-azure-database-postgresql/allow-access-to-azure-services.png)

### <a name="usererrorcontainernotaccessible"></a>UserErrorContainerNotAccessible

#### <a name="permission-to-restore-to-a-storage-account-container-when-restoring-as-files"></a>還原為檔案時還原至儲存體帳戶容器的許可權

1. 使用 Azure 入口網站為備份保存庫 MSI 提供存取儲存體帳戶容器的許可權。
    1. 移至 **儲存體帳戶**  ->  **存取控制**  ->  **新增角色指派** 。
    1. 將 **儲存體 Blob 資料參與者** 角色指派給備份保存庫 MSI。

    ![指派儲存體 Blob 資料參與者角色](./media/backup-azure-database-postgresql/assign-storage-blog-data-contributor-role.png)

1. 或者，您也可以使用 Azure CLI [az role 指派 create](/cli/azure/role/assignment) 命令，為您要還原的特定容器提供細微許可權。

    ```azurecli
    az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
    ```

    1. 將受託人參數取代為保存庫 MSI 的 **應用程式識別碼** ，並使用範圍參數來參考您的特定容器。
    1. 若要取得保存庫 MSI 的 **應用程式識別碼** ，請選取 [ **應用程式類型** ] 下的 [ **所有應用程式** ]：

        ![選取所有應用程式](./media/backup-azure-database-postgresql/select-all-applications.png)

    1. 搜尋保存庫名稱，然後複製應用程式識別碼：

        ![搜尋保存庫名稱](./media/backup-azure-database-postgresql/search-for-vault-name.png)

## <a name="next-steps"></a>後續步驟

- [備份保存庫總覽](backup-vault-overview.md)
