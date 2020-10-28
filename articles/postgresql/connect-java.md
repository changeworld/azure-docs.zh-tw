---
title: 搭配使用 Java 和 JDBC 與適用於 PostgreSQL 的 Azure 資料庫
description: 了解如何搭配使用 Java 和 JDBC 與適用於 PostgreSQL 的 Azure 資料庫。
author: jdubois
ms.author: judubois
ms.service: postgresql
ms.custom: mvc, devcenter, devx-track-azurecli
ms.topic: quickstart
ms.devlang: java
ms.date: 08/17/2020
ms.openlocfilehash: 402e69606196ecb1030023c49fa5afefd31b9e94
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92734977"
---
# <a name="use-java-and-jdbc-with-azure-database-for-postgresql"></a>搭配使用 Java 和 JDBC 與適用於 PostgreSQL 的 Azure 資料庫

本主題示範如何建立使用 Java 和 [JDBC](https://en.wikipedia.org/wiki/Java_Database_Connectivity) 的範例應用程式，以在[適用於 PostgreSQL 的 Azure 資料庫](./index.yml)中儲存及擷取資訊。

JDBC 是用來連線到傳統關聯式資料庫的標準 JAVA API。

## <a name="prerequisites"></a>必要條件

- 一個 Azure 帳戶。 如果您沒有帳戶，請[取得免費試用帳戶](https://azure.microsoft.com/free/)。
- [Azure Cloud Shell](../cloud-shell/quickstart.md) 或 [Azure CLI](/cli/azure/install-azure-cli)。 建議使用 Azure Cloud Shell，因此您將會自動登入，並可存取您需要的所有工具。
- 支援的 [JAVA 開發工具組](/azure/developer/java/fundamentals/java-jdk-long-term-support) 版本 8 (包含在 Azure Cloud Shell 中)。
- [Apache Maven](https://maven.apache.org/) 建置工具。

## <a name="prepare-the-working-environment"></a>準備工作環境

我們將使用環境變數來限制輸入錯誤，並讓您更輕鬆地依特定需求自訂下列組態。

使用下列命令設定這些環境變數：

```bash
AZ_RESOURCE_GROUP=database-workshop
AZ_DATABASE_NAME=<YOUR_DATABASE_NAME>
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_POSTGRESQL_USERNAME=demo
AZ_POSTGRESQL_PASSWORD=<YOUR_POSTGRESQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

將預留位置取代為下列值，本文通篇都將使用這些值：

- `<YOUR_DATABASE_NAME>`:您的 PostgreSQL 伺服器名稱。 此名稱在整個 Azure 中必須是唯一的。
- `<YOUR_AZURE_REGION>`:您將使用的 Azure 區域。 根據預設，您可以使用 `eastus`，但建議您將區域設定為您居住地附近的位置。 您可以輸入 `az account list-locations`，以取得可用區域的完整清單。
- `<YOUR_POSTGRESQL_PASSWORD>`:您的 PostgreSQL 資料庫伺服器密碼。 該密碼至少要有八個字元。 這些字元應屬於下列三種類別：英文大寫字母、英文小寫字母、數字 (0-9) 及非英數字元 (!、$、#、% 等等)。
- `<YOUR_LOCAL_IP_ADDRESS>`:本機電腦的 IP 位址，您將在此執行 java 應用程式。 您可以將瀏覽器指向 [whatismyip.akamai.com](http://whatismyip.akamai.com/)，以便找出此資訊。

接下來，使用下列命令建立資源群組：

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
  	| jq
```

> [!NOTE]
> 我們會使用 `jq` 公用程式來顯示 JSON 資料，並使其更容易閱讀。 這個公用程式預設會安裝在 [Azure Cloud Shell](https://shell.azure.com/) 上。 如果您想使用該公用程式，可以安全地將所有會使用之命令的 `| jq` 部分移除。

## <a name="create-an-azure-database-for-postgresql-instance"></a>建立「適用於 PostgreSQL 的 Azure 資料庫」執行個體

我們首先要建立的是受控 PostgreSQL 伺服器。

> [!NOTE]
> 您可以在[使用 Azure 入口網站建立適用於 PostgreSQL 的 Azure 資料庫伺服器](./quickstart-create-server-database-portal.md)中，閱讀更多關於如何建立 PostgreSQL 伺服器的詳細資訊。

在 [Azure Cloud Shell](https://shell.azure.com/) 中執行下列命令：

```azurecli
az postgres server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --sku-name B_Gen5_1 \
    --storage-size 5120 \
    --admin-user $AZ_POSTGRESQL_USERNAME \
    --admin-password $AZ_POSTGRESQL_PASSWORD \
  	| jq
```

此命令會建立小型 PostgreSQL 伺服器。

### <a name="configure-a-firewall-rule-for-your-postgresql-server"></a>設定 PostgreSQL 伺服器的防火牆規則

適用於 PostgreSQL 的 Azure 資料庫執行個體依預設會受到保護。 其防火牆不允許任何連入連線。 若要能夠使用您的資料庫，您必須新增防火牆規則，讓本機 IP 位址能夠存取資料庫伺服器。

由於您已在本文開頭設定本機 IP 位址，您可以執行下列命令來開啟伺服器的防火牆：

```azurecli
az postgres server firewall-rule create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME-database-allow-local-ip \
    --server $AZ_DATABASE_NAME \
    --start-ip-address $AZ_LOCAL_IP_ADDRESS \
    --end-ip-address $AZ_LOCAL_IP_ADDRESS \
  	| jq
```

### <a name="configure-a-postgresql-database"></a>設定 PostgreSQL 資料庫

您先前建立的 PostgreSQL 伺服器是空的。 其中未包含任何可供 Java 應用程式使用的資料庫。 使用下列命令建立名為 `demo` 的新資料庫：

```azurecli
az postgres db create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name demo \
    --server-name $AZ_DATABASE_NAME \
  	| jq
```

### <a name="create-a-new-java-project"></a>建立新的 Java 專案

使用您最愛的 IDE，建立新的 Java 專案，並在其根目錄中新增 `pom.xml` 檔案：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>demo</name>

    <properties>
        <java.version>1.8</java.version>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
            <version>42.2.12</version>
        </dependency>
    </dependencies>
</project>
```

此檔案是 [Apache Maven](https://maven.apache.org/)，可設定要使用的專案：

- Java 8
- 適用於 Java 的最新 PostgreSQL 驅動程式

### <a name="prepare-a-configuration-file-to-connect-to-azure-database-for-postgresql"></a>準備用來連線到適用於 PostgreSQL 的 Azure 資料庫的組態檔

建立 src/main/resources/application.properties 檔案，然後新增：

```properties
url=jdbc:postgresql://$AZ_DATABASE_NAME.postgres.database.azure.com:5432/demo?ssl=true&sslmode=require
user=demo@$AZ_DATABASE_NAME
password=$AZ_POSTGRESQL_PASSWORD
```

- 將兩個 `$AZ_DATABASE_NAME` 變數取代為您在本文開頭所設定的值。
- 將 `$AZ_POSTGRESQL_PASSWORD` 變數取代為您在本文開頭所設定的值。

> [!NOTE]
> 我們會將 `?ssl=true&sslmode=require` 附加至組庇屬性 `url`，以指示 JDBC 驅動程式在連線到資料庫時，使用 TLS ([傳輸層安全性](https://en.wikipedia.org/wiki/Transport_Layer_Security))。 您必須使用 TLS 搭配適用於 PostgreSQL 的 Azure 資料庫，這是很好的安全性做法。

### <a name="create-an-sql-file-to-generate-the-database-schema"></a>建立 SQL 檔案以產生資料庫結構描述

我們將使用 src/main/resources/`schema.sql` 檔案，以建立資料庫結構描述。 建立含有下列內容的檔案：

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
```

## <a name="code-the-application"></a>撰寫應用程式程式碼

### <a name="connect-to-the-database"></a>連接至資料庫

接下來，新增將使用 JDBC 的 Java 程式碼，以從您的 PostgreSQL 伺服器儲存和擷取資料。

建立 src/main/java/DemoApplication.java 檔案，其中包含：

```java
package com.example.demo;

import java.sql.*;
import java.util.*;
import java.util.logging.Logger;

public class DemoApplication {

    private static final Logger log;

    static {
        System.setProperty("java.util.logging.SimpleFormatter.format", "[%4$-7s] %5$s %n");
        log =Logger.getLogger(DemoApplication.class.getName());
    }

    public static void main(String[] args) throws Exception {
        log.info("Loading application properties");
        Properties properties = new Properties();
        properties.load(DemoApplication.class.getClassLoader().getResourceAsStream("application.properties"));

        log.info("Connecting to the database");
        Connection connection = DriverManager.getConnection(properties.getProperty("url"), properties);
        log.info("Database connection test: " + connection.getCatalog());

        log.info("Create database schema");
        Scanner scanner = new Scanner(DemoApplication.class.getClassLoader().getResourceAsStream("schema.sql"));
        Statement statement = connection.createStatement();
        while (scanner.hasNextLine()) {
            statement.execute(scanner.nextLine());
        }

        /*
        Todo todo = new Todo(1L, "configuration", "congratulations, you have set up JDBC correctly!", true);
        insertData(todo, connection);
        todo = readData(connection);
        todo.setDetails("congratulations, you have updated data!");
        updateData(todo, connection);
        deleteData(todo, connection);
        */

        log.info("Closing database connection");
        connection.close();
    }
}
```

此 Java 程式碼會使用 application.properties 以及稍早建立的 schema.sql 檔案，以與 PostgreSQL 伺服器連線並建立將用來儲存資料的結構描述。

在此檔案中，您可以看到我們已針對插入、讀取、更新和刪除資料的方法加上註解，並在本文的其他部分撰寫這些方法的程式碼，讓您能夠逐一取消這些方法的註解。

> [!NOTE]
> 資料庫認證會儲存在 application.properties 檔案的 user 和 password 屬性中。 這些認證會在執行 `DriverManager.getConnection(properties.getProperty("url"), properties);` 時使用，因為屬性檔會作為引數傳遞。

您現在可以使用慣用的工具來執行此主要類別：

- 使用 IDE 時，您應該能夠以滑鼠右鍵按一下 DemoApplication 類別，然後加以執行。
- 您可以使用 Maven 執行 `mvn exec:java -Dexec.mainClass="com.example.demo.DemoApplication"` 以執行應用程式。

應用程式應該與適用於 PostgreSQL 的 Azure 資料庫連線、建立資料庫結構描述，然後關閉連線，接著您應該會在主控台記錄中看到：

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Closing database connection 
```

### <a name="create-a-domain-class"></a>建立領域類別

在 `DemoApplication` 類別旁建立新的 `Todo` Java 類別，並新增下列程式碼：

```java
package com.example.demo;

public class Todo {

    private Long id;
    private String description;
    private String details;
    private boolean done;

    public Todo() {
    }

    public Todo(Long id, String description, String details, boolean done) {
        this.id = id;
        this.description = description;
        this.details = details;
        this.done = done;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    public String getDetails() {
        return details;
    }

    public void setDetails(String details) {
        this.details = details;
    }

    public boolean isDone() {
        return done;
    }

    public void setDone(boolean done) {
        this.done = done;
    }

    @Override
    public String toString() {
        return "Todo{" +
                "id=" + id +
                ", description='" + description + '\'' +
                ", details='" + details + '\'' +
                ", done=" + done +
                '}';
    }
}
```

這個類別是在您執行 schema.sql 指令碼時建立的 `todo` 資料表中對應的領域模型。

### <a name="insert-data-into-azure-database-for-postgresql"></a>將資料插入至適用於 PostgreSQL 的 Azure 資料庫

在 src/main/java/DemoApplication.jav 檔案中，在 main 方法後面新增下列方法，以將資料插入資料庫：

```java
private static void insertData(Todo todo, Connection connection) throws SQLException {
    log.info("Insert data");
    PreparedStatement insertStatement = connection
            .prepareStatement("INSERT INTO todo (id, description, details, done) VALUES (?, ?, ?, ?);");

    insertStatement.setLong(1, todo.getId());
    insertStatement.setString(2, todo.getDescription());
    insertStatement.setString(3, todo.getDetails());
    insertStatement.setBoolean(4, todo.isDone());
    insertStatement.executeUpdate();
}
```

您現在可以取消 `main` 方法中下列兩行的註解：

```java
Todo todo = new Todo(1L, "configuration", "congratulations, you have set up JDBC correctly!", true);
insertData(todo, connection);
```

執行主要類別現在應該會產生下列輸出：

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Closing database connection
```

### <a name="reading-data-from-azure-database-for-postgresql"></a>從適用於 PostgreSQL 的 Azure 資料庫讀取資料

讓我們讀取先前插入的資料，以驗證程式碼是否正確運作。

在 src/main/java/DemoApplication.java 檔案中的 `insertData` 方法後面新增下列方法，以讀取資料庫中的資料：

```java
private static Todo readData(Connection connection) throws SQLException {
    log.info("Read data");
    PreparedStatement readStatement = connection.prepareStatement("SELECT * FROM todo;");
    ResultSet resultSet = readStatement.executeQuery();
    if (!resultSet.next()) {
        log.info("There is no data in the database!");
        return null;
    }
    Todo todo = new Todo();
    todo.setId(resultSet.getLong("id"));
    todo.setDescription(resultSet.getString("description"));
    todo.setDetails(resultSet.getString("details"));
    todo.setDone(resultSet.getBoolean("done"));
    log.info("Data read from the database: " + todo.toString());
    return todo;
}
```

您現在可以取消 `main` 方法中下列行的註解：

```java
todo = readData(connection);
```

執行主要類別現在應該會產生下列輸出：

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Closing database connection 
```

### <a name="updating-data-in-azure-database-for-postgresql"></a>更新「適用於 PostgreSQL 的 Azure 資料庫」中的資料

讓我們更新先前插入的資料。

在 src/main/java/DemoApplication.java 檔案中的 `readData` 方法後面新增下列方法，以更新資料庫中的資料：

```java
private static void updateData(Todo todo, Connection connection) throws SQLException {
    log.info("Update data");
    PreparedStatement updateStatement = connection
            .prepareStatement("UPDATE todo SET description = ?, details = ?, done = ? WHERE id = ?;");

    updateStatement.setString(1, todo.getDescription());
    updateStatement.setString(2, todo.getDetails());
    updateStatement.setBoolean(3, todo.isDone());
    updateStatement.setLong(4, todo.getId());
    updateStatement.executeUpdate();
    readData(connection);
}
```

您現在可以取消 `main` 方法中下列兩行的註解：

```java
todo.setDetails("congratulations, you have updated data!");
updateData(todo, connection);
```

執行主要類別現在應該會產生下列輸出：

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Update data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have updated data!', done=true} 
[INFO   ] Closing database connection 
```

### <a name="deleting-data-in-azure-database-for-postgresql"></a>刪除「適用於 PostgreSQL 的 Azure 資料庫」中的資料

最後，讓我們刪除先前插入的資料。

在 src/main/java/DemoApplication.java 檔案中的 `updateData` 方法後面新增下列方法，以刪除資料庫中的資料：

```java
private static void deleteData(Todo todo, Connection connection) throws SQLException {
    log.info("Delete data");
    PreparedStatement deleteStatement = connection.prepareStatement("DELETE FROM todo WHERE id = ?;");
    deleteStatement.setLong(1, todo.getId());
    deleteStatement.executeUpdate();
    readData(connection);
}
```

您現在可以取消 `main` 方法中下列行的註解：

```java
deleteData(todo, connection);
```

執行主要類別現在應該會產生下列輸出：

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Update data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have updated data!', done=true} 
[INFO   ] Delete data 
[INFO   ] Read data 
[INFO   ] There is no data in the database! 
[INFO   ] Closing database connection 
```

## <a name="conclusion-and-resources-clean-up"></a>結論和資源清除

恭喜！ 您建立了一個 Java 應用程式，會使用 JDBC 從適用於 PostgreSQL 的 Azure 資料庫中儲存和擷取資料。

若要清除在此快速入門期間使用的所有資源，請使用下列命令刪除資源群組：

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [使用匯出和匯入來移轉資料庫](./howto-migrate-using-export-and-import.md)
