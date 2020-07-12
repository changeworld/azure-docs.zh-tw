---
title: 使用 Windows 安全性保護在 Windows 上執行的叢集
description: 了解如何使用 Windows 安全性在 Windows 上執行的獨立叢集上設定節點對節點和用戶端對節點安全性。
author: dkkapur
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: dekapur
ms.openlocfilehash: 360bba2ffc344175214c44e2c9c1d3c0859ac3e5
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86255960"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>使用 Windows 安全性保護 Windows 上的獨立叢集
為避免有人未經授權存取 Service Fabric 叢集，您必須保護叢集。 當叢集執行生產工作負載時，安全性尤其重要。 本文說明如何在 *ClusterConfig.JSON* 檔案中使用 Windows 安全性，設定節點對節點和用戶端對節點的安全性。  此程序會對應[建立在 Windows 上執行的獨立叢集](service-fabric-cluster-creation-for-windows-server.md)的設定安全性步驟。 如需有關 Service Fabric 如何使用 Windows 安全性的詳細資訊，請參閱[叢集安全性案例](service-fabric-cluster-security.md)。

> [!NOTE]
> 請仔細考慮選擇節點對節點安全性，因為您無法選擇將叢集從某種安全性升級到另一種安全性。 若要變更安全性選擇，您必須重建完整叢集。
>
>

## <a name="configure-windows-security-using-gmsa"></a>使用 gMSA 設定 Windows 安全性  
隨著 [Microsoft.Azure.ServiceFabric.WindowsServer\<version>.zip](https://go.microsoft.com/fwlink/?LinkId=730690) 獨立叢集封裝下載的範例 *ClusterConfig.gMSA.Windows.MultiMachine.JSON* 組態檔，包含可供使用[群組受控服務帳戶 (gMSA)](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)) 來設定 Windows 安全性的範本：  

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {  
        "ClustergMSAIdentity": "[gMSA Identity]",
        "ClusterSPN": "[Registered SPN for the gMSA account]",
        "ClientIdentities": [
            {
                "Identity": "domain\\username",
                "IsAdmin": true
            }
        ]
    }
}
```

| **設定** | **描述** |
| --- | --- |
| ClusterCredentialType |設定為 [ *windows* ] 可啟用節點節點通訊的 windows 安全性。  | 
| ServerCredentialType |設定為 [Windows]** 可為用戶端對節點通訊啟用 Windows 安全性。 |
| WindowsIdentities |包含叢集和用戶端身分識別。 |
| ClustergMSAIdentity |設定節點對節點安全性。 群組受管理的服務帳戶。 |
| ClusterSPN |gMSA 帳戶的已註冊 SPN|
| ClientIdentities |設定用戶端對節點安全性。 用戶端使用者帳戶的陣列。 |
| 身分識別 |新增網域使用者 (domain\username) 以做為用戶端身分識別。 |
| IsAdmin |設定為 true 可指定網域使用者具有系統管理員用戶端存取權，設定為 false 則具有使用者用戶端存取權。 |

> [!NOTE]
> ClustergMSAIdentity 值的格式必須是 " mysfgmsa@mydomain "。

需要在 gMSA 下執行 Service Fabric 時，可透過 **ClustergMSAIdentity** 來設定[節點對節點安全性](service-fabric-cluster-security.md#node-to-node-security)。 為了建置節點之間的信任關係，它們必須注意彼此。 有兩種不同的方式可達成此目的︰指定群組受控服務帳戶 (其中包含叢集中的所有節點)，或指定包含叢集中所有節點的網域電腦群組。 強烈建議使用 [群組受控服務帳戶 (gMSA)](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)) 方法，特別適合於較大型的叢集 (超過 10 個節點) 或可能會擴大或縮小的叢集。  
此方法不需要建立叢集系統管理員已獲得存取權限的網域群組，即可加入和移除成員。 進行自動密碼管理時，這些帳戶也很有用。 如需詳細資訊，請參閱 [開始使用群組受控服務帳戶](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11))。  
 
[ClientIdentities](service-fabric-cluster-security.md#client-to-node-security) 可設定 **ClientIdentities**的設定安全性步驟。 若要建立用戶端與叢集之間的信任，您必須設定叢集才能知道用戶端可以信任的身分識別。 有兩種不同的方式可達成此目的︰指定可以連線的網域群組使用者，或指定可以連線的網域節點使用者。 Service Fabric 針對連線到 Service Fabric 叢集的用戶端，支援兩種不同的存取控制類型：系統管理員和使用者。 存取控制可讓叢集系統管理員針對不同的使用者群組限制特定類型的叢集作業的存取權，讓叢集更加安全。  系統管理員可以完整存取管理功能 (包括讀取/寫入功能)。 使用者預設只具有管理功能的讀取存取權 (例如查詢功能)，以及解析應用程式和服務的能力。 如需存取控制的詳細資訊，請參閱[角色型存取控制 (適用於 Service Fabric 用戶端)](service-fabric-cluster-security-roles.md)。  
 
下列範例 **security** 區段可使用 gMSA 設定 Windows 安全性，並指定 *ServiceFabric.clusterA.contoso.com* gMSA 中的電腦隸屬於叢集，而 *CONTOSO\usera* 具有系統管理員用戶端存取權︰  
  
```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClustergMSAIdentity" : "ServiceFabric.clusterA.contoso.com",
        "ClusterSPN" : "http/servicefabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
}
```
  
## <a name="configure-windows-security-using-a-machine-group"></a>使用電腦群組設定 Windows 安全性  
此模型已被淘汰。 建議使用 gMSA，如上所述。 隨著 [Microsoft.Azure.ServiceFabric.WindowsServer\<version>.zip](https://go.microsoft.com/fwlink/?LinkId=730690) 獨立叢集封裝下載的範例 *ClusterConfig.Windows.MultiMachine.JSON* 組態檔包含可供設定 Windows 安全性的範本。  Windows 安全性於 **Properties** 區段中設定︰ 

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "[domain\machinegroup]",
        "ClientIdentities": [{
            "Identity": "[domain\username]",
            "IsAdmin": true
        }]
    }
}
```

| **設定** | **描述** |
| --- | --- |
| ClusterCredentialType |設定為 [ *windows* ] 可啟用節點節點通訊的 windows 安全性。  |
| ServerCredentialType |設定為 [Windows]** 可為用戶端對節點通訊啟用 Windows 安全性。 |
| WindowsIdentities |包含叢集和用戶端身分識別。 |
| ClusterIdentity |使用電腦群組名稱 (domain\machinegroup) 來設定節點對節點安全性。 |
| ClientIdentities |設定用戶端對節點安全性。 用戶端使用者帳戶的陣列。 |  
| 身分識別 |新增網域使用者 (domain\username) 以做為用戶端身分識別。 |  
| IsAdmin |設定為 true 可指定網域使用者具有系統管理員用戶端存取權，設定為 false 則具有使用者用戶端存取權。 |  

如果您想要在 Active Directory 網域內使用電腦群組，請使用**ClusterIdentity**設定[節點對節點安全性](service-fabric-cluster-security.md#node-to-node-security)。 如需詳細資訊，請參閱[在 Active Directory 中建立電腦群組 (Create a Machine Group in Active Directory)](/previous-versions/commerce-server/aa545347(v=cs.70))。

[用戶端對節點安全性](service-fabric-cluster-security.md#client-to-node-security)是使用**clientidentities 來設定**來設定。 若要在用戶端與叢集之間建立信任，您必須設定叢集，讓叢集知道它可以信任的用戶端身分識別。 您可以透過兩種不同方式建立信任︰

- 指定可以連線的網域群組使用者。
- 指定可以連線的網域節點使用者。

Service Fabric 針對連線到 Service Fabric 叢集的用戶端，支援兩種不同的存取控制類型：系統管理員和使用者。 存取控制可讓叢集系統管理員針對不同的使用者群組限制特定類型的叢集作業的存取權，讓叢集更加安全。  系統管理員可以完整存取管理功能 (包括讀取/寫入功能)。 使用者預設只具有管理功能的讀取存取權 (例如查詢功能)，以及解析應用程式和服務的能力。  

下列範例 **security** 區段可設定 Windows 安全性，指定 ServiceFabric/clusterA.contoso.com** 中的電腦隸屬於叢集，並指定 CONTOSO\usera** 具有系統管理員用戶端存取權︰

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
},
```

> [!NOTE]
> 不應在網域控制站上部署 Service Fabric。 請確定在使用電腦群組或群組受控服務帳戶 (gMSA) 時，ClusterConfig.json 不會包含網域控制站的 IP 位址。
>
>

## <a name="next-steps"></a>後續步驟
在 *ClusterConfig.JSON* 檔案中設定 Windows 安全性之後，繼續進行 [建立在 Windows 上執行的獨立叢集](service-fabric-cluster-creation-for-windows-server.md)中的叢集建立程序。

如需有關節點對節點安全性、用戶端對節點安全性和角色型存取控制的詳細資訊，請參閱[叢集安全性案例](service-fabric-cluster-security.md)。

如需使用 PowerShell 或 FabricClient 來連線的範例，請參閱[連線到安全的叢集](service-fabric-connect-to-secure-cluster.md) 。
