---
title: 從 JAVA SDK 遷移至 Maven
description: 更新用於使用 Service Fabric Java SDK 的舊版 Java 應用程式，以從 Maven 擷取 Service Fabric Java 相依性。 完成此設定之後，就能夠建置舊版 Java 應用程式。
author: rapatchi
ms.topic: conceptual
ms.date: 08/23/2017
ms.custom: devx-track-java
ms.author: rapatchi
ms.openlocfilehash: 3efa51f5632dd5cdc274ea39df5178aa0351a01f
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97652291"
---
# <a name="update-your-previous-java-service-fabric-application-to-fetch-java-libraries-from-maven"></a>更新先前的 Java Service Fabric 應用程式，以從 Maven 擷取 Java 程式庫
Service Fabric JAVA 二進位檔已從 Service Fabric JAVA SDK 移至 Maven 裝載。 您可以使用 **>mavencentral** 來提取最新的 Service Fabric JAVA 相依性。 本指南將協助您使用 Yeoman 範本或 Eclipse 來更新針對 Service Fabric JAVA SDK 建立的現有 JAVA 應用程式，使其與 Maven 架構的組建相容。

## <a name="prerequisites"></a>Prerequisites

1. 首先，將現有的 JAVA SDK 卸載。

   ```bash
   sudo dpkg -r servicefabricsdkjava
   ```

2. 依照[這裡](service-fabric-cli.md)所述的步驟，安裝最新的 Service Fabric CLI。

3. 若要建立及處理 Service Fabric JAVA 應用程式，請確定您已安裝 JDK 1.8 和 Gradle。 如果尚未安裝，您可以執行下列命令來安裝 JDK 1.8 (openjdk-8-jdk) 和 Gradle -

   ```bash
   sudo apt-get install openjdk-8-jdk-headless
   sudo apt-get install gradle
   ```

4. 依照[這裡](service-fabric-application-lifecycle-sfctl.md)所述的步驟，更新您應用程式的安裝/解除安裝指令碼，以使用新的 Service Fabric CLI。 您可以參考我們的快速入門[範例](https://github.com/Azure-Samples/service-fabric-java-getting-started)以供參考。

>[!TIP]
> 解除安裝 Service Fabric Java SDK 之後，Yeoman 將無法運作。 依照[這裡](service-fabric-create-your-first-linux-application-with-java.md)所提及的必要條件，啟動並執行 Service Fabric Yeoman Java 範本產生器。

## <a name="service-fabric-java-libraries-on-maven"></a>Maven 上的 Service Fabric Java 程式庫

Service Fabric Java 程式庫已裝載於 Maven 中。 您可以在專案的 ``pom.xml`` 或 ``build.gradle`` 中新增相依性，以從 **mavenCentral** 使用 Service Fabric Java 程式庫。

### <a name="actors"></a>動作項目

您應用程式的 Service Fabric Reliable Actor 支援。

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors:1.0.0'
  }
  ```

### <a name="services"></a>服務

您應用程式的 Service Fabric 無狀態服務支援。

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services:1.0.0'
  }
  ```

### <a name="others"></a>其他

#### <a name="transport"></a>傳輸

Service Fabric Java 應用程式的傳輸層支援。 除非您在傳輸層進行程式設計，否則不需要明確地將此相依性新增至 Reliable Actor 或服務應用程式。

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport:1.0.0'
  }
  ```

#### <a name="fabric-support"></a>網狀架構支援

Service Fabric 的系統層級支援，其可與原生 Service Fabric 執行階段交談。 您不需要明確地將此相依性新增至 Reliable Actor 或服務應用程式。 當您納入上述其他相依性時，可從 Maven 自動提取此相依性。

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf:1.0.0'
  }
  ```

## <a name="migrating-service-fabric-stateless-service"></a>移轉 Service Fabric 無狀態服務

若要能夠使用從 Maven 提取的 Service Fabric 相依性，建立現有的 Service Fabric 無狀態 Java 服務，您需要更新服務內的 ``build.gradle`` 檔案。 它先前通常如下所示 -

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':Interface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
}
.
.
.
task copyDeps <<{
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('libj*.so')
    }
}
```

現在，若要從 Maven 提取相依性，**已更新的** ``build.gradle`` 會有對應的組件，如下所示 -

```gradle
repositories {
        mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':Interface')
    azuresf ('com.microsoft.servicefabric:sf-services:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": mpath)
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
   }
}
.
.
.
task copyDeps <<{
    copy {
        from("lib/")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*')
    }
}
```

一般而言，若要取得有關如何組建指令碼看起來像 Service Fabric 無狀態 Java 服務的整體概念，您可以參考我們的快速入門範例中的任何範例。 以下是 EchoServer 範例的 [build.gradle](https://github.com/Azure-Samples/service-fabric-java-getting-started/blob/master/reliable-services-actor-sample/build.gradle)。

## <a name="migrating-service-fabric-actor-service"></a>移轉 Service Fabric 動作項目服務

若要能夠使用從 Maven 提取的 Service Fabric 相依性，建立現有的 Service Fabric 動作項目 Java 應用程式，您需要更新介面套件和服務套件內的 ``build.gradle`` 檔案。 如果您有 TestClient 套件，您需要一併更新。 因此，針對您的動作項目 ``Myactor``，以下是您需要更新的位置 -

```
./Myactor/build.gradle
./MyactorInterface/build.gradle
./MyactorTestClient/build.gradle
```

#### <a name="updating-build-script-for-the-interface-project"></a>更新介面專案的組建指令碼

它先前通常如下所示 -

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
}
.
.
```

現在，若要從 Maven 提取相依性，**已更新的** ``build.gradle`` 會有對應的組件，如下所示 -

```gradle
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
```

#### <a name="updating-build-script-for-the-actor-project"></a>更新動作項目專案的組建指令碼

它先前通常如下所示 -

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':MyactorInterface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
      baseName "myactor"
    destinationDir = file('./../myjavaapp/MyactorPkg/Code')
    }
}
.
.
.
task copyDeps<< {
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('libj*.so')
    }
    copy {
        from("../MyactorInterface/out/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
}
```

現在，若要從 Maven 提取相依性，**已更新的** ``build.gradle`` 會有對應的組件，如下所示 -

```gradle
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": mpath)
    baseName "myactor"
    destinationDir = file('../myjavaapp/MyactorPkg/Code')}
 }
.
.
.
task copyDeps<< {
      copy {
              from("lib/")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*')
      }
      copy {
              from("../MyactorInterface/out/lib")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*.jar')
      }
}
```

#### <a name="updating-build-script-for-the-test-client-project"></a>更新測試用戶端專案的組建指令碼

此處的變更類似於上一節所討論的變更，也就是動作項目專案。 Gradle 指令碼先前通常如下所示 -

```gradle
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':MyactorInterface')
}
.
.
.
jar
{
    manifest {
    attributes(
        'Main-Class': 'reliableactor.test.MyactorTestClient',
        "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    }
    baseName "myactor-test"
    destinationDir = file('out/lib')
}
.
.
.
task copyDeps<< {
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('libj*.so')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```

現在，若要從 Maven 提取相依性，**已更新的** ``build.gradle`` 會有對應的組件，如下所示 -

```gradle
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors:1.0.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar
{
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
    attributes(
                'Main-Class': 'reliableactor.test.MyactorTestClient',
                "Class-Path": mpath)
    baseName "myactor-test"
    destinationDir = file('./out/lib')
        }
}
.
.
.
task copyDeps<< {
        copy {
                from("lib/")
                into("./out/lib/lib")
                include('*')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```

## <a name="next-steps"></a>後續步驟

* [使用 Yeoman 在 Linux 上建立和部署第一個 Service Fabric Java 應用程式](service-fabric-create-your-first-linux-application-with-java.md)
* [在 Linux 上使用適用於 Eclipse 的 Service Fabric 外掛程式建立和部署第一個 Service Fabric Java 應用程式](service-fabric-get-started-eclipse.md)
* [使用 Service Fabric CLI 與 Service Fabric 叢集互動](service-fabric-cli.md)
