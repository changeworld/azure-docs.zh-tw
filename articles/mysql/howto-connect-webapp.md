---
title: 連接到 Azure App Service-適用於 MySQL 的 Azure 資料庫
description: 說明如何將現有的 Azure App Service 正確地連線到適用於 MySQL 的 Azure 資料庫
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ba75daa47ca1f77cd5828d13877238a64deeeb41
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80062468"
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>將現有的 Azure App Service 連線到適用於 MySQL 伺服器的 Azure 資料庫
本主題說明如何將現有的 Azure App Service 連線到適用於 MySQL 伺服器的 Azure 資料庫。

## <a name="before-you-begin"></a>開始之前
登入 [Azure 入口網站](https://portal.azure.com)。 建立適用於 MySQL 伺服器的 Azure 資料庫。 如需詳細資訊，請參閱[如何從入口網站建立適用於 MySQL 伺服器的 Azure 資料庫](quickstart-create-mysql-server-database-using-azure-portal.md)或[如何使用 CLI 建立適用於 MySQL 伺服器的 Azure 資料庫](quickstart-create-mysql-server-database-using-azure-cli.md)。

目前有兩種解決方案可讓您從 Azure App Service 存取適用於 MySQL 的 Azure 資料庫。 這兩種解決方案都需要設定伺服器層級防火牆規則。

## <a name="solution-1---allow-azure-services"></a>解決方案 1 - 允許 Azure 服務
適用於 MySQL 的 Azure 資料庫提供存取安全性，使用防火牆來保護您的資料。 Azure App Service 連線到適用於 MySQL 伺服器的 Azure 資料庫時，請注意 App Service 的輸出 IP 本質上是動態的。 選擇 [允許存取 Azure 服務] 選項，可讓 App Service 連線至 MySQL 伺服器。

1. 在 [MySQL 伺服器] 刀鋒視窗的 [設定] 標題下，按一下 [連線安全性]****，開啟「適用於 MySQL 的 Azure 資料庫」的 [連線安全性] 刀鋒視窗。

   ![Azure 入口網站 - 按一下 [連線安全性]](./media/howto-connect-webapp/1-connection-security.png)

2. 選取 [允許存取 Azure 服務]**** 中的 [開啟]****，然後選取 [儲存]****。
   ![Azure 入口網站 - 允許 Azure 存取](./media/howto-connect-webapp/allow-azure.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>解決方案 2 - 建立防火牆規則以明確允許輸出 IP
您可以明確新增 Azure App Service 的所有輸出 IP。

1. 在 App Service 的 [屬性] 刀鋒視窗上，檢視您的 [輸出 IP 位址]****。

   ![Azure 入口網站 - 檢視輸出 IP](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. 在 MySQL 的 [連線安全性] 刀鋒視窗上，逐一新增輸出 IP。

   ![Azure 入口網站 - 新增明確的 IP](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. 記得**儲存**您的防火牆規則。

雖然 Azure App Service 會嘗試將 IP 位址保持固定一段時間，IP 位址有時還是可能會變更。 例如，此狀控可能發生應用程式回收或調整規模作業時，或在 Azure 地區資料中心內新增電腦以增加容量時。 當 IP 位址變更時，應用程式可能會在無法再連線到 MySQL 伺服器時發生停機。 選擇上述其中一種解決方案時，請考慮這個可能性。

## <a name="ssl-configuration"></a>SSL 設定
適用於 MySQL 的 Azure 資料庫預設會啟用 SSL。 如果您的應用程式未使用 SSL 連線到資料庫，則必須在 MySQL 伺服器上停用 SSL。 如需如何設定 SSL 的詳細資訊，請參閱[使用 SSL 與適用於 MySQL 的 Azure 資料庫](howto-configure-ssl.md)。

### <a name="django-pymysql"></a>Django （PyMySQL）
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin@mydemoserver',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ssl-ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}
        }
    }
}
```

## <a name="next-steps"></a>後續步驟
如需連接字串的詳細資訊，請參閱[連接字串](howto-connection-string.md)。
