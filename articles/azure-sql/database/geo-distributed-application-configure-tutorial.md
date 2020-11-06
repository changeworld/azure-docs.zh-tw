---
title: 實行地理位置分散的解決方案
description: 瞭解如何在 Azure SQL Database 與用戶端應用程式中設定您的資料庫，以便容錯移轉至複寫的資料庫，以及測試容錯移轉。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurecli, devx-track-azurepowershell
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 03/12/2019
ms.openlocfilehash: 98e3eb4927b8eb9e52fd974c1ef7c417aff2ad54
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422785"
---
# <a name="tutorial-implement-a-geo-distributed-database-azure-sql-database"></a>教學課程： (Azure SQL Database) 執行異地分散式資料庫
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

在 SQL Database 和用戶端應用程式中設定資料庫，以便容錯移轉至遠端區域及測試容錯移轉計畫。 您會了解如何：

> [!div class="checklist"]
>
> - 建立 [容錯移轉群組](auto-failover-group-overview.md)
> - 在 SQL Database 中執行 JAVA 應用程式以查詢資料庫
> - 測試容錯移轉

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Azure SQL Database 仍然支援 PowerShell Azure Resource Manager 模組，但所有未來的開發都是針對 Az.Sql 模組。 如需這些 Cmdlet，請參閱 [AzureRM.Sql](/powershell/module/AzureRM.Sql/) \(英文\)。 Az 模組和 AzureRm 模組中命令的引數本質上完全相同。

若要完成本教學課程，請確定您已安裝下列項目：

- [Azure PowerShell](/powershell/azure/)
- Azure SQL Database 中的單一資料庫。 若要建立一次使用，
  - [Azure 入口網站](single-database-create-quickstart.md)
  - [Azure CLI](az-cli-script-samples-content-guide.md)
  - [PowerShell](powershell-script-content-guide.md)

  > [!NOTE]
  > 教學課程使用 *AdventureWorksLT* 範例資料庫。

- Java 和 Maven，請參閱 [使用 SQL Server 建立應用程式](https://www.microsoft.com/sql-server/developer-get-started/)，反白顯示 **Java** 並選取您的環境，然後遵循步驟進行。

> [!IMPORTANT]
> 務必設定防火牆規則，使用您在本教學課程中執行步驟的電腦所用的公用 IP 位址。 資料庫層級防火牆規則將會自動複寫到次要伺服器。
>
> 如需詳細資訊，請參閱 [建立資料庫層級防火牆規則](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) 或判斷用於電腦之伺服器層級防火牆規則的 IP 位址，請參閱 [建立伺服器層級防火牆](firewall-create-server-level-portal-quickstart.md)。  

## <a name="create-a-failover-group"></a>建立容錯移轉群組

使用 Azure PowerShell，在現有的伺服器與另一個區域中的新伺服器之間建立 [容錯移轉群組](auto-failover-group-overview.md) 。 然後將範例資料庫新增到容錯移轉群組。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

若要建立容錯移轉群組，請執行下列指令碼：

```powershell
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>"
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
New-AzSqlServer -ResourceGroupName $resourceGroup -ServerName $drServer `
    -Location $drLocation -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
    -ArgumentList $admin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# create a failover group between the servers
New-AzSqlDatabaseFailoverGroup –ResourceGroupName $resourceGroup -ServerName $server `
    -PartnerServerName $drServer –FailoverGroupName $failoverGroup –FailoverPolicy Automatic -GracePeriodWithDataLossHours 2

# add the database to the failover group
Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $server -DatabaseName $database | `
    Add-AzSqlDatabaseToFailoverGroup -ResourceGroupName $resourceGroup -ServerName $server -FailoverGroupName $failoverGroup
```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!IMPORTANT]
> 執行 `az login` 以登入 Azure。

```azurecli
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>" # must be different then $location
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
az sql server create --admin-password $password --admin-user $admin `
    --name $drServer --resource-group $resourceGroup --location $drLocation

# create a failover group between the servers
az sql failover-group create --name $failoverGroup --partner-server $drServer `
    --resource-group $resourceGroup --server $server --add-db $database `
    --failover-policy Automatic --grace-period 2
```

* * *

您也可以在 Azure 入口網站中變更異地複寫設定，方法是選取您的資料庫，然後選取 [異地複寫 **設定** ]  >  **** 。

![異地複寫設定](./media/geo-distributed-application-configure-tutorial/geo-replication.png)

## <a name="run-the-sample-project"></a>執行範例專案

1. 在主控台中，使用下列命令建立 Maven 專案：

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. 輸入 **Y** ，然後按 **ENTER** 。

1. 將目錄變更為新的專案。

   ```bash
   cd SqlDbSample
   ```

1. 使用您最愛的編輯器，開啟專案資料夾中的 *pom.xml* 檔案。

1. 新增下列 `dependency` 區段，以便新增 Microsoft JDBC Driver for SQL Server 相依性。 相依性必須貼入較大的 `dependencies` 區段內。

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. 在 `dependencies` 區段之後新增 `properties` 區段來指定 Java 版本：

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. 在 `properties` 區段之後新增 `build` 區段來支援資訊清單檔：

   ```xml
   <build>
     <plugins>
       <plugin>
         <groupId>org.apache.maven.plugins</groupId>
         <artifactId>maven-jar-plugin</artifactId>
         <version>3.0.0</version>
         <configuration>
           <archive>
             <manifest>
               <mainClass>com.sqldbsamples.App</mainClass>
             </manifest>
           </archive>
        </configuration>
       </plugin>
     </plugins>
   </build>
   ```

1. 儲存並關閉 *pom.xml* 檔案。

1. 開啟位於 ..\SqlDbSample\src\main\java\com\sqldbsamples 中的 *App.java* 檔案，並將內容取代為下列內容：

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String FAILOVER_GROUP_NAME = "<your failover group name>";  // add failover group name
  
      private static final String DB_NAME = "<your database>";  // add database name
      private static final String USER = "<your admin>";  // add database user
      private static final String PASSWORD = "<your password>";  // add database password

      private static final String READ_WRITE_URL = String.format("jdbc:" +
         "sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:" +
         "sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println("");

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " +
                   (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " +
                   (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name so we can find the product again
      String sql = "INSERT INTO SalesLT.Product " +
         "(Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(READ_ONLY_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id stored in the table to be able to make unique inserts
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```

1. 儲存並關閉 *應用程式 .java* 檔案。

1. 在命令主控台中，執行下列命令：

   ```bash
   mvn package
   ```

1. 啟動應用程式執行大約 1 小時，直到手動停止為止，以便您有時間執行容錯移轉測試。

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   ```output
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ...
   ```

## <a name="test-failover"></a>測試容錯移轉

執行下列指令碼，以模擬容錯移轉，並觀察應用程式結果。 請注意，在資料庫移轉期間，一些插入和選取會失敗。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

您可以使用下列命令，在測試期間檢查嚴重損壞修復伺服器的角色：

```powershell
(Get-AzSqlDatabaseFailoverGroup -FailoverGroupName $failoverGroup `
    -ResourceGroupName $resourceGroup -ServerName $drServer).ReplicationRole
```

若要測試容錯移轉：

1. 啟動容錯移轉群組的手動容錯移轉：

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $resourceGroup `
    -ServerName $drServer -FailoverGroupName $failoverGroup
   ```

1. 將容錯移轉群組還原至主要伺服器：

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $resourceGroup `
    -ServerName $server -FailoverGroupName $failoverGroup
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

您可以使用下列命令，在測試期間檢查嚴重損壞修復伺服器的角色：

```azurecli
az sql failover-group show --name $failoverGroup --resource-group $resourceGroup --server $drServer
```

若要測試容錯移轉：

1. 啟動容錯移轉群組的手動容錯移轉：

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $drServer
   ```

1. 將容錯移轉群組還原至主要伺服器：

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   ```

* * *

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已在 Azure SQL Database 中設定資料庫，並在應用程式中設定容錯移轉至遠端區域及測試容錯移轉計畫的應用程式。 您已了解如何︰

> [!div class="checklist"]
>
> - 建立異地複寫容錯移轉群組
> - 在 SQL Database 中執行 JAVA 應用程式以查詢資料庫
> - 測試容錯移轉

前進到下一個教學課程，以瞭解如何將 Azure SQL 受控執行個體的實例新增至容錯移轉群組：

> [!div class="nextstepaction"]
> [將 Azure SQL 受控執行個體的實例新增至容錯移轉群組](../managed-instance/failover-group-add-instance-tutorial.md)
