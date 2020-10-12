---
title: 在適用於 MySQL 的 Azure 資料庫彈性的伺服器中使用 TLS/SSL 進行加密的連接
description: 有關如何在適用於 MySQL 的 Azure 資料庫彈性的伺服器中使用 TLS/SSL 連線的指示和資訊。
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 24a8dd4d21cb6ab6edeb985db4e6e6a1349a758d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936651"
---
# <a name="encrypted-connectivity-using-transport-layer-security-tls-12-in-azure-database-for-mysql---flexible-server"></a>使用傳輸層安全性 (TLS 1.2) 在適用於 MySQL 的 Azure 資料庫彈性的伺服器中進行加密的連接

> [!IMPORTANT]
> 適用於 MySQL 的 Azure 資料庫彈性伺服器目前處於公開預覽狀態

適用於 MySQL 的 Azure 資料庫彈性的伺服器支援使用傳輸層安全性 (TLS) （先前稱為安全通訊端層 (SSL) ）將您的用戶端應用程式連接至 MySQL 服務。 TLS 是一種業界標準通訊協定，可確保您的資料庫伺服器與用戶端應用程式之間的加密網路連線，讓您遵守合規性需求。

適用於 MySQL 的 Azure 資料庫彈性的伺服器僅支援使用傳輸層安全性 (TLS 1.2) 加密的連線，且 TLS 1.0 和 TLS 1.1 的所有連入連線都會遭到拒絕。 針對所有具彈性的伺服器，TLS 連線的強制執行都已啟用，而且您無法停用 TLS/SSL 以連接到有彈性的伺服器。

## <a name="applications-that-require-certificate-verification-for-tlsssl-connectivity"></a>需要 TLS/SSL 連線的憑證驗證的應用程式
在某些情況下，應用程式需要由信任的憑證授權單位單位產生的本機憑證檔案 (CA) 憑證檔案，才能安全地連接。 適用於 MySQL 的 Azure 資料庫彈性的伺服器使用 *DigiCert 的全域根 CA*。 下載此憑證，以透過 SSL 從 [DigiCert 全域根 CA](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) 進行通訊，然後將憑證檔案儲存到您的慣用位置。 例如，本教學課程會使用 `c:\ssl`。

### <a name="connect-using-mysql-command-line-client-with-tlsssl"></a>使用 mysql 命令列用戶端搭配 TLS/SSL 連線

如果您使用私人存取 (VNet 整合) 建立彈性伺服器，就必須從與伺服器相同的 VNet 中的資源連線到您的伺服器。 您可以建立虛擬機器，並新增至使用您彈性伺服器建立的 VNet。

如果您使用公用存取 (允許的 IP 位址)建立彈性伺服器，可以將本機 IP 位址新增至伺服器上的防火牆規則清單。

您可以選擇 [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) 或 [MySQL 工作臺](./connect-workbench.md)--> 從本機環境連接到伺服器。 

下列範例示範如何使用 mysql 命令列介面連接到您的伺服器。 使用 `--ssl-mode=REQUIRED` 連接字串設定來強制執行 TLS/SSL 憑證驗證。 將本機憑證檔案路徑傳遞至 `--ssl-ca` 參數。 以實際的伺服器名稱和密碼取代這些值。 

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\DigiCertGlobalRootCA.crt.pem
```
> [!Note]
> 確認傳遞給的值 `--ssl-ca` 符合您儲存之憑證的檔案路徑。

### <a name="verify-the-tlsssl-connection"></a>確認 TLS/SSL 連線

執行 mysql **status** 命令，確認您已使用 TLS/SSL 連線到您的 mysql 伺服器：

```dos
mysql> status
```
藉由檢查輸出來確認連線已加密，這應該會顯示：  **SSL： Cipher 在使用中是 AES256-SHA**。 此加密套件顯示一個範例，並根據用戶端，您可以看到不同的密碼套件。

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>確定您的應用程式或架構支援 TLS 連接

某些使用 MySQL 做為資料庫服務的應用程式架構，在安裝期間預設不會啟用 TLS。 您的 MySQL 伺服器會強制執行 TLS 連線，但如果未針對 TLS 設定應用程式，則應用程式可能無法連線到您的資料庫伺服器。 請參閱您應用程式的檔，以瞭解如何啟用 TLS 連接。

## <a name="sample-code"></a>範例程式碼
在 [連接字串] 頁面中可供您的伺服器在 Azure 入口網站中預先定義的連接字串，包括使用 TLS/SSL 連線到您的資料庫伺服器所需的通用語言參數。 TLS/SSL 參數會根據連接器而有所不同。 例如，"useSSL = true"、"sslmode = required" 或 "ssl_verify_cert = true" 和其他變化。

若要從您的應用程式透過 TLS/SSL 建立與您的彈性伺服器的加密連線，請參閱下列程式碼範例：

### <a name="php"></a>PHP

```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootCA.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

### <a name="php-using-pdo"></a>使用 PDO) 的 PHP (

```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'myadmin', 'yourpassword', $options);
```

### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)

```python
try:
    conn = mysql.connector.connect(user='myadmin',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/DigiCertGlobalRootCA.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)

```python
conn = pymysql.connect(user='myadmin',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'})
```

### <a name="django-pymysql"></a>Django (PyMySQL) 

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'}
        }
    }
}
```

### <a name="ruby"></a>Ruby

```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com',
        :username => 'myadmin',
        :password => 'yourpassword',
        :database => 'quickstartdb',
        :sslca => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
    )
```

### <a name="golang"></a>Golang

```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/DigiCertGlobalRootCA.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')
db, _ := sql.Open("mysql", connectionString)
```

### <a name="java-mysql-connector-for-java"></a>JAVA (適用于 JAVA) 的 MySQL 連接器

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

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="java-mariadb-connector-for-java"></a>JAVA (適用于 mariadb Connector for JAVA) 

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

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector) 

```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    SslCa = "DigiCertGlobalRootCA.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

## <a name="next-steps"></a>後續步驟
- [使用 MySQL 工作臺在適用於 MySQL 的 Azure 資料庫彈性的伺服器中連接和查詢資料](./connect-workbench.md)
- [使用 PHP 在適用於 MySQL 的 Azure 資料庫彈性的伺服器中連接和查詢資料](./connect-php.md)
- [使用 Azure CLI 建立及管理適用於 MySQL 的 Azure 資料庫彈性的伺服器虛擬網路](./how-to-manage-virtual-network-cli.md)。
- 深入瞭解[適用於 MySQL 的 Azure 資料庫彈性伺服器中的網路](./concepts-networking.md)功能
- 深入瞭解 [適用於 MySQL 的 Azure 資料庫彈性的伺服器防火牆規則](./concepts-networking.md#public-access-allowed-ip-addresses)
