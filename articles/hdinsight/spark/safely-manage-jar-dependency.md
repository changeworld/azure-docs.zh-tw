---
title: 安全地管理 jar 相依性-Azure HDInsight
description: 本文討論為 HDInsight 應用程式管理 JAVA 封存 (JAR) 相依性的最佳做法。
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 02/05/2020
ms.openlocfilehash: 9868e32665c89bbe9aadc06f1c2834704e6534e1
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942395"
---
# <a name="safely-manage-jar-dependencies"></a>安全地管理 Jar 相依性

安裝在 HDInsight 叢集上的元件具有協力廠商程式庫的相依性。 通常，這些內建元件會參考特定版本的常見模組（例如 Guava）。 當您提交具有相依性的應用程式時，可能會導致相同模組的不同版本之間發生衝突。 如果您先在類別路徑中參考的元件版本，內建元件可能會因為版本不相容而擲回例外狀況。 但是，如果內建元件先將其相依性插入至類別路徑，您的應用程式可能會擲回錯誤，例如 `NoSuchMethod` 。

若要避免版本衝突，請考慮將應用程式相依性加上陰影。

## <a name="what-does-package-shading-mean"></a>封裝陰影的意義為何？
網底提供一種方式來包含和重新命名相依性。 它會重新放置類別，並重寫受影響的位元組程式碼和資源，以建立相依性的私用複本。

## <a name="how-to-shade-a-package"></a>如何為套件加上網底？

### <a name="use-uber-jar"></a>使用 uber-jar
Uber-jar 是一個 jar 檔案，其中包含應用程式 jar 及其相依性。 Uber 中的相依性依預設不會加上陰影。 在某些情況下，如果其他元件或應用程式參考不同版本的程式庫，這可能會導致版本衝突。 若要避免這種情況，您可以建立 Uber-Jar 檔案，其中包含部分 (或所有相依性的) 陰影。

### <a name="shade-package-using-maven"></a>使用 Maven 將封裝加上陰影
Maven 可以建立以 JAVA 和 Scala 撰寫的應用程式。 Maven-陰影-外掛程式可協助您輕鬆建立陰影的 uber jar。

下列範例顯示已更新的檔案 `pom.xml` ，以使用 maven-陰影-外掛程式將套件加上陰影。  XML 區段藉 `<relocation>…</relocation>` `com.google.guava` `com.google.shaded.guava` 由移動對應的 JAR 檔案專案並重寫受影響的位元組程式碼，將類別從封裝移至封裝中。

變更之後 `pom.xml` ，您可以執行 `mvn package` 以建立陰影 uber jar。

```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <relocations>
                <relocation>
                  <pattern>com.google.guava</pattern>
                  <shadedPattern>com.google.shaded.guava</shadedPattern>
                </relocation>
              </relocations>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

### <a name="shade-package-using-sbt"></a>使用 SBT 將封裝加上陰影
SBT 也是 Scala 和 JAVA 的組建工具。 SBT 沒有陰影外掛程式，例如 maven-陰影-外掛程式。 您可以修改檔案 `build.sbt` 以將封裝加上陰影。 

例如， `com.google.guava` 您可以將下列命令新增至檔案中，以加上網底 `build.sbt` ：

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

然後，您可以執行 `sbt clean` 和 `sbt assembly` 來建立陰影的 jar 檔案。 

## <a name="next-steps"></a>後續步驟

* [使用 HDInsight IntelliJ 工具](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)

* [在 IntelliJ 中為 Spark 建立 Scala Maven 應用程式](./apache-spark-create-standalone-application.md)