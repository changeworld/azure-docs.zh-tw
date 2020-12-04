---
title: 指定服務的環境變數
description: 示範如何在 Service Fabric 中使用應用程式的環境變數
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: 610e44ac98b8b8a2ce5a91fdbcfda145ae36a94b
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576735"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>如何在 Service Fabric 中指定服務的環境變數

本文會示範如何在 Service Fabric 中指定服務或容器的環境變數。

## <a name="procedure-for-specifying-environment-variables-for-services"></a>指定服務環境變數的程序

在此範例中，您可以設定容器的環境變數。 本文假設您已有應用程式和服務資訊清單。

1. 開啟 ServiceManifest.xml 檔案。
2. 在 `CodePackage` 元素中，為每個環境變數新增新的 `EnvironmentVariables` 元素和 `EnvironmentVariable` 元素。

    ```xml
    <CodePackage Name="MyCode" Version="CodeVersion1">
            ...
            <EnvironmentVariables>
                  <EnvironmentVariable Name="MyEnvVariable" Value="DefaultValue"/>
                  <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentVariables>
    </CodePackage>
    ```

   在應用程式資訊清單中可以覆寫環境變數。

3. 若要在應用程式資訊清單中覆寫環境變數，請使用 `EnvironmentOverrides` 元素。

    ```xml
      <ServiceManifestImport>
        <ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="specifying-environment-variables-dynamically-using-docker-compose"></a>使用 Docker Compose 動態指定環境變數

Service Fabric 支援 [使用 Docker Compose 進行部署](service-fabric-docker-compose.md#supported-compose-directives)的能力。 撰寫檔案可以從 shell 進行來源環境變數。 此行為可用來動態取代所需的環境值：

```yml
environment:
  - "hostname:${hostname}"
```

## <a name="next-steps"></a>後續步驟
若要深入了解這篇文章所討論的某些核心概念，請參閱[管理多個環境發行項的應用程式](service-fabric-manage-multiple-environment-app-configuration.md)。

如需 Visual Studio 中其他可用的應用程式管理功能的相關資訊，請參閱 [在 Visual Studio 中管理 Service Fabric 應用程式](service-fabric-manage-application-in-visual-studio.md)。
