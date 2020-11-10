---
title: 使用 Azure Red Hat OpenShift 4 叢集上的 Open 可自由/WebSphere，部署 JAVA 應用程式
description: 使用 Azure Red Hat OpenShift 4 叢集上的 Open 可自由/WebSphere，來部署 JAVA 應用程式。
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 10/30/2020
keywords: java、jakartaee、javaee、microprofile、開放式-自由、websphere-自由、aro、openshift、red hat
ms.openlocfilehash: ee4baf8eed26a43728fa52289bce86108c9e8c4a
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414423"
---
# <a name="deploy-a-java-application-with-open-libertywebsphere-liberty-on-an-azure-red-hat-openshift-4-cluster"></a>使用 Azure Red Hat OpenShift 4 叢集上的 Open 可自由/WebSphere，部署 JAVA 應用程式

本指南示範如何在 Open Open/WebSphere 的自由執行時間上執行 JAVA、JAVA EE、 [雅加達 EE](https://jakarta.ee/)或 [MicroProfile](https://microprofile.io/) 應用程式，然後將容器化應用程式部署至 Azure RED Hat OpenShift (ARO) 4 叢集（使用開放式的自由運算子）。 本文將逐步引導您準備可操作的應用程式、建立應用程式 Docker 映射，並在 ARO 4 叢集上執行容器化應用程式。  如需開啟 [自由] 的詳細資訊，請參閱 [open [開放式專案] 頁面](https://openliberty.io/)。 如需 IBM WebSphere 的詳細資訊，請參閱 [WebSphere 的「自由產品」頁面](https://www.ibm.com/cloud/websphere-liberty)。

[!INCLUDE [aro-support](includes/aro-support.md)]

## <a name="prerequisites"></a>先決條件

請完成下列必要條件，才能成功地逐步解說本指南。

> [!NOTE]
> Azure Red Hat OpenShift 至少需要 40 個核心，才能建立和執行 OpenShift 叢集。 新 Azure 訂用帳戶的預設 Azure 資源配額不符合這項需求。 若要要求增加資源限制，請參閱[標準配額：VM 系列的增加限制](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)。 請注意，免費試用訂用帳戶不符合配額增加的資格，請 [升級為隨用隨付訂](https://docs.microsoft.com/azure/cost-management-billing/manage/upgrade-azure-subscription) 用帳戶，再要求增加配額。

1. 使用已 (安裝 Unix 的作業系統（例如 Ubuntu、macOS) ）來準備本機電腦。
1. 安裝 JAVA SE 執行 (例如 [AdoptOpenJDK OpenJDK 8 LTS/openj9 上執行](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)) 。
1. 安裝 [Maven](https://maven.apache.org/download.cgi) 3.5.0 或更新版本。
1. 安裝適用于您作業系統的 [Docker](https://docs.docker.com/get-docker/) 。
1. 安裝 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) >2.0.75 或更新版本。
1. [`envsubst`](https://command-not-found.com/envsubst)如果未預先安裝在您的作業系統中，請檢查並安裝。
1. 在您的本機系統上複製此範例的程式碼。 範例是在 [GitHub](https://github.com/Azure-Samples/open-liberty-on-aro)上。
1. 遵循 [建立 Azure Red Hat OpenShift 4](/azure/openshift/tutorial-create-cluster)叢集中的指示。

   雖然「取得 Red Hat pull 秘密」步驟標示為選擇性，但 **這是本文的必要專案** 。  提取密碼可讓您的 Azure Red Hat OpenShift 叢集找出開放式的「自由」操作員。

   如果您打算在叢集上執行需要大量記憶體的應用程式，請使用參數為背景工作節點指定適當的虛擬機器大小 `--worker-vm-size` 。 例如， `Standard_E4s_v3` 是在叢集上安裝 Elasticsearch 操作員的最小虛擬機器大小。 如需詳細資訊，請參閱：

   * [Azure CLI 建立叢集](https://docs.microsoft.com/cli/azure/aro?view=azure-cli-latest&preserve-view=true#az-aro-create)
   * [記憶體優化的支援虛擬機器大小](/azure/openshift/support-policies-v4#memory-optimized)
   * [安裝 Elasticsearch 操作員的必要條件](https://docs.openshift.com/container-platform/4.3/logging/cluster-logging-deploying.html#cluster-logging-deploy-eo-cli_cluster-logging-deploying)

1. 遵循 [連接至 Azure Red Hat OpenShift 4](/azure/openshift/tutorial-connect-cluster)叢集中的步驟，連接到叢集。
   * 請務必遵循「安裝 OpenShift CLI」中的步驟，因為我們將 `oc` 在本文稍後使用命令。
   * 寫下類似的叢集主控台 URL `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` 。
   * 記下 `kubeadmin` 認證。

1. 確認您可以使用使用者的權杖來登入 OpenShift CLI `kubeadmin` 。

### <a name="enable-the-built-in-container-registry-for-openshift"></a>啟用 OpenShift 的內建容器登錄

本教學課程中的步驟會建立 Docker 映射，必須推送至 OpenShift 可存取的容器登錄。 最簡單的選項是使用 OpenShift 所提供的內建登錄。 若要啟用內建的容器登錄，請依照 [設定適用于 Azure Red Hat OpenShift 4 的內建容器](built-in-container-registry.md)登錄中的步驟進行。 本文會使用這些步驟中的三個專案。

* 登入 OpenShift web 主控台的 Azure AD 使用者使用者名稱和密碼。
* 在遵循登 `oc whoami` 入 OPENSHIFT CLI 的步驟之後的輸出。  此值稱為 **aad 使用者** 進行討論。
* 容器登錄 URL。

當您完成啟用內建容器登錄的步驟時，請注意這些專案。

### <a name="create-an-openshift-namespace-for-the-java-app"></a>建立 JAVA 應用程式的 OpenShift 命名空間

1. 使用認證，從瀏覽器登入 OpenShift web 主控台 `kubeadmin` 。
2. 流覽至系統 **管理**  >  **命名空間**  >  **建立命名空間** 。
3. 填寫 `open-liberty-demo` [ **名稱** ] 並選取 [ **建立** ]，如下所示。

   ![建立命名空間](./media/howto-deploy-java-liberty-app/create-namespace.png)

### <a name="create-an-administrator-for-the-demo-project"></a>建立示範專案的系統管理員

除了影像管理之外， **aad 使用者** 也會被授與管理許可權，以便在 ARO 4 叢集的示範專案中管理資源。  登入 OpenShift CLI，並依照下列步驟授與 **aad 使用者** 必要的許可權。

1. 使用認證，從瀏覽器登入 OpenShift web 主控台 `kubeadmin` 。
1. 在 web 主控台的右上方，展開已登入使用者的內容功能表，然後選取 [ **複製登** 入] 命令。
1. 如有必要，請使用相同的使用者登入新的索引標籤視窗。
1. 選取 [ **顯示標記** ]。
1. 將下列以 **此權杖登** 入的值複製到剪貼簿，然後在 shell 中執行，如下所示。
1. 執行下列命令，以將 `admin` 角色授與命名空間中的 **aad 使用者** `open-liberty-demo` 。

   ```bash
   # Switch to project "open-liberty-demo"
   oc project open-liberty-demo
   Now using project "open-liberty-demo" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   # Note: replace "<aad-user>" with the one noted by executing the steps in
   # Configure built-in container registry for Azure Red Hat OpenShift 4
   oc adm policy add-role-to-user admin <aad-user>
   clusterrole.rbac.authorization.k8s.io/admin added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

### <a name="install-the-open-liberty-openshift-operator"></a>安裝 Open 自由 OpenShift 操作員

建立並聯機到叢集之後，請安裝 Open 「自由」操作員。  Open 自由運算子的主要起始頁面是在 [GitHub](https://github.com/OpenLiberty/open-liberty-operator)上。

1. 使用認證，從瀏覽器登入 OpenShift web 主控台 `kubeadmin` 。
2. 流覽至 [ **運算子** ]  >  **OperatorHub** ，並搜尋 Open 的 **自由運算子** 。
3. 從搜尋結果中選取 [ **開放式自由操作操作員** ]。
4. 選取 [安裝]。
5. 在快顯 **建立操作員訂** 用帳戶中，檢查叢集 **上的所有命名空間 (預設)** 的 **安裝模式** 、 **Beta 版** 的 **更新通道** ，以及 **自動****核准策略** ：

   ![建立開放式自由運算子的操作員訂用帳戶](./media/howto-deploy-java-liberty-app/install-operator.png)
6. 選取 [ **訂閱** ] 並等候一或兩分鐘，直到顯示 [開啟的自由操作] 運算子為止。
7. 觀察 Open 的「自由」運算子，其狀態為「成功」。  如果沒有，請先診斷並解決問題，再繼續進行。
   :::image type="content" source="media/howto-deploy-java-liberty-app/open-liberty-operator-installed.png" alt-text="已安裝顯示 Open Open 的已安裝操作員。":::

## <a name="prepare-the-liberty-application"></a>準備自由應用程式

在本指南中，我們將使用 JAVA EE 8 應用程式作為我們的範例。 Open [自由] 是 [JAVA EE 8 完整設定檔](https://javaee.github.io/javaee-spec/javadocs/) 相容的伺服器，因此可以輕鬆地執行應用程式。  Open 「自由」也是 [雅加達的 EE 8 完整設定檔相容](https://jakarta.ee/specifications/platform/8/apidocs/)。

### <a name="run-the-application-on-open-liberty"></a>在 Open Open 上執行應用程式

若要在 Open Open 上執行應用程式，您需要建立開放式的自由伺服器設定檔，讓 [自由 Maven 外掛程式](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin) 可以封裝應用程式以進行部署。 將應用程式部署至 OpenShift 不需要自由 Maven 外掛程式。  不過，我們將在此範例中使用它搭配開放式的 (開發) 模式的開發人員。  開發人員模式可讓您輕鬆地在本機執行應用程式。 在您的本機電腦上完成下列步驟。

1. 複製 `2-simple/src/main/liberty/config/server.xml` 到 `1-start/src/main/liberty/config` ，並覆寫現有的零長度檔案。 這會 `server.xml` 以 JAVA EE 功能設定開放式的自由伺服器。
1. 複製 `2-simple/pom.xml` 到 `1-start/pom.xml` 。  此步驟會將加入 `liberty-maven-plugin` 至 POM。
1. 將目錄變更為 `1-start` 您的本機複製。
1. `mvn clean package`在主控台中執行，以 `javaee-cafe.war` 在目錄中產生 war 套件 `./target` 。
1. 執行 `mvn liberty:dev` 以在開發模式中開始開啟「自由」。
1. 等候伺服器啟動。 主控台輸出的結尾應該會顯示下列訊息：

   ```Text
   [INFO] CWWKM2015I: Match number: 1 is [6/10/20 10:26:09:517 CST] 00000022 com.ibm.ws.kernel.feature.internal.FeatureManager            A CWWKF0011I: The defaultServer server is ready to run a smarter planet. The defaultServer server started in 6.447 seconds..
   [INFO] Press the Enter key to run tests on demand. To stop the server and quit dev mode, use Ctrl-C or type 'q' and press the Enter key.
   [INFO] Source compilation was successful.
   ```

1. [http://localhost:9080/](http://localhost:9080/)在您的瀏覽器中開啟，以流覽應用程式首頁。 應用程式看起來會如下圖所示：

   ![JAVAEE 咖啡廳 Web UI](./media/howto-deploy-java-liberty-app/javaee-cafe-web-ui.png)
1. 按 **ctrl-c** 停止應用程式，並開啟 [自由伺服器]。

您的 `2-simple` 本機複製目錄會顯示已套用上述變更的 Maven 專案。

## <a name="prepare-the-application-image"></a>準備應用程式映射

若要在 ARO 4 叢集上部署及執行您的自由應用程式，請使用 [開放式的自由容器映射](https://github.com/OpenLiberty/ci.docker) 或 WebSphere 的 [自由容器映射](https://github.com/WASdev/ci.docker)，以 Docker 映射的形式將您的應用程式。

### <a name="build-application-image"></a>建立應用程式映射

請完成下列步驟來建立應用程式映射：

1. 將目錄變更為 `2-simple` 您的本機複製。
2. 執行 `mvn clean package` 以封裝應用程式。
3. 執行下列其中一個命令來建立應用程式映射。
   * 使用開放式的自由基底映射建立：

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary Open Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull .
     ```

   * 使用 WebSphere 的自由基底映射組建：

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary WebSphere Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull --file=Dockerfile-wlp .
     ```

### <a name="run-the-application-locally-with-docker"></a>使用 Docker 在本機執行應用程式

將容器化應用程式部署到遠端叢集之前，請先使用您的本機 Docker 執行，以確認它是否正常運作：

1. `docker run -it --rm -p 9080:9080 javaee-cafe-simple:1.0.0`在主控台中執行。
2. 等候「自由伺服器」啟動，並成功部署應用程式。
3. [http://localhost:9080/](http://localhost:9080/)在您的瀏覽器中開啟，以流覽應用程式首頁。
4. 按 **ctrl + C** 以停止應用程式和自由伺服器。

### <a name="push-the-image-to-the-container-image-registry"></a>將映射推送至容器映射登錄

當您滿意應用程式的狀態時，請遵循下列指示將其推送至內建的容器映射登錄。

#### <a name="log-in-to-the-openshift-cli-as-the-azure-ad-user"></a>以 Azure AD 使用者的身份登入 OpenShift CLI

1. 使用 Azure AD 使用者的認證，從瀏覽器登入 OpenShift web 主控台。

   1. 您可以使用 InPrivate、Incognito 或其他對等的瀏覽器視窗功能來登入主控台。
   1. 選取 **openid**

   > [!NOTE]
   > 記下您用來登入的使用者名稱和密碼。 此使用者名稱和密碼會以系統管理員的身分來執行，以及其他文章中的其他動作。
1. 使用 OpenShift CLI 登入，方法是使用下列步驟。  針對討論，此程式稱為 `oc login` 。
   1. 在 web 主控台的右上方，展開已登入使用者的內容功能表，然後選取 [ **複製登** 入] 命令。
   1. 如有必要，請使用相同的使用者登入新的索引標籤視窗。
   1. 選取 [ **顯示標記** ]。
   1. 將下列以 **此權杖登** 入的值複製到剪貼簿，然後在 shell 中執行，如下所示。

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

#### <a name="push-the-container-image-to-the-container-registry-for-openshift"></a>將容器映射推送至 container registry 以進行 OpenShift

執行這些命令，將映射推送至容器登錄以進行 OpenShift。

```bash
# Note: replace "<Container_Registry_URL>" with the fully qualified name of the registry
Container_Registry_URL=<Container_Registry_URL>

# Create a new tag with registry info that refers to source image
docker tag javaee-cafe-simple:1.0.0 ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0

# Sign in to the built-in container image registry
docker login -u $(oc whoami) -p $(oc whoami -t) ${Container_Registry_URL}
```

成功的輸出看起來會如下所示。

```bash
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Login Succeeded
```

使用下列命令將映射推送至內建容器映射登錄。

```bash

docker push ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0
```

## <a name="deploy-application-on-the-aro-4-cluster"></a>在 ARO 4 叢集上部署應用程式

現在您可以將範例可使用的應用程式部署到您稍早在完成必要條件時所建立的 Azure Red Hat OpenShift 4 叢集。

### <a name="deploy-the-application-from-the-web-console"></a>從 web 主控台部署應用程式

因為我們使用開放式的自由運算子來管理自由應用程式，所以我們需要建立其 *自訂資源定義* 的實例，其類型為 "OpenLibertyApplication"。 操作員接著將負責管理部署所需的 OpenShift 資源。

1. 使用 Azure AD 使用者的認證，從瀏覽器登入 OpenShift web 主控台。
1. 展開 [ **首頁** ]，然後選取 [ **專案**  >  **開放-自由-示範** ]。
1. 流覽至 **Operators**  >  **已安裝** 操作員的操作員。
1. 在頁面的中間，選取 [ **開放式自由運算子** ]。
1. 在頁面的中間，選取 [ **開放式自由應用程式** ]。  使用者介面中的專案導覽會反映所使用之技術的實際內含專案階層。
   <!-- Diagram source https://github.com/Azure-Samples/open-liberty-on-aro/blob/master/diagrams/aro-java-containment.vsdx -->
   ![ARO JAVA 內含專案](./media/howto-deploy-java-liberty-app/aro-java-containment.png)
1. 選取 [ **建立 OpenLibertyApplication**
1. 將產生的 yaml 取代為您所在的位置 `<path-to-repo>/2-simple/openlibertyapplication.yaml` 。
1. 選取 [建立]。 您將會回到 OpenLibertyApplications 清單。
1. 選取 [ **javaee]-[咖啡館-簡單** ]。
1. 在頁面的中間，選取 [ **資源** ]。
1. 在資料表中，選取 **javaee** 的連結，其中包含 **路由** 的 **種類** 。
1. 在開啟的頁面上，選取下列 **位置** 的連結。

您會在瀏覽器中看到開啟的應用程式首頁。

### <a name="delete-the-application-from-the-web-console"></a>從 web 主控台刪除應用程式

當您完成應用程式時，請遵循下列步驟，從 Open Shift 中刪除應用程式。

1. 在左側流覽窗格中，展開 [ **運算子** ] 的專案。
1. 選取 [ **已安裝的操作員** ]。
1. 選取 [ **開放式自由運算子** ]。
1. 在頁面的中間，選取 [ **開放式自由應用程式** ]。
1. 選取垂直省略號 (三個垂直點) 然後選取 [ **刪除 OpenLiberty 應用程式** ]。

### <a name="deploy-the-application-from-cli"></a>從 CLI 部署應用程式

您可以從 CLI 部署應用程式，而不是使用 web 主控台 GUI。 如果您尚未這麼做，請 `oc` 遵循 Red Hat 檔 [消費者入門與 CLI 一起](https://docs.openshift.com/container-platform/4.2/cli_reference/openshift_cli/getting-started-cli.html)下載並安裝命令列工具。

1. 使用 Azure AD 使用者的認證，從瀏覽器登入 OpenShift web 主控台。
2. 使用 Azure AD 使用者的權杖登入 OpenShift CLI。
3. 將目錄變更為 `2-simple` 您的本機複製，然後執行下列命令，以將您的自由應用程式部署到 ARO 4 叢集。  命令輸出也會以內嵌方式顯示。

   ```bash
   # Switch to namespace "open-liberty-demo" where resources of demo app will belong to
   oc project open-liberty-demo

   Now using (or already on) project "open-liberty-demo" on server "https://api.aqlm62xm.rnfghf.aroapp.io:6443".

   # Create OpenLibertyApplication "javaee-cafe-simple"
   oc create -f openlibertyapplication.yaml

   openlibertyapplication.openliberty.io/javaee-cafe-simple created

   # Check if OpenLibertyApplication instance is created
   oc get openlibertyapplication javaee-cafe-simple

   NAME                 IMAGE                      EXPOSED   RECONCILED   AGE
   javaee-cafe-simple   javaee-cafe-simple:1.0.0   true      True         36s

   # Check if deployment created by Operator is ready
   oc get deployment javaee-cafe-simple

   NAME                 READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-cafe-simple   1/1     1            0           102s
   ```

4. 在繼續之前，請先檢查資料 `1/1` 行下的 `READY` 。  如果沒有，請調查並解決問題，然後再繼續進行。
5. 使用命令探索應用程式的路由主機 `oc get route` ，如下所示。

   ```bash
   # Get host of the route
   HOST=$(oc get route javaee-cafe-simple --template='{{ .spec.host }}')
   echo "Route Host: $HOST"

   Route Host: javaee-cafe-simple-open-liberty-demo.apps.aqlm62xm.rnfghf.aroapp.io
   ```

   當「自由」應用程式啟動並執行之後，請在瀏覽器中開啟 **路由主機** 的輸出，以造訪應用程式首頁。

### <a name="delete-the-application-from-cli"></a>從 CLI 刪除應用程式

藉由執行此命令，從 CLI 刪除應用程式。

```bash
oc delete -f openlibertyapplication.yaml
```

## <a name="clean-up-resources"></a>清除資源

遵循[教學課程：刪除 Azure Red Hat OpenShift 4](/azure/openshift/tutorial-delete-cluster)叢集中的步驟來刪除 ARO 叢集

## <a name="next-steps"></a>後續步驟

在本指南中，您已瞭解如何：
> [!div class="checklist"]
>
> * 準備自由應用程式
> * 建立應用程式映射
> * 使用 GUI 和 CLI 在 ARO 4 叢集上執行容器化應用程式

您可以從本指南所使用的參考中深入瞭解：

* [開啟自由](https://openliberty.io/)
* [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [開放式自由運算子](https://github.com/OpenLiberty/open-liberty-operator)
* [開啟 [自由伺服器設定]](https://openliberty.io/docs/ref/config/)
* [自由 Maven 外掛程式](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [開啟自由容器映射](https://github.com/OpenLiberty/ci.docker)
* [WebSphere 自由容器映射](https://github.com/WASdev/ci.docker)
