---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 76a080d40721fa78ad703f77f6dbe7a3363ab77e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "68857334"
---
## <a name="install-wordpress"></a>安裝 WordPress

如果您想要嘗試您的堆疊，請安裝範例應用程式。 例如，下列步驟可安裝開放原始碼 [WordPress](https://wordpress.org/) 平台以建立網站和部落格。 其他可嘗試的工作負載包括 [Drupal](http://www.drupal.org) 和 [Moodle](https://moodle.org/)。 

此 WordPress 設定只適用於概念證明。 若要在生產環境中使用建議的安全性設定安裝最新的 WordPress，請參閱 [WordPress 文件](https://codex.wordpress.org/Main_Page)。 



### <a name="install-the-wordpress-package"></a>安裝 WordPress 套件

執行以下命令：

```bash
sudo apt install wordpress
```

### <a name="configure-wordpress"></a>設定 WordPress

將 WordPress 設定為使用 MySQL 和 PHP。

在工作目錄中，建立文字檔 `wordpress.sql` 以設定 WordPress 的 MySQL 資料庫： 

```bash
sudo sensible-editor wordpress.sql
```

新增下列命令，以您選擇的資料庫密碼替代 *yourPassword* (其他值維持不變)。 如果您先前已設定 MySQL 安全性原則來驗證密碼強度，請確定此密碼符合強度需求。 儲存檔案。

```sql
CREATE DATABASE wordpress;
GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER
ON wordpress.*
TO wordpress@localhost
IDENTIFIED BY 'yourPassword';
```

執行下列命令來建立資料庫：

```bash
cat wordpress.sql | sudo mysql --defaults-extra-file=/etc/mysql/debian.cnf
```

`wordpress.sql` 檔案中包含資料庫認證，因此請在使用下列命令後將其刪除：

```bash
sudo rm wordpress.sql
```

若要設定 PHP，請執行下列命令來開啟您選擇的文字編輯器，並建立 `/etc/wordpress/config-localhost.php` 檔案：

```bash
sudo sensible-editor /etc/wordpress/config-localhost.php
```
將下列幾行複製到檔案，以您的 WordPress 資料庫密碼替代 *yourPassword* (其他值維持不變)。 然後儲存檔案。

```php
<?php
define('DB_NAME', 'wordpress');
define('DB_USER', 'wordpress');
define('DB_PASSWORD', 'yourPassword');
define('DB_HOST', 'localhost');
define('WP_CONTENT_DIR', '/usr/share/wordpress/wp-content');
?>
```


將 WordPress 安裝移至 Web 伺服器文件根目錄：

```bash
sudo ln -s /usr/share/wordpress /var/www/html/wordpress

sudo mv /etc/wordpress/config-localhost.php /etc/wordpress/config-default.php
```

您現在即可完成 WordPress 設定並且在平台上發佈。 開啟瀏覽器並前往 `http://yourPublicIPAddress/wordpress`。 替換為您 VM 的公用 IP 位址。 該頁面看起來應該類似下圖。

![WordPress 安裝頁面](./media/virtual-machines-linux-tutorial-wordpress/wordpressstartpage.png)
