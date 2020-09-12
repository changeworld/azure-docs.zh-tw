---
title: 在 Azure Spring Cloud 中設定 Config Server 執行個體
description: 了解如何在 Azure 入口網站上為您的 Azure Spring Cloud 設定 Spring Cloud Config Server 執行個體
ms.service: spring-cloud
ms.topic: how-to
ms.author: brendm
author: bmitchell287
ms.date: 10/18/2019
ms.custom: devx-track-java
ms.openlocfilehash: 665d508a7d95e177d0db09ad86e2a5f0ee4d605b
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89291291"
---
# <a name="set-up-a-spring-cloud-config-server-instance-for-your-service"></a>為您的服務設定 Spring Cloud Config Server 執行個體

本文將說明如何將 Spring Cloud Config Server 執行個體連線至 Azure Spring Cloud 服務。

Spring Cloud Config 能夠在分散式系統中提供外部化設定的伺服器端和用戶端支援。 透過 Config Server 執行個體，您可以在所有環境中集中管理應用程式的外部屬性。 如需詳細資訊，請參閱 [Spring Cloud Config Server 參考](https://spring.io/projects/spring-cloud-config)。

## <a name="prerequisites"></a>必要條件
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
* 已佈建且正在執行的 Azure Spring Cloud 服務。 若要設定和啟動 Azure Spring Cloud 服務，請參閱[快速入門：使用 Azure CLI 來啟動 Java Spring 應用程式](spring-cloud-quickstart.md)。

## <a name="restriction"></a>限制

當您使用 Config Server 搭配 Git 後端時，會有一些限制。 有些屬性將會自動插入至您的應用程式環境，以存取 Config Server 和服務探索。 如果您也從 Config Server 檔案設定這些屬性，可能會發生衝突和非預期的行為。 這些屬性包括： 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
spring.jmx.enabled
```

> [!CAUTION]
> 強烈建議您_不要_將上述屬性放在您的 Config Server 應用程式檔案中。

## <a name="create-your-config-server-files"></a>建立 Config Server 檔案

Azure Spring Cloud 支援 Azure DevOps、GitHub、GitLab 和 Bitbucket，以儲存您的 Config Server 檔案。 當您的存放庫準備就緒時，請使用下列指示建立組態檔，並將其儲存在該處。

此外，某些可設定的屬性僅適用於特定類型。 下列小節會列出每個存放庫類型的屬性。

### <a name="public-repository"></a>公用存放庫

使用公用存放庫時，可設定的屬性會受到更多限制。

下表列出用來設定公用 Git 存放庫的所有可設定屬性：

> [!NOTE]
> 使用連字號 (-) 來分隔字組是目前唯一支援的命名慣例。 例如，您可以使用 *default-label*，但不能使用 *defaultLabel*。

| 屬性        | 必要 | 功能                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | 是    | 作為 Config Server 後端的 Git 存放庫，URI 會以 *http://* 、*https://* 、*git@* 或 *ssh://* 開頭。 |
| `default-label` | 否     | Git 存放庫的預設標籤應為存放庫的*分支名稱*、*標籤名稱*或*認可識別碼*。 |
| `search-paths`  | 否     | 用來搜尋 Git 存放庫子目錄的字串陣列。 |

------

### <a name="private-repository-with-ssh-authentication"></a>使用 SSH 驗證的私人存放庫

下表列出用來對使用 SSH 的私人 Git 存放庫進行設定的所有可設定屬性：

> [!NOTE]
> 使用連字號 (-) 來分隔字組是目前唯一支援的命名慣例。 例如，您可以使用 *default-label*，但不能使用 *defaultLabel*。

| 屬性                   | 必要 | 功能                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | 是    | 作為 Config Server 後端的 Git 存放庫，其 URI 應以 *http://* 、*https://* 、*git@* 或 *ssh://* 開頭。 |
| `default-label`            | 否     | Git 存放庫的預設標籤應為存放庫的*分支名稱*、*標籤名稱*或*認可識別碼*。 |
| `search-paths`             | 否     | 用來搜尋 Git 存放庫子目錄的字串陣列。 |
| `private-key`              | 否     | 用來存取 Git 存放庫的 SSH 私密金鑰，若 URI 以 *git@* 或 *ssh://* 開頭，則為_必要_項目。 |
| `host-key`                 | 否     | Git 存放庫伺服器的主機金鑰不應包含 `host-key-algorithm` 所涵蓋的演算法前置詞。 |
| `host-key-algorithm`       | 否     | 主機金鑰演算法應為 *ssh-dss*、*ssh-rsa*、*ecdsa-sha2-nistp256*、*ecdsa-sha2-nistp384* 或 *ecdsa-sha2-nistp521*。 只有在 `host-key` 存在時才*需要*。 |
| `strict-host-key-checking` | 否     | 指出在使用私密 `host-key` 時，Config Server 執行個體是否無法啟動。 應為 *true* (預設值) 或 *false*。 |

-----

### <a name="private-repository-with-basic-authentication"></a>使用基本驗證的私人存放庫

以下列出透過基本驗證設定私人 Git 存放庫所使用的所有可設定屬性。

> [!NOTE]
> 使用連字號 (-) 來分隔字組是目前唯一支援的命名慣例。 例如，請使用 *default-label*，而不是 *defaultLabel*。

| 屬性        | 必要 | 功能                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | 是    | 作為 Config Server 後端的 Git 存放庫，其 URI 應以 *http://* 、*https://* 、*git@* 或 *ssh://* 開頭。 |
| `default-label` | 否     | Git 存放庫的預設標籤應為存放庫的*分支名稱*、*標籤名稱*或*認可識別碼*。 |
| `search-paths`  | 否     | 用來搜尋 Git 存放庫子目錄的字串陣列。 |
| `username`      | 否     | 用來存取存放庫伺服器的使用者名稱，若 Git 存放庫伺服器支援 `Http Basic Authentication`，則為_必要_項目。 |
| `password`      | 否     | 用來存取 Git 存放庫伺服器的密碼，若 Git 存放庫伺服器支援 `Http Basic Authentication`，則為_必要_項目。 |

> [!NOTE]
> 許多 `Git` 存放庫伺服器都支援使用權杖，而不是 HTTP 基本驗證的密碼。 某些存放庫 (例如 GitHub) 可讓權杖無限期保存。 不過，有些 Git 存放庫伺服器 (包括 Azure DevOps) 會強制權杖在數小時內過期。 會導致權杖過期的存放庫不應使用權杖型驗證搭配 Azure Spring Cloud。

### <a name="git-repositories-with-pattern"></a>使用模式的 Git 存放庫

以下列出透過模式設定 Git 存放庫所使用的所有可設定屬性。

> [!NOTE]
> 使用連字號 (-) 來分隔字組是目前唯一支援的命名慣例。 例如，請使用 *default-label*，而不是 *defaultLabel*。

| 屬性                           | 必要         | 功能                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | 否             | 包含具名 Git 存放庫設定的對應。 |
| `repos."uri"`                      | 在 `repos` 上為 [是] | 作為 Config Server 後端的 Git 存放庫，其 URI 應以 *http://* 、*https://* 、*git@* 或 *ssh://* 開頭。 |
| `repos."name"`                     | 在 `repos` 上為 [是] | 用來識別 Git 存放庫的名稱，只有在 `repos` 存在時才_需要_。 例如 *team-A*、*team-B*。 |
| `repos."pattern"`                  | 否             | 用來比對應用程式名稱的字串陣列。 每個模式都應使用 `{application}/{profile}` 格式搭配萬用字元。 |
| `repos."default-label"`            | 否             | Git 存放庫的預設標籤應為存放庫的*分支名稱*、*標籤名稱*或*認可識別碼*。 |
| `repos."search-paths`"             | 否             | 用來搜尋 Git 存放庫子目錄的字串陣列。 |
| `repos."username"`                 | 否             | 用來存取存放庫伺服器的使用者名稱，若 Git 存放庫伺服器支援 `Http Basic Authentication`，則為_必要_項目。 |
| `repos."password"`                 | 否             | 用來存取 Git 存放庫伺服器的密碼，若 Git 存放庫伺服器支援 `Http Basic Authentication`，則為_必要_項目。 |
| `repos."private-key"`              | 否             | 用來存取 Git 存放庫的 SSH 私密金鑰，若 URI 以 *git@* 或 *ssh://* 開頭，則為_必要_項目。 |
| `repos."host-key"`                 | 否             | Git 存放庫伺服器的主機金鑰不應包含 `host-key-algorithm` 所涵蓋的演算法前置詞。 |
| `repos."host-key-algorithm"`       | 否             | 主機金鑰演算法應為 *ssh-dss*、*ssh-rsa*、*ecdsa-sha2-nistp256*、*ecdsa-sha2-nistp384* 或 *ecdsa-sha2-nistp521*。 只有在 `host-key` 存在時才*需要*。 |
| `repos."strict-host-key-checking"` | 否             | 指出在使用私密 `host-key` 時，Config Server 執行個體是否無法啟動。 應為 *true* (預設值) 或 *false*。 |

## <a name="attach-your-config-server-repository-to-azure-spring-cloud"></a>將 Config Server 存放庫連結至 Azure Spring Cloud

現在，您已將組態檔儲存在存放庫中，接下來您必須將 Azure Spring Cloud 連線至該存放庫。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 移至 Azure Spring Cloud 的 [概觀] 頁面。

1. 選取要設定的服務。

1. 在服務頁面的左窗格中，於 [設定] 底下選取 [Config Server] 索引標籤。

![Config Server 視窗](media/spring-cloud-tutorial-config-server/portal-config-server.png)

### <a name="enter-repository-information-directly-to-the-azure-portal"></a>將存放庫資訊直接輸入至 Azure 入口網站

#### <a name="default-repository"></a>預設存放庫

* **公用存放庫**：在 [預設存放庫] 區段的 [URI] 方塊中，貼上存放庫 URI。  將 [標籤] 設定為 **config**。請確定 [驗證] 設定是 [公用]，然後選取 [套用] 以完成作業。 

* **私人存放庫**：Azure Spring Cloud 支援基本密碼/權杖型驗證和 SSH。

    * **基本驗證**：在 [預設存放庫] 區段的 [URI] 方塊中，貼上存放庫 URI，然後選取 [驗證] (「鉛筆」圖示) 按鈕。 在 [編輯驗證] 窗格的 [驗證類型] 下拉式清單中，選取 [HTTP 基本]，然後輸入您的使用者名稱和密碼/權杖，以授與 Azure Spring Cloud 的存取權。 依序 [確定] 和 [套用]，以完成 Config Server 執行個體的設定。

    ![編輯驗證窗格基本驗證](media/spring-cloud-tutorial-config-server/basic-auth.png)
    
    > [!CAUTION]
    > 某些 Git 存放庫伺服器 (例如 GitHub) 會使用*個人權杖*或*存取權杖* (例如密碼) 進行**基本驗證**。 您可以使用這種權杖作為 Azure Spring Cloud 中的密碼，因為該權杖永遠不會過期。 但對於其他 Git 存放庫伺服器 (例如 Bitbucket 和 Azure DevOps)，*存取權杖*會在一或兩個小時後過期。 這表示在搭配 Azure Spring Cloud 使用這些存放庫伺服器時，無法使用此選項。

    * **SSH**：在 [預設存放庫] 區段的 [URI] 方塊中，貼上存放庫 URI，然後選取 [驗證] (「鉛筆」圖示) 按鈕。 在 [編輯驗證] 窗格的 [驗證類型] 下拉式清單中，選取 [SSH]，然後輸入您的 [私密金鑰]。 您可以選擇性地指定**主機金鑰**和**主機金鑰演算法**。 請務必將您的公開金鑰包含在 Config Server 存放庫中。 依序 [確定] 和 [套用]，以完成 Config Server 執行個體的設定。

    ![編輯驗證窗格 ssh 驗證](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="pattern-repository"></a>模式存放庫

如果您想要使用選擇性的**模式存放庫**來設定服務，請指定 **URI** 和**驗證**，指定方式與**預設存放庫**相同。 請務必包含您模式的**名稱**，然後選取 [套用] 以將其連結至您的執行個體。 

### <a name="enter-repository-information-into-a-yaml-file"></a>在 YAML 檔案中輸入存放庫資訊

如果您已使用存放庫設定來撰寫 YAML 檔案，您可以將該檔案直接從本機電腦匯入至 Azure Spring Cloud。 在使用基本驗證的私人存放庫中，簡單的 YAML 檔案看起來像這樣：

```yml
spring:
    cloud:
        config:
            server:
                git:
                    uri: https://github.com/azure-spring-cloud-samples/config-server-repository.git
                    username: <username>
                    password: <password/token>

```

選取 [匯入設定] 按鈕，然後從您的專案目錄中選取 YAML 檔案。 選取 [匯入]，您的 [通知] 中將會顯示 `async` 作業。 1-2 分鐘後，系統應該會回報成功訊息。

![[Config Server 通知] 窗格](media/spring-cloud-tutorial-config-server/local-yml-success.png)


YAML 檔案中的資訊應該會顯示在 Azure 入口網站中。 選取 [套用] 以完成作業。 

## <a name="using-azure-repos-for-azure-spring-cloud-configuration"></a>使用適用于 Azure 春季雲端設定的 Azure Repos

Azure 春季 Cloud 可以存取公開、由 SSH 保護的 Git 存放庫，或使用 HTTP 基本驗證來保護其安全。 我們將使用最後一個選項，因為使用 Azure Repos 可以更輕鬆地建立和管理。

### <a name="get-repo-url-and-credentials"></a>取得存放庫 url 和認證
1. 在您專案的 Azure Repos 入口網站中，按一下 [複製] 按鈕：

    ![複製按鈕](media/spring-cloud-tutorial-config-server/clone-button.png)

1. 複製文字方塊中的複製 URL。 此 URL 通常會採用下列格式：

    ```Text
    https://<organization name>@dev.azure.com/<organization name>/<project name>/_git/<repository name>
    ```

    移除前後 `https://` 的所有專案 `dev.azure.com` ，包括 `@` 。 產生的 URL 應該採用下列格式：

    ```Text
    https://dev.azure.com/<organization name>/<project name>/_git/<repository name>
    ```

    儲存此 URL 以供下一節使用。

1. 按一下 [產生 Git 認證]。 系統將會顯示使用者名稱和密碼。 儲存這些內容以供下一節使用。


### <a name="configure-azure-spring-cloud-to-access-the-git-repository"></a>設定 Azure Spring Cloud 以存取 Git 存放庫

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 移至 Azure Spring Cloud 的 [概觀] 頁面。

1. 選取要設定的服務。

1. 在 [服務] 頁面的左窗格中，選取 [ **設定**] 下的 [ **Config Server** ] 索引標籤。設定我們先前建立的存放庫：
   - 新增您在上一節中儲存的存放庫 URL
   - 按一下 `Authentication` 並選取 `HTTP Basic`
   - 使用者 __名稱__ 是從上一節儲存的使用者名稱
   - __密碼__是前一節所儲存的密碼
   - 按一下 [套用] 並等候作業成功

   ![Spring Cloud 設定伺服器](media/spring-cloud-tutorial-config-server/config-server-azure-repos.png)

## <a name="delete-your-app-configuration"></a>刪除應用程式設定

儲存組態檔之後，[刪除應用程式設定] 按鈕將會出現在 [設定] 索引標籤中。選取此按鈕將會完全清除您現有的設定。 如果您想要將 Config Server 執行個體連線至另一個來源 (例如從 GitHub 移至 Azure DevOps)，則應選取此選項。



## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何啟用和設定 Spring Cloud Config Server 執行個體。 若要深入了解如何管理應用程式，請參閱[在 Azure Spring Cloud 中調整應用程式](spring-cloud-tutorial-scale-manual.md)。
