---
title: 針對 Azure Migrate 設備部署和探索進行疑難排解
description: 取得部署 Azure Migrate 設備及探索機器的協助。
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 3e25f55d82ba146f9076e38faf1e399c5228d947
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "79080375"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>針對 Azure Migrate 設備和探索進行疑難排解

本文可協助您針對部署[Azure Migrate](migrate-services-overview.md)設備時的問題進行疑難排解，並使用設備來探索內部部署機器。


## <a name="whats-supported"></a>支援的項目？

請[參閱](migrate-appliance.md)設備支援需求。


## <a name="invalid-ovf-manifest-entry"></a>「OVF 資訊清單專案無效」

如果您收到「提供的資訊清單檔案無效：不正確 OVF 資訊清單專案」錯誤，請執行下列動作：

1. 藉由檢查其雜湊值，確認已正確下載 Azure Migrate 設備 OVA 檔案。 [詳細資訊](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware)。 如果雜湊值不相符，請再次下載 OVA 檔案，然後重試部署。
2. 如果部署仍然失敗，而且您使用 VMware vSphere 用戶端來部署 OVF 檔案，請嘗試透過 vSphere web 用戶端進行部署。 如果部署仍然失敗，請嘗試使用不同的網頁瀏覽器。
3. 如果您使用 vSphere web 用戶端，並嘗試在 vCenter Server 6.5 或6.7 上部署它，請嘗試直接在 ESXi 主機上部署 OVA：
   - 使用 web 用戶端（HTTPs：//<*主機 IP 位址*>/ui）直接連接到 ESXi 主機（而不是 vCenter Server）。
   - 在 **[首頁** > 清查] 中 **，選取 [** **檔案** > **部署 OVF 範本**]。 流覽至 OVA 並完成部署。
4. 如果部署仍然失敗，請連絡 Azure Migrate 的支援。

## <a name="cant-connect-to-the-internet"></a>無法連線到網際網路

如果設備機器位於 proxy 後方，就會發生這種情況。

- 如果 proxy 需要授權認證，請務必提供這些認證。
- 如果您使用以 URL 為基礎的防火牆 proxy 來控制輸出連線能力，請將[這些 url](migrate-appliance.md#url-access)新增至允許清單。
- 如果您使用攔截 proxy 來連線到網際網路，請使用[這些步驟](https://docs.microsoft.com/azure/migrate/concepts-collector)將 proxy 憑證匯入至應用裝置 VM。

##  <a name="datetime-synchronization-error"></a>日期/時間同步處理錯誤

有關日期和時間同步處理（802）的錯誤指出伺服器時鐘可能未與目前時間同步超過五分鐘。 變更收集器 VM 上的時鐘時間以符合目前的時間：

1. 在虛擬機器上開啟系統管理命令提示字元。
2. 若要檢查時區，請執行**w32tm/tz**。
3. 若要同步處理時間，請執行**w32tm/resync**。


## <a name="unabletoconnecttoserver"></a>"UnableToConnectToServer"

如果您收到此連接錯誤，您可能無法連接到 vCenter Server *Servername*.com：9443。 錯誤詳細資料指出沒有在 HTTPs://*servername*.com： 9443/sdk 接聽可接受訊息的端點。

- 檢查您是否正在執行最新版本的設備。 如果不是，請將設備升級至[最新版本](https://docs.microsoft.com/azure/migrate/concepts-collector)。
- 如果問題仍然發生在最新版本中，設備可能無法解析指定的 vCenter Server 名稱，或指定的埠可能錯誤。 根據預設，如果未指定埠，收集器會嘗試連接到埠號碼443。

    1. 從設備 Ping *Servername*.com。
    2. 如果步驟1失敗，請嘗試使用 IP 位址連接到 vCenter server。
    3. 識別要連接到 vCenter Server 的正確埠號碼。
    4. 確認 vCenter Server 已啟動且正在執行。


## <a name="error-6005260039-appliance-might-not-be-registered"></a>錯誤60052/60039：設備可能未註冊

- 如果用來註冊應用裝置的 Azure 帳戶許可權不足，就會發生錯誤60052：「設備可能未成功註冊至 Azure Migrate 專案」。
    - 請確定用來註冊應用裝置的 Azure 使用者帳戶至少具有訂用帳戶的參與者許可權。
    - [深入瞭解](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware)必要的 Azure 角色和許可權。
- 如果註冊失敗，因為找不到用來註冊應用裝置的 Azure Migrate 專案，錯誤60039「可能無法成功地將設備註冊到 Azure Migrate 專案」。
    - 在 Azure 入口網站中，檢查項目是否存在於資源群組中。
    - 如果專案不存在，請在您的資源群組中建立新的 Azure Migrate 專案，然後重新註冊設備。 [瞭解如何](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool)建立新的專案。

## <a name="error-6003060031-key-vault-management-operation-failed"></a>錯誤60030/60031： Key Vault 管理操作失敗

如果您收到錯誤60030或60031「Azure Key Vault 管理作業失敗」，請執行下列動作：
- 請確定用來註冊應用裝置的 Azure 使用者帳戶至少擁有訂用帳戶的參與者許可權。
- 請確定帳戶可存取錯誤訊息中指定的金鑰保存庫，然後再次嘗試操作。
- 如果問題持續發生， 請連絡 Microsoft 支援服務。
- [深入瞭解](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware)必要的 Azure 角色和許可權。

## <a name="error-60028-discovery-couldnt-be-initiated"></a>錯誤60028：無法起始探索

錯誤60028：「無法起始探索，因為發生錯誤。 指定的主機或叢集清單的操作失敗，表示因為存取或抓取 VM 資訊時發生問題，所以無法在錯誤中所列的主機上啟動探索。 已成功新增其餘的主機。

- 使用 [**新增主機**] 選項，再次新增錯誤中所列的主機。
- 如果發生驗證錯誤，請檢查補救指引以修正錯誤，然後再次嘗試 [**儲存並啟動探索**] 選項。

## <a name="error-60025-azure-ad-operation-failed"></a>錯誤60025： Azure AD 操作失敗 
錯誤60025：「Azure AD 操作失敗。 當用來起始探索的 Azure 使用者帳戶不同于用來註冊應用裝置的帳戶時，建立或更新 Azure AD 應用程式時發生錯誤。 執行下列其中一個動作：

- 確定起始探索的使用者帳戶與用來註冊應用裝置的帳戶相同。
- 針對探索作業失敗的使用者帳戶，提供 Azure Active Directory 的應用程式存取權限。
- 刪除先前為 Azure Migrate 專案所建立的資源群組。 建立另一個資源群組以重新開機。
- [深入瞭解](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware)Azure Active Directory 應用程式許可權。


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>錯誤50004：無法連接到主機或叢集

錯誤50004：「無法連接到主機或叢集，因為無法解析伺服器名稱。 如果設備的 Azure DNS 服務無法解析您提供的叢集或主機名稱，則會發生 WinRM 錯誤碼： 0x803381B9 "。

- 如果您在叢集上看到此錯誤，請叢集 FQDN。
- 對於叢集中的主機，您可能也會看到此錯誤。 這表示設備可以連接到叢集，但叢集會傳回不是 Fqdn 的主機名稱。 若要解決此錯誤，請新增 IP 位址和主機名稱的對應，以更新設備上的 hosts 檔案：
    1. 以系統管理員身分開啟 [記事本]。
    2. 開啟 C:\Windows\System32\Drivers\etc\hosts 檔案。
    3. 在資料列中新增 IP 位址和主機名稱。 針對您看到此錯誤的每個主機或叢集重複此動作。
    4. 儲存並關閉 hosts 檔案。
    5. 使用設備管理應用程式，檢查設備是否可連線至主機。 30分鐘後，您應該會在 Azure 入口網站中看到這些主機的最新資訊。

## <a name="discovered-vms-not-in-portal"></a>探索到的 Vm 不在入口網站中

如果探索狀態為「正在探索」，但在入口網站中尚未看到 Vm，請等候幾分鐘：
- VMware VM 需要大約15分鐘的時間。
- 針對每個新增的 Hyper-v VM 探索主機，大約需要兩分鐘的時間。

如果您等待且狀態不會變更，請**選取 [** **伺服器**] 索引標籤上的 [重新整理]。這應該會顯示探索到的伺服器計數 Azure Migrate：伺服器評估和 Azure Migrate：伺服器遷移。

如果沒有作用，而且您正在探索 VMware 伺服器：

- 請確認您指定的 vCenter 帳戶已正確設定許可權，且至少有一個 VM 的存取權。
- 如果 vCenter 帳戶具有在 vCenter VM 資料夾層級授與的存取權，Azure Migrate 就無法探索 VMware Vm。 [深入瞭解](tutorial-assess-vmware.md#set-the-scope-of-discovery)範圍探索。

## <a name="vm-data-not-in-portal"></a>VM 資料不在入口網站中

如果探索到的 Vm 不會出現在入口網站中，或 VM 資料已過期，請等候幾分鐘的時間。 最多需要30分鐘的時間，探索到的 VM 設定資料中的變更才會出現在入口網站中。 可能需要幾個小時的時間，應用程式資料的變更才會出現。 如果在這段時間之後沒有任何資料，請嘗試重新整理，如下所示

1. 在 [**伺服器** > **Azure Migrate 伺服器評估**] 中，選取 **[總覽**]。
2. 在 [**管理**] 下，選取 [**代理程式健全狀況**]。
3. 選取 [重新整理**代理程式**]。
4. 等待重新整理作業完成。 您現在應該會看到最新的資訊。

## <a name="deleted-vms-appear-in-portal"></a>已刪除的 Vm 會出現在入口網站中

如果您刪除 Vm，而且它們仍會出現在入口網站中，請等候30分鐘。 如果仍然出現，請重新整理，如上所述。

## <a name="common-app-discovery-errors"></a>常見的應用程式探索錯誤

Azure Migrate 支援使用 Azure Migrate：伺服器評估來探索應用程式、角色和功能。 目前僅支援 VMware 的應用程式探索。 [深入瞭解](how-to-discover-applications.md)設定應用程式探索的需求和步驟。

一般應用程式探索錯誤摘要于表格中。 

**錯誤** | **原因** | **動作**
--- | --- | --- | ---
10000：「無法探索安裝在伺服器上的應用程式」。 | 如果電腦作業系統不是 Windows 或 Linux，就可能發生這種情況。 | 僅使用適用于 Windows/Linux 的應用程式探索。
10001：「無法取得已安裝伺服器的應用程式」。 | 內部錯誤-設備中有些遺失的檔案。 | 連絡 Microsoft 支援服務。
10002：「無法取得已安裝伺服器的應用程式」。 | 設備上的探索代理程式可能無法正常運作。 | 如果問題無法在24小時內自行解決，請聯絡支援人員。
10003「無法取得已安裝伺服器的應用程式」。 | 設備上的探索代理程式可能無法正常運作。 | 如果問題無法在24小時內自行解決，請聯絡支援人員。
10004：「無法探索 < Windows/Linux > 機的已安裝應用程式。」 |  設備中未提供存取 < Windows/Linux > 機的認證。| 將認證新增至可存取 < Windows/Linux > 機的應用裝置。
10005：「無法存取內部部署伺服器」。 | 存取認證可能錯誤。 | 更新設備認證，並確定您可以使用它們來存取相關的電腦。 
10006：「無法存取內部部署伺服器」。 | 如果電腦作業系統不是 Windows 或 Linux，就可能發生這種情況。|  僅使用適用于 Windows/Linux 的應用程式探索。
10007：「無法處理抓取的中繼資料」 | 嘗試還原序列化 JSON 時發生此內部錯誤 | 聯絡 Microsoft 支援服務以解決問題
9000/9001/9002：「無法探索安裝在伺服器上的應用程式」。 | VMware 工具可能未安裝或已損毀。 | 在相關電腦上安裝/重新安裝 VMware 工具，並檢查它是否正在執行。
9003：無法探索安裝在伺服器上的應用程式」。 | 如果電腦作業系統不是 Windows 或 Linux，就可能發生這種情況。 | 僅使用適用于 Windows/Linux 的應用程式探索。
9004：「無法探索安裝在伺服器上的應用程式」。 | 如果 VM 已關閉電源，可能就會發生這種情況。 | 針對 [探索]，請確定 VM 已開啟。
9005：「無法探索安裝在 VM 上的應用程式。 | 如果電腦作業系統不是 Windows 或 Linux，就可能發生這種情況。 | 僅使用適用于 Windows/Linux 的應用程式探索。
9006/9007：「無法取得已安裝伺服器的應用程式」。 | 設備上的探索代理程式可能無法正常運作。 | 如果問題無法在24小時內自行解決，請聯絡支援人員。
9008：「無法取得已安裝伺服器的應用程式」。 | 可能是內部錯誤。  | Tf 問題不會在24小時內自行解決，請聯絡支援人員。
9009：「無法取得已安裝伺服器的應用程式」。 | 如果伺服器上的 Windows 使用者帳戶控制（UAC）設定受到限制，並防止探索已安裝的應用程式，就會發生此問題。 | 搜尋伺服器上的 [使用者帳戶控制] 設定，並將伺服器上的 UAC 設定設為較低兩個層級的其中一個。
9010：「無法取得已安裝伺服器的應用程式」。 | 可能是內部錯誤。  | Tf 問題不會在24小時內自行解決，請聯絡支援人員。
9011：在來賓 VM 上找不到「從來賓下載的檔案」 | 問題可能是內部錯誤所造成。 | 此問題應該會在24小時內自動解決。 如果問題仍然持續發生，請洽詢 Microsoft 支援服務。
9012：「結果檔案內容是空的。」 | 問題可能是內部錯誤所造成。 | 此問題應該會在24小時內自動解決。 如果問題仍然持續發生，請洽詢 Microsoft 支援服務。
9013：「每次登入 VMware VM 時，都會建立新的暫存設定檔」 | 每次登入 VM 時，都會建立新的暫存設定檔 | 請確定來賓 VM 認證中提供的使用者名稱是 UPN 格式。
9015：「因為 vCenter 上的許可權不足，所以無法連接到 VMware Vm」 | VCenter 使用者帳戶未啟用來賓作業角色 | 請確定已在 vCenter 使用者帳戶上啟用來賓作業角色。
9016：「無法連線至 VMware Vm，因為來賓作業代理程式已超出資料」 | VMware 工具未正確安裝或不是最新版本。 | 請確認 VMware 工具已正確安裝且處於最新狀態。
9017：「在 VM 上找不到具有探索到的中繼資料的檔案」。 | 問題可能是內部錯誤所造成。 | 請聯絡 Microsoft 支援服務以取得解決方法。
9018：「來賓 Vm 中未安裝 PowerShell。」 | 在來賓 VM 中無法使用 PowerShell。 | 在來賓 VM 中安裝 PowerShell。
9019：「因為來賓 VM 操作失敗而無法探索」 | VM 上的 VMware 來賓操作失敗。 | 請確認 VM 認證有效，而且來賓 VM 認證中提供的使用者名稱是 UPN 格式。
9020：「檔案建立許可權遭到拒絕」。 | 與使用者或群組原則相關聯的角色，會限制使用者在資料夾中建立檔案。 | 檢查來賓使用者所提供的是否具有資料夾中檔案的 create 許可權。 請參閱伺服器評估中的**通知**，以取得資料夾的名稱。
9021：「資料夾系統暫存路徑中的檔案建立許可權遭到拒絕」。 | 不支援 VM 上的 VMware 工具版本 | 將您的 VMware 工具版本升級至10.2.0。
9022：「取得 WMI 物件存取遭到拒絕」。 | 與使用者或群組原則相關聯的角色會限制使用者存取 WMI 物件。 | 請連絡 Microsoft 支援。
9023：「SystemRoot 環境變數值是空的。」 | 未知 | 請連絡 Microsoft 支援。
9024：「TEMP 環境變數值是空的。」 | 未知 | 請連絡 Microsoft 支援。
9025：「來賓 Vm 中的 PowerShell 已損毀。」 | 未知 | 在來賓 VM 中重新安裝 PowerShell，並檢查是否可以在來賓 VM 上執行 PowerShell。
8084：「因為 VMware 錯誤而無法探索應用程式： <Exception from VMware>」 | Azure Migrate 設備會使用 VMware Api 來探索應用程式。 如果在嘗試探索應用程式時 vCenter Server 擲回例外狀況，就會發生這個問題。 來自 VMware 的錯誤訊息會顯示在入口網站中顯示的錯誤訊息中。 | 搜尋[VMware 檔](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html)中的訊息，並遵循步驟來修正。 如果您無法修正，請聯絡 Microsoft 支援服務。



## <a name="next-steps"></a>後續步驟
為[VMware](how-to-set-up-appliance-vmware.md)、 [hyper-v](how-to-set-up-appliance-hyper-v.md)或[實體伺服器](how-to-set-up-appliance-physical.md)設定設備。
