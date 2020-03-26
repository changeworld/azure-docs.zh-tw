---
title: 教學課程 - 如何將適用於 MySQL 的 Azure 資料庫執行個體繫結至您的 Azure Spring Cloud 應用程式
description: 本教學課程說明如何將適用於 MySQL 的 Azure 資料庫執行個體繫結至您的 Azure Spring Cloud 應用程式
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 657aa70d77fd1af9fd2121a3e98ea3aca7773642
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277549"
---
# <a name="tutorial-bind-an-azure-database-for-mysql-instance-to-your-azure-spring-cloud-application"></a>教學課程：將適用於 MySQL 的 Azure 資料庫執行個體繫結至您的 Azure Spring Cloud 應用程式 

透過 Azure Spring Cloud，您可以將特定的 Azure 服務自動繫結至應用程式，而無須手動設定 Spring Boot 應用程式。 本教學課程說明如何將應用程式繫結至適用於 MySQL 的 Azure 資料庫執行個體。

## <a name="prerequisites"></a>Prerequisites

* 已部署的 Azure Spring Cloud 執行個體
* 適用於 MySQL 的 Azure 資料庫帳戶
* Azure CLI

如果您沒有已部署的 Azure Spring Cloud 執行個體，請依照下列快速入門中的指示操作：[快速入門：使用 Azure 入口網站來啟動 Azure Spring Cloud 應用程式](spring-cloud-quickstart-launch-app-portal.md)，以部署您的第一個 Spring Cloud 應用程式。

## <a name="bind-your-app-to-your-azure-database-for-mysql-instance"></a>將您的應用程式繫結至適用於 MySQL 的 Azure 資料庫執行個體

1. 請記下您「適用於 MySQL 的 Azure 資料庫」帳戶的管理使用者名稱和密碼。 

1. 連線至伺服器，從 MySQL 用戶端建立名為 **testdb** 的資料庫，然後建立新的非管理員帳戶。

1. 在您專案的 *pom.xml* 檔案中，新增下列相依性：

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. 在 *application.properties* 檔案中，移除任何 `spring.datasource.*` 屬性。

1. 執行 `az spring-cloud app update` 以更新目前的部署，或執行 `az spring-cloud app deployment create` 建立新部署以進行此變更。  這些命令會以新的相依性來更新或建立應用程式。

1. 在 Azure 入口網站中的 [Azure Spring Cloud 服務]  頁面上，尋找 [應用程式儀表板]  ，然後選取要繫結至適用於 MySQL 的 Azure 資料庫執行個體的應用程式。  這是您在先前的步驟中更新或部署的相同應用程式。 

1. 選取 [服務繫結]  ，然後選取 [建立服務繫結]  按鈕。 

1. 選取 [Azure MySQL]  作為 [繫結類型]  ，使用您在第一個步驟中記下的使用者名稱和密碼，以填寫表單。

1. 重新啟動應用程式，此繫結現在應該會正常運作。

1. 若要確定服務繫結的正確性，請選取繫結名稱並確認其詳細資料。 `property` 欄位看起來應該如下所示：
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何將 Azure Spring Cloud 應用程式繫結至適用於 MySQL 的 Azure 資料庫執行個體。  若要深入了解如何管理 Azure Spring Cloud 服務，請閱讀關於服務探索和註冊的文章。

> [!div class="nextstepaction"]
> [使用 Spring Cloud Service Registry 來啟用服務探索和註冊](spring-cloud-service-registration.md)
