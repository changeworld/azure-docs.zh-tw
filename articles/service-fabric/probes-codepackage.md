---
title: Azure 服務結構探測器
description: 如何使用應用程式和服務清單檔在 Azure 服務結構中建模即時探測。
ms.topic: conceptual
ms.date: 3/12/2020
ms.openlocfilehash: 38f3888a29bf505b723d40bc7cd08fb0c7e29eff
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431211"
---
# <a name="liveness-probe"></a>活度探頭
從 7.1 服務結構開始支援[容器化][containers-introduction-link]應用的活度探測機制。 Liveness 探測有助於宣佈容器化應用程式的真實性,當它們不及時回應時,將導致重新啟動。
本文概述了如何通過清單檔定義動態探測。

在繼續本文之前,我們建議熟悉[服務結構應用程式模型][application-model-link][和服務結構託管模型][hosting-model-link]。

> [!NOTE]
> 僅在 NAT 網路模式下的容器中支援活動探測。

## <a name="semantics"></a>語意
每個容器只能指定一個活動探測,並且可以使用以下欄位控制其行為:

* `initialDelaySeconds`:容器啟動后,在數秒內開始執行探測的初始延遲。 支援的值為 int。預設值為 0。 最小值為 0。

* `timeoutSeconds`:在探測器未成功完成之後,我們將探測器視為失敗。 支援的值為 int。預設值為 1。 最小值為 1。

* `periodSeconds`:以秒為單位,指定我們探測的頻率。 支援的值為 int。預設值為 10。 最小值為 1。

* `failureThreshold`:一旦我們達到故障閾值,容器將重新啟動。 支援的值為 int。預設值為 3。 最小值為 1。

* `successThreshold`:在失敗時,要將探測器視為成功,必須成功執行成功閾值。 支援的值為 int。預設值為 1。 最小值為 1。

一次最多會有 1 個探頭到容器。 如果探頭沒有在逾時中完成**秒,** 我們會一直等待並將其計入**故障閾值**。 

此外,ServiceFabric 將在部署的服務包上引發以下探測[運行狀況報告][health-introduction-link]:

* `Ok`:如果探測器**成功,那麼**我們將運行狀況報告為"確定"。

* `Error`:如果探測失敗Count =**失敗閾值**,則在重新啟動容器之前,我們會報告錯誤。

* `Warning`: 
    1. 如果探測失敗,並且故障計數<**故障閾值**,我們會報告警告。 此健康報告一直持續到失敗計數達到**失敗閾值**或**成功閾值**。
    2. 關於成功失敗后,我們仍然報告警告,但更新的連續成功。

## <a name="specifying-liveness-probe"></a>指定活動性探測

您可以在「服務清單匯入」下的應用程式清單.xml 中指定探測:

探頭可以之一:

1. HTTP
2. TCP
3. Exec 

## <a name="http-probe"></a>HTTP 探頭

對於 HTTP 探測,服務交換矩陣將向指定的埠和路徑發送 HTTP 請求。 返回代碼大於或等於 200 且小於 400 表示成功。

下面是如何指定 HTTPGet 探測器的範例:

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

HTTPGet 偵測器具有您可以設定的其他屬性:

* `path`:HTTP 請求上訪問的路徑。

* `port`:要進入探頭的埠。 範圍為 1 到 65535。 Mandatory。

* `scheme`:用於連接到代碼包的方案。 如果設置為 HTTPS,將跳過憑證驗證。 預設值為 HTTP

* `httpHeader`:要在請求中設置的標頭。 您可以指定其中的多個。

* `host`:要連接到的主機 IP。

## <a name="tcp-probe"></a>TCP 探頭

對於 TCP 探測,Service Fabric 將嘗試打開容器上的套接字,該套接字帶有指定的埠。 如果可以建立連接,則探測器將被視為成功。 下面是如何指定使用 TCP 通訊通訊的探測的範例:

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

## <a name="exec-probe"></a>執行探測

此探測將發出執行器到容器中,並等待命令完成。

> [!NOTE]
> Exec 命令採用逗號分隔字串。 範例中的以下指令適用於 Linux 容器。
> 如果您正在嘗試視窗容器,請使用<Command>cmd</Command>

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
有關相關信息,請參閱以下文章。
* [服務結構和容器。][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

