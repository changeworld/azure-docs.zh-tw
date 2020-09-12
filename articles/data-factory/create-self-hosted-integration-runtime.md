---
title: 建立自我裝載整合執行階段
description: 瞭解如何在 Azure Data Factory 中建立自我裝載整合執行時間，讓資料處理站存取私人網路中的資料存放區。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 06/09/2020
ms.openlocfilehash: cac7b4f376300722762b1cedbf52a5c2e0ecb6e4
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89596105"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>建立和設定自我裝載整合執行階段

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

整合執行階段 (IR) 是 Azure Data Factory 所使用的計算基礎結構，可提供跨不同網路環境的資料整合功能。 如需 IR 的詳細資訊，請參閱 [Integration runtime 總覽](concepts-integration-runtime.md)。

自我裝載整合執行時間可以在雲端資料存放區與私人網路中的資料存放區之間執行複製活動。 它也可以針對內部部署網路或 Azure 虛擬網路中的計算資源分派轉換活動。 自我裝載整合執行時間的安裝需要內部部署電腦或私人網路內的虛擬機器。  

本文說明如何建立和設定自我裝載 IR。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="setting-up-a-self-hosted-integration-runtime"></a>設定自我裝載整合執行時間

若要建立和設定自我裝載整合執行時間，請使用下列程式。

### <a name="create-a-self-hosted-ir-via-azure-powershell"></a>透過 Azure PowerShell 建立自我裝載 IR

1. 您可以使用此工作的 Azure PowerShell。 請看以下範例：

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [下載](https://www.microsoft.com/download/details.aspx?id=39717)並在本機電腦上安裝自我裝載整合執行階段。

3. 擷取驗證金鑰，並使用該金鑰註冊自我裝載整合執行階段。 以下是 PowerShell 範例︰

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

### <a name="create-a-self-hosted-ir-via-azure-data-factory-ui"></a>透過 Azure Data Factory UI 建立自我裝載 IR

使用下列步驟，使用 Azure Data Factory UI 來建立自我裝載 IR。

1. 在 Azure Data Factory UI 的 [開始吧] 頁面上，從最左邊窗格中選取 [[管理] 索引標籤](https://docs.microsoft.com/azure/data-factory/author-management-hub)。

   ![首頁的 [管理] 按鈕](media/doc-common-process/get-started-page-manage-button.png)

1. 在左窗格上選取 [整合執行階段]，然後選取 [+ 新增]。

   ![建立整合執行階段](media/doc-common-process/manage-new-integration-runtime.png)

1. 在 [ **整合執行時間設定** ] 頁面上，選取 [ **Azure]、[自我**裝載]，然後選取 [ **繼續**]。 

1. 在下列頁面上，選取 [ **自我** 裝載] 以建立自我裝載 IR，然後選取 [ **繼續**]。
   ![建立 selfhosted IR](media/create-self-hosted-integration-runtime/new-selfhosted-integration-runtime.png)

1. 輸入 IR 的名稱，然後選取 [ **建立**]。

1. 在 [ **整合執行時間設定** ] 頁面上，選取 **選項 1** 底下的連結，以開啟電腦上的快速安裝。 或依照 **選項 2** 下的步驟手動設定。 下列指示以手動設定為基礎：

   ![整合執行階段設定](media/create-self-hosted-integration-runtime/integration-runtime-setting-up.png)

    1. 複製並貼上驗證金鑰。 選取 [ **下載並安裝整合運行**時間]。

    1. 在本機 Windows 電腦上下載自我裝載的整合執行階段。 執行安裝程式。

    1. 在 [ **註冊 Integration Runtime (自我裝載) ** ] 頁面上，貼上您稍早儲存的金鑰，然後選取 [ **註冊**]。
    
       ![註冊整合執行階段](media/create-self-hosted-integration-runtime/register-integration-runtime.png)

    1. 在 [新增 Integration Runtime (自我裝載) 節點] 頁面上，選取 [完成]。

1. 自我裝載整合執行時間成功註冊之後，您會看到下列視窗：

    ![註冊成功](media/create-self-hosted-integration-runtime/registered-successfully.png)

### <a name="set-up-a-self-hosted-ir-on-an-azure-vm-via-an-azure-resource-manager-template"></a>透過 Azure Resource Manager 範本，在 Azure VM 上設定自我裝載 IR

您可以使用 [Create self HOST ir 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime)，將 Azure 虛擬機器上的自我裝載 ir 設定自動化。 此範本提供簡單的方法，讓您在 Azure 虛擬網路內擁有功能完整的自我裝載 IR。 如果您將節點計數設定為2或更高，則 IR 具有高可用性和擴充性功能。

### <a name="set-up-an-existing-self-hosted-ir-via-local-powershell"></a>透過本機 PowerShell 設定現有的自我裝載 IR

您可以使用命令列來設定或管理現有的自我裝載 IR。 此使用方式有助於將自我裝載 IR 節點的安裝和註冊自動化。

Dmgcmd.exe 包含在自我裝載的安裝程式中。 它通常位於 C:\Program Files\Microsoft Integration Runtime\4.0\Shared\ 資料夾。 此應用程式支援不同的參數，而且可以使用批次腳本進行自動化以透過命令列叫用。

使用應用程式，如下所示：

```powershell
dmgcmd [ -RegisterNewNode "<AuthenticationKey>" -EnableRemoteAccess "<port>" ["<thumbprint>"] -EnableRemoteAccessInContainer "<port>" ["<thumbprint>"] -DisableRemoteAccess -Key "<AuthenticationKey>" -GenerateBackupFile "<filePath>" "<password>" -ImportBackupFile "<filePath>" "<password>" -Restart -Start -Stop -StartUpgradeService -StopUpgradeService -TurnOnAutoUpdate -TurnOffAutoUpdate -SwitchServiceAccount "<domain\user>" ["<password>"] -Loglevel <logLevel> ]
```

以下是應用程式參數和屬性的詳細資料： 

| 屬性                                                    | 描述                                                  | 必要 |
| ----------------------------------------------------------- | ------------------------------------------------------------ | -------- |
| **RegisterNewNode** "`<AuthenticationKey>`"                     | 使用指定的驗證金鑰註冊自我裝載整合執行時間節點。 | 否       |
| **RegisterNewNode** "`<AuthenticationKey>`" "`<NodeName>`"      | 使用指定的驗證金鑰和節點名稱註冊自我裝載的整合執行時間節點。 | 否       |
| **EnableRemoteAccess** "`<port>`" ["`<thumbprint>`"]            | 啟用目前節點上的遠端存取，以設定高可用性叢集。 或直接針對自我裝載 IR 啟用設定認證，而不需要 Azure Data Factory。 您可以從相同網路中的遠端電腦使用 **AzDataFactoryV2LinkedServiceEncryptedCredential 指令程式** ，以進行後者。 | 否       |
| **EnableRemoteAccessInContainer** "`<port>`" ["`<thumbprint>`"] | 當節點在容器中執行時，啟用對目前節點的遠端存取。 | 否       |
| **DisableRemoteAccess**                                         | 停用對目前節點的遠端存取。 多節點設定需要遠端存取。 即使已停用遠端存取， **AzDataFactoryV2LinkedServiceEncryptedCredential** PowerShell Cmdlet 仍可運作。 只要 Cmdlet 是在與自我裝載 IR 節點相同的電腦上執行，這種行為就是如此。 | 否       |
| **金鑰** "`<AuthenticationKey>`"                                 | 覆寫或更新先前的驗證金鑰。 請小心使用此動作。 如果金鑰是新的整合執行時間，您先前的自我裝載 IR 節點可能會離線。 | 否       |
| **GenerateBackupFile** "`<filePath>`" "`<password>`"            | 產生目前節點的備份檔案。 備份檔案包含節點金鑰和資料存放區認證。 | 否       |
| **ImportBackupFile** "`<filePath>`" "`<password>`"              | 從備份檔案還原節點。                          | 否       |
| **重新啟動**                                                     | 重新開機自我裝載 integration runtime 主機服務。   | 否       |
| **啟動**                                                       | 啟動自我裝載 integration runtime 主機服務。     | 否       |
| **停止**                                                        | 停止自我裝載 integration runtime 主機服務。        | 否       |
| **StartUpgradeService**                                         | 啟動自我裝載整合執行時間升級服務。       | 否       |
| **StopUpgradeService**                                          | 停止自我裝載整合執行時間升級服務。        | 否       |
| **TurnOnAutoUpdate**                                            | 開啟自我裝載整合執行時間自動更新。        | 否       |
| **TurnOffAutoUpdate**                                           | 關閉自我裝載整合執行時間自動更新。       | 否       |
| **SwitchServiceAccount** "`<domain\user>`" ["`<password>`"]           | 將 DIAHostService 設定為以新帳戶執行。 針對系統帳戶和虛擬帳戶，請使用空白密碼 ""。 | 否       |


## <a name="command-flow-and-data-flow"></a>命令流程和資料流程

當您在內部部署和雲端之間移動資料時，活動會使用自我裝載整合執行時間，在內部部署資料來源與雲端之間傳輸資料。

以下是使用自我裝載 IR 進行複製之資料流程步驟的高階摘要：

![資料流程的高層級總覽](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. 資料開發人員使用 PowerShell Cmdlet 在 Azure data factory 中建立自我裝載整合執行時間。 目前，Azure 入口網站不支援這項功能。
1. 資料開發人員建立內部部署資料存放區的連結服務。 開發人員藉由指定服務應該用來連接到資料存放區的自我裝載整合執行時間實例來執行此動作。
1. 自我裝載整合執行階段節點會使用 Windows 資料保護應用程式開發介面 (DPAPI) 將加密，並將認證儲存在本機上。 如果有多個節點設定為高可用性，則該認證會進一步同步處理到其他節點。 每個節點都會使用 DPAPI 來加密認證，並將其儲存在本機上。 認證同步處理無需資料開發人員介入，並且由自我裝載 IR 處理。
1. Azure Data Factory 與自我裝載整合執行時間進行通訊，以排程和管理作業。 通訊是透過使用共用 [Azure 服務匯流排轉送](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it#wcf-relay) 連接的控制通道來進行。 當需要執行活動作業時，Data Factory 會將要求和任何認證資訊排入佇列。 如果認證尚未儲存在自我裝載整合執行時間中，它就會這麼做。 自我裝載整合執行時間會在輪詢佇列之後啟動作業。
1. 自我裝載整合執行時間會在內部部署存放區與雲端儲存體之間複製資料。 複製的方向取決於在資料管線中如何設定複製活動。 在此步驟中，自我裝載整合執行時間會透過安全的 HTTPS 通道，直接與雲端式儲存體服務（例如 Azure Blob 儲存體）進行通訊。

## <a name="considerations-for-using-a-self-hosted-ir"></a>使用自我裝載 IR 的考量

- 您可以針對多個內部部署資料來源使用單一自我裝載整合執行時間。 您也可以在相同 Azure Active Directory (Azure AD) 租使用者中，與另一個資料處理站共用。 如需詳細資訊，請參閱[共用自我裝載整合執行階段](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory)。
- 您只能在任何單一電腦上安裝一個自我裝載整合執行時間實例。 如果您有兩個需要存取內部部署資料來源的資料處理站，請使用 [自我裝載 ir 共用功能](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory) 來共用自我裝載 ir，或在兩部內部部署電腦上安裝自我裝載 ir （每個資料處理站各一個）。  
- 自我裝載整合執行時間不一定要在與資料來源相同的電腦上。 不過，讓自我裝載整合執行時間接近資料來源，可以減少自我裝載整合執行時間連接到資料來源的時間。 建議您在與裝載內部部署資料來源不同的電腦上安裝自我裝載整合執行時間。 當自我裝載整合執行時間和資料來源位於不同的電腦上時，自我裝載整合執行時間不會與資源的資料來源競爭。
- 您可以在不同電腦上有多個自我裝載整合執行階段，但它們皆連接至相同的內部部署資料來源。 例如，如果您有兩個自我裝載整合執行時間來服務兩個資料處理站，則相同的內部部署資料來源可以同時向這兩個資料處理站註冊。
- 如果您的電腦上已安裝閘道來提供 Power BI 案例，請在另一部電腦上安裝個別的自我裝載整合執行時間，以便 Data Factory。
- 使用自我裝載整合執行時間支援 Azure 虛擬網路內的資料整合。
- 即使您使用 Azure ExpressRoute，也應該將資料來源視為在防火牆後的內部部署資料來源。 使用自我裝載整合執行時間，將服務連接到資料來源。
- 即使資料存放區位於 Azure 基礎結構即服務 (IaaS) 虛擬機器的雲端中，仍請使用自我裝載整合執行時間。
- 在已啟用 FIPS 相容加密的 Windows server 上安裝的自我裝載整合執行時間中，工作可能會失敗。 若要解決此問題，請在伺服器上停用符合 FIPS 規範的加密。 若要停用符合 FIPS 規範的加密，請將下列登錄子機碼值從 1 (已啟用) 變更為 0 (停用) ： `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled` 。

## <a name="prerequisites"></a>必要條件

- 支援的 Windows 版本為：
  + Windows 7 Service Pack 1
  + Windows 8。1
  + Windows 10
  + Windows Server 2008 R2 SP1
  + Windows Server 2012
  + Windows Server 2012 R2
  + Windows Server 2016
  + Windows Server 2019
   
   不支援在網域控制站上安裝自我裝載整合執行時間。
- 必須要有 .NET Framework 4.6.1 或更新版本。 如果您要在 Windows 7 電腦上安裝自我裝載整合執行階段，必須安裝 .NET Framework 4.6.1 或更新版本。 如需詳細資訊，請參閱 [.NET Framework 系統需求](/dotnet/framework/get-started/system-requirements) 。
- 「自我裝載整合執行時間」電腦的建議最小設定是具有4個核心、8 GB RAM 和 80 GB 可用硬碟空間的 2 GHz 處理器。
- 如果主機電腦休眠，自我裝載整合執行時間就不會回應資料要求。 因此，安裝自我裝載整合執行階段之前，請先在電腦上設定適當的電源計劃。 如果電腦已設定為休眠，自我裝載整合執行時間安裝程式會提示訊息。
- 您必須是電腦上的系統管理員，才能成功安裝和設定自我裝載整合執行時間。
- 複製-活動執行會以特定的頻率進行。 電腦上的處理器和 RAM 使用量遵循與尖峰和閒置時間相同的模式。 資源使用量也取決於移動的資料量。 如果有多個複製作業正在進行，您會看到資源使用量在尖峰時段增加。
- 在 Parquet、ORC 或 Avro 格式的資料解壓縮期間，工作可能會失敗。 如需 Parquet 的詳細資訊，請參閱 [Azure Data Factory 中的 Parquet 格式](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime)。 檔案建立會在自我裝載整合機器上執行。 若要如預期般運作，檔案建立需要下列必要條件：
    - [Visual C++ 2010](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe) 可轉散發套件封裝 (x64) 
    - 從 JRE 提供者（例如 [採用 OpenJDK](https://adoptopenjdk.net/)），JAVA 執行時間 (jre) 8 版。 確定 `JAVA_HOME` 已設定環境變數。

## <a name="installation-best-practices"></a>安裝最佳做法

您可以從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=39717)下載受控識別安裝套件來安裝自我裝載整合執行時間。 請參閱文章 [在內部部署和雲端之間移動資料](tutorial-hybrid-copy-powershell.md) ，以取得逐步指示。

- 為自我裝載整合執行時間設定主機電腦上的電源計劃，使電腦不會進入休眠狀態。 如果主機電腦休眠，自我裝載整合執行階段就會離線。
- 定期備份與自我裝載整合執行時間相關聯的認證。
- 若要將自我裝載 IR 設定作業自動化，請參閱透過 [PowerShell 設定現有的自我裝載 ir](#setting-up-a-self-hosted-integration-runtime)。  

## <a name="install-and-register-a-self-hosted-ir-from-microsoft-download-center"></a>從 Microsoft 下載中心安裝和註冊自我裝載 IR

1. 瀏覽至 [Microsoft 整合執行階段下載頁面](https://www.microsoft.com/download/details.aspx?id=39717)。
1. 選取 [ **下載**]，選取64位版本，然後選取 **[下一步]**。 不支援32位版本。
1. 直接執行受控識別檔案，或將它儲存至硬碟並加以執行。
1. 在 [ **歡迎使用** ] 視窗中選取語言，然後選取 **[下一步**]。
1. 接受「Microsoft 軟體授權條款」，然後選取 [下一步]****。
1. 選取 [資料夾]**** 來安裝自我裝載整合執行階段，接著按一下 [下一步]****。
1. 在 [ **安裝準備就緒** ] 頁面上，選取 [ **安裝**]。
1. 選取 **[完成]** 以完成安裝。
1. 使用 PowerShell 取得驗證金鑰。 擷取驗證金鑰的 PowerShell 範例：

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```

1. 在電腦上執行 Microsoft Integration Runtime Configuration Manager 的 [ **註冊 Integration Runtime (自我裝載) ** 視窗中，執行下列步驟：

    1. 將驗證金鑰貼到文字區域。

    1. (選擇性) 選取 [顯示驗證金鑰]**** 以查看金鑰文字。

    1. 選取 [註冊]。

## <a name="high-availability-and-scalability"></a>高可用性與延展性

您可以將自我裝載整合執行時間與 Azure 中的多個內部部署機器或虛擬機器產生關聯。 這些電腦稱為節點。 一個自我裝載整合執行階段最多可與四個節點建立關聯。 在內部部署機器上擁有多個節點的優點是針對邏輯閘道安裝了閘道：

* 自我裝載整合執行時間的高可用性，使其不再是您的大型資料解決方案或與 Data Factory 的雲端資料整合中的單一失敗點。 當您使用最多四個節點時，此可用性有助於確保持續性。
* 提升在內部部署和雲端資料存放區之間移動資料時的效能和輸送量。 如需詳細資訊，請參閱[效能比較](copy-activity-performance.md)。

您可以從 [下載中心](https://www.microsoft.com/download/details.aspx?id=39717)安裝自我裝載整合執行時間軟體，藉此建立多個節點的關聯。 然後，使用從 **AzDataFactoryV2IntegrationRuntimeKey** Cmdlet 取得的其中一個驗證金鑰進行註冊，如 [教學](tutorial-hybrid-copy-powershell.md)課程中所述。

> [!NOTE]
> 您不需要建立新的自我裝載整合執行時間來關聯每個節點。 您可以將自我裝載整合執行階段安裝在另一部電腦上，並使用相同的驗證金鑰進行註冊。

> [!NOTE]
> 在您新增其他節點以達到高可用性和擴充性之前，請確定已在第一個節點上啟用 [ **遠端存取內部** 網路] 選項。 若要這樣做， **Microsoft Integration Runtime Configuration Manager**請選取 [  >  **Settings**  >  **遠端存取內部**網路] Microsoft Integration Runtime Configuration Manager 設定。

### <a name="scale-considerations"></a>調整考量

#### <a name="scale-out"></a>擴增

當處理器使用量很高，且自我裝載 IR 上的可用記憶體不足時，請新增節點以協助在機器之間擴充負載。 如果活動因為過期或自我裝載 IR 節點已離線而失敗，則在將節點新增至閘道時，會有所説明。

#### <a name="scale-up"></a>相應增加

當處理器和可用的 RAM 未充分利用，但是並行作業的執行達到節點的限制時，會增加節點可執行檔並行作業數目來擴大規模。 您也可能想要在活動超時時擴大，因為自我裝載的 IR 超載。 您可以增加一個節點的容量上限，如下圖所示：  

![增加可在節點上執行的並行作業數目](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL 憑證需求

以下是您用來保護整合執行時間節點之間通訊的 TLS/SSL 憑證需求：

- 憑證必須是受信任的 X509 v3 公開憑證。 建議您使用由公用夥伴憑證授權單位單位所發行的憑證 (CA) 。
- 每個整合執行階段節點都必須信任此憑證。
- 我們不建議 (SAN) 憑證的主體別名，因為只會使用最後一個 SAN 專案。 系統會忽略所有其他 SAN 專案。 例如，如果您有 SAN 憑證，其 San 為 **node1.domain.contoso.com** 和 **node2.domain.contoso.com**，則只能在其完整功能變數名稱 (FQDN) 為 **node2.domain.contoso.com**的電腦上使用此憑證。
- 憑證可以針對 TLS/SSL 憑證使用 Windows Server 2012 R2 所支援的任何金鑰大小。
- 不支援使用 CNG 金鑰的憑證。  

> [!NOTE]
> 使用此憑證：
>
> - 加密自我裝載 IR 節點上的埠。
> - 適用于狀態同步處理的節點對節點通訊，包括跨節點的連結服務的認證同步處理。
> - 從區域網路內部將 PowerShell Cmdlet 用於連結服務認證設定時。
>
> 如果您的私人網路環境不安全，或您想要保護私人網路中節點之間的通訊，建議您使用此憑證。
>
> 從自我裝載 IR 傳輸到其他資料存放區的資料移動一律會在加密通道內進行，無論是否已設定此憑證。

## <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>在 Azure Data Factory 中建立共用的自我裝載整合執行階段

您可以重複使用您已經在資料處理站中設定的現有自我裝載整合執行階段基礎結構。 這項重複使用可讓您藉由參考現有的共用自我裝載 IR，在不同的資料處理站中建立連結的自我裝載整合執行時間。

若要查看這項功能的簡介與示範，請觀看下列12分鐘的影片：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>詞彙

- **共用 ir**：在實體基礎結構上執行的原始自我裝載 ir。  
- **連結的 ir**：參考另一個共用 IR 的 ir。 連結的 IR 是邏輯 IR，並使用另一個共用自我裝載 IR 的基礎結構。

### <a name="methods-to-share-a-self-hosted-integration-runtime"></a>共用自我裝載整合執行時間的方法

若要與多個資料處理站共用自我裝載整合執行時間，請參閱 [建立共用的自我裝載整合運行](create-shared-self-hosted-integration-runtime-powershell.md) 時間以取得詳細資料。

### <a name="monitoring"></a>監視

#### <a name="shared-ir"></a>共用 IR

![尋找共用整合執行時間的選取專案](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

![監視共用整合執行時間](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

#### <a name="linked-ir"></a>連結的 IR

![選取以尋找連結的整合執行時間](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

![監視連結的整合執行時間](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>自我裝載整合執行階段共用的已知限制

* 建立連結 IR 的資料處理站必須有 [受控識別](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)。 根據預設，在 Azure 入口網站或 PowerShell Cmdlet 中建立的資料處理站具有隱含建立的受控識別。 但是，透過 Azure Resource Manager 範本或 SDK 建立資料處理站時，您必須明確設定 **Identity** 屬性。 此設定可確保 Resource Manager 建立包含受控識別的 data factory。

* 支援此功能的 Data Factory .NET SDK 必須是1.1.0 版或更新版本。

* 若要授與許可權，您需要有共用 IR 存在之 data factory 中的擁有者角色或繼承的擁有者角色。

* 共用功能只適用于相同 Azure AD 租使用者內的資料處理站。

* 針對 Azure AD [來賓使用者](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews)，UI 中的搜尋功能（使用搜尋關鍵字列出所有資料處理站）將 [無法運作](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits)。 但只要來賓使用者是 data factory 的擁有者，您就可以在沒有搜尋功能的情況下共用 IR。 針對需要共用 IR 的資料處理站受控識別，請在 [ **指派許可權** ] 方塊中輸入該受控識別，然後選取 Data Factory UI 中的 [ **新增** ]。

  > [!NOTE]
  > 這項功能僅適用于 Data Factory V2。

## <a name="notification-area-icons-and-notifications"></a>通知區域圖示和通知

如果您將游標移至通知區域中的圖示或訊息，您可以查看自我裝載整合執行時間狀態的詳細資料。

![通知區域中的通知](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewalls"></a>埠和防火牆

有兩個要考慮的防火牆：

- 在組織的中央路由器上執行的*公司防火牆*
- 在安裝自我裝載整合執行時間的本機電腦上，設定為 daemon 的*Windows 防火牆*

![防火牆](media/create-self-hosted-integration-runtime/firewall.png)

在公司防火牆層級，您需要設定下列網域和輸出連接埠：

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]


在 Windows 防火牆層級或電腦層級上，通常會啟用這些輸出埠。 如果不是，您可以在自我裝載整合執行時間電腦上設定網域和埠。

> [!NOTE]
> 根據您的來源和接收器，您可能需要在公司防火牆或 Windows 防火牆中允許額外的網域和輸出埠。
>
> 針對某些雲端資料庫，例如 Azure SQL Database 和 Azure Data Lake，您可能需要允許其防火牆設定上的自我裝載整合執行時間電腦的 IP 位址。

### <a name="copy-data-from-a-source-to-a-sink"></a>將資料從來源複製到接收器

確定您已在公司防火牆、自我裝載整合執行時間電腦的 Windows 防火牆，以及資料存放區本身上，正確啟用防火牆規則。 啟用這些規則可讓自我裝載整合執行時間成功地連接到來源和接收。 請為複製作業所涉及的每個資料存放區啟用規則。

例如，若要從內部部署資料存放區複製到先前的 SQL 資料倉儲) 接收 SQL Database 接收或 Azure Synapse Analytics (，請執行下列步驟：

1. 針對 Windows 防火牆和公司防火牆，允許埠1433上的輸出 TCP 通訊。
1. 設定 SQL Database 的防火牆設定，將自我裝載整合執行時間電腦的 IP 位址新增至允許的 IP 位址清單。

> [!NOTE]
> 如果您的防火牆不允許輸出埠1433，自我裝載整合執行時間將無法直接存取 SQL 資料庫。 在此情況下，您可以使用 [分段複製](copy-activity-performance.md) 來 SQL Database 和 Azure Synapse Analytics。 在此案例中，您只需要 HTTPS (埠 443) 來移動資料。

## <a name="proxy-server-considerations"></a>Proxy 伺服器考量

如果您的公司網路環境使用 Proxy 伺服器來存取網際網路，請將自我裝載整合執行階段設定為使用適當的 Proxy 設定。 您可以在初始註冊階段期間設定 Proxy。

![指定 proxy](media/create-self-hosted-integration-runtime/specify-proxy.png)

當設定時，自我裝載整合執行時間會使用 proxy 伺服器來連線到雲端服務的來源和目的地 (使用 HTTP 或 HTTPS 通訊協定) 。 這就是您在初始安裝期間選取 **變更連結** 的原因。

![設定 proxy](media/create-self-hosted-integration-runtime/set-http-proxy.png)

有三個組態選項：

- **請勿使用 proxy**：自我裝載整合執行時間不會明確地使用任何 proxy 來連線到雲端服務。
- **使用系統 proxy**：自我裝載整合執行時間會使用 diahost.exe.config 和 diawp.exe.config 中所設定的 proxy 設定。如果這些檔案未指定 proxy 設定，自我裝載整合執行時間會直接連接到雲端服務，而不會通過 proxy。
- **使用自訂 proxy**：設定要用於自我裝載整合執行時間的 HTTP proxy 設定，而不是使用 diahost.exe.config 和 diawp.exe.config 中的設定。需要 **位址** 和 **埠** 值。 **使用者名稱** 和 **密碼** 值是選擇性的，視您的 proxy 驗證設定而定。 自我裝載整合執行階段上的所有設定都會使用 Windows DPAPI 加密，並儲存在本機電腦上。

當您儲存更新的 proxy 設定之後，integration runtime 主機服務會自動重新開機。

註冊自我裝載整合執行時間之後，如果您想要查看或更新 proxy 設定，請使用 Microsoft Integration Runtime Configuration Manager。

1. 開啟 [Microsoft Integration Runtime 管理員]****。
1. 選取 [Settings] \(設定\) 索引標籤。
1. 在 [ **Http proxy**] 底下，選取 [ **變更** ] 連結以開啟 [ **設定 HTTP proxy** ] 對話方塊。
1. 選取 [下一步] 。 接著，您會看到一則警告，要求您儲存 proxy 設定並重新啟動 integration runtime 主機服務的許可權。

您可以使用 configuration manager 工具來查看和更新 HTTP proxy。

![查看及更新 proxy](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> 如果您使用 NTLM 驗證設定 proxy 伺服器，integration runtime 主機服務會在網域帳戶下執行。 如果您稍後變更網域帳戶的密碼，請記得更新服務的設定，然後重新開機服務。 由於這項需求，我們建議您使用不需要經常更新密碼的私人網路域帳戶來存取 proxy 伺服器。

### <a name="configure-proxy-server-settings"></a>設定 Proxy 伺服器設定

如果您為 HTTP proxy 選取 [ **使用系統 proxy** ] 選項，自我裝載整合執行時間會使用 diahost.exe.config 和 diawp.exe.config 的 proxy 設定。當這些檔案未指定 proxy 時，自我裝載整合執行時間會直接連接到雲端服務，而不會通過 proxy。 下列程序說明如何更新 diahost.exe.config 檔案：

1. 在檔案總管中，建立 C:\Program Files\Microsoft 整合 Runtime\4.0\Shared\diahost.exe.config 的安全副本，做為原始檔案的備份。
1. 以系統管理員身分開啟 [記事本]。
1. 在 [記事本] 中，開啟 [文字檔 C:\Program Files\Microsoft Integration Runtime\4.0\Shared\diahost.exe.config]。
1. 尋找預設的 **system.net** 標記，如下列程式碼所示：

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    接著，您可以新增 Proxy 伺服器詳細資料，如以下範例所示：

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Proxy 標記可讓其他屬性指定必要的設定，例如 `scriptLocation` 。 如需語法，請參閱[ \<proxy\> (網路設定) 元素](https://msdn.microsoft.com/library/sa91de1e.aspx)。

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
1. 將設定檔儲存在其原始位置。 然後重新開機自我裝載 integration runtime 主機服務，以挑選變更。

   若要重新開機服務，請使用主控台中的 [服務] 小程式。 或從「整合執行階段組態管理員」中，選取 [停止服務]**** 按鈕，然後再選取 [啟動服務]****。

   如果服務未啟動，您可能在所編輯的應用程式佈建檔中新增了不正確的 XML 標記語法。

> [!IMPORTANT]
> 別忘了同時更新 diahost.exe.config 和 diawp.exe.config。

您也必須確認 Microsoft Azure 位於公司的允許清單中。 您可以從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=41653)下載有效的 Azure IP 位址清單。

### <a name="possible-symptoms-for-issues-related-to-the-firewall-and-proxy-server"></a>防火牆和 proxy 伺服器相關問題的可能徵兆

如果您看到類似下列的錯誤訊息，可能是因為防火牆或 proxy 伺服器的設定不正確。 這類設定可防止自我裝載的整合執行時間連接到 Data Factory，以驗證自己的身分。 請參閱上一節，以確保您的防火牆和 Proxy 伺服器的設定皆正確。

* 當您嘗試註冊自我裝載整合執行時間時，您會收到下列錯誤訊息：「無法註冊此 Integration Runtime 節點！ 請確認驗證金鑰有效，且 integration service 主機服務正在這部電腦上執行。
* 當您開啟「Integration Runtime 組態管理員」時，您會看到「已中斷連線」**** 或「正在連線」**** 狀態。 當您在 [**事件檢視器**應用程式和服務記錄檔] Microsoft Integration Runtime 下查看 Windows 事件記錄檔時  >  **Application and Services Logs**  >  ** **，您會看到如下的錯誤訊息：

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enable-remote-access-from-an-intranet"></a>啟用來自內部網路的遠端存取

如果您使用 PowerShell 從網路電腦加密認證，而不是安裝自我裝載整合執行時間，您可以啟用從內部網路進行 **遠端存取** 選項。 如果您在安裝自我裝載整合執行時間的電腦上執行 PowerShell 來加密認證，則無法啟用 **從內部網路進行遠端存取**。

在您新增其他節點以獲得高可用性和擴充性之前，請先啟用內部網路的 **遠端存取** 。  

當您執行自我裝載整合執行時間安裝3.3 版或更新版本時，自我裝載整合執行時間安裝程式預設會停用自我裝載整合執行時間電腦上內部網路的 **遠端存取** 。

當您使用夥伴或其他夥伴的防火牆時，可以手動開啟埠8060或使用者設定的埠。 如果您在設定自我裝載整合執行時間時遇到防火牆問題，請使用下列命令來安裝自我裝載整合執行時間，而不設定防火牆：

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

如果您選擇不開啟自我裝載整合執行時間電腦上的埠8060，請使用 [設定認證] 應用程式以外的機制來設定資料存放區認證。 例如，您可以使用 **AzDataFactoryV2LinkedServiceEncryptCredential** PowerShell Cmdlet。

## <a name="next-steps"></a>接下來的步驟

如需逐步指示，請參閱教學課程 [：將內部部署資料複製到雲端](tutorial-hybrid-copy-powershell.md)。
