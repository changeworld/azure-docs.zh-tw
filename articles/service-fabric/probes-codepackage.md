---
title: Azure Service Fabric 探查
description: 如何使用應用程式和服務資訊清單檔案，在 Azure Service Fabric 中建立活動探查的模型。
ms.topic: conceptual
author: tugup
ms.author: tugup
ms.date: 3/12/2020
ms.openlocfilehash: 07a1b836ca7ea79244e303f54654dfcaa6e5fcb9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82137581"
---
# <a name="liveness-probe"></a>活動探查
從7.1 版開始，Azure Service Fabric 支援[容器][containers-introduction-link]化應用程式的活動探查機制。 活動探查有助於報告容器化應用程式的活動，如果它沒有快速回應，則會重新開機。
本文概述如何使用資訊清單檔案定義活動探查。

在您繼續進行本文之前，請先熟悉[Service Fabric 應用程式模型][application-model-link]和[Service Fabric 裝載模型][hosting-model-link]。

> [!NOTE]
> 只有 NAT 網路模式中的容器支援活動探查。

## <a name="semantics"></a>語意
您只能為每個容器指定一個活動探查，並可使用下欄欄位來控制其行為：

* `initialDelaySeconds`：在容器啟動之後開始執行探查的初始延遲（以秒為單位）。 支援的值為**int**。預設值為0，而最小值為0。

* `timeoutSeconds`：我們將探查視為失敗的期間（以秒為單位），如果未順利完成。 支援的值為**int**。預設值為1，而最小值為1。

* `periodSeconds`：指定探查頻率的期間（以秒為單位）。 支援的值為**int**。預設值為10，而最小值為1。

* `failureThreshold`：當我們達到此值時，容器將會重新開機。 支援的值為**int**。預設值為3，而最小值為1。

* `successThreshold`：失敗時，若要將探查視為成功，必須成功執行此值。 支援的值為**int**。預設值為1，而最小值為1。

在任何時候，最多可以有一個容器進行一個探查。 如果探查未在**timeoutSeconds**中設定的時間內完成，請等候並計算**failureThreshold**的時間。 

此外，Service Fabric 將會在**DeployedServicePackage**上引發下列探查[健全狀況報告][health-introduction-link]：

* `OK`：已在**successThreshold**中設定的值的探查成功。

* `Error`：在**failureCount**  ==   容器重新開機之前，探查 failureCount**failureThreshold**。

* `Warning`: 
    * 探查失敗並**failureCount**  <  **failureThreshold**。 此健康情況報告會持續，直到**failureCount**達到**failureThreshold**或**successThreshold**中設定的值為止。
    * 在失敗後成功時，會保留警告，但更新會連續成功。

## <a name="specifying-a-liveness-probe"></a>指定活動探查

您可以在 [ **ServiceManifestImport**] 下的 [ApplicationManifest.xml] 檔案中指定探查。

探查可以是下列任何一項：

* HTTP
* TCP
* Exec 

### <a name="http-probe"></a>HTTP 探查

針對 HTTP 探查，Service Fabric 會將 HTTP 要求傳送至您指定的埠和路徑。 大於或等於200且小於400的傳回碼表示成功。

以下是如何指定 HTTP 探查的範例：

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <HttpGet Path="/" Port="8081" Scheme="http">
              <HttpHeader Name="Foo" Value="Val"/>
              <HttpHeader Name="Bar" Value="val1"/>
            </HttpGet>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

HTTP 探查有您可以設定的其他屬性：

* `path`：要在 HTTP 要求中使用的路徑。

* `port`：用於探查的埠。 這個屬性是必要的。 範圍是1到65535。

* `scheme`：用來連接到程式碼套件的配置。 如果此屬性設定為 HTTPS，則會略過憑證驗證。 預設設定為 HTTP。

* `httpHeader`：要在要求中設定的標頭。 您可以指定多個標頭。

* `host`：要連接的主機 IP 位址。

### <a name="tcp-probe"></a>TCP 探查

針對 TCP 探查，Service Fabric 會嘗試使用指定的埠開啟容器上的通訊端。 如果可以建立連線，探查會被視為成功。 以下是如何指定使用 TCP 通訊端之探查的範例：

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <TcpSocket Port="8081"/>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

### <a name="exec-probe"></a>Exec 探查

此探查會在容器中發出**exec**命令，並等候命令完成。

> [!NOTE]
> **Exec**命令接受以逗號分隔的字串。 下列範例中的命令將適用于 Linux 容器。
> 如果您正嘗試探查 Windows 容器，請使用**cmd**。

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <Exec>
              <Command>ping,-c,2,localhost</Command>
            </Exec>
          </Probe>        
       </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="next-steps"></a>後續步驟
如需相關資訊，請參閱下列文章：
* [Service Fabric 和容器][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

