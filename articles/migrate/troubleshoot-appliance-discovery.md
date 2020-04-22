---
title: 排除 Azure 移植裝置部署與發現故障
description: 獲取有關部署 Azure 遷移設備和發現電腦的説明。
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 6cb83a87f2e96eb62696e5d92095ef2b8d7c7def
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677332"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>排除 Azure 移植裝置與發現故障

本文可説明您在部署[Azure 遷移](migrate-services-overview.md)設備以及使用該設備發現本地電腦時解決問題。


## <a name="whats-supported"></a>支援的項目？

[查看](migrate-appliance.md)設備支援要求。


## <a name="invalid-ovf-manifest-entry"></a>"無效 OVF 清單條目"

如果您收到錯誤「提供的清單檔無效:OVF清單項目無效」,則執行以下操作:

1. 通過檢查 Azure 遷移裝置 OVA 檔是否正確下載。 [深入了解](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware)。 如果哈希值不匹配,請再次下載 OVA 檔並重試部署。
2. 如果部署仍然失敗,並且您正在使用 VMware vSphere 用戶端來部署 OVF 檔,請嘗試透過 vSphere Web 用戶端進行部署。 如果部署仍然失敗,請嘗試使用其他 Web 瀏覽器。
3. 如果您使用的是 vSphere Web 用戶端並嘗試在 vCenter Server 6.5 或 6.7 上部署它,請嘗試直接在 ESXi 主機上部署 OVA:
   - 直接與 Web 客戶端(HTTPs://<*主機 IP 位址*>/ui)連接到 ESXi 主機(而不是 vCenter 伺服器)。
   - 在 **「家庭** > **庫存」** 中,選擇 **「檔** > **部署 OVF」樣本**。 瀏覽到 OVA 並完成部署。
4. 如果部署仍然失敗，請連絡 Azure Migrate 的支援。

## <a name="cant-connect-to-the-internet"></a>無法連線到網際網路

如果設備機器位於代理後面,則可能發生此情況。

- 如果代理需要授權認證,請確保提供授權認證。
- 如果使用基於 URL 的防火牆代理來控制出站連接,請[將這些 URL](migrate-appliance.md#url-access)添加到允許清單中。
- 如果您使用攔截代理連接到互聯網,請使用[以下步驟](https://docs.microsoft.com/azure/migrate/concepts-collector)將代理證書導入設備 VM。

## <a name="cant-sign-into-azure-from-the-appliance-web-app"></a>無法從裝置 Web 應用程式登入 Azure

如果您使用不正確的 Azure 帳戶登錄到 Azure,則會出現"對不起,但我們登錄時遇到問題"錯誤。 發生錯誤有幾個原因:

- 如果您為公共雲登錄到設備 Web 應用程式,請使用政府雲門戶的使用者帳戶憑據。
- 如果使用私有雲門戶的使用者帳戶憑據登錄政府雲的設備 Web 應用程式。

確保您使用的憑據正確。

##  <a name="datetime-synchronization-error"></a>日期/時間同步錯誤

有關日期和時間同步 (802) 的錯誤指示伺服器時鐘可能與當前時間不同步超過五分鐘。 變更收集器 VM 上的時鐘時間以符合目前的時間:

1. 在虛擬機器上開啟系統管理命令提示字元。
2. 要檢查時區,請執行**w32tm /tz**。
3. 要同步時間,執行**w32tm /重新同步**。


## <a name="unabletoconnecttoserver"></a>"無法連接伺服器"

如果收到此連接錯誤,可能無法連接到 vCenter*伺服器名稱*.com:9443。 錯誤詳細資訊指示沒有終結點偵聽`https://\*servername*.com:9443/sdk`,可以接受該消息。

- 檢查是否運行最新版本的設備。 如果不是,請將裝置升級到[最新版本](https://docs.microsoft.com/azure/migrate/concepts-collector)。
- 如果最新版本中仍出現問題,則設備可能無法解析指定的 vCenter Server 名稱,或者指定的埠可能是錯誤的。 默認情況下,如果未指定埠,收集器將嘗試連接到埠號 443。

    1. 從裝置*ping 伺服器名稱*.com。
    2. 如果步驟 1 失敗,請嘗試使用 IP 位址連接到 vCenter 伺服器。
    3. 標識要連接到 vCenter 伺服器的正確埠號。
    4. 驗證 vCenter 伺服器是否啟動並運行。


## <a name="error-6005260039-appliance-might-not-be-registered"></a>錯誤 60052/60039:設備可能未註冊

- 錯誤 60052,"如果用於註冊設備的 Azure 帳戶的許可權不足,則發生"設備可能無法成功註冊到 Azure 遷移專案"的情況。
    - 確保用於註冊設備的 Azure 使用者帳戶至少具有訂閱的"參與者"
    - [詳細瞭解](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware)所需的 Azure 角色和許可權。
- 錯誤 60039,"如果註冊失敗,則可能會發生"設備可能無法成功註冊到 Azure 遷移專案",因為找不到用於註冊設備的 Azure 遷移專案。
    - 在 Azure 門戶中,檢查專案是否存在在資源組中。
    - 如果專案不存在,請在資源組中創建新的 Azure 遷移專案,然後再次註冊設備。 [瞭解如何](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool)創建新專案。

## <a name="error-6003060031-key-vault-management-operation-failed"></a>錯誤 60030/60031:金鑰保管庫管理操作失敗

如果收到錯誤 60030 或 60031,「Azure 密鑰保管庫管理操作失敗」,則執行以下操作:
- 確保用於註冊設備的 Azure 使用者帳戶至少具有訂閱的"參與者"
- 確保帳戶有權訪問錯誤消息中指定的密鑰保管庫,然後重試該操作。
- 如果問題持續發生， 請連絡 Microsoft 支援服務。
- [詳細瞭解所需的](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware)Azure 角色和許可權。

## <a name="error-60028-discovery-couldnt-be-initiated"></a>錯誤 60028:無法啟動發現

錯誤 60028:「由於錯誤,無法啟動發現。 指定主機或群集清單的操作失敗"表示由於訪問或檢索 VM 資訊時出現問題,無法在錯誤中列出的主機上啟動發現。 已成功添加其餘主機。

- 使用 **「新增主機」** 選項再次新增錯誤中列出的主機。
- 如果存在驗證錯誤,請查看修復指南以修復錯誤,然後重試 **「儲存」並再次啟動發現**選項。

## <a name="error-60025-azure-ad-operation-failed"></a>錯誤 60025:Azure AD 操作失敗 
錯誤 60025:「Azure AD 操作失敗。 創建或更新 Azure AD 應用程式時發生錯誤",當用於啟動發現的 Azure 使用者帳戶與用於註冊設備的帳戶不同時發生。 執行下列其中一個動作：

- 確保啟動發現的使用者帳戶與用於註冊設備的使用者帳戶相同。
- 向發現操作失敗的使用者帳戶提供 Azure 活動目錄應用程式訪問許可權。
- 刪除以前為 Azure 遷移項目創建的資源組。 建立另一個資源組以重新開始。
- [瞭解有關](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware)Azure 活動目錄應用程式許可權的更多內容。


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>錯誤 50004: 無法連線到主機或群組

錯誤 50004:「無法連接到主機或群集,因為無法解析伺服器名稱。 如果設備的 Azure DNS 服務無法解決您提供的群集或主機名,則可能發生 WinRM 錯誤代碼:0x803381B9"。

- 如果在群集、群集 FQDN 上看到此錯誤。
- 您可能還會看到群集中的主機出現此錯誤。 這表示設備可以連接到群集,但群集返回不是 FQDN 的主機名。 要解決此錯誤,請透過新增 IP 位址與主機名稱的映射來更新裝置上的主機檔:
    1. 以管理員身份打開記事本。
    2. 打開 C:[視窗]系統32\驅動程式\等\主機檔。
    3. 在行中添加 IP 位址和主機名稱。 對看到此錯誤的每個主機或群集重複上述操作。
    4. 儲存並關閉主機檔案。
    5. 使用設備管理應用檢查設備是否可以連接到主機。 30 分鐘后,您應該在 Azure 門戶中看到這些主機的最新資訊。

## <a name="discovered-vms-not-in-portal"></a>未在門戶中發現的 VM

如果發現狀態為「正在發現」,但尚未在門戶中看到 VM,請等待幾分鐘:
- VMware VM 大約需要15分鐘。
- 對於 Hyper-V VM 發現,每個添加的主機大約需要兩分鐘。

如果等待且狀態不變,請在「**伺服器」** 選項卡上選擇 **「刷新**」。這將顯示 Azure 遷移中發現的伺服器的計數:伺服器評估和 Azure 遷移:伺服器遷移。

如果這不起作用,並且您正在發現 VMware 伺服器:

- 驗證您指定的 vCenter 帳戶是否具有正確設置的許可權,並有權訪問至少一個 VM。
- 如果 vCenter 帳戶具有 vCenter VM 資料夾等級授予的訪問許可權,則 Azure 遷移無法發現 VMware VM。 [瞭解有關](set-discovery-scope.md)範圍發現數的更多。

## <a name="vm-data-not-in-portal"></a>不在門戶中的 VM 資料

如果發現的 VM 未顯示在門戶中,或者 VM 數據已過期,請等待幾分鐘。 發現 VM 設定數據的更改最多需要 30 分鐘才能顯示在門戶中。 應用程序數據更改可能需要幾個小時才能顯示。 如果在此時間之後沒有數據,請嘗試刷新,如下所示

1. 在**伺服器** > **Azure 遷移伺服器評估中**,選擇 **「概述**」。
2. 在 **"管理"** 下,選擇 **「代理運行狀況**」。。
3. 選擇**刷新代理**程式 。
4. 等待刷新操作完成。 您現在應該看到最新資訊。

## <a name="deleted-vms-appear-in-portal"></a>已移除的 VM 顯示在門戶中

如果刪除 VM 並且它們仍顯示在門戶中,請等待 30 分鐘。 如果它們仍然出現,請刷新上述內容。

## <a name="common-app-discovery-errors"></a>常見應用發現錯誤

Azure 遷移支援使用 Azure 遷移:伺服器評估發現應用程式、角色和功能。 當前僅支援 VMware 應用發現。 [詳細瞭解](how-to-discover-applications.md)設置應用發現的要求和步驟。

典型的應用發現錯誤匯總在表中。 

**錯誤** | **原因** | **動作**
--- | --- | --- | ---
10000:「無法發現伺服器上安裝的應用程式」。 | 如果電腦作業系統不是 Windows 或 Linux,則可能發生此情況。 | 僅對 Windows/Linux 使用應用發現。
10001:「無法檢索安裝的伺服器的應用程式」。 | 內部錯誤 - 設備中缺少一些檔。 | 連絡 Microsoft 支援服務。
10002:「無法檢索安裝的伺服器的應用程式」。 | 產品上的發現代理可能無法正常工作。 | 如果問題在 24 小時內無法自行解決,請與支持人員聯繫。
10003 「無法檢索安裝的伺服器的應用程式」。 | 產品上的發現代理可能無法正常工作。 | 如果問題在 24 小時內無法自行解決,請與支持人員聯繫。
10004:「無法發現 <Windows/Linux>计算机的已安装应用程序。 |  設備中未提供訪問 windows/Linux <>計算機的認證。| 向有權訪問<Windows/Linux>计算机的设备添加凭据。
10005:「無法訪問本地伺服器」。。 | 訪問憑據可能是錯誤的。 | 更新設備憑據請確保可以使用它們訪問相關計算機。 
10006:「無法訪問本地伺服器」。。 | 如果電腦作業系統不是 Windows 或 Linux,則可能發生此情況。|  僅對 Windows/Linux 使用應用發現。
10007:"無法處理檢索的元數據" | 此內部錯誤在嘗試反序列化 JSON 時發生 | 請與微軟支援部門聯繫,瞭解解決方案
9000/9001/9002:"無法發現伺服器上安裝的應用程式"。 | VMware 工具可能未安裝或已損壞。 | 在相關電腦上安裝/重新安裝 VMware 工具,並檢查其是否正在運行。
9003:無法發現伺服器上安裝的應用程式」。 | 如果電腦作業系統不是 Windows 或 Linux,則可能發生此情況。 | 僅對 Windows/Linux 使用應用發現。
9004:「無法發現伺服器上安裝的應用程式」。 | 如果關閉 VM 電源,則可能發生此情況。 | 要發現,請確保 VM 處於打開點。
9005:「無法發現安裝在 VM 上的應用程式。 | 如果電腦作業系統不是 Windows 或 Linux,則可能發生此情況。 | 僅對 Windows/Linux 使用應用發現。
9006/9007:「無法檢索安裝的伺服器的應用程式」。。 | 產品上的發現代理可能無法正常工作。 | 如果問題在 24 小時內無法自行解決,請與支持人員聯繫。
9008:「無法檢索安裝的伺服器的應用程式」。。 | 可能是內部錯誤。  | Tf 問題無法在 24 小時內自行解決,請聯繫支持人員。
9009:「無法檢索安裝的伺服器的應用程式」。。 | 如果伺服器上的 Windows 使用者帳戶控制 (UAC) 設置受到限制,並且阻止發現已安裝的應用程式,則可能發生。 | 在伺服器上搜索「使用者帳戶控制」設置,並將伺服器上的 UAC 設定配置為較低的兩個級別之一。
9010:「無法檢索安裝的伺服器的應用程式」。。 | 可能是內部錯誤。  | Tf 問題無法在 24 小時內自行解決,請聯繫支持人員。
9011:"在來賓 VM 上找不到從來賓下載的檔" | 由於內部錯誤,可能會出現此問題。 | 問題應在 24 小時內自動解決。 如果問題仍然存在,請聯繫 Microsoft 支援。
9012:「結果文件內容為空。 | 由於內部錯誤,可能會出現此問題。 | 問題應在 24 小時內自動解決。 如果問題仍然存在,請聯繫 Microsoft 支援。
9013:"每次登錄 VMware VM 都會創建新的臨時配置檔" | 為每次登入 VM 建立新的暫存檔 | 確保來賓 VM 認證中提供的使用者名為 UPN 格式。
9015:"由於 vCenter 上的許可權不足,無法連接到 VMware VM" | vCenter 使用者帳戶上未啟用來賓操作角色 | 確保在 vCenter 使用者帳戶上啟用了來賓操作角色。
9016:"由於來賓操作代理數據已失,無法連接到 VMware VM" | VMware 工具安裝不正確或不是最新的。 | 確保 VMware 工具安裝正確且最新。
9017:「在 VM 上找不到包含已發現的元數據的檔。 | 由於內部錯誤,可能會出現此問題。 | 有關解決方案,請與Microsoft支援部門聯繫。
9018:「來賓 VM 中未安裝 PowerShell。 | 電源外殼在來賓 VM 中不可用。 | 在來賓 VM 中安裝 PowerShell。
9019:"由於來賓 VM 操作失敗而無法發現" | VM 上的 VMware 來賓操作失敗。 | 確保 VM 認證,來賓 VM 認證中提供的使用者名為 UPN 格式。
9020:「檔案創建許可權被拒絕。 | 與使用者或群組政策關聯的角色正在限制使用者在資料夾中建立檔案 | 檢查提供的來賓使用者是否具有資料夾中的檔案創建許可權。 有關資料夾的名稱,請參閱伺服器評估中**的通知**。
9021:「在資料夾系統臨時路徑中拒絕文件創建許可權。 | 不支援 VM 上的 VMware 工具版本 | 將 VMware 工具版本升級到 10.2.0 以上。
9022:「獲取 WMI 對象訪問被拒絕。 | 與使用者或組策略關聯的角色正在限制使用者訪問 WMI 物件。 | 請連絡 Microsoft 支援。
9023:「系統根環境變數值為空。 | 不知道 | 請連絡 Microsoft 支援。
9024:「TEMP 環境變數值為空。 | 不知道 | 請連絡 Microsoft 支援。
9025:「電源外殼在來賓 VM 中已損壞。 | 不知道 | 在來賓 VM 中重新安裝 PowerShell,並檢查是否可以在來賓 VM 上運行 PowerShell。
8084:"由於 VMware 錯誤,無法發現 <Exception from VMware>應用程式:" | Azure 遷移設備使用 VMware API 來發現應用程式。 如果 vCenter Server 在嘗試發現應用程式時引發異常,則可能發生此問題。 來自 VMware 的錯誤消息顯示在門戶中顯示的錯誤消息中。 | 在[VMware 文檔中](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html)搜索消息,然後按照修復步驟進行操作。 如果無法修復,請與 Microsoft 支援部門聯繫。



## <a name="next-steps"></a>後續步驟
為[VMware、Hyper-V](how-to-set-up-appliance-vmware.md)或[實體伺服器](how-to-set-up-appliance-physical.md)設置設備。 [Hyper-V](how-to-set-up-appliance-hyper-v.md)
