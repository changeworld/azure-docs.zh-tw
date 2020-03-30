---
title: 管理 JAR 依賴項 - Azure HDInsight
description: 本文討論了管理 HDInsight 應用程式的 JAVA 存檔 （JAR） 依賴項的最佳做法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: da3387dd9846847f7643ded43c8cbff8ed8b166e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77135729"
---
# <a name="jar-dependency-management-best-practices"></a>JAR 依賴項管理最佳實踐

安裝在 HDInsight 群集上的元件依賴于協力廠商庫。 通常，這些內建群組件會引用諸如 Guava 等通用模組的特定版本。 當您提交具有其依賴項的應用程式時，可能會導致同一模組的不同版本之間的衝突。 如果首先在類路徑中引用的元件版本，則內建群組件可能會由於版本不相容而引發異常。 但是，如果內建群組件首先將其依賴項注入到類路徑，則應用程式可能會引發類似`NoSuchMethod`的錯誤。

為了避免版本衝突，請考慮對應用程式依賴項進行對等進行對等的還原。

## <a name="what-does-package-shading-mean"></a>包裝上帶下什麼含義？
對下定義提供了一種包含和重命名依賴項的方法。 它重新置放類並重寫受影響的位元組碼和資源，以創建依賴項的私有副本。

## <a name="how-to-shade-a-package"></a>如何為包裹著色？

### <a name="use-uber-jar"></a>使用優步
Uber-jar 是一個包含應用程式 jar 及其依賴項的單個 jar 檔。 預設情況下，Uber-jar 中的依賴項沒有上用。 在某些情況下，如果其他元件或應用程式引用這些庫的不同版本，則可能會引入版本衝突。 為了避免這種情況，您可以構建一個 Uber-Jar 檔，該檔包含一些（或全部）依賴項。

### <a name="shade-package-using-maven"></a>使用 Maven 的陰影包
Maven 可以構建以 JAVA 和 Scala 編寫的應用程式。 Maven-shade-外掛程式可以説明您輕鬆創建遮陽超級外掛程式。

下面的示例顯示了一個檔`pom.xml`，該檔已更新，用於使用 maven-shade-外掛程式對包進行著色。  XML 部分`<relocation>…</relocation>`通過移動相應的`com.google.guava`JAR`com.google.shaded.guava`檔條目並重寫受影響的位元組碼，將類從包移動到包中。

更改`pom.xml`後，可以執行`mvn package`以構建具有掃描的 uber-jar。

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

### <a name="shade-package-using-sbt"></a>使用 SBT 的陰影包
SBT 也是 Scala 和 JAVA 的構建工具。 SBT 沒有像 maven-shade 外掛程式那樣的陰影外掛程式。 您可以將檔修改`build.sbt`為陰影包。 

例如，要著色`com.google.guava`，可以向`build.sbt`檔添加以下命令：

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

然後，您可以運行`sbt clean`並`sbt assembly`生成已掃描的 jar 檔。 

## <a name="next-steps"></a>後續步驟

* [使用 HDInsight 智慧 J 工具](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox)

* [在 IntelliJ 中為 Spark 創建 Scala Maven 應用程式](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-create-standalone-application)
