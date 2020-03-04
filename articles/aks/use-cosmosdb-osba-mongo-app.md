---
title: 將現有的 MongoDB 應用程式與適用於 MongoDB 的 Azure Cosmos DB API 和 Open Service Broker for Azure (OSBA) 整合
description: 在本文中，您會了解如何將現有的 Java 和 MongoDB 應用程式與適用於 MongoDB 的 Azure Cosmos DB API 和 Open Service Broker for Azure (OSBA) 整合。
author: zr-msft
ms.service: azure-dev-spaces
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: zarhoads
ms.custom: mvc
keywords: Cosmos DB, Open Service Broker, Open Service Broker for Azure
ms.openlocfilehash: ddaa3b9aa198bc142e1bcbcab6b7b1e028eff2aa
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78247928"
---
# <a name="integrate-existing-mongodb-application-with-azure-cosmos-db-api-for-mongodb-and-open-service-broker-for-azure-osba"></a>將現有的 MongoDB 應用程式與適用於 MongoDB 的 Azure Cosmos DB API 和 Open Service Broker for Azure (OSBA) 整合

Azure Cosmos DB 是全域散發的多模型資料庫服務。 它也提供與數個 NoSQL API (包括適用於 MongoDB 的 API) 的 Wire Protocol 相容性。 適用於 MongoDB 的 Cosmos DB API 可讓您搭配現有的 MongoDB 應用程式使用 Cosmos DB，而無須變更應用程式的資料庫驅動程式或實作。 您也可以使用 Open Service Broker for Azure 來佈建 Cosmos DB 服務。

在本文中，您會採用一個使用 MongoDB 資料庫的現有 Java 應用程式，然後使用 Open Service Broker for Azure 將它更新成使用 Cosmos DB 資料庫。

## <a name="prerequisites"></a>必要條件

在繼續之前，您必須先：
    
* 建立一個 [Azure Kubernetes Service 叢集](kubernetes-walkthrough.md)。
* [在 AKS 叢集上安裝並設定 Open Service Broker for Azure](integrate-azure.md)。 
* 安裝並設定 [Service Catalog CLI](https://svc-cat.io/docs/install/) 以執行 `svcat` 命令。
* 擁有一個現有的 [MongoDB](https://www.mongodb.com/) 資料庫。 例如，您可以在[開發機器](https://docs.mongodb.com/manual/administration/install-community/)上或 [Azure VM](../virtual-machines/linux/install-mongodb.md) 中執行 MongoDB。
* 擁有一個連線至 MongoDB 資料庫並進行查詢的方法，例如 [Mongo 殼層](https://docs.mongodb.com/manual/mongo/)。

## <a name="get-application-code"></a>取得應用程式程式碼
    
在本文中，您會使用[來自 Cloud Foundry 的 Spring 音樂範例應用程式](https://github.com/cloudfoundry-samples/spring-music)來示範使用 MongoDB 資料庫的應用程式。
    
從 GitHub 複製應用程式，並瀏覽至其目錄：
    
```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring-music
```

## <a name="prepare-the-application-to-use-your-mongodb-database"></a>準備應用程式以使用您的 MongoDB 資料庫

Spring 音樂範例應用程式提供許多資料來源選項。 在本文中，您會將它設定成使用現有的 MongoDB 資料庫。 

請將下列 YAML 新增到 *src/main/resources/application.yml* 結尾。 這個新增會建立一個名為 *mongodb* 的設定檔，並設定 URI 和資料庫名稱。 請以您現有 MongoDB 資料庫的連線資訊取代 URI。 將包含使用者名稱和密碼的 URI 直接新增到這個檔案**僅限用於開發**，而**一律不應該新增到版本控制中**。

```yaml
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://user:password@serverAddress:port/musicdb"
      database: musicdb
```



當您啟動應用程式並告知它使用 *mongodb* 設定檔時，它會連線到 MongoDB 資料庫，並使用它來儲存應用程式的資料。

建置您的應用程式：

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```

啟動應用程式並告知它使用 *mongodb* 設定檔：

```cmd
java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

在瀏覽器中瀏覽至 `http://localhost:8080`。

![有預設資料的春天音樂應用程式](media/music-app.png)

請注意，應用程式已填入一些[預設資料](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json)。 請刪除一些現有專輯並建立幾個新專輯，來與其進行互動。

您可以藉由連線到 MongoDB 資料庫並查詢 *musicdb* 資料庫，來確認應用程式目前是否使用 MongoDB 資料庫：

```cmd
mongo serverAddress:port/musicdb -u user -p password
use musicdb
db.album.find()

{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446d"), "title" : "Nevermind", "artist" : "Nirvana", "releaseYear" : "1991", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446e"), "title" : "Pet Sounds", "artist" : "The Beach Boys", "releaseYear" : "1966", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446f"), "title" : "What's Going On", "artist" : "Marvin Gaye", "releaseYear" : "1971", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
...
```

上述範例使用 [Mongo 殼層](https://docs.mongodb.com/manual/mongo/) 來連線到 MongoDB 資料庫並查詢它。 您也可以藉由停止應用程式、重新啟動應用程式，然後在瀏覽器中瀏覽回該應用程式，來確認您的變更是否已保存下來。 請注意，您所做的變更還在。


## <a name="create-a-cosmos-db-database"></a>建立 Cosmos DB 資料庫

若要使用 Open Service Broker 在 Azure 中建立 Cosmos DB 資料庫，請使用 `svcat provision` 命令：

```cmd
svcat provision musicdb --class azure-cosmosdb-mongo-account --plan account  --params-json '{
  "location": "eastus",
  "resourceGroup": "MyResourceGroup",
  "ipFilters" : {
    "allowedIPRanges" : ["0.0.0.0/0"]
  }
}'
```

上述命令會在 Azure 之 *eastus* 區域的 *MyResourceGroup* 資源群組中，佈建 Cosmos DB資料庫。 如需有關 *resourceGroup*、*location* 及其他 Azure 特定 JSON 參數的詳細資訊，請參閱 [Cosmos DB 模組參考文件](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/cosmosdb.md#provision-3) \(英文\)。

若要確認資料庫是否已完成佈建，請使用 `svcat get instance` 命令：

```cmd
$ svcat get instance musicdb

   NAME     NAMESPACE              CLASS                PLAN     STATUS
+---------+-----------+------------------------------+---------+--------+
  musicdb   default     azure-cosmosdb-mongo-account   account   Ready
```

當您在 *STATUS* 底下看到 *Ready* 時，即表示資料庫已就緒。

在資料庫完成佈建之後，您必須將其中繼資料繫結至 [Kubernetes 祕密](https://kubernetes.io/docs/concepts/configuration/secret/)。 接著，其他應用程式便可在資料繫結至祕密之後，存取該資料。 若要將資料庫的中繼資料繫結至祕密，請使用 `svcat bind` 命令：

```cmd
$ svcat bind musicdb

  Name:        musicdb
  Namespace:   default
  Status:
  Secret:      musicdb
  Instance:    musicdb

Parameters:
  No parameters defined
```


## <a name="use-the-cosmos-db-database-with-your-application"></a>搭配您的應用程式使用 Cosmos DB 資料庫

若要搭配您的應用程式使用 Cosmos DB 資料庫，您必須知道 URI，才能與其連線。 若要取得此資訊，請使用 `kubectl get secret` 命令：

```cmd
$ kubectl get secret musicdb -o=jsonpath='{.data.uri}' | base64 --decode

mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
```

上述命令會取得 *musicdb* 祕密並只顯示 URI。 祕密會以 Base64 格式儲存，以便上述命令也可將其解碼。

使用 Cosmos DB 資料庫的 URI，將 *src/main/resources/application.yml* 更新成使用它：

```yaml
...
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb"
      database: musicdb
```

將包含使用者名稱和密碼的 URI 直接更新到這個檔案中**僅限用於開發**，而**一律不應該新增到版本控制中**。

重建及啟動您的應用程式，以開始使用 Cosmos DB 資料庫：

```cmd
./gradlew clean assemble

java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

請注意，您的應用程式仍使用 *mongodb* 設定檔和開頭為 *mongodb://* 的 URI 來連線到 Cosmos DB 資料庫。 [適用於 MongoDB 的 Azure Cosmos DB API](../cosmos-db/mongodb-introduction.md) 提供此相容性。 它可讓您的應用程式就像使用 MongoDB 資料庫一樣繼續運作，但其實使用的是 Cosmos DB。

在瀏覽器中瀏覽至 `http://localhost:8080`。 請注意，預設資料已被還原。 請刪除一些現有專輯並建立幾個新專輯，來與其進行互動。 您可以藉由停止應用程式、重新啟動應用程式，然後在瀏覽器中瀏覽回該應用程式，來確認您的變更是否已保存下來。 請注意，您所做的變更還在。 這些變更會保存至您使用 Service Broker for Azure 來建立的 Cosmos DB 中。


## <a name="run-your-application-on-your-aks-cluster"></a>在 AKS 叢集上執行您的應用程式

您可以使用 [Azure Dev Spaces](../dev-spaces/azure-dev-spaces.md) 將應用程式部署到您的 AKS 叢集。 Azure Dev Spaces 可協助您產生成品（例如 Dockerfile 和 Helm 圖表），以及在 AKS 中部署和執行應用程式。

在您的 AKS 叢集中啟用 Azure Dev Spaces：

```azurecli
az aks enable-addons --addons http_application_routing -g MyResourceGroup -n MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

使用 Azure Dev Spaces 工具來準備您的應用程式，以在 AKS 中執行：

```cmd
azds prep --public
```

此命令會在專案根目錄產生數個成品，包括 *charts/* 資料夾，這是您的 Helm 圖表。 此命令無法為這個特定專案產生 *Dockerfile*，因此您必須建立它。

使用下列內容，在您專案的根目錄建立一個名為 *Dockerfile* 的檔案：

```dockerfile
FROM openjdk:8-jdk-alpine
EXPOSE 8080
WORKDIR /app
COPY build/libs/spring-music-1.0.jar .
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
```

此外，您還必須將 *azds.yaml* 中的 *configurations.develop.build* 屬性更新成 *false*：
```yaml
...
configurations:
  develop:
    build:
      useGitIgnore: false
```

您也必須將 *charts/spring-music/templates/deployment.yaml*中的 *containerPort* 屬性更新成 *8080*：

```yaml
...
spec:
  ...
  template:
    ...
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          ports:
            - name: http
              containerPort: 8080
              protocol: TCP
```

將您的應用程式部署至 AKS：

```cmd
$ azds up

Using dev space 'default' with target 'MyAKS'
Synchronizing files...1m 18s
Installing Helm chart...5s
Waiting for container image build...23s
Building container image...
Step 1/5 : FROM openjdk:8-jdk-alpine
Step 2/5 : EXPOSE 8080
Step 3/5 : WORKDIR /app
Step 4/5 : COPY build/libs/spring-music-1.0.jar .
Step 5/5 : ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
Built container image in 21s
Waiting for container...8s
Service 'spring-music' port 'http' is available at http://spring-music.1234567890abcdef1234.eastus.aksapp.io/
Service 'spring-music' port 8080 (TCP) is available at http://localhost:57892
press Ctrl+C to detach
...
```

請瀏覽至記錄中顯示的 URL。 在上述範例中，您會使用 *http://spring-music.1234567890abcdef1234.eastus.aksapp.io/* 。 

請確認您看到應用程式，以及您所做的變更。

## <a name="next-steps"></a>後續步驟

本文說明如何將現有的應用程式從使用 MongoDB 更新成使用適用於 MongoDB 的 Cosmos DB API。 本文也涵蓋如何使用 Open Service Broker for Azure 來佈建 Cosmos DB 服務，以及使用 Azure Dev Spaces 將該應用程式部署至 AKS。

如需有關 Cosmos DB、Open Service Broker for Azure 及 Azure Dev Spaces 的詳細資訊，請參閱：
* [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)
* [Open Service Broker for Azure](https://osba.sh) \(英文\)
* [使用 Dev Spaces 進行開發](../dev-spaces/azure-dev-spaces.md)
