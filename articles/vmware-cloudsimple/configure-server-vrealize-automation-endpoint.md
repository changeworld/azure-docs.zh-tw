---
title: Azure VMware 解決方案（按雲簡單 - 在私有雲上設置 vCenter 以實現自動化）
description: 描述如何在雲簡單私有雲上設置 VMware vCenter 伺服器，作為 VMware v實現自動化的終結點
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: df73acfc469a8b7b5329b61095aefdbd73baafd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024835"
---
# <a name="set-up-vcenter-on-your-private-cloud-for-vmware-vrealize-automation"></a>在私有雲上設置 vCenter，用於 VMware v實現自動化

您可以在雲簡單私有雲上設置 VMware vCenter 伺服器，作為 VMware v實現自動化的終結點。

## <a name="before-you-begin"></a>開始之前

在配置 vCenter 伺服器之前完成這些任務：

* 配置本地環境和私有雲之間的[網站到網站 VPN 連接](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)。
* [將本地 DNS 請求的 DNS 轉發](on-premises-dns-setup.md)配置到專用雲的 DNS 伺服器。
* 提交[支援請求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)以創建具有下表中列出的一組許可權的 v實現自動化 IaaS 管理使用者。

| 屬性值 | 權限 |
------------ | ------------- |  
| 資料存放區 |  分配空間 <br> 流覽資料存儲 |
| 資料存儲群集 | 配置資料存儲群集 |
| 資料夾 | 建立資料夾 <br>刪除資料夾 |
| 全域 |  管理自訂屬性<br>設定自訂屬性 |
| 網路 | 分配網路 |
| 權限 | 修改權限 |
| 資源 | 將 VM 分配給資源池<br>遷移已關閉的虛擬機器<br>在虛擬機器上遷移電源 |
| 虛擬機器庫存 |  從現有創建<br>建立新的<br>移動<br>移除 | 
| 虛擬機器交互 |  配置 CD 媒體<br>主控台交互<br>裝置連線<br>關閉電源<br>開啟電源<br>重設<br>暫止<br>工具安裝 | 
| 虛擬機器組態 |  添加現有磁片<br>添加新磁片<br>添加或刪除<br>刪除磁片<br>進階<br>更改 CPU 計數<br>更改資源<br>擴展虛擬磁片<br>磁片更改跟蹤<br>記憶體<br>修改設備設置<br>重新命名<br>設置注釋（版本 5.0 及更高版本）<br>設定<br>交換檔放置 |
| 佈建 |  自訂<br>克隆範本<br>再製虛擬機器<br>部署範本<br>閱讀自訂規範 |
| 虛擬機器狀態 | 創建快照<br>刪除快照<br>還原為快照 |

## <a name="install-vrealize-automation-in-your-on-premises-environment"></a>在本地環境中安裝 v實現自動化

1. 以 CloudSimple 支援為您創建的 IaaS 管理員的身份登錄到 v實現自動化 IaaS 伺服器設備。
2. 為 v實現自動化終結點部署 vSphere 代理。
    1. 轉到HTTPs://*vra-url*：5480/安裝程式，其中*vra-url*是用於訪問 v實現自動化管理 UI 的 URL。
    2. 按一下**IaaS 安裝程式**以下載安裝程式。<br>
    安裝程式檔的命名約定*setup_vra-url。* @5480.exe
    3. 執行安裝程式。 在 [歡迎使用] 畫面中，按 [下一步]****。
    4. 接受 EULA 並按一下 **"下一步**"。
    5. 提供登錄資訊，按一下"**接受證書**"，然後按一下"**下一步**"。
    ![vRA 憑據](media/configure-vra-endpoint-login.png)
    6. 選擇**自訂安裝**和**代理代理**，然後按一下 **"下一步**"。
    ![vRA 安裝類型](media/configure-vra-endpoint-install-type.png)
    7. 輸入 IaaS 伺服器登錄資訊，然後按一下 **"下一步**"。 如果使用活動目錄，請輸入**域\使用者**格式的使用者名。 否則，請使用**user@domain**格式。
    ![vRA 登錄資訊](media/configure-vra-endpoint-account.png)
    8. 對於代理設置，輸入**代理類型的** **vSphere。** 輸入代理程式的名稱。
    9. 在**管理器服務主機**和**模型管理器 Web 服務主機**欄位中輸入 IaaS 伺服器 FQDN。 按一下 **"測試**"以測試每個 FQDN 值的連接。 如果測試失敗，請修改 DNS 設置，以便解析 IaaS 伺服器主機名稱。
    10. 輸入私有雲的 vCenter 伺服器終結點的名稱。 記錄名稱，以便稍後在配置過程中使用。

        ![vRA 安裝代理](media/configure-vra-endpoint-proxy.png)

    11. 按 [下一步]****。
    12. 按一下 **[安裝]**。

## <a name="configure-the-vsphere-agent"></a>配置 vSphere 代理

1. 轉到HTTPs://*vra-url*/vcac 並登錄為**建構管理員**。
2. 選擇**基礎結構** > **終結點** > **終結點**。
3. 選擇**新的** > **虛擬** > **虛擬虛擬虛擬虛擬虛擬虛擬虛擬虛擬虛擬虛擬虛擬虛擬虛擬虛擬虛擬虛擬虛擬**
4. 輸入您在上一過程中指定的 vSphere 終結點名稱。
5. 對於**位址**，以*vcenter-fqdn*/sdk HTTPs:// 格式輸入私有雲 vCenter 伺服器 URL，其中*vcenter-fqdn*是 vCenter 伺服器的名稱。
6. 輸入 CloudSimple 支援為您創建的 v實現自動化 IaaS 管理使用者的憑據。
7. 按一下 **"測試連接**"以驗證使用者憑據。 如果測試失敗，請驗證 URL、帳戶資訊和[終結點名稱](#verify-the-endpoint-name)，然後再次進行測試。
8. 測試成功後，按一下"**確定"** 以創建 vSphere 終結點。
    ![vRA 端點配置訪問](media/configure-vra-endpoint-vra-edit.png)

### <a name="verify-the-endpoint-name"></a>驗證終結點名稱

要標識正確的 vCenter 伺服器終結點名稱，請執行以下操作：

1. 打開 IaaS 設備上的命令提示。
2. 將目錄更改為 C：_程式檔 （x86）\VMware\vCAC\代理\代理名稱，其中*代理名稱*是分配給 vCenter 伺服器終結點的名稱。
3. 執行下列命令。

```
..\..\Server\DynamicOps.Vrm.VRMencrypt.exe VRMAgent.exe.config get
```

輸出類似于以下內容。 `managementEndpointName`欄位的值是終結點名稱。

```
managementEndpointName: cslab1pc3-vc
doDeletes: true
```
