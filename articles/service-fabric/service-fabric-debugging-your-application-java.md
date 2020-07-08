---
title: 在 Eclipse 中對應用程式進行 Debug
description: 透過 Eclipse 在本機開發叢集上進行服務開發和偵錯，來改善您服務的可靠性和效能。
author: suhuruli
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: 15448a9bd8998a99e8fce578b05130694ecd5fd0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75614480"
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>使用 Eclipse 針對 Java Service Fabric 應用程式進行偵錯
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
> 

1. 遵循 [設定 Service Fabric 開發環境](service-fabric-get-started-linux.md)中的步驟來啟動本機開發叢集。

2. 更新您想要偵錯之服務的 entryPoint.sh，使其以遠端偵錯參數開始 Java 處理程序。 您可以在以下位置找到此檔案：`ApplicationName\ServiceNamePkg\Code\entrypoint.sh`。 此範例已設定連接埠 8001 來進行偵錯。

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. 針對所要偵錯的服務，將執行個體計數或複本計數設定為 1，來更新「應用程式資訊清單」。 此設定可避免用於偵錯的連接埠發生衝突。 例如，針對無狀態服務，請設定 `InstanceCount="1"`，而針對具狀態服務，則請將目標和最小複本集大小設定為 1，如下所示：`TargetReplicaSetSize="1" MinReplicaSetSize="1"`。

4. 部署應用程式。

5. 在 Eclipse IDE 中，選取 [Run] \(執行) -> [Debug Configurations] \(偵錯組態) -> [Remote Java Application and input connection properties] \(遠端 Java 應用程式和輸入連線屬性)****，然後依照下列方式設定屬性：

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  在想要的點設定中斷點並對應用程式進行偵錯。

如果應用程式當掉，您可能也會想要啟用核心傾印。 請在殼層中執行 `ulimit -c`，如果它傳回 0，則表示未啟用核心傾印。 若要啟用無限制的核心傾印，請執行下列命令：`ulimit -c unlimited`。 您也可以使用 `ulimit -a` 命令來確認狀態。  如果您想要更新核心傾印產生路徑，請執行 `echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern`。 

### <a name="next-steps"></a>後續步驟

* [使用 Linux Azure 診斷來收集記錄](service-fabric-diagnostics-how-to-setup-lad.md)。
* 在[本機監視及診斷服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)。
