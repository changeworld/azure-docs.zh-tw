---
title: 創建a.Net核心應用並將其發佈到遠端 Linux 群集
description: 創建和發佈 .Net Core 應用程式，從 Visual Studio 創建遠端 Linux 群集
author: peterpogorski
ms.topic: troubleshooting
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: c30eedb6782e4172d677f16e27441f28c78cdd89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614344"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>使用 Visual Studio 創建和發佈面向遠端 Linux 服務交換矩陣群集的 .Net Core 應用程式
借助 Visual Studio 工具，您可以開發和發佈面向 Linux 服務交換矩陣群集的服務交換矩陣 .Net Core 應用程式。 SDK 版本必須為 3.4 或以上，才能從 Visual Studio 部署針對 Linux 服務交換矩陣群集的 .Net Core 應用程式。

> [!Note]
> Visual Studio 不支援調試面向 Linux 的服務交換矩陣應用程式。
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>創建面向 .Net 核心的服務交換矩陣應用程式
1. 以**系統管理員**身分啟動 Visual Studio。
2. 使用**檔>"新>專案創建專案**"。
3. 在 **"新專案**"對話方塊中，選擇**雲->服務結構應用程式**。
![創建應用程式]
4. 命名應用程式，然後按一下 **"確定**"。
5. 在 **"新服務交換矩陣服務"** 頁上，選擇要在 **.Net 核心部分**下創建的服務類型。
![創建服務]

## <a name="deploy-to-a-remote-linux-cluster"></a>部署到遠端 Linux 群集
1. 在解決方案資源管理器中，按右鍵應用程式並選擇 **"生成**"。
![生成應用程式]
2. 完成應用程式的生成過程後，按右鍵服務並選擇編輯**csproj 檔**。
![編輯-csproj]
3. 如果服務是**參與者專案類型**，則從 True 編輯 UpdateServiceFabricManifest 啟用屬性從 True 到**false。** 如果應用程式沒有參與者服務，請跳到步驟 4。
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> 將更新服務格式清單設置為 false，將在生成期間禁用對 ServiceManifest.xml 的更新。 任何更改（如向服務添加、刪除或重命名）將不會反映在 ServiceManifest.xml 中。 如果進行了任何更改，則必須手動更新服務清單，或臨時將 UpdateServiceFabricManifestEnabled 設置為 true，並生成將更新 ServiceManifest.xml 的服務，然後將其還原為 false。
>

4. 將運行時 Indetifier 從 win7-x64 更新到服務專案中的目標平臺。
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. 在服務清單中，更新進入點程式以刪除 .exe。 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. 在解決方案資源管理器中，按右鍵應用程式並選擇 **"發佈**"。 [發佈]**** 對話方塊隨即出現。
7. 在**連接終結點**中，選擇要定位的遠端服務結構 Linux 群集的終結點。
![發佈應用程式]

<!--Image references-->
[創建應用程式]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[創建服務]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[生成應用程式]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[編輯-csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[發佈應用程式]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>後續步驟
* 瞭解[使用 .Net 核心開始使用服務交換矩陣](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)
