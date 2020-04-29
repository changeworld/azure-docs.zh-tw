---
title: 設定 SSL-適用於 MariaDB 的 Azure 資料庫
description: 有關如何適當設定「適用於 MariaDB 的 Azure 資料庫」及相關聯應用程式以適當使用 SSL 連線的指示
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/21/2020
ms.openlocfilehash: 0af94d04f4e50fd039f01c5746b3a5d47a3698b3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81769934"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mariadb"></a>在您的應用程式中設定 SSL 連線能力，以安全地連線至適用於 MariaDB 的 Azure 資料庫
適用於 MariaDB 的 Azure 資料庫支援使用安全通訊端層 (SSL)，將適用於 MariaDB 的 Azure 資料庫伺服器連線至用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將兩者之間的資料流加密，有助於抵禦「中間人」攻擊。

## <a name="obtain-ssl-certificate"></a>取得 SSL 憑證
下載透過 SSL 與您的適用於 MariaDB 的 Azure 資料庫伺服器進行通訊所需的[https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)憑證，並將憑證檔案儲存到本機磁片磁碟機（例如，本教學課程使用 c:\ssl）。
**針對 Microsoft Internet Explorer 和 Microsoft Edge：** 在下載完成後，請將憑證重新命名為 BaltimoreCyberTrustRoot.crt.pem。

## <a name="bind-ssl"></a>繫結 SSL

### <a name="connecting-to-server-using-mysql-workbench-over-ssl"></a>使用 MySQL 工作臺透過 SSL 連線到伺服器
設定使 MySQL Workbench 安全地透過 SSL 連線。 

1. 從 [設定新連線] 對話方塊，瀏覽至 [SSL]**** 索引標籤。 

1. 將 [**使用 SSL** ] 欄位更新為 [需要]。

1. 在 [SSL CA 檔案:]**** 欄位中輸入 **BaltimoreCyberTrustRoot.crt.pem** 的檔案位置。 
    
    ![儲存 SSL 設定](./media/howto-configure-ssl/mysql-workbench-ssl.png)

針對現有的連線，您可以用滑鼠右鍵按一下連線圖示來系結 SSL，然後選擇 [編輯]。 然後瀏覽至 [SSL]**** 索引標籤上，並繫結憑證檔案。

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>使用 MySQL CLI 透過 SSL 連線至伺服器
有另一個繫結 SSL 憑證的方法，就是藉由執行下列命令來使用 MySQL 命令列介面。 

```bash
mysql.exe -h mydemoserver.mariadb.database.azure.com -u Username@mydemoserver -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

> [!NOTE]
> 在 Windows 上使用 MySQL 命令列介面時，您可能會收到 `SSL connection error: Certificate signature check failed` 錯誤。 如果發生此狀況，請以 `--ssl` 取代 `--ssl-mode=REQUIRED --ssl-ca={filepath}` 參數。

## <a name="enforcing-ssl-connections-in-azure"></a>強制在 Azure 中使用 SSL 連線 
### <a name="using-the-azure-portal"></a>使用 Azure 入口網站
使用 Azure 入口網站，瀏覽適用於 MariaDB 的 Azure 資料庫伺服器，然後按一下 [連線安全性]****。 使用切換按鈕來啟用或停用 [強制使用 SSL 連線]**** 設定，然後按一下 [儲存]****。 Microsoft 建議一律啟用 [強制使用 SSL 連線]**** 設定，以增強安全性。
![啟用 ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>使用 Azure CLI
您可以在 Azure CLI 中分別使用 Enabled 或 Disabled 值，以啟用或停用 **ssl-enforcement** 參數。
```azurecli-interactive
az mariadb server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="verify-the-ssl-connection"></a>確認 SSL 連線
執行 mysql **status** 命令，確認您已使用 SSL 連線至 MariaDB 伺服器︰
```sql
status
```
藉由檢閱輸出確認連線已加密，顯示結果應類似：**SSL: Cipher in use is AES256-SHA** 

## <a name="sample-code"></a>範例程式碼
若要從您的應用程式透過 SSL 對「適用於 MariaDB 的 Azure 資料庫」建立安全連線，請參閱下列程式碼範例：

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mariadb.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)
```python
try:
    conn = mysql.connector.connect(user='myadmin@mydemoserver',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mariadb.database.azure.com',
                                   ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```
### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user='myadmin@mydemoserver',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mariadb.database.azure.com',
                       ssl={'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
```

### <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mariadb.database.azure.com', 
        :username => 'myadmin@mydemoserver',      
        :password => 'yourpassword',    
        :database => 'quickstartdb',
        :sslca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
        :ssl_mode => 'required'
    )
```
#### <a name="ruby-on-rails"></a>Ruby on Rails
```ruby
default: &default
  adapter: mysql2
  username: username@mydemoserver
  password: yourpassword
  host: mydemoserver.mariadb.database.azure.com
  sslca: BaltimoreCyberTrustRoot.crt.pem
  sslverify: true
```

### <a name="golang"></a>Golang
```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/BaltimoreCyberTrustRoot.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mariadb.database.azure.com', 'quickstartdb') 
db, _ := sql.Open("mysql", connectionString)
```
### <a name="java-jdbc"></a>Java (JDBC)
```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " + 
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " + 
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore 
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```
### <a name="java-mariadb"></a>Java (MariaDB)
```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " + 
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " + 
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore 
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET （MySqlConnector）
```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin@mydemoserver",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    CACertificateFile = "BaltimoreCyberTrustRoot.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

<!-- 
## Next steps
Review various application connectivity options following [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md)
-->
