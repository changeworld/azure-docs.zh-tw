---
title: 教學課程：在 Azure SQL Database 中使用安全記憶體保護區來開始使用 Always Encrypted
description: 本教學課程會教您如何使用 Azure SQL Database 的安全記憶體保護區建立 Always Encrypted 的基本環境，以及如何就地加密資料，以及如何使用 SQL Server Management Studio (SSMS) 針對加密資料行發出豐富的機密查詢。
keywords: 加密資料、sql 加密、資料庫加密、機密資料、Always Encrypted、安全記憶體保護區、SGX、證明
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: tutorial
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: 94923b13181290a290f13339da5b05f6fdddff38
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252134"
---
# <a name="tutorial-getting-started-with-always-encrypted-with-secure-enclaves-in-azure-sql-database"></a>教學課程：在 Azure SQL Database 中使用安全記憶體保護區來開始使用 Always Encrypted

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted 與 Azure SQL Database 的安全記憶體保護區目前處於 **公開預覽** 狀態。

本教學課程會教您如何以 Azure SQL Database [的安全記憶體保護區](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-enclaves) 開始使用 Always Encrypted。 它會顯示：

> [!div class="checklist"]
> - 如何使用安全記憶體保護區建立用來測試和評估 Always Encrypted 的環境。
> - 如何使用 SQL Server Management Studio (SSMS) ，就地加密資料並針對加密資料行發出豐富的機密查詢。

## <a name="prerequisites"></a>必要條件

本教學課程需要 Azure PowerShell 和 [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)。

### <a name="powershell-requirements"></a>PowerShell 需求

如需如何安裝和執行 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 概觀](https://docs.microsoft.com/powershell/azure)。 

支援證明作業所需的 Az 模組最低版本：

- Az 4.5.0
- Az.Accounts 1.9.2
- Az.Attestation 0.1.8

執行下列命令，以確認已安裝的所有 Az 模組版本：

```powershell
Get-InstalledModule
```

如果版本不符合最低需求，請執行 `Update-Module` 命令。

PowerShell 資源庫已淘汰傳輸層安全性 (TLS) 1.0 和1.1 版。 建議使用 TLS 1.2 或更新版本。 如果您使用的 TLS 版本低於1.2，您可能會收到下列錯誤：

- `WARNING: Unable to resolve package source 'https://www.powershellgallery.com/api/v2'`
- `PackageManagement\Install-Package: No match was found for the specified search criteria and module name.`

若要繼續與 PowerShell 資源庫互動，請在 Install-Module 命令之前執行下列命令

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
```

### <a name="ssms-requirements"></a>SSMS 需求

如需有關如何下載 SSMS 的詳細資訊，請參閱 [下載 SQL Server Management Studio (ssms) ](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 。

所需的 SSMS 最小版本為18.8。


## <a name="step-1-create-a-server-and-a-dc-series-database"></a>步驟1：建立伺服器和 DC 系列資料庫

 在此步驟中，您將使用 DC 系列硬體設定來建立新的 Azure SQL Database 邏輯伺服器和新的資料庫。 Always Encrypted 在 Azure SQL Database 中使用安全記憶體保護區，會使用 DC 系列硬體設定中支援的 Intel SGX 記憶體保護區。 如需詳細資訊，請參閱 [DC 系列](service-tiers-vcore.md#dc-series)。

1. 開啟 PowerShell 主控台並登入 Azure。 如有需要，請 [切換至](https://docs.microsoft.com/powershell/azure/manage-subscriptions-azureps) 您在本教學課程中使用的訂用帳戶。

  ```PowerShell
  Connect-AzAccount
  $subscriptionId = <your subscription ID>
  Set-AzContext -Subscription $serverSubscriptionId
  ```

2. 建立資源群組，以包含您的資料庫伺服器。 

  ```powershell
  $serverResourceGroupName = "<server resource group name>"
  $serverLocation = "<Azure region that supports DC-series in SQL Database>"
  New-AzResourceGroup -Name $serverResourceGroupName -Location $serverLocation 
  ```

  > [!IMPORTANT]
  > 您必須在支援 DC 系列硬體設定的區域中建立資源群組。 如需目前支援的區域清單，請參閱 [DC 系列的可用性](service-tiers-vcore.md#dc-series-1)。

3. 建立資料庫伺服器。 出現提示時，輸入伺服器系統管理員名稱和密碼。

  ```powershell
  $serverName = "<server name>" 
  New-AzSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -Location $serverLocation
  ```

4. 建立伺服器防火牆規則，以允許從指定的 IP 範圍進行存取
  
  ```powershell
  # The ip address range that you want to allow to access your server
  $startIp = "<start of IP range>"
  $endIp = "<end of IP range>"
  $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
  ```

5. 將受控系統身分識別指派給您的伺服器。 您稍後將需要它來授與伺服器存取 Microsoft Azure 證明。

  ```powershell
  Set-AzSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -AssignIdentity 
  ```

6. 抓取指派給您的伺服器之身分識別的物件識別碼。 儲存產生的物件識別碼。 您將需要稍後章節中的識別碼。

  > [!NOTE]
  > 新指派的受控系統身分識別可能需要幾秒鐘的時間才能傳播至 Azure Active Directory。 如果下列腳本傳回空白的結果，請重試。

  ```PowerShell
  $server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 
  $serverObjectId = $server.Identity.PrincipalId
  $serverObjectId
  ```

7. 建立 DC 系列資料庫。

  ```powershell
  $databaseName = "ContosoHR"
  $edition = "GeneralPurpose"
  $vCore = 2
  $generation = "DC"
  New-AzSqlDatabase -ResourceGroupName $serverResourceGroupName -ServerName $serverName -DatabaseName $databaseName -Edition $edition -Vcore $vCore -ComputeGeneration $generation
  ```

## <a name="step-2-configure-an-attestation-provider"></a>步驟2：設定證明提供者

在此步驟中，您將在 Microsoft Azure 證明中建立並設定證明提供者。 這是在資料庫伺服器中證明安全記憶體保護區的必要項。

1. 複製下列證明原則，並將原則儲存 (txt) 的文字檔。 如需下列原則的詳細資訊，請參閱 [建立和設定證明提供者](always-encrypted-enclaves-configure-attestation.md#create-and-configure-an-attestation-provider)。

  ```output
  version= 1.0;
  authorizationrules 
  {
       [ type=="x-ms-sgx-is-debuggable", value==false ]
        && [ type=="x-ms-sgx-product-id", value==4639 ]
        && [ type=="x-ms-sgx-svn", value>= 0 ]
        && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
    => permit();
  };
  ```

2. 匯入所需的 `Az.Accounts` 和版本 `Az.Attestation` 。  

  ```powershell
  Import-Module "Az.Accounts" -MinimumVersion "1.9.2"
  Import-Module "Az.Attestation" -MinimumVersion "0.1.8"
  ```

3. 為證明提供者建立資源群組。

  ```powershell
  $attestationLocation = $serverLocation
  $attestationResourceGroupName = "<attestation provider resource group name>"
  New-AzResourceGroup -Name $attestationResourceGroupName -Location $location  
  ```

4. 建立證明提供者。 

  ```powershell
  $attestationProviderName = "<attestation provider name>" 
  New-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName -Location $attestationLocation
  ```

5. 設定您的證明原則。
  
  ```powershell
  $policyFile = "<the pathname of the file from step 1 in this section"
  $teeType = "SgxEnclave"
  $policyFormat = "Text"
  $policy=Get-Content -path $policyFile -Raw
  Set-AzAttestationPolicy -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName -Tee $teeType -Policy $policy -PolicyFormat  $policyFormat
  ```

6. 將您的 Azure SQL 邏輯伺服器存取權授與您的證明提供者。 在此步驟中，我們會使用您稍早指派給伺服器的受控服務身分識別的物件識別碼。

  ```powershell
  New-AzRoleAssignment -ObjectId $serverObjectId -RoleDefinitionName "Attestation Reader" -ResourceGroupName $attestationResourceGroupName  
  ```

7. 取得證明 URL。

  ```powershell
  $attestationProvider = Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName 
  $attestationUrl = $attestationProvider.AttestUri + “/attest/SgxEnclave”
  Write-Host "Your attestation URL is: " $attestationUrl 
  ```

8.  儲存產生的證明 URL，指向您針對 SGX 記憶體保護區設定的證明原則。 稍後您將會用到此資訊。 證明 URL 看起來應該像這樣： `https://contososqlattestation.uks.attest.azure.net/attest/SgxEnclave`

## <a name="step-3-populate-your-database"></a>步驟3：填入您的資料庫

在此步驟中，您將建立一個資料表，並填入您稍後將加密和查詢的一些資料。

1. 開啟 SSMS，並聯機至您建立的 Azure SQL 邏輯伺服器中的 **ContosoHR** 資料庫， **而不** Always Encrypted 在資料庫連接中啟用。
    1. 在 [ **連接到伺服器** ] 對話方塊中，指定您的伺服器名稱 (例如 *Myserver123.database.windows.net*) ，然後輸入您稍早設定的使用者名稱和密碼。
    2. 按一下 [ **選項] >>** ，然後選取 [ **連接屬性** ] 索引標籤。請務必選取 **ContosoHR** 資料庫， (不是預設的 master 資料庫) 。 
    3. 選取 [Always Encrypted] 索引標籤。
    4. 請確定 **未** 選取 [啟用 Always Encrypted (資料行加密)] 核取方塊。

        ![無 Always Encrypted 連接](media/always-encrypted-enclaves/connect-without-always-encrypted-ssms.png)

    5. 按一下 [連線]。

2. 建立名為 **Employees** 的新資料表。

    ```sql
    CREATE SCHEMA [HR];
    GO

    CREATE TABLE [HR].[Employees]
    (
        [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
        [SSN] [char](11) NOT NULL,
        [FirstName] [nvarchar](50) NOT NULL,
        [LastName] [nvarchar](50) NOT NULL,
        [Salary] [money] NOT NULL
    ) ON [PRIMARY];
    GO
    ```

3. 在 **Employees** 資料表中新增一些員工記錄。

    ```sql
    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('795-73-9838'
            , N'Catherine'
            , N'Abel'
            , $31692);

    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('990-00-6818'
            , N'Kim'
            , N'Abercrombie'
            , $55415);
    ```


## <a name="step-4-provision-enclave-enabled-keys"></a>步驟4：布建啟用記憶體保護區的金鑰

在此步驟中，您將建立資料行主要金鑰和資料行加密金鑰，以允許記憶體保護區計算。

1. 使用上個步驟中的 SSMS 執行個體，在 **物件總管** 中展開您的資料庫，然後巡覽至 [安全性] > [Always Encrypted 金鑰]。
1. 佈建已啟用記憶體保護區的新資料行主要金鑰：
    1. 以滑鼠右鍵按一下 [Always Encrypted 金鑰]，然後選取 [新增資料行主要金鑰]。
    2. 選取您的資料行主要金鑰名稱：**CMK1**。
    3. 請確定您選取 [Windows 憑證存放區 (目前的使用者或本機電腦)] 或 [Azure Key Vault]。
    4. 選取 [允許記憶體保護區運算]。
    5. 如果您選取 Azure Key Vault，請登入 Azure，然後選取您的金鑰保存庫。 如需如何建立 Always Encrypted 金鑰保存庫的詳細資訊，請參閱 [Manage your key vaults from Azure portal](/archive/blogs/kv/manage-your-key-vaults-from-new-azure-portal) (從 Azure 入口網站管理金鑰保存庫)。
    6. 選取您的憑證或 Azure Key Vault 金鑰 (如果已經存在)，或按一下 [產生憑證] 按鈕來建立一個新的憑證。
    7. 選取 [確定]。

        ![允許記憶體保護區運算](media/always-encrypted-enclaves/allow-enclave-computations.png)

1. 建立已啟用記憶體保護區的新資料行加密金鑰：

    1. 以滑鼠右鍵按一下 [Always Encrypted 金鑰]，然後選取 [新增資料行加密金鑰]。
    2. 輸入新資料行加密金鑰的名稱：**CEK1**。
    3. 在 [資料行主要金鑰] 下拉式清單中，選取您在先前步驟中建立的資料行主要金鑰。
    4. 選取 [確定]。

## <a name="step-5-encrypt-some-columns-in-place"></a>步驟5：就地加密某些資料行

在此步驟中，您將加密儲存在伺服器端記憶體保護區內 **SSN** 和 **薪資** 資料行中的資料，然後測試資料的 SELECT 查詢。

1. 開啟新的 SSMS 實例，並連接到您的資料庫，並針對資料庫連接 **啟用 Always Encrypted。**
    1. 啟動新的 SSMS 執行個體。
    2. 在 [ **連接到伺服器** ] 對話方塊中，指定您的伺服器名稱、選取驗證方法，然後指定您的認證。
    3. 按一下 [ **選項] >>** ，然後選取 [ **連接屬性** ] 索引標籤。請務必選取 **ContosoHR** 資料庫， (不是預設的 master 資料庫) 。 
    4. 選取 [Always Encrypted] 索引標籤。
    5. 確認已選取 [ **啟用 Always Encrypted (資料行加密])** 核取方塊。
    6. 遵循 [步驟2：設定證明提供者](#step-2-configure-an-attestation-provider)中的步驟，指定您所取得的記憶體保護區證明 URL。 請參閱下列螢幕擷取畫面。

        ![使用證明連接](media/always-encrypted-enclaves/connect-to-server-configure-attestation.png)

    7. 選取 [連線]。
    8. 如果系統提示您啟用 Always Encrypted 查詢的參數化，請選取 [ **啟用**]。



1. 使用相同的 SSMS 實例 (搭配 Always Encrypted 啟用的) ，開啟新的查詢視窗，並執行下列語句來加密 **SSN** 和 **薪資** 資料行。

    ```sql
    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [SSN] [char] (11) COLLATE Latin1_General_BIN2
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [Salary] [money]
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER DATABASE SCOPED CONFIGURATION CLEAR PROCEDURE_CACHE;
    ```

    > [!NOTE]
    > 請注意上述指令碼中用來清除資料庫查詢計畫快取的 ALTER DATABASE SCOPED CONFIGURATION CLEAR PROCEDURE_CACHE 陳述式。 更改資料表之後，您必須清除存取資料表的所有批次和預存程式的計畫，以重新整理參數加密資訊。 

1. 為驗證 **SSN** 和 **Salary** 資料行現已加密，請在 **未** 針對資料庫連接啟用 Always Encrypted 的 SSMS 執行個體中開啟新的查詢視窗，並執行以下陳述式。 查詢視窗應該傳回 **SSN** 和 **Salary** 資料行中加密的值。 如果使用已啟用 Always Encrypted 的 SSMS 執行個體執行同一查詢，您應該會看到解密的資料。

    ```sql
    SELECT * FROM [HR].[Employees];
    ```

## <a name="step-6-run-rich-queries-against-encrypted-columns"></a>步驟6：針對加密的資料行執行豐富的查詢

您可以針對加密的資料行執行豐富的查詢。 在伺服器端記憶體保護區內，將會執行一些查詢處理。 

1. 在 **已** 啟用 Always Encrypted 的 SSMS 執行個體中，請確定也已啟用 Always Encrypted 的參數化。
    1. 從 SSMS 的主功能表選取 [工具]。
    2. 選取 [選項]。
    3. 瀏覽至 [查詢執行] > [SQL Server] > [進階]。
    4. 確定已選取 [啟用 Always Encrypted 的參數化]。
    5. 選取 [確定]。
2. 開啟新的查詢視窗，貼上下列查詢，然後執行。 查詢應該會傳回純文字值和符合指定搜尋準則的資料列。

    ```sql
    DECLARE @SSNPattern [char](11) = '%6818';
    DECLARE @MinSalary [money] = $1000;
    SELECT * FROM [HR].[Employees]
    WHERE SSN LIKE @SSNPattern AND [Salary] >= @MinSalary;
    ```

3. 在未啟用 Always Encrypted 的 SSMS 實例中，再次嘗試相同的查詢。 應會發生失敗。
 
## <a name="next-steps"></a>後續步驟

完成本教學課程之後，您可以移至下列其中一個教學課程：
- [教學課程：使用具有安全記憶體保護區的 Always Encrypted 開發 .NET 應用程式](https://docs.microsoft.com/sql/connect/ado-net/sql/tutorial-always-encrypted-enclaves-develop-net-apps)
- [教學課程：使用具有安全記憶體保護區的 Always Encrypted 開發 .NET Framework 應用程式](https://docs.microsoft.com/sql/relational-databases/security/tutorial-always-encrypted-enclaves-develop-net-framework-apps)
- [教學課程：使用隨機化加密在已啟用記憶體保護區的資料行上建立及使用索引](https://docs.microsoft.com/sql/relational-databases/security/tutorial-creating-using-indexes-on-enclave-enabled-columns-using-randomized-encryption)

## <a name="see-also"></a>另請參閱

- [設定和使用具有安全記憶體保護區的 Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-always-encrypted-enclaves)