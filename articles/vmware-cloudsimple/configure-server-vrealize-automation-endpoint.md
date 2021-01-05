---
title: Azure VMware Solution by CloudSimple-在私人雲端上設定 vCenter 以進行 vRealize 自動化
description: 說明如何將 CloudSimple 私人雲端上的 VMware vCenter server 設定為 VMware vRealize Automation 的端點
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9b6c6a320e6299808a91214476c8c0460f9f53d9
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895048"
---
# <a name="set-up-vcenter-on-your-private-cloud-for-vmware-vrealize-automation"></a>在您的私人雲端上設定 vCenter 以進行 VMware vRealize 自動化

您可以將 CloudSimple 私人雲端上的 VMware vCenter 伺服器設定為 VMware vRealize 自動化的端點。

## <a name="before-you-begin"></a>開始之前

設定 vCenter server 之前，請先完成這些工作：

* 設定內部部署環境與私人雲端之間的 [站對站 VPN](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) 連線。
* [設定內部部署 dns 要求](on-premises-dns-setup.md) 至私人雲端的 dns 伺服器的 dns 轉送。
* 提交 [支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) ，以使用下表所列的一組許可權來建立 VRealize Automation IaaS 系統管理使用者。

| 屬性值 | 權限 |
------------ | ------------- |  
| 資料存放區 |  配置空間 <br> 流覽資料存放區 |
| 資料存放區叢集 | 設定資料存放區叢集 |
| 資料夾 | 建立資料夾 <br>刪除資料夾 |
| 全球 |  管理自訂屬性<br>設定自訂屬性 |
| 網路 | 指派網路 |
| 權限 | 修改權限 |
| 資源 | 將 VM 指派給資源集區<br>遷移電源關閉的虛擬機器<br>在虛擬機器上遷移電源 |
| 虛擬機器詳細目錄 |  從現有的建立<br>建立新的<br>移動<br>移除 | 
| 虛擬機器互動 |  設定 CD 媒體<br>主控台互動<br>裝置連線<br>關閉電源<br>開啟電源<br>重設<br>暫止<br>工具安裝 | 
| 虛擬機器設定 |  新增現有的磁片<br>新增磁片<br>新增或移除<br>移除磁片<br>進階<br>變更 CPU 計數<br>變更資源<br>擴充虛擬磁片<br>磁片變更追蹤<br>記憶體<br>修改裝置設定<br>重新命名<br>設定注釋 (5.0 版和更新版本) <br>設定<br>Cloud-init 放置 |
| 佈建 |  自訂<br>複製範本<br>再製虛擬機器<br>部署範本<br>讀取自訂規格 |
| 虛擬機器狀態 | 建立快照集<br>移除快照集<br>還原為快照集 |

## <a name="install-vrealize-automation-in-your-on-premises-environment"></a>在您的內部部署環境中安裝 vRealize 自動化

1. 以 CloudSimple 支援為您建立的 IaaS 系統管理員身分登入 vRealize Automation IaaS 伺服器設備。
2. 部署 vRealize 自動化端點的 vSphere 代理程式。
    1. 移至 HTTPs://*vra-url*： 5480/installer，其中 *vra-url* 是用來存取 VREALIZE Automation 系統管理 UI 的 url。
    2. 按一下 **IaaS 安裝程式** 以下載安裝程式。<br>
    安裝程式檔案的命名慣例是 setup_ *vra-url* @5480.exe 。
    3. 執行安裝程式。 在 [歡迎畫面上，按 **[下一步]**。
    4. 接受 EULA，然後按 **[下一步]**。
    5. 提供登入資訊，按一下 [ **接受憑證**]，然後按 **[下一步]**。
    ![vRA 認證](media/configure-vra-endpoint-login.png)
    6. 選取 **自訂安裝** 和 **Proxy 代理** 程式，然後按 **[下一步]**。
    ![vRA 安裝類型](media/configure-vra-endpoint-install-type.png)
    7. 輸入 IaaS 伺服器登入資訊，然後按 **[下一步]**。 如果您使用 Active Directory，請輸入 domain\user 格式的 **使用者** 名稱。 否則，請使用 **user@domain** format。
    ![vRA 登入資訊](media/configure-vra-endpoint-account.png)
    8. 針對 [proxy 設定]，輸入 **代理程式類型** 的 **vSphere** 。 輸入代理程式的名稱。
    9. 在 **管理員服務主機** 和 [ **模型管理員 Web 服務主機** ] 欄位中輸入 IaaS 伺服器 FQDN。 按一下 [ **測試** ] 以測試每個 FQDN 值的連接。 如果測試失敗，請修改您的 DNS 設定，以便解析 IaaS 伺服器主機名稱。
    10. 輸入私人雲端的 vCenter 伺服器端點名稱。 記錄名稱，以便稍後在設定程式中使用。

        ![vRA 安裝 proxy](media/configure-vra-endpoint-proxy.png)

    11. 按一下 [下一步] 。
    12. 按一下 [安裝]  。

## <a name="configure-the-vsphere-agent"></a>設定 vSphere 代理程式

1. 移至 HTTPs://*vra-url*/vcac，並以 **ConfigurationAdmin** 的形式登入。
2. 選取 **基礎結構**  >  **端點**  >  **端點**。
3. 選取 [**新增**  >  **虛擬**  >  **vSphere**]。
4. 輸入您在上一個程式中指定的 vSphere 端點名稱。
5. 在 [ **位址**] 中，以 HTTPs://*vcenter-fqdn*/Sdk 格式輸入私人雲端 vCenter Server URL，其中 *vcenter-fqdn* 是 vcenter Server 的名稱。
6. 輸入 CloudSimple 支援為您建立之 vRealize Automation IaaS 系統管理使用者的認證。
7. 按一下 [ **測試連接** ] 以驗證使用者認證。 如果測試失敗，請驗證 URL、帳戶資訊和 [端點名稱](#verify-the-endpoint-name) ，然後再次測試。
8. 成功測試之後，請按一下 **[確定]** 以建立 vSphere 端點。
    ![vRA 端點設定存取](media/configure-vra-endpoint-vra-edit.png)

### <a name="verify-the-endpoint-name"></a>驗證端點名稱

若要識別正確的 vCenter server 端點名稱，請執行下列動作：

1. 在 IaaS 設備上開啟命令提示字元。
2. 將目錄變更為 C:\Program Files (x86) \VMware\vCAC\Agents\agent-name，其中 *agent-name* 是您指派給 vCenter server 端點的名稱。
3. 執行下列命令。

```
..\..\Server\DynamicOps.Vrm.VRMencrypt.exe VRMAgent.exe.config get
```

輸出如下所示。 此欄位的值 `managementEndpointName` 為端點名稱。

```
managementEndpointName: cslab1pc3-vc
doDeletes: true
```
