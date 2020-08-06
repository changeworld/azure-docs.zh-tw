---
title: Microsoft Azure 儲存體總管版本資訊
description: 閱讀最新版本 Microsoft Azure 儲存體總管的版本資訊。 也會顯示先前版本的版本資訊。
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/12/2018
ms.author: cawa
ms.openlocfilehash: 91bc7adaf7829766c471056c50c1c3abd70dda63
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87828773"
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>Microsoft Azure 儲存體總管版本資訊

本文包含 Azure 儲存體總管的最新版本資訊，以及先前版本的版本資訊。 

[Microsoft Azure 儲存體總管](./vs-azure-tools-storage-manage-with-storage-explorer.md) 是一個獨立應用程式，可讓您在 Windows、macOS 和 Linux 上輕鬆使用 Azure 儲存體資料。

若要下載舊版的儲存體總管，您可以流覽 GitHub 存放庫的 [[發行] 頁面](https://github.com/microsoft/AzureStorageExplorer/releases)。

## <a name="version-1110"></a>版本1.11。0
11/4/2019

### <a name="new"></a>新增
* Blob、ADLS Gen2 和受控磁碟的作業會使用整合式 AzCopy。 更明確地說，下列作業會使用 AzCopy 完成：
   * Blob
      * 開啟以進行編輯 + 上傳
      * 上傳，包括拖曳 & drop
      * 下載
      * 複製 & 貼上 #1249
      * 刪除
   * ADLS Gen2 Blob
      * 上傳，包括拖曳 & drop
      * 下載
      * 複製 & 貼上
      * 刪除，包括資料夾刪除
   * 受控磁碟
      * 上傳
      * 下載
      * 複製 & 貼上

   此外，整合式 AzCopy 體驗已新增數個經常要求的功能：
   * 衝突解決方式-在傳輸期間會提示您解決衝突。 #1455
   * 上傳為分頁 blob-您可以選擇是否要 AzCopy 上傳 .vhd 和 .vhdx 檔案作為分頁 blob。 #1164 和 #1601
   * 可設定的 AzCopy 參數-已新增數個設定來微調 AzCopy 的效能和資源使用量。 請參閱下方的詳細資料。

* 為了啟用 ADLS Gen2 和 Blob 的多重通訊協定存取，並進一步增強 ADLS Gen2 體驗，我們已為 ADLS Gen2 帳戶新增下列功能：
   * 使用易記名稱搜尋以設定 ACL 許可權
   * 查看隱藏的容器，例如 $logs 和 $web
   * 取得和中斷容器租用
   * 取得和中斷 Blob 租用 #848
   * 管理容器存取原則
   * 設定 Blob 存取層
   * 複製 & 貼上 Blob

* 在此版本中，我們將預覽17種其他語言。 您可以在 [設定] 頁面的 [應用程式] → [地區設定] → [Language (Preview) ] 底下切換至您選擇的語言。 我們仍在努力翻譯其他字串，並改善翻譯品質。 如果您有任何關於翻譯的意見反應，或如果您注意到尚未轉譯的字串，請[在 GitHub 上開啟問題](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%F0%9F%8C%90%20localization&template=bug-report.md&title=)。
* 在每個版本中，我們會嘗試上架幾個設定，以啟用儲存體總管的微調。 在此版本中，我們新增了設定以進一步設定 AzCopy，以及隱藏服務節點：
   * AzCopy 頻寬限制-協助控制網路 AzCopy 所使用的數量。 您可以在「傳輸」→「AzCopy」→「最大傳輸速率」中找到此設定。 #1099
   * AzCopy MD5 檢查-可讓您設定是否及如何在下載時嚴格 AzCopy 檢查 MD5 雜湊。 您可以在「傳輸」→「AzCopy」→「檢查 MD5」找到這種設定。
   * AzCopy 並行和記憶體緩衝區大小-根據預設，AzCopy 會分析您的電腦，以判斷這些設定是否有合理的預設值。 但是，如果您遇到效能問題，這些先進的設定可以用來進一步量身打造 AzCopy 在電腦上執行的方式。 您可以在 [傳輸] → [AzCopy] 下找到這些設定。 #994
   * 顯示和隱藏服務節點-這些設定可讓您選擇顯示或隱藏儲存體總管支援的任何 Azure 服務。 您可以在 [服務] 區段下找到這些設定。 #1877

* 建立受控磁片的快照集時，現在會提供預設名稱。 #1847
* 附加 Azure AD 時，如果您附加 ADLS Gen2 Blob 容器，則會在節點旁顯示「 (ADLS Gen2) 」。 #1861

### <a name="fixes"></a>修正
* 複製、上傳或下載大型磁片時，儲存體總管有時候會無法撤銷操作所涉及的磁片存取權。 這個問題已經修正。 #2048
* 查看分割區索引鍵查詢時，資料表的統計資料失敗。 這個問題已經修正。 #1886

### <a name="known-issues"></a>已知問題
* 儲存體總管1.11.0 現在需要 DFS 端點 (，例如 "myaccount.dfs.core.windows.net" ) 附加至 ADLS Gen2 容器。 舊版的儲存體總管允許您使用 blob 端點。 這些附件在升級至1.11.0 之後可能無法再使用。 如果您遇到這個問題，請使用 DFS 端點重新附加。
* 不會檢查數值設定是否位於有效範圍內。 #2140
* 將 blob 容器從一個儲存體帳戶複製到樹狀檢視中的另一個，可能會失敗。 我們正在調查此問題。 #2124
* [自動重新整理] 設定還不會影響 [Blob Explorer] 中的所有作業。
* Azure Stack 不支援受控磁片功能。
* 如果磁片上傳或貼上失敗，且在失敗前建立了新的磁片，儲存體總管將不會為您刪除該磁片。
* 視您取消磁片上傳或貼上的時間而定，您可以讓新磁片處於損毀狀態。 如果發生這種情況，您需要刪除新的磁片，或手動呼叫磁片 Api 以取代磁片的內容，使其不再損毀。
* 使用 RBAC 時，儲存體總管需要一些管理層許可權，才能存取您的儲存體資源。 如需詳細資訊，請參閱[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。
* 中斷連結透過 SAS URI 連結的資源 (例如 Blob 容器) 可能會導致錯誤，使得其他附件無法正確顯示。 若要解決此問題，只需重新整理群組節點。 如需詳細資訊，請參閱 #537。
* 如果您使用 VS for Mac，而且曾建立自訂 AAD 設定，則您可能無法登入。 若要解決此問題，請刪除 ~/.IdentityService/AadConfigurations 的內容。 如果這麼做無法將您解除封鎖，請在此問題加上註解。
* Azurite 尚未完全實作所有的儲存體 API。 因此，對於開發儲存體使用 Azurite 時，可能出現未預期的錯誤或行為。
* 在少數情況下，樹狀焦點可能會固定在快速存取上。 若要取消固定焦點，您可以 [全部重新整理]。
* 由於 NodeJS 中的錯誤，造成無法從您的 OneDrive 資料夾上傳。 已修正該 Bug，但是尚未整合至 Electron。 若要解決這個在上傳到 Blob 容器或從 Blob 容器下載時的問題，您可以使用實驗性的 AzCopy 功能。
* 當目標為 Azure Stack，以附加 Blob 方式上傳特定檔案會失敗。
* 按一下工作上的 [取消] 之後，該工作可能需要經過一段時間才會取消。 這是因為我們使用這裡所述的取消篩選器因應措施。
* 如果您選擇錯誤的 PIN/智慧卡憑證，則必須重新啟動，才能使儲存體總管忘記該決定。
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案及實體的所有其他屬性和中繼資料在重新命名期間都會保留。
* Azure Stack 不支援下列功能。 嘗試在使用 Azure Stack 資源時使用這些功能，可能會導致非預期的錯誤。
   * 檔案共用
   * 存取層級
   * 虛刪除
   * ADLS Gen2
   * 受控磁碟
* 儲存體總管使用的 Electron 殼層具有一些 GPU (圖形處理單元) 硬體加速的問題。 如果儲存體總管顯示空白 (空的) 主視窗，您可以嘗試從命令列啟動儲存體總管並透過新增 `--disable-gpu` 切換停用 GPU 加速：

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* 在 Linux 上執行儲存體總管需要先安裝特定相依性。 如需詳細資訊，請參閱儲存體總管[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies)。

## <a name="previous-releases"></a>舊版

* [版本1.10.1 版](#version-1101)
* [版本1.10。0](#version-1100)
* [版本1.9。0](#version-190)
* [版本1.8。1](#version-181)
* [版本1.8。0](#version-180)
* [1.7.0 版](#version-170)
* [1.6.2 版](#version-162)
* [1.6.1 版](#version-161)
* [版本1.6。0](#version-160)
* [1.5.0 版](#version-150)
* [1.4.4 版](#version-144)
* [1.4.3 版](#version-143)
* [1.4.2 版](#version-142)
* [1.4.1 版](#version-141)
* [1.3.0 版](#version-130)
* [版本 1.2.0](#version-120)
* [1.1.0 版](#version-110)
* [1\.0.0 版](#version-100)
* [0.9.6 版](#version-096)
* [0.9.5 版](#version-095)
* [0.9.4 和 0.9.3 版](#version-094-and-093)
* [0.9.2 版](#version-092)
* [0.9.1 和 0.9.0 版](#version-091-and-090)
* [0.8.16 版](#version-0816)
* [版本 0.8.14](#version-0814)
* [0.8.13 版](#version-0813)
* [0.8.12、0.8.11 和 0.8.10 版](#version-0812-and-0811-and-0810)
* [0.8.9 和 0.8.8 版](#version-089-and-088)
* [0.8.7 版](#version-087)
* [0.8.6 版](#version-086)
* [0.8.5 版](#version-085)
* [0.8.4 版](#version-084)
* [0.8.3 版](#version-083)
* [0.8.2 版](#version-082)
* [0.8.0 版](#version-080)
* [0.7.20160509.0 版](#version-07201605090)
* [0.7.20160325.0 版](#version-07201603250)
* [0.7.20160129.1 版](#version-07201601291)
* [0.7.20160105.0 版](#version-07201601050)
* [0.7.20151116.0 版](#version-07201511160)

## <a name="version-1101"></a>版本1.10.1 版
2019/9/19

### <a name="hotfix"></a>Hotfix
* 某些使用者嘗試在其 ADLS Gen 1 帳戶中查看其資料時，1.10.0 發生錯誤。 此錯誤導致 explorer 面板無法正確呈現。 這個問題已經修正。 #1853 #1865

### <a name="new"></a>新增
* 儲存體總管現在有專用的設定 UI。 您可以從 [編輯] → [設定]，或按一下 [設定] 圖示， (左側垂直工具列中的齒輪) 。 這項功能是我們為了提供各種[使用者要求的設定](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate)而採取的第一步。 從這個版本開始，支援下列設定：
  * 佈景主題
  * Proxy
  * 退出時登出 #6
  * 啟用裝置程式碼流程登入
  * 自動重新整理 #1526
  * 啟用 AzCopy
  * AzCopy SAS 持續時間：如果您想要新增其他設定，請[在 GitHub 上開啟問題](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=)，並說明您想要查看的設定。
* 儲存體總管現在支援受控磁碟。 您可以：
  * 將內部部署 VHD 上傳至新的磁片
  * 下載磁片
  * 跨資源群組和區域複製並貼上磁片
  * 刪除磁片
  * 建立磁片的快照集

上傳、下載和跨區域複製的磁片都是由 AzCopy v10 提供技術支援。
* 儲存體總管現在可以透過 Linux 上的「貼齊存放區」安裝。 當您透過 [貼齊存放區] 安裝時，系統會為您安裝所有相依性，包括 .NET Core！ 我們目前已確認儲存體總管在 Ubuntu 和 CentOS 上正常執行。 如果您在其他 Linux 散發版本上從「貼齊存放區」安裝時遇到問題，請[在 GitHub 上開啟問題](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=)。 若要深入瞭解如何從「貼齊存放區」進行安裝，請參閱我們的[快速入門手冊](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux)。 #68
* 有兩項主要的變更是要附加至 Azure Active Directory (Azure AD) ，目的是要讓此功能更適合 ADLS Gen2 使用者：
  * 您現在可以選取您要附加的資源所在的租使用者。 這表示您不再需要擁有資源訂用帳戶的 RBAC 存取權。
  * 如果您要附加 ADLS Gen2 Blob 容器，您現在可以附加至容器中的特定路徑。
* 管理 ADLS Gen2 檔案和資料夾的 Acl 時，儲存體總管現在會在 ACL 中顯示實體的易記名稱。 #957
* 透過 OID 新增至 ADLS Gen2 ACL 時，儲存體總管現在會驗證 OID 屬於您租使用者中的有效實體。 #1603
* 在索引標籤之間流覽的鍵盤快速鍵現在會使用較多的標準按鍵組合。 #1018
* 按一下索引標籤，即可立即將它關閉。 #1348
* 如果 AzCopy 傳輸包含略過但沒有失敗，儲存體總管現在會顯示警告圖示，以反白顯示發生的略過。 #1490
* 整合式 AzCopy 已更新為版本10.2.1。 此外，您現在可以在 [關於] 對話方塊中，查看已安裝的 AzCopy 版本。 #1343

### <a name="fixes"></a>修正
* 許多使用者在使用附加的儲存體帳戶時，已遇到各種「無法讀取未定義的版本」或「無法讀取未定義的連接」錯誤。 雖然我們仍繼續調查此問題的根本原因，但在1.10.0 中，我們已改善載入附加儲存體帳戶的錯誤處理。 #1626、#985 和 #1532
*  (左側) 的瀏覽器樹狀結構可能會進入狀態，而焦點會重複跳到最上層節點。 這個問題已經修正。 #1596
* 管理 blob 的快照集時，螢幕讀取器不會讀取與快照集相關聯的時間戳記。 這個問題已經修正。 #1202
* 未設定 macOS 上的 Proxy 設定，以供驗證程式使用它們。 這個問題已經修正。 #1567
* 如果主權雲端中的儲存體帳戶是使用名稱和金鑰所連接，AzCopy 將無法使用。 這個問題已經修正。 #1544
* 透過連接字串附加時，儲存體總管現在會移除尾端空格。 #1387

### <a name="known-issues"></a>已知問題
* [自動重新整理] 設定還不會影響 [Blob Explorer] 中的所有作業。
* Azure Stack 不支援受控磁片功能。
* 如果磁片上傳或貼上失敗，且在失敗前建立了新的磁片，儲存體總管將不會為您刪除該磁片。
* 視您取消磁片上傳或貼上的時間而定，您可以讓新磁片處於損毀狀態。 如果發生這種情況，您需要刪除新的磁片，或手動呼叫磁片 Api 以取代磁片的內容，使其不再損毀。
* 視您取消磁片上傳或貼上的時間而定，您可以讓新磁片處於損毀狀態。 如果發生這種情況，您需要刪除新的磁片，或手動呼叫磁片 Api 以取代磁片的內容，使其不再損毀。
* 執行非 AzCopy Blob 下載時，不會驗證大型檔案的 MD5。 這是因為儲存體 SDK 中的錯誤所造成。 [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* 使用 RBAC 時，儲存體總管需要一些管理層許可權，才能存取您的儲存體資源。 如需詳細資訊，請參閱[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。
* 中斷連結透過 SAS URI 連結的資源 (例如 Blob 容器) 可能會導致錯誤，使得其他附件無法正確顯示。 若要解決此問題，只需重新整理群組節點。 如需詳細資訊，請參閱 #537。
* 如果您使用 VS for Mac，而且曾建立自訂 AAD 設定，則您可能無法登入。 若要解決此問題，請刪除 ~/.IdentityService/AadConfigurations 的內容。 如果這麼做無法將您解除封鎖，請在此問題加上註解。
* Azurite 尚未完全實作所有的儲存體 API。 因此，對於開發儲存體使用 Azurite 時，可能出現未預期的錯誤或行為。
* 在少數情況下，樹狀焦點可能會固定在快速存取上。 若要取消固定焦點，您可以 [全部重新整理]。
* 由於 NodeJS 中的錯誤，造成無法從您的 OneDrive 資料夾上傳。 已修正該 Bug，但是尚未整合至 Electron。 若要解決這個在上傳到 Blob 容器或從 Blob 容器下載時的問題，您可以使用實驗性的 AzCopy 功能。
* 當目標為 Azure Stack，以附加 Blob 方式上傳特定檔案會失敗。
* 按一下工作上的 [取消] 之後，該工作可能需要經過一段時間才會取消。 這是因為我們使用這裡所述的取消篩選器因應措施。
* 如果您選擇錯誤的 PIN/智慧卡憑證，則必須重新啟動，才能使儲存體總管忘記該決定。
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案及實體的所有其他屬性和中繼資料在重新命名期間都會保留。
* Azure Stack 不支援下列功能。 嘗試在使用 Azure Stack 資源時使用這些功能，可能會導致非預期的錯誤。
   * 檔案共用
   * 存取層級
   * 虛刪除
   * ADLS Gen2
   * 受控磁碟
* 儲存體總管使用的 Electron 殼層具有一些 GPU (圖形處理單元) 硬體加速的問題。 如果儲存體總管顯示空白 (空的) 主視窗，您可以嘗試從命令列啟動儲存體總管並透過新增 `--disable-gpu` 切換停用 GPU 加速：

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* 在 Linux 上執行儲存體總管需要先安裝特定相依性。 如需詳細資訊，請參閱儲存體總管[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies)。


## <a name="version-1100"></a>版本1.10。0
9/12/2019

### <a name="new"></a>新增

* 儲存體總管現在有專用的設定 UI。 您可以從 [編輯] → [設定]，或按一下 [設定] 圖示， (左側垂直工具列中的齒輪) 。 這項功能是我們為了提供各種[使用者要求的設定](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate)而採取的第一步。 從這個版本開始，支援下列設定：
    * 佈景主題
    * Proxy
    * 退出時登出[#6](https://www.github.com/Microsoft/AzureStorageExplorer/issues/6)
    * 啟用裝置程式碼流程登入
    * 自動重新整理[#1526](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1526)
    * 啟用 AzCopy
    * AzCopy SAS 持續時間

    如果您想要新增其他設定，請[在 GitHub 上開啟問題，並說明您想要查看的設定](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=)。
* 儲存體總管現在支援受控磁碟。 您可以：
    * 將內部部署 VHD 上傳至新的磁片
    * 下載磁片
    * 跨資源群組和區域複製並貼上磁片
    * 刪除磁片
    * 建立磁片的快照集

    上傳、下載和跨區域複製的磁片都是由 AzCopy v10 提供技術支援。
* 儲存體總管現在可以透過 Linux 上的「貼齊存放區」安裝。 當您透過 [貼齊存放區] 安裝時，系統會為您安裝所有相依性，包括 .NET Core！ 我們目前已確認儲存體總管在 Ubuntu 和 CentOS 上正常執行。 如果您在其他 Linux 散發版本上從「貼齊存放區」安裝時遇到問題，請[在 GitHub 上開啟問題](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=)。 若要深入瞭解如何從「貼齊存放區」進行安裝，請參閱我們的[快速入門手冊](https://aka.ms/storageexplorer/snapinformation)。 [#68](https://www.github.com/Microsoft/AzureStorageExplorer/issues/68)
* 有兩項主要變更可附加至 Azure Active Directory (Azure AD) ，目的是要讓該功能更適合 ADLS Gen2 使用者： * 您現在可以選取您所附加資源所在的租使用者。 這表示您不再需要擁有資源訂用帳戶的 RBAC 存取權。
        * 如果您要附加 ADLS Gen2 Blob 容器，您現在可以附加至容器中的特定路徑。
* 管理 ADLS Gen2 檔案和資料夾的 Acl 時，儲存體總管現在會在 ACL 中顯示實體的易記名稱。 [#957](https://www.github.com/Microsoft/AzureStorageExplorer/issues/957)
* 透過 OID 新增至 ADLS Gen2 ACL 時，儲存體總管現在會驗證 OID 屬於您租使用者中的有效實體。 [#1603](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1603)
* 在索引標籤之間流覽的鍵盤快速鍵現在會使用較多的標準按鍵組合。 [#1018](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1018)
* 按一下索引標籤，即可立即將它關閉。 [#1348](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1348)
* 如果 AzCopy 傳輸包含略過但沒有失敗，儲存體總管現在會顯示警告圖示，以反白顯示發生的略過。 [#1490](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1490)
* 整合式 AzCopy 已更新為版本10.2.1。 此外，您現在可以在 [關於] 對話方塊中，查看已安裝的 AzCopy 版本。 [#1343](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1343)

### <a name="fixes"></a>修正

* 許多使用者在使用附加的儲存體帳戶時，已遇到各種「無法讀取未定義的版本」或「無法讀取未定義的連接」錯誤。 雖然我們仍繼續調查此問題的根本原因，但在1.10.0 中，我們已改善載入附加儲存體帳戶的錯誤處理。 [#1626](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1626)、 [#985](https://www.github.com/Microsoft/AzureStorageExplorer/issues/985)和[#1532](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1532)
*  (左側) 的瀏覽器樹狀結構可能會進入狀態，而焦點會重複跳到最上層節點。 這個問題已經修正。 [#1596](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1596)
* 管理 blob 的快照集時，螢幕讀取器不會讀取與快照集相關聯的時間戳記。 這個問題已經修正。 [#1202](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1202)
* 未設定 macOS 上的 Proxy 設定，以供驗證程式使用它們。 這個問題已經修正。 [#1567](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1567)
* 如果主權雲端中的儲存體帳戶是使用名稱和金鑰所連接，AzCopy 將無法使用。 這個問題已經修正。 [#1544](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1544)
* 透過連接字串附加時，儲存體總管現在會移除尾端空格。 [#1387](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1387)

### <a name="known-issues"></a>已知問題

* [自動重新整理] 設定還不會影響 [Blob Explorer] 中的所有作業。
* Azure Stack 不支援受控磁片功能。
* 如果磁片上傳或貼上失敗，且在失敗前建立了新的磁片，儲存體總管將不會為您刪除該磁片。
* 視您取消磁片上傳或貼上的時間而定，您可以讓新磁片處於損毀狀態。 如果發生這種情況，您需要刪除新的磁片，或手動呼叫磁片 Api 以取代磁片的內容，使其不再損毀。
* 執行非 AzCopy Blob 下載時，不會驗證大型檔案的 MD5。 這是因為儲存體 SDK 中的錯誤所造成。 [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* 使用 RBAC 時，儲存體總管需要一些管理層許可權，才能存取您的儲存體資源。 如需詳細資訊，請參閱[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。
* 中斷連結透過 SAS URI 連結的資源 (例如 Blob 容器) 可能會導致錯誤，使得其他附件無法正確顯示。 若要解決此問題，只需重新整理群組節點。 如需詳細資訊，請參閱 #537。
* 如果您使用 VS for Mac 並已建立自訂 AAD 設定，則可能無法登入。 若要解決此問題，請刪除 ~/.IdentityService/AadConfigurations 的內容。 如果這麼做無法將您解除封鎖，請在此問題加上註解。
* Azurite 尚未完全實作所有的儲存體 API。 因此，對於開發儲存體使用 Azurite 時，可能出現未預期的錯誤或行為。
* 在少數情況下，樹狀焦點可能會固定在快速存取上。 若要取消固定焦點，您可以 [全部重新整理]。
* 由於 NodeJS 中的錯誤，造成無法從您的 OneDrive 資料夾上傳。 已修正該 Bug，但是尚未整合至 Electron。 若要解決這個在上傳到 Blob 容器或從 Blob 容器下載時的問題，您可以使用實驗性的 AzCopy 功能。
* 當目標為 Azure Stack，以附加 Blob 方式上傳特定檔案會失敗。
* 按一下工作上的 [取消] 之後，該工作可能需要經過一段時間才會取消。 這是因為我們使用這裡所述的取消篩選器因應措施。
* 如果您選擇錯誤的 PIN/智慧卡憑證，則必須重新啟動，才能使儲存體總管忘記該決定。
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案及實體的所有其他屬性和中繼資料在重新命名期間都會保留。
* Azure Stack 不支援下列功能。 嘗試在使用 Azure Stack 資源時使用這些功能，可能會導致非預期的錯誤。
   * 檔案共用
   * 存取層級
   * 虛刪除
   * ADLS Gen2
   * 受控磁碟
* 儲存體總管使用的 Electron 殼層具有一些 GPU (圖形處理單元) 硬體加速的問題。 如果儲存體總管顯示空白 (空的) 主視窗，您可以嘗試從命令列啟動儲存體總管並透過新增 `--disable-gpu` 切換停用 GPU 加速：

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* 在 Linux 上執行儲存體總管需要先安裝特定相依性。 如需詳細資訊，請參閱儲存體總管[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies)。

## <a name="version-190"></a>版本1.9。0
7/1/2019

### <a name="download-azure-storage-explorer-190"></a>下載 Azure 儲存體總管1.9。0
- [適用于 Windows 的 Azure 儲存體總管1.9。0](https://go.microsoft.com/fwlink/?LinkId=708343)
- [適用于 Mac 的 Azure 儲存體總管1.9。0](https://go.microsoft.com/fwlink/?LinkId=708342)
- [適用于 Linux 的 Azure 儲存體總管1.9。0](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>新增

* 您現在可以透過 Azure AD (RBAC 或 ACL 許可權) 來附加 Blob 容器。 這項功能的目的是要協助具有容器存取權的使用者，而不是容器所在的儲存體帳戶。 如需這項功能的詳細資訊，請參閱我們的消費者入門指南。
* 取得和中斷租用現在可與 RBAC 搭配使用。 [#1354](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1354)
* 管理存取原則和設定公用存取層級現在可與 RBAC 搭配使用。 [#1355](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1355)
* 刪除 blob 資料夾現在可與 RBAC 搭配使用。 [#1450](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1450)
* 變更 blob 存取層現在可與 RBAC 搭配使用。 [#1446](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1446)
* 您現在可以透過 [說明] → [重設] 快速重設快速存取。 [#1327](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1327)

### <a name="preview-features"></a>預覽功能

* 裝置程式碼流程登入現在可供預覽。 若要啟用它，請移至 [預覽] → [使用裝置程式碼流程登入]。 我們鼓勵任何已發生空白登入 windows 問題的使用者嘗試這項功能，因為它可能會證明是更可靠的登入形式。
* 與 AzCopy 整合的儲存體總管目前可供預覽。 若要啟用它，請移至 [預覽] → [使用 AzCopy 改善 Blob 上傳及下載]。 已完成 AzCopy 的 Blob 傳輸，應更快速且更具效能。

### <a name="fixes"></a>修正

* 已修正無法為一個帳戶載入超過50個訂閱。 [#1416](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1416)
* 修正 [登入] 按鈕無法在直接連結失敗時顯示的資訊列上運作。 [#1358](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1358)
* 已修正未在 macOS 上上傳應用程式檔。 [#1119](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1119)
* 已修正失敗的 blob 重新命名時，「全部重試」無法運作的問題。 [#992](https://www.github.com/Microsoft/AzureStorageExplorer/issues/992)
* 已修正開啟 blob 時，[取消] 無法運作的問題。 [#1464](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1464)
* 已修正產品中的多個拼寫與工具提示問題。 很多人都感謝回報這些問題！ [#1303](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1303)、 [#1328](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1328)、 [#1329](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1329)、 [#1331](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1331)、 [#1336](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1336)、 [#1352](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1352)、 [#1368](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1368)、 [#1395](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1395)

### <a name="known-issues"></a>已知問題

* 執行非 AzCopy Blob 下載時，不會驗證大型檔案的 MD5。 這是因為儲存體 SDK 中的錯誤所造成。 [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* 使用 RBAC 時，儲存體總管需要一些管理層許可權，才能存取您的儲存體資源。 如需詳細資訊，請參閱[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。
* 在 proxy 後方嘗試存取 ADLS Gen2 Blob 可能會失敗。
* 中斷連結透過 SAS URI 連結的資源 (例如 Blob 容器) 可能會導致錯誤，使得其他附件無法正確顯示。 若要解決此問題，只需重新整理群組節點。 如需詳細資訊，請參閱 #537。
* 如果您使用 VS for Mac，而且曾建立自訂 AAD 設定，則您可能無法登入。 若要解決此問題，請刪除 ~/.IdentityService/AadConfigurations 的內容。 如果這麼做無法將您解除封鎖，請在此問題加上註解。
* Azurite 尚未完全實作所有的儲存體 API。 因此，對於開發儲存體使用 Azurite 時，可能出現未預期的錯誤或行為。
* 在少數情況下，樹狀焦點可能會固定在快速存取上。 若要取消固定焦點，您可以 [全部重新整理]。
* 由於 NodeJS 中的錯誤，造成無法從您的 OneDrive 資料夾上傳。 已修正該 Bug，但是尚未整合至 Electron。 若要解決這個在上傳到 Blob 容器或從 Blob 容器下載時的問題，您可以使用實驗性的 AzCopy 功能。
* 當目標為 Azure Stack，以附加 Blob 方式上傳特定檔案會失敗。
* 按一下工作上的 [取消] 之後，該工作可能需要經過一段時間才會取消。 這是因為我們使用這裡所述的取消篩選器因應措施。
* 如果您選擇錯誤的 PIN/智慧卡憑證，則必須重新啟動，才能使儲存體總管忘記該決定。
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案及實體的所有其他屬性和中繼資料在重新命名期間都會保留。
* Azure Stack 不支援下列功能。 嘗試在使用 Azure Stack 資源時使用這些功能，可能會導致非預期的錯誤。
   * 檔案共用
   * 存取層級
   * 虛刪除
   * ADLS Gen2
* 儲存體總管使用的 Electron 殼層具有一些 GPU (圖形處理單元) 硬體加速的問題。 如果儲存體總管顯示空白 (空的) 主視窗，您可以嘗試從命令列啟動儲存體總管並透過新增 `--disable-gpu` 切換停用 GPU 加速：

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* 在 Linux 上執行儲存體總管需要先安裝特定相依性。 如需詳細資訊，請參閱儲存體總管[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies)。

## <a name="version-181"></a>版本1.8。1
5/13/2019

### <a name="hotfixes"></a>Hotfix
* 在某些情況下，在資源層級按一下 [載入更多] 時，不會傳回下一個資源頁面。 這個問題已經修正。 [#1359](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1359)
* 在 Windows 上，如果正在下載單一檔案或資料夾，而且檔案或資料夾的名稱具有對 Windows 路徑不正確字元，則 AzCopy 下載將會失敗。 這個問題已經修正。 [#1350](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1350)
* 在極罕見的情況下，在檔案共用中執行檔案共用的重新命名或重新命名時，如果重新命名的複本失敗，或如果存放裝置探索無法使用 Azure 確認複本是否成功，在複製完成之前，可能會有儲存體總管刪除原始檔案。 這個問題已經修正。

### <a name="new"></a>新增

* 整合式 AzCopy 版本已更新為版本10.1.0。
* 現在可以使用 Ctrl/Cmd + R 來重新整理目前焦點的編輯器。 [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* Azure Stack 儲存體 API 版本已變更為2017-04-17。
* ADLS Gen2 的 [管理存取] 對話方塊現在會以類似于其他 POSIX 許可權工具的方式來保持遮罩的同步。 如果進行的變更導致使用者或群組的許可權超過遮罩的範圍，UI 也會警告您。 [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* 針對 AzCopy 上傳，現在已啟用用來計算和設定 MD5 雜湊的旗標。 [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>預覽功能

* 裝置程式碼流程登入現在可供預覽。 若要啟用它，請移至 [預覽] → [使用裝置程式碼流程登入]。 我們鼓勵任何已發生空白登入 windows 問題的使用者嘗試這項功能，因為它可能會證明是更可靠的登入形式。
* 與 AzCopy 整合的儲存體總管目前可供預覽。 若要啟用它，請移至 [預覽] → [使用 AzCopy 改善 Blob 上傳及下載]。 已完成 AzCopy 的 Blob 傳輸，應更快速且更具效能。

### <a name="fixes"></a>修正

* [存取原則] 對話方塊將不會再于沒有到期的儲存體存取原則上設定到期日。 [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* 已對 [產生 SAS] 對話方塊進行一些變更，以確保在產生 SAS 時，會正確使用預存的存取原則。 [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* 嘗試將非512位元組對齊的檔案上傳至分頁 Blob 時，儲存體總管現在會公開更相關的錯誤。 [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* 複製使用顯示名稱的 Blob 容器將會失敗。 現在會使用 Blob 容器的實際名稱。 [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* 嘗試在其名稱中有 unicode 字元的 ADLS Gen2 資料夾上執行特定動作會失敗。 所有動作現在都應該可以正常執行。 [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>已知問題

* 執行非 AzCopy Blob 下載時，不會驗證大型檔案的 MD5。 這是因為儲存體 SDK 中的錯誤所造成。 [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* 使用 RBAC 時，儲存體總管需要一些管理層許可權，才能存取您的儲存體資源。 如需詳細資訊，請參閱[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。
* 在 proxy 後方嘗試存取 ADLS Gen2 Blob 可能會失敗。
* 中斷連結透過 SAS URI 連結的資源 (例如 Blob 容器) 可能會導致錯誤，使得其他附件無法正確顯示。 若要解決此問題，只需重新整理群組節點。 如需詳細資訊，請參閱 #537。
* 如果您使用 VS for Mac，而且曾建立自訂 AAD 設定，則您可能無法登入。 若要解決此問題，請刪除 ~/.IdentityService/AadConfigurations 的內容。 如果這麼做無法將您解除封鎖，請在此問題加上註解。
* Azurite 尚未完全實作所有的儲存體 API。 因此，對於開發儲存體使用 Azurite 時，可能出現未預期的錯誤或行為。
* 在少數情況下，樹狀焦點可能會固定在快速存取上。 若要取消固定焦點，您可以 [全部重新整理]。
* 由於 NodeJS 中的錯誤，造成無法從您的 OneDrive 資料夾上傳。 已修正該 Bug，但是尚未整合至 Electron。 若要解決這個在上傳到 Blob 容器或從 Blob 容器下載時的問題，您可以使用實驗性的 AzCopy 功能。
* 當目標為 Azure Stack，以附加 Blob 方式上傳特定檔案會失敗。
* 按一下工作上的 [取消] 之後，該工作可能需要經過一段時間才會取消。 這是因為我們使用這裡所述的取消篩選器因應措施。
* 如果您選擇錯誤的 PIN/智慧卡憑證，則必須重新啟動，才能使儲存體總管忘記該決定。
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案及實體的所有其他屬性和中繼資料在重新命名期間都會保留。
* Azure Stack 不支援下列功能。 嘗試在使用 Azure Stack 資源時使用這些功能，可能會導致非預期的錯誤。
   * 檔案共用
   * 存取層級
   * 虛刪除
   * ADLS Gen2
* 儲存體總管使用的 Electron 殼層具有一些 GPU (圖形處理單元) 硬體加速的問題。 如果儲存體總管顯示空白 (空的) 主視窗，您可以嘗試從命令列啟動儲存體總管並透過新增 `--disable-gpu` 切換停用 GPU 加速：

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* 在 Linux 上執行儲存體總管需要先安裝特定相依性。 如需詳細資訊，請參閱儲存體總管[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies)。

## <a name="version-180"></a>版本1.8。0
2019/5/1

### <a name="new"></a>新增

* 整合式 AzCopy 版本已更新為版本10.1.0。
* 現在可以使用 Ctrl/Cmd + R 來重新整理目前焦點的編輯器。 [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* Azure Stack 儲存體 API 版本已變更為2017-04-17。
* ADLS Gen2 的 [管理存取] 對話方塊現在會以類似于其他 POSIX 許可權工具的方式來保持遮罩的同步。 如果進行的變更導致使用者或群組的許可權超過遮罩的範圍，UI 也會警告您。 [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* 針對 AzCopy 上傳，現在已啟用用來計算和設定 MD5 雜湊的旗標。 [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>預覽功能

* 裝置程式碼流程登入現在可供預覽。 若要啟用它，請移至 [預覽] → [使用裝置程式碼流程登入]。 我們鼓勵任何已發生空白登入 windows 問題的使用者嘗試這項功能，因為它可能會證明是更可靠的登入形式。
* 與 AzCopy 整合的儲存體總管目前可供預覽。 若要啟用它，請移至 [預覽] → [使用 AzCopy 改善 Blob 上傳及下載]。 已完成 AzCopy 的 Blob 傳輸，應更快速且更具效能。

### <a name="fixes"></a>修正

* [存取原則] 對話方塊將不會再于沒有到期的儲存體存取原則上設定到期日。 [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* 已對 [產生 SAS] 對話方塊進行一些變更，以確保在產生 SAS 時，會正確使用預存的存取原則。 [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* 嘗試將非512位元組對齊的檔案上傳至分頁 Blob 時，儲存體總管現在會公開更相關的錯誤。 [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* 複製使用顯示名稱的 Blob 容器將會失敗。 現在會使用 Blob 容器的實際名稱。 [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* 嘗試在其名稱中有 unicode 字元的 ADLS Gen2 資料夾上執行特定動作會失敗。 所有動作現在都應該可以正常執行。 [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>已知問題

* 執行非 AzCopy Blob 下載時，不會驗證大型檔案的 MD5。 這是因為儲存體 SDK 中的錯誤所造成。 [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* 使用 RBAC 時，儲存體總管需要一些管理層許可權，才能存取您的儲存體資源。 如需詳細資訊，請參閱[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。
* 在 proxy 後方嘗試存取 ADLS Gen2 Blob 可能會失敗。
* 中斷連結透過 SAS URI 連結的資源 (例如 Blob 容器) 可能會導致錯誤，使得其他附件無法正確顯示。 若要解決此問題，只需重新整理群組節點。 如需詳細資訊，請參閱 #537。
* 如果您使用 VS for Mac，而且曾建立自訂 AAD 設定，則您可能無法登入。 若要解決此問題，請刪除 ~/.IdentityService/AadConfigurations 的內容。 如果這麼做無法將您解除封鎖，請在此問題加上註解。
* Azurite 尚未完全實作所有的儲存體 API。 因此，對於開發儲存體使用 Azurite 時，可能出現未預期的錯誤或行為。
* 在少數情況下，樹狀焦點可能會固定在快速存取上。 若要取消固定焦點，您可以 [全部重新整理]。
* 由於 NodeJS 中的錯誤，造成無法從您的 OneDrive 資料夾上傳。 已修正該 Bug，但是尚未整合至 Electron。 若要解決這個在上傳到 Blob 容器或從 Blob 容器下載時的問題，您可以使用實驗性的 AzCopy 功能。
* 當目標為 Azure Stack，以附加 Blob 方式上傳特定檔案會失敗。
* 按一下工作上的 [取消] 之後，該工作可能需要經過一段時間才會取消。 這是因為我們使用這裡所述的取消篩選器因應措施。
* 如果您選擇錯誤的 PIN/智慧卡憑證，則必須重新啟動，才能使儲存體總管忘記該決定。
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案及實體的所有其他屬性和中繼資料在重新命名期間都會保留。
* Azure Stack 不支援下列功能。 嘗試在使用 Azure Stack 資源時使用這些功能，可能會導致非預期的錯誤。
   * 檔案共用
   * 存取層級
   * 虛刪除
   * ADLS Gen2
* 儲存體總管使用的 Electron 殼層具有一些 GPU (圖形處理單元) 硬體加速的問題。 如果儲存體總管顯示空白 (空的) 主視窗，您可以嘗試從命令列啟動儲存體總管並透過新增 `--disable-gpu` 切換停用 GPU 加速：

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* 在 Linux 上執行儲存體總管需要先安裝特定相依性。 如需詳細資訊，請參閱儲存體總管[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#linux-dependencies)。

## <a name="version-170"></a>1.7.0 版
3/5/2019

### <a name="download-azure-storage-explorer-170"></a>下載 Azure 儲存體總管1.7。0
- [適用于 Windows 的 Azure 儲存體總管1.7。0](https://go.microsoft.com/fwlink/?LinkId=708343)
- [適用于 Mac 的 Azure 儲存體總管1.7。0](https://go.microsoft.com/fwlink/?LinkId=708342)
- [適用于 Linux 的 Azure 儲存體總管1.7。0](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>新增

* 您現在可以在管理 ADLS Gen2 容器、檔案或資料夾的存取權時，變更擁有者和擁有群組。
* 在 Windows 上，更新產品內的儲存體總管現在是累加式安裝。 這應該會產生更快的更新體驗。 如果您偏好全新安裝，則可以自行下載[安裝程式](https://azure.microsoft.com/features/storage-explorer/)，然後手動安裝。 #1089

### <a name="preview-features"></a>預覽功能

* 裝置程式碼流程登入現在可供預覽。 若要啟用它，請移至 [預覽] → [使用裝置程式碼流程登入]。 我們鼓勵任何已發生空白登入 windows 問題的使用者嘗試這項功能，因為它可能會證明是更可靠的登入形式。 #938
* 與 AzCopy 整合的儲存體總管目前可供預覽。 若要啟用它，請移至 [預覽] → [使用 AzCopy 改善 Blob 上傳及下載]。 已完成 AzCopy 的 Blob 傳輸，應更快速且更具效能。

### <a name="fixes"></a>修正

* 您現在可以選擇您想要上傳的 blob 類型，如同 AzCopy 啟用時。 #1111
* 先前，如果您已啟用 ADLS Gen2 儲存體帳戶的靜態網站，然後使用名稱和金鑰附加它，儲存體總管就不會偵測到已啟用階層命名空間。 這個問題已經修正。 #1081
* 在 [blob 編輯器] 中，依剩餘保留天數或狀態已中斷排序。 這個問題已經修正。 #1106
* 在1.5.0 之後，儲存體總管不會再等待伺服器端複本完成，然後再于重新命名或複製 & 貼上時，才報告成功。 這個問題已經修正。 #976
* 使用實驗性 AzCopy 功能時，在按一下 [將命令複製到剪貼簿] 之後複製的命令不一定會自行執行。 現在，手動執行傳輸所需的所有命令都會被覆制。 #1079
* 先前，如果您是在 proxy 後方，就無法存取 ADLS Gen2 的 blob。 這是因為儲存 SDK 所使用的新網路程式庫中有錯誤。 在1.7.0 中，嘗試減輕這個問題，但有些人可能會繼續看到問題。 在未來的更新中將會發行完整的修正程式。 #1090
* 在1.7.0 中，[儲存檔案] 對話方塊現在會正確地記住您儲存檔案的最後一個位置。 #16
* 在 [屬性] 面板中，儲存體帳戶的 SKU 層會顯示為帳戶的種類。 這個問題已經修正。 #654
* 有時候，即使您已正確輸入 blob 的名稱，也無法中斷 blob 的租用。 這個問題已經修正。 #1070

### <a name="known-issues"></a>已知問題

* 使用 RBAC 時，儲存體總管需要一些管理層許可權，才能存取您的儲存體資源。 如需詳細資訊，請參閱[疑難排解指南](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting)。
* 在 proxy 後方嘗試存取 ADLS Gen2 Blob 可能會失敗。
* 中斷連結透過 SAS URI 連結的資源 (例如 Blob 容器) 可能會導致錯誤，使得其他附件無法正確顯示。 若要解決此問題，只需重新整理群組節點。 如需詳細資訊，請參閱 #537。
* 中斷連結透過 SAS URI 連結的資源 (例如 Blob 容器) 可能會導致錯誤，使得其他附件無法正確顯示。 若要解決此問題，只需重新整理群組節點。 如需詳細資訊，請參閱 #537。
* 如果您使用 VS for Mac，而且曾建立自訂 AAD 設定，則您可能無法登入。 若要解決此問題，請刪除 ~/.IdentityService/AadConfigurations 的內容。 如果這麼做無法將您解除封鎖，請在此問題加上註解。
* Azurite 尚未完全實作所有的儲存體 API。 因此，對於開發儲存體使用 Azurite 時，可能出現未預期的錯誤或行為。
* 在少數情況下，樹狀焦點可能會固定在快速存取上。 若要取消固定焦點，您可以 [全部重新整理]。
* 由於 NodeJS 中的錯誤，造成無法從您的 OneDrive 資料夾上傳。 已修正該 Bug，但是尚未整合至 Electron。 若要解決這個在上傳到 Blob 容器或從 Blob 容器下載時的問題，您可以使用實驗性的 AzCopy 功能。
* 當目標為 Azure Stack，以附加 Blob 方式上傳特定檔案會失敗。
* 按一下工作上的 [取消] 之後，該工作可能需要經過一段時間才會取消。 這是因為我們使用這裡所述的取消篩選器因應措施。
* 如果您選擇錯誤的 PIN/智慧卡憑證，則必須重新啟動，才能使儲存體總管忘記該決定。
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案及實體的所有其他屬性和中繼資料在重新命名期間都會保留。
* Azure Stack 不支援下列功能。 嘗試在使用 Azure Stack 資源時使用這些功能，可能會導致非預期的錯誤。
   * 檔案共用
   * 存取層級
   * 虛刪除
* 儲存體總管使用的 Electron 殼層具有一些 GPU (圖形處理單元) 硬體加速的問題。 如果儲存體總管顯示空白 (空的) 主視窗，您可以嘗試從命令列啟動儲存體總管並透過新增 `--disable-gpu` 切換停用 GPU 加速：

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* 對於 Linux 使用者，您必須安裝 [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)。
* 針對使用 Ubuntu 14.04 的使用者，您必須確定 GCC 已是最新版本，做法是執行下列命令，然後重新啟動電腦即可：

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* 使用 Ubuntu 17.04 的使用者必須安裝 GConf，這可以透過執行下列命令並重新啟動電腦來完成：

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-162"></a>1.6.2 版
2019 年 1 月 9 日

### <a name="hotfixes"></a>Hotfix
* 在 1.6.1 中，由 ObjectId 新增至 ADLS Gen2 ACL 的實體只要不是使用者便一律新增為群組。 現在，只有群組會新增為群組，「企業應用程式」和「服務主體」等實體則新增為使用者。 [#1049](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1049)
* 如果 ADLS Gen2 儲存體帳戶沒有容器且已連結名稱和金鑰，則「儲存體總管」無法將該「儲存體帳戶」偵測為 ADLS Gen2。 這個問題已經修正。 [#1048](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1048)
* 在 1.6.0 中，在複製和貼上期間發生衝突時，不會提示您解決衝突。 取而代之的是，衝突的複製會直接失敗。 現在，在第一次發生衝突時，將會詢問您要如何解決。 [#1014](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1014)
* 由於 API 限制，[管理存取] 對話方塊中的所有 ObjectId 驗證已停都用。 現在只會對使用者 UPN 進行驗證。 [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* 在 ADLS Gen2 的 [管理存取] 對話方塊中，無法修改群組的權限。 這個問題已經修正。 [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* 已新增 ADLS Gen2 編輯器的拖放上傳支援。 [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* ADLS Gen2 檔案和資料夾的 [屬性] 對話方塊中的 URL 屬性有時會遺漏 '/'。 這個問題已經修正。 [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* 如果無法取得 ADLS Gen2 容器、檔案或資料夾的目前權限，則錯誤現在會正確顯示於活動記錄中。 [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* 為了開啟檔案所建立的暫存路徑已縮短，可減少在 Windows 上建立長度超過 MAX_PATH 的路徑的機會。 [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* 若沒有已登入的使用者或未連結任何資源，[連線] 對話方塊現在可正確地出現。 [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* 在 1.6.0 中，儲存非 HNS Blob 和檔案的屬性會將每個屬性的值編碼。 這會導致只包含 ASCII 字元的值進行不必要的編碼。 現在，只有當值包含非 ASCII 字元時才會進行編碼。 [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* 如果使用了 SAS 且 SAS 沒有讀取權限，則無法將資料夾上傳到非 HNS Blob 容器。 這個問題已經修正。 [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* 取消 AzCopy 傳輸無法運作。 這個問題已經修正。 [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* 嘗試從 ADLS Gen2 Blob 容器下載資料夾時，如果資料夾的名稱中包含空格，則 AzCopy 會失敗。 這個問題已經修正。 [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* CosmosDB 編輯器已無法在 1.6.0 中使用。 現在已修正。 [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>新增

* 您現在可以使用儲存體總管，透過 [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409) 存取您的 Blob 資料。 如果您已登入且儲存體總管無法擷取儲存體帳戶的金鑰，則 OAuth 權杖將在處理資料時用來驗證。
* 儲存體總管現在支援 ADLS Gen2 Storage 帳戶。 當儲存體總管偵測到儲存體帳戶已啟用階層式命名空間時，您會在您儲存體帳戶的名稱旁邊看見「(ADLS Gen2 預覽)」。 儲存體總管能夠偵測當您登入時是否已啟用階層式命名空間，或您是否已連結具有名稱和金鑰的儲存體帳戶。 對於 ADLS Gen2 儲存體帳戶，您可以使用儲存體總管：
  * 建立和刪除容器
  * 管理容器屬性和權限 (左側)
  * 檢視及瀏覽容器內的資料
  * 建立新資料夾
  * 上傳、下載、重新命名及刪除檔案和資料夾
  * 管理檔案和資料夾屬性及權限 (右側)。
    
    目前無法使用其他一般 Blob 功能 (例如虛刪除) 和快照集。 管理權限也僅限於登入後使用。 此外，在 ADLS Gen2 儲存體帳戶中運作時，儲存體總管會使用 AzCopy 進行所有的上傳和下載，且預設為對所有作業使用名稱和金鑰認證 (如果有的話)。
* 在使用者強烈反應之後，中斷租用可再度用於一次在多個 Blob 上中斷租用。

### <a name="known-issues"></a>已知問題

* 從 ADLS Gen2 儲存體帳戶下載時，如果其中一個傳輸中的檔案已經存在，則 AzCopy 有時會當機。 這個問題將於即將推出的 Hotfix 中獲得修正。
* 中斷連結透過 SAS URI 連結的資源 (例如 Blob 容器) 可能會導致錯誤，使得其他附件無法正確顯示。 若要解決此問題，只需重新整理群組節點。 如需詳細資訊，請參閱 #537。
* 如果您使用 VS for Mac，而且曾建立自訂 AAD 設定，則您可能無法登入。 若要解決此問題，請刪除 ~/.IdentityService/AadConfigurations 的內容。 如果這麼做無法將您解除封鎖，請在此問題加上註解。
* Azurite 尚未完全實作所有的儲存體 API。 因此，對於開發儲存體使用 Azurite 時，可能出現未預期的錯誤或行為。
* 在少數情況下，樹狀焦點可能會固定在快速存取上。 若要取消固定焦點，您可以 [全部重新整理]。
* 由於 NodeJS 中的錯誤，造成無法從您的 OneDrive 資料夾上傳。 已修正該 Bug，但是尚未整合至 Electron。 若要解決這個在上傳到 Blob 容器或從 Blob 容器下載時的問題，您可以使用實驗性的 AzCopy 功能。
* 當目標為 Azure Stack，以附加 Blob 方式上傳特定檔案會失敗。
* 按一下工作上的 [取消] 之後，該工作可能需要經過一段時間才會取消。 這是因為我們使用這裡所述的取消篩選器因應措施。
* 如果您選擇錯誤的 PIN/智慧卡憑證，則必須重新啟動，才能使儲存體總管忘記該決定。
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案及實體的所有其他屬性和中繼資料在重新命名期間都會保留。
* Azure Stack 不支援下列功能。 嘗試在使用 Azure Stack 資源時使用這些功能，可能會導致非預期的錯誤。
   * 檔案共用
   * 存取層級
   * 虛刪除
* 儲存體總管使用的 Electron 殼層具有一些 GPU (圖形處理單元) 硬體加速的問題。 如果儲存體總管顯示空白 (空的) 主視窗，您可以嘗試從命令列啟動儲存體總管並透過新增 `--disable-gpu` 切換停用 GPU 加速：

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* 對於 Linux 使用者，您必須安裝 [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)。
* 針對使用 Ubuntu 14.04 的使用者，您必須確定 GCC 已是最新版本，做法是執行下列命令，然後重新啟動電腦即可：

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* 使用 Ubuntu 17.04 的使用者必須安裝 GConf，這可以透過執行下列命令並重新啟動電腦來完成：

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-161"></a>1.6.1 版
12/18/2018

### <a name="hotfixes"></a>Hotfix
* 由於 API 限制，[管理存取] 對話方塊中的所有 ObjectId 驗證已停都用。 現在只會對使用者 UPN 進行驗證。 [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* 在 ADLS Gen2 的 [管理存取] 對話方塊中，無法修改群組的權限。 這個問題已經修正。 [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* 已新增 ADLS Gen2 編輯器的拖放上傳支援。 [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* ADLS Gen2 檔案和資料夾的 [屬性] 對話方塊中的 URL 屬性有時會遺漏 '/'。 這個問題已經修正。 [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* 如果無法取得 ADLS Gen2 容器、檔案或資料夾的目前權限，則錯誤現在會正確顯示於活動記錄中。 [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* 為了開啟檔案所建立的暫存路徑已縮短，可減少在 Windows 上建立長度超過 MAX_PATH 的路徑的機會。 [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* 若沒有已登入的使用者或未連結任何資源，[連線] 對話方塊現在可正確地出現。 [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* 在 1.6.0 中，儲存非 HNS Blob 和檔案的屬性會將每個屬性的值編碼。 這會導致只包含 ASCII 字元的值進行不必要的編碼。 現在，只有當值包含非 ASCII 字元時才會進行編碼。 [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* 如果使用了 SAS 且 SAS 沒有讀取權限，則無法將資料夾上傳到非 HNS Blob 容器。 這個問題已經修正。 [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* 取消 AzCopy 傳輸無法運作。 這個問題已經修正。 [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* 嘗試從 ADLS Gen2 Blob 容器下載資料夾時，如果資料夾的名稱中包含空格，則 AzCopy 會失敗。 這個問題已經修正。 [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* CosmosDB 編輯器已無法在 1.6.0 中使用。 現在已修正。 [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>新增

* 您現在可以使用儲存體總管，透過 [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409) 存取您的 Blob 資料。 如果您已登入且儲存體總管無法擷取儲存體帳戶的金鑰，則 OAuth 權杖將在處理資料時用來驗證。
* 儲存體總管現在支援 ADLS Gen2 Storage 帳戶。 當儲存體總管偵測到儲存體帳戶已啟用階層式命名空間時，您會在您儲存體帳戶的名稱旁邊看見「(ADLS Gen2 預覽)」。 儲存體總管能夠偵測當您登入時是否已啟用階層式命名空間，或您是否已連結具有名稱和金鑰的儲存體帳戶。 對於 ADLS Gen2 儲存體帳戶，您可以使用儲存體總管：
  * 建立和刪除容器
  * 管理容器屬性和權限 (左側)
  * 檢視及瀏覽容器內的資料
  * 建立新資料夾
  * 上傳、下載、重新命名及刪除檔案和資料夾
  * 管理檔案和資料夾屬性及權限 (右側)。
    
    目前無法使用其他一般 Blob 功能 (例如虛刪除) 和快照集。 管理權限也僅限於登入後使用。 此外，在 ADLS Gen2 儲存體帳戶中運作時，儲存體總管會使用 AzCopy 進行所有的上傳和下載，且預設為對所有作業使用名稱和金鑰認證 (如果有的話)。
* 在使用者強烈反應之後，中斷租用可再度用於一次在多個 Blob 上中斷租用。

### <a name="known-issues"></a>已知問題

* 從 ADLS Gen2 儲存體帳戶下載時，如果其中一個傳輸中的檔案已經存在，則 AzCopy 有時會當機。 這個問題將於即將推出的 Hotfix 中獲得修正。
* 中斷連結透過 SAS URI 連結的資源 (例如 Blob 容器) 可能會導致錯誤，使得其他附件無法正確顯示。 若要解決此問題，只需重新整理群組節點。 如需詳細資訊，請參閱 #537。
* 如果您使用 VS for Mac，而且曾建立自訂 AAD 設定，則您可能無法登入。 若要解決此問題，請刪除 ~/.IdentityService/AadConfigurations 的內容。 如果這麼做無法將您解除封鎖，請在此問題加上註解。
* Azurite 尚未完全實作所有的儲存體 API。 因此，對於開發儲存體使用 Azurite 時，可能出現未預期的錯誤或行為。
* 在少數情況下，樹狀焦點可能會固定在快速存取上。 若要取消固定焦點，您可以 [全部重新整理]。
* 由於 NodeJS 中的錯誤，造成無法從您的 OneDrive 資料夾上傳。 已修正該 Bug，但是尚未整合至 Electron。 若要解決這個在上傳到 Blob 容器或從 Blob 容器下載時的問題，您可以使用實驗性的 AzCopy 功能。
* 當目標為 Azure Stack，以附加 Blob 方式上傳特定檔案會失敗。
* 按一下工作上的 [取消] 之後，該工作可能需要經過一段時間才會取消。 這是因為我們使用這裡所述的取消篩選器因應措施。
* 如果您選擇錯誤的 PIN/智慧卡憑證，則必須重新啟動，才能使儲存體總管忘記該決定。
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案及實體的所有其他屬性和中繼資料在重新命名期間都會保留。
* Azure Stack 不支援下列功能。 嘗試在使用 Azure Stack 資源時使用這些功能，可能會導致非預期的錯誤。
   * 檔案共用
   * 存取層級
   * 虛刪除
* 儲存體總管使用的 Electron 殼層具有一些 GPU (圖形處理單元) 硬體加速的問題。 如果儲存體總管顯示空白 (空的) 主視窗，您可以嘗試從命令列啟動儲存體總管並透過新增 `--disable-gpu` 切換停用 GPU 加速：

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* 對於 Linux 使用者，您必須安裝 [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)。
* 針對使用 Ubuntu 14.04 的使用者，您必須確定 GCC 已是最新版本，做法是執行下列命令，然後重新啟動電腦即可：

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* 使用 Ubuntu 17.04 的使用者必須安裝 GConf，這可以透過執行下列命令並重新啟動電腦來完成：

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-160"></a>1.6.0 版
12/5/2018

### <a name="new"></a>新增

* 您現在可以使用儲存體總管，透過 [RBAC](https://go.microsoft.com/fwlink/?linkid=2045904&clcid=0x409) 存取您的 Blob 資料。 如果您已登入且儲存體總管無法擷取儲存體帳戶的金鑰，則 OAuth 權杖將在處理資料時用來驗證。
* 儲存體總管現在支援 ADLS Gen2 Storage 帳戶。 當儲存體總管偵測到儲存體帳戶已啟用階層式命名空間時，您會在您儲存體帳戶的名稱旁邊看見「(ADLS Gen2 預覽)」。 儲存體總管能夠偵測當您登入時是否已啟用階層式命名空間，或您是否已連結具有名稱和金鑰的儲存體帳戶。 對於 ADLS Gen2 儲存體帳戶，您可以使用儲存體總管：
  * 建立和刪除容器
  * 管理容器屬性和權限 (左側)
  * 檢視及瀏覽容器內的資料
  * 建立新資料夾
  * 上傳、下載、重新命名及刪除檔案和資料夾
  * 管理檔案和資料夾屬性及權限 (右側)。
    
    目前無法使用其他一般 Blob 功能 (例如虛刪除) 和快照集。 管理權限也僅限於登入後使用。 此外，在 ADLS Gen2 儲存體帳戶中運作時，儲存體總管會使用 AzCopy 進行所有的上傳和下載，且預設為對所有作業使用名稱和金鑰認證 (如果有的話)。
* 在使用者強烈反應之後，中斷租用可再度用於一次在多個 Blob 上中斷租用。

### <a name="known-issues"></a>已知問題

* 從 ADLS Gen2 儲存體帳戶下載時，如果其中一個傳輸中的檔案已經存在，則 AzCopy 有時會當機。 這個問題將於即將推出的 Hotfix 中獲得修正。
* 中斷連結透過 SAS URI 連結的資源 (例如 Blob 容器) 可能會導致錯誤，使得其他附件無法正確顯示。 若要解決此問題，只需重新整理群組節點。 如需詳細資訊，請參閱 #537。
* 如果您使用 VS for Mac，而且曾建立自訂 AAD 設定，則您可能無法登入。 若要解決此問題，請刪除 ~/.IdentityService/AadConfigurations 的內容。 如果這麼做無法將您解除封鎖，請在此問題加上註解。
* Azurite 尚未完全實作所有的儲存體 API。 因此，對於開發儲存體使用 Azurite 時，可能出現未預期的錯誤或行為。
* 在少數情況下，樹狀焦點可能會固定在快速存取上。 若要取消固定焦點，您可以 [全部重新整理]。
* 由於 NodeJS 中的錯誤，造成無法從您的 OneDrive 資料夾上傳。 已修正該 Bug，但是尚未整合至 Electron。 若要解決這個在上傳到 Blob 容器或從 Blob 容器下載時的問題，您可以使用實驗性的 AzCopy 功能。
* 當目標為 Azure Stack，以附加 Blob 方式上傳特定檔案會失敗。
* 按一下工作上的 [取消] 之後，該工作可能需要經過一段時間才會取消。 這是因為我們使用這裡所述的取消篩選器因應措施。
* 如果您選擇錯誤的 PIN/智慧卡憑證，則必須重新啟動，才能使儲存體總管忘記該決定。
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案及實體的所有其他屬性和中繼資料在重新命名期間都會保留。
* Azure Stack 不支援下列功能。 嘗試在使用 Azure Stack 資源時使用這些功能，可能會導致非預期的錯誤。
   * 檔案共用
   * 存取層級
   * 虛刪除
* 儲存體總管使用的 Electron 殼層具有一些 GPU (圖形處理單元) 硬體加速的問題。 如果儲存體總管顯示空白 (空的) 主視窗，您可以嘗試從命令列啟動儲存體總管並透過新增 `--disable-gpu` 切換停用 GPU 加速：

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* 對於 Linux 使用者，您必須安裝 [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)。
* 針對使用 Ubuntu 14.04 的使用者，您必須確定 GCC 已是最新版本，做法是執行下列命令，然後重新啟動電腦即可：

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* 使用 Ubuntu 17.04 的使用者必須安裝 GConf，這可以透過執行下列命令並重新啟動電腦來完成：

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-150"></a>1.5.0 版
2018 年 10 月 29 日

### <a name="new"></a>新增

* 您現在可以使用 [AzCopy v10 (預覽)](https://github.com/Azure/azure-storage-azcopy) 來上傳和下載 Blob。 若要啟用此功能，請移至 [Experimental] \(實驗性\) 功能表，然後按一下 [Use AzCopy for Improved Blob Upload and Download] \(使用 AzCopy 來改善 Blob 上傳和下載\)。 啟用時，會在下列情況下使用 AzCopy：
   * 將資料夾和檔案上傳到 Blob 容器 (不論是透過工具列還是拖放)。
   * 下載資料夾和檔案 (不論是透過工具列還是操作功能表)。

* 此外，使用 AzCopy 時：
   * 您可以將用來執行傳輸的 AzCopy 命令複製到剪貼簿。 只要按一下活動記錄中的 [Copy AzCopy Command to Clipboard] \(將 AzCopy 命令複製到剪貼簿\) 即可。
   * 上傳之後，您將需要手動重新整理 Blob 編輯器。
   * 不支援上傳檔案來附加 Blob，而且 vhd 檔案將會以分頁 Blob 的形式上傳，而所有其他檔案則會以區塊 Blob 的形式上傳。
   * 在上傳或下載期間如有發生錯誤和衝突，將在上傳或下載完成之後才會顯示。

最後，未來將會支援搭配「檔案共用」使用 AzCopy。
* 「儲存體總管」目前使用 Electron 2.0.11 版。
* 目前只能一次在一個 Blob 上執行中斷租用。 此外，您還必須輸入要中斷租用的 Blob 名稱。 進行這項變更是為了降低不小心中斷租用的可能性 (尤其適用於 VM)。 #394
* 如果您遇到登入問題，現在已可嘗試重設驗證。 請移至 [Help] \(說明\) 功能表，然後按一下 [Reset] \(重設\) 以存取此功能。 #419

### <a name="fix"></a>修正

* 在收到強烈的使用者意見反應之後，我們已重新啟用預設模擬器節點。 您仍然可以透過 [Connect] \(連接\) 對話方塊來新增額外的模擬器連線，但如果您的模擬器已設定為使用預設連接埠，則您也可以使用 [Local & Attached] \(本機和已連結\) > [Storage Accounts] \(儲存體帳戶\) 底下的.[Emulator * Default Ports] \(模擬器 * 預設連接埠\)。 #669
* 「儲存體總管」將不再允許您設定開頭或結尾包含空白字元的 Blob 中繼資料值。 #760
* 在相同的 [Connect] \(連接\) 對話方塊頁面上一律啟用了 [Sign In] \(登入\) 按鈕。 現在已在適當的情況下予以停用。 #761
* 未新增任何「快速存取」項目時，「快速存取」不會再於主控台中產生錯誤。

### <a name="known-issues"></a>已知問題

* 中斷連結透過 SAS URI 連結的資源 (例如 Blob 容器) 可能會導致錯誤，使得其他附件無法正確顯示。 若要解決此問題，只需重新整理群組節點。 如需詳細資訊，請參閱 #537。
* 如果您使用 VS for Mac，而且曾建立自訂 AAD 設定，則您可能無法登入。 若要解決此問題，請刪除 ~/.IdentityService/AadConfigurations 的內容。 如果這麼做無法將您解除封鎖，請在此問題加上註解。
* Azurite 尚未完全實作所有的儲存體 API。 因此，對於開發儲存體使用 Azurite 時，可能出現未預期的錯誤或行為。
* 在少數情況下，樹狀焦點可能會固定在快速存取上。 若要取消固定焦點，您可以 [全部重新整理]。
* 由於 NodeJS 中的錯誤，造成無法從您的 OneDrive 資料夾上傳。 已修正該 Bug，但是尚未整合至 Electron。 若要解決這個在上傳到 Blob 容器或從 Blob 容器下載時的問題，您可以使用實驗性的 AzCopy 功能。
* 當目標為 Azure Stack，以附加 Blob 方式上傳特定檔案會失敗。
* 按一下工作上的 [取消] 之後，該工作可能需要經過一段時間才會取消。 這是因為我們使用這裡所述的取消篩選器因應措施。
* 如果您選擇錯誤的 PIN/智慧卡憑證，則必須重新啟動，才能使儲存體總管忘記該決定。
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案及實體的所有其他屬性和中繼資料在重新命名期間都會保留。
* Azure Stack 不支援下列功能。 嘗試在使用 Azure Stack 資源時使用這些功能，可能會導致非預期的錯誤。
   * 檔案共用
   * 存取層級
   * 虛刪除
* 儲存體總管使用的 Electron 殼層具有一些 GPU (圖形處理單元) 硬體加速的問題。 如果儲存體總管顯示空白 (空的) 主視窗，您可以嘗試從命令列啟動儲存體總管並透過新增 `--disable-gpu` 切換停用 GPU 加速：

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* 對於 Linux 使用者，您必須安裝 [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)。
* 針對使用 Ubuntu 14.04 的使用者，您必須確定 GCC 已是最新版本，做法是執行下列命令，然後重新啟動電腦即可：

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* 使用 Ubuntu 17.04 的使用者必須安裝 GConf，這可以透過執行下列命令並重新啟動電腦來完成：

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-144"></a>1.4.4 版
10/15/2018

### <a name="hotfixes"></a>Hotfix
* 已復原 Azure 資源管理 API 版本，以將 Azure US Government 使用者解除封鎖。 [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* 載入進度環現在使用 CSS 動畫來減少儲存體總管所使用的 GPU。 [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>新增
* 已針對 SAS 連線和模擬器等大幅改良外部資源附件。 現在您可以：
   * 針對要連結的資源自訂顯示名稱。 [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * 使用不同連接埠連結至多個本機模擬器。 [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * 將連結的資源新增至快速存取。 [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* 儲存體總管現在支援使虛刪除。 您可以：
   * 以滑鼠右鍵按一下您儲存體帳戶的 Blob 容器節點，即可設定虛刪除原則。
   * 在瀏覽列旁的下拉式清單中選取 [作用中和已刪除的 Blob]，即可在 Blob 編輯器中檢視虛刪除的 Blob。
   * 取消刪除虛刪除的 Blob。

### <a name="fixes"></a>修正
* 由於進階儲存體帳戶不支援 CORS，因此進階儲存體帳戶上不再有 [配置 CORS 設定] 動作。 [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* SAS 連結服務現在有共用存取簽章屬性。 [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* [設定預設存取層] 動作現在可用於釘選到快速存取的 Blob 和 GPV2 儲存體帳戶。 [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* 有時候，儲存體總管會無法顯示傳統儲存體帳戶。 [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>已知問題
* 使用模擬器 (例如「Azure 儲存體模擬器」或 Azurite) 時，您將必須讓它們在其預設連接埠上接聽連線。 否則，「儲存體總管」將無法連線至這些模擬器。
* 如果您使用 VS for Mac，而且曾建立自訂 AAD 設定，則您可能無法登入。 若要解決此問題，請刪除 ~/.IdentityService/AadConfigurations 的內容。 如果這麼做無法將您解除封鎖，請對[此問題](https://github.com/Microsoft/AzureStorageExplorer/issues/97)提出意見。
* Azurite 尚未完全實作所有的儲存體 API。 因此，對於開發儲存體使用 Azurite 時，可能出現未預期的錯誤或行為。
* 在少數情況下，樹狀焦點可能會固定在快速存取上。 若要取消固定焦點，您可以 [全部重新整理]。
* 由於 NodeJS 中的錯誤，造成無法從您的 OneDrive 資料夾上傳。 已修正該 Bug，但是尚未整合至 Electron。
* 當目標為 Azure Stack，以附加 Blob 方式上傳特定檔案會失敗。
* 按一下工作上的 [取消] 之後，該工作可能需要經過一段時間才會取消。 這是因為我們使用[這裡](https://github.com/Azure/azure-storage-node/issues/317)所述的取消篩選器因應措施。
* 如果您選擇錯誤的 PIN/智慧卡憑證，則必須重新啟動，才能使儲存體總管忘記該決定。
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案及實體的所有其他屬性和中繼資料在重新命名期間都會保留。
* 雖然 Azure Stack 目前並不支援檔案共用，檔案共用節點仍然會出現在附加的 Azure Stack 儲存體帳戶之下。
* 儲存體總管使用的 Electron 殼層具有一些 GPU (圖形處理單元) 硬體加速的問題。 如果儲存體總管顯示空白 (空的) 主視窗，您可以嘗試從命令列啟動儲存體總管並透過新增 `--disable-gpu` 切換停用 GPU 加速：

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* 對於 Linux 使用者，您必須安裝 [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)。
* 針對使用 Ubuntu 14.04 的使用者，您必須確定 GCC 已是最新版本，做法是執行下列命令，然後重新啟動電腦即可：

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* 使用 Ubuntu 17.04 的使用者必須安裝 GConf，這可以透過執行下列命令並重新啟動電腦來完成：

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-143"></a>1.4.3 版
10/11/2018

### <a name="hotfixes"></a>Hotfix
* 已復原 Azure 資源管理 API 版本，以將 Azure US Government 使用者解除封鎖。 [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* 載入進度環現在使用 CSS 動畫來減少儲存體總管所使用的 GPU。 [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>新增
* 已針對 SAS 連線和模擬器等大幅改良外部資源附件。 現在您可以：
   * 針對要連結的資源自訂顯示名稱。 [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * 使用不同連接埠連結至多個本機模擬器。 [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * 將連結的資源新增至快速存取。 [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* 儲存體總管現在支援使虛刪除。 您可以：
   * 以滑鼠右鍵按一下您儲存體帳戶的 Blob 容器節點，即可設定虛刪除原則。
   * 在瀏覽列旁的下拉式清單中選取 [作用中和已刪除的 Blob]，即可在 Blob 編輯器中檢視虛刪除的 Blob。
   * 取消刪除虛刪除的 Blob。

### <a name="fixes"></a>修正
* 由於進階儲存體帳戶不支援 CORS，因此進階儲存體帳戶上不再有 [配置 CORS 設定] 動作。 [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* SAS 連結服務現在有共用存取簽章屬性。 [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* [設定預設存取層] 動作現在可用於釘選到快速存取的 Blob 和 GPV2 儲存體帳戶。 [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* 有時候，儲存體總管會無法顯示傳統儲存體帳戶。 [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>已知問題
* 使用模擬器 (例如「Azure 儲存體模擬器」或 Azurite) 時，您將必須讓它們在其預設連接埠上接聽連線。 否則，「儲存體總管」將無法連線至這些模擬器。
* 如果您使用 VS for Mac，而且曾建立自訂 AAD 設定，則您可能無法登入。 若要解決此問題，請刪除 ~/.IdentityService/AadConfigurations 的內容。 如果這麼做無法將您解除封鎖，請對[此問題](https://github.com/Microsoft/AzureStorageExplorer/issues/97)提出意見。
* Azurite 尚未完全實作所有的儲存體 API。 因此，對於開發儲存體使用 Azurite 時，可能出現未預期的錯誤或行為。
* 在少數情況下，樹狀焦點可能會固定在快速存取上。 若要取消固定焦點，您可以 [全部重新整理]。
* 由於 NodeJS 中的錯誤，造成無法從您的 OneDrive 資料夾上傳。 已修正該 Bug，但是尚未整合至 Electron。
* 當目標為 Azure Stack，以附加 Blob 方式上傳特定檔案會失敗。
* 按一下工作上的 [取消] 之後，該工作可能需要經過一段時間才會取消。 這是因為我們使用[這裡](https://github.com/Azure/azure-storage-node/issues/317)所述的取消篩選器因應措施。
* 如果您選擇錯誤的 PIN/智慧卡憑證，則必須重新啟動，才能使儲存體總管忘記該決定。
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案及實體的所有其他屬性和中繼資料在重新命名期間都會保留。
* 雖然 Azure Stack 目前並不支援檔案共用，檔案共用節點仍然會出現在附加的 Azure Stack 儲存體帳戶之下。
* 儲存體總管使用的 Electron 殼層具有一些 GPU (圖形處理單元) 硬體加速的問題。 如果儲存體總管顯示空白 (空的) 主視窗，您可以嘗試從命令列啟動儲存體總管並透過新增 `--disable-gpu` 切換停用 GPU 加速：

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* 對於 Linux 使用者，您必須安裝 [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)。
* 針對使用 Ubuntu 14.04 的使用者，您必須確定 GCC 已是最新版本，做法是執行下列命令，然後重新啟動電腦即可：

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* 使用 Ubuntu 17.04 的使用者必須安裝 GConf，這可以透過執行下列命令並重新啟動電腦來完成：

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-142"></a>1.4.2 版
09/24/2018

### <a name="hotfixes"></a>Hotfix
* 將 Azure 資源管理 API 版本更新為 2018-07-01 以新增新 Azure 儲存體帳戶類型的支援。 [#652](https://github.com/Microsoft/AzureStorageExplorer/issues/652)

### <a name="new"></a>新增
* 已針對 SAS 連線和模擬器等大幅改良外部資源附件。 現在您可以：
   * 針對要連結的資源自訂顯示名稱。 [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * 使用不同連接埠連結至多個本機模擬器。 [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * 將連結的資源新增至快速存取。 [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* 儲存體總管現在支援使虛刪除。 您可以：
   * 以滑鼠右鍵按一下您儲存體帳戶的 Blob 容器節點，即可設定虛刪除原則。
   * 在瀏覽列旁的下拉式清單中選取 [作用中和已刪除的 Blob]，即可在 Blob 編輯器中檢視虛刪除的 Blob。
   * 取消刪除虛刪除的 Blob。

### <a name="fixes"></a>修正
* 由於進階儲存體帳戶不支援 CORS，因此進階儲存體帳戶上不再有 [配置 CORS 設定] 動作。 [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* SAS 連結服務現在有共用存取簽章屬性。 [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* [設定預設存取層] 動作現在可用於釘選到快速存取的 Blob 和 GPV2 儲存體帳戶。 [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* 有時候，儲存體總管會無法顯示傳統儲存體帳戶。 [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>已知問題
* 使用模擬器 (例如「Azure 儲存體模擬器」或 Azurite) 時，您將必須讓它們在其預設連接埠上接聽連線。 否則，「儲存體總管」將無法連線至這些模擬器。
* 如果您使用 VS for Mac，而且曾建立自訂 AAD 設定，則您可能無法登入。 若要解決此問題，請刪除 ~/.IdentityService/AadConfigurations 的內容。 如果這麼做無法將您解除封鎖，請對[此問題](https://github.com/Microsoft/AzureStorageExplorer/issues/97)提出意見。
* Azurite 尚未完全實作所有的儲存體 API。 因此，對於開發儲存體使用 Azurite 時，可能出現未預期的錯誤或行為。
* 在少數情況下，樹狀焦點可能會固定在快速存取上。 若要取消固定焦點，您可以 [全部重新整理]。
* 由於 NodeJS 中的錯誤，造成無法從您的 OneDrive 資料夾上傳。 已修正該 Bug，但是尚未整合至 Electron。
* 當目標為 Azure Stack，以附加 Blob 方式上傳特定檔案會失敗。
* 按一下工作上的 [取消] 之後，該工作可能需要經過一段時間才會取消。 這是因為我們使用[這裡](https://github.com/Azure/azure-storage-node/issues/317)所述的取消篩選器因應措施。
* 如果您選擇錯誤的 PIN/智慧卡憑證，則必須重新啟動，才能使儲存體總管忘記該決定。
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案及實體的所有其他屬性和中繼資料在重新命名期間都會保留。
* 雖然 Azure Stack 目前並不支援檔案共用，檔案共用節點仍然會出現在附加的 Azure Stack 儲存體帳戶之下。
* 儲存體總管使用的 Electron 殼層具有一些 GPU (圖形處理單元) 硬體加速的問題。 如果儲存體總管顯示空白 (空的) 主視窗，您可以嘗試從命令列啟動儲存體總管並透過新增 `--disable-gpu` 切換停用 GPU 加速：

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* 對於 Linux 使用者，您必須安裝 [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)。
* 針對使用 Ubuntu 14.04 的使用者，您必須確定 GCC 已是最新版本，做法是執行下列命令，然後重新啟動電腦即可：

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* 使用 Ubuntu 17.04 的使用者必須安裝 GConf，這可以透過執行下列命令並重新啟動電腦來完成：

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-141"></a>1.4.1 版
08/28/2018

### <a name="hotfixes"></a>Hotfix
* 第一次啟動時，儲存體總管無法產生用來加密敏感性資料的金鑰。 這會在使用快速存取和連結資源時造成問題。 [#535](https://github.com/Microsoft/AzureStorageExplorer/issues/535)
* 如果您的帳戶沒有對其主租用戶要求 MFA，但對其他租用戶有此要求，那麼儲存體總管將無法列出訂用帳戶。 現在，登入這類帳戶之後，儲存體總管將會要求您重新輸入認證和執行 MFA。 [#74](https://github.com/Microsoft/AzureStorageExplorer/issues/74)
* 儲存體總管無法從 Azure 德國及 Azure 美國政府連結資源。 [#572](https://github.com/Microsoft/AzureStorageExplorer/issues/572)
* 如果您登入兩個具有相同電子郵件地址的帳戶，儲存體總管有時會無法在樹狀檢視中顯示您的資源。 [#580](https://github.com/Microsoft/AzureStorageExplorer/issues/580)
* 在速度較慢的 Windows 機器上，啟動顯示畫面有時會需要很長的時間才會出現。 [#586](https://github.com/Microsoft/AzureStorageExplorer/issues/586)
* 即使沒有連結的帳戶或服務，還是會出現 [連線] 對話方塊。 [#588](https://github.com/Microsoft/AzureStorageExplorer/issues/588)

### <a name="new"></a>新增
* 已針對 SAS 連線和模擬器等大幅改良外部資源附件。 現在您可以：
   * 針對要連結的資源自訂顯示名稱。 [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * 使用不同連接埠連結至多個本機模擬器。 [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * 將連結的資源新增至快速存取。 [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* 儲存體總管現在支援使虛刪除。 您可以：
   * 以滑鼠右鍵按一下您儲存體帳戶的 Blob 容器節點，即可設定虛刪除原則。
   * 在瀏覽列旁的下拉式清單中選取 [作用中和已刪除的 Blob]，即可在 Blob 編輯器中檢視虛刪除的 Blob。
   * 取消刪除虛刪除的 Blob。

### <a name="fixes"></a>修正
* 由於進階儲存體帳戶不支援 CORS，因此進階儲存體帳戶上不再有 [配置 CORS 設定] 動作。 [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* SAS 連結服務現在有共用存取簽章屬性。 [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* [設定預設存取層] 動作現在可用於釘選到快速存取的 Blob 和 GPV2 儲存體帳戶。 [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* 有時候，儲存體總管會無法顯示傳統儲存體帳戶。 [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>已知問題
* 使用模擬器 (例如「Azure 儲存體模擬器」或 Azurite) 時，您將必須讓它們在其預設連接埠上接聽連線。 否則，「儲存體總管」將無法連線至這些模擬器。
* 如果您使用 VS for Mac，而且曾建立自訂 AAD 設定，則您可能無法登入。 若要解決此問題，請刪除 ~/.IdentityService/AadConfigurations 的內容。 如果這麼做無法將您解除封鎖，請對[此問題](https://github.com/Microsoft/AzureStorageExplorer/issues/97)提出意見。
* Azurite 尚未完全實作所有的儲存體 API。 因此，對於開發儲存體使用 Azurite 時，可能出現未預期的錯誤或行為。
* 在少數情況下，樹狀焦點可能會固定在快速存取上。 若要取消固定焦點，您可以 [全部重新整理]。
* 由於 NodeJS 中的錯誤，造成無法從您的 OneDrive 資料夾上傳。 已修正該 Bug，但是尚未整合至 Electron。
* 當目標為 Azure Stack，以附加 Blob 方式上傳特定檔案會失敗。
* 按一下工作上的 [取消] 之後，該工作可能需要經過一段時間才會取消。 這是因為我們使用[這裡](https://github.com/Azure/azure-storage-node/issues/317)所述的取消篩選器因應措施。
* 如果您選擇錯誤的 PIN/智慧卡憑證，則必須重新啟動，才能使儲存體總管忘記該決定。
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案及實體的所有其他屬性和中繼資料在重新命名期間都會保留。
* 雖然 Azure Stack 目前並不支援檔案共用，檔案共用節點仍然會出現在附加的 Azure Stack 儲存體帳戶之下。
* 儲存體總管使用的 Electron 殼層具有一些 GPU (圖形處理單元) 硬體加速的問題。 如果儲存體總管顯示空白 (空的) 主視窗，您可以嘗試從命令列啟動儲存體總管並透過新增 `--disable-gpu` 切換停用 GPU 加速：

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* 對於 Linux 使用者，您必須安裝 [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)。
* 針對使用 Ubuntu 14.04 的使用者，您必須確定 GCC 已是最新版本，做法是執行下列命令，然後重新啟動電腦即可：

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* 使用 Ubuntu 17.04 的使用者必須安裝 GConf，這可以透過執行下列命令並重新啟動電腦來完成：

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-130"></a>版本 1.3.0
07/09/2018

### <a name="new"></a>新增
* 現在支援存取靜態網站所使用的 $web 容器。 這可讓您輕鬆上傳及管理您的網站所使用的檔案和資料夾。 [#223](https://github.com/Microsoft/AzureStorageExplorer/issues/223)
* macOS 上的應用程式列已重整。 變更包括 [檔案] 功能表、一些快速鍵變更，以及應用程式功能表底下的數個新命令。 [#99](https://github.com/Microsoft/AzureStorageExplorer/issues/99)
* 用於登入 Azure US Government 的授權單位端點已變更為 https://login.microsoftonline.us/
* 協助工具：如果螢幕助讀程式為作用中，鍵盤瀏覽現在可搭配用於在右側顯示項目的資料表運作。 您可以使用方向鍵來瀏覽資料列和資料行，使用 Enter 鍵叫用預設動作，使用操作功能表鍵來開啟操作功能表中的項目，以及使用 Shift 或 Ctrl 鍵來選取多個項目。 [#103](https://github.com/Microsoft/AzureStorageExplorer/issues/103)

### <a name="fixes"></a>修正
*  在某些電腦上，子處理序花了很長的時間才能啟動。 發生這種情況時，會出現「子處理無法及時啟動」錯誤。 為了讓子處理序啟動而配置的時間現在已從 20 增加為 90 秒。 如果您仍然受到此問題影響，請註解連結的 GitHub 問題。 [#281](https://github.com/Microsoft/AzureStorageExplorer/issues/281)
* 使用沒有讀取權限的 SAS 時，無法上傳大型 blob。 上傳的邏輯已修改成在此案例中運作。 [#305](https://github.com/Microsoft/AzureStorageExplorer/issues/305)
* 設定容器的公開存取層級會移除所有的存取原則，反之亦然。 現在，若設定公開存取層級和存取原則其中一項，則會同時保留兩者。 [#197](https://github.com/Microsoft/AzureStorageExplorer/issues/197)
* "AccessTierChangeTime" 在 [屬性] 對話方塊中遭到截斷。 這個問題已經修正。 [#145](https://github.com/Microsoft/AzureStorageExplorer/issues/145)
* [建立新目錄] 對話方塊中遺漏 "Microsoft Azure 儲存體總管 -" 前置詞。 這個問題已經修正。 [#299](https://github.com/Microsoft/AzureStorageExplorer/issues/299)
* 協助工具：使用 VoiceOver 時難以瀏覽 [新增實體] 對話方塊。 已進行改進。 [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* 協助工具：[動作] 和 [屬性] 窗格的摺疊/展開按鈕背景色彩，與黑色高對比佈景主題中類似的 UI 控制項不一致。 此色彩已變更。 [#123](https://github.com/Microsoft/AzureStorageExplorer/issues/123)
* 協助工具：在黑色高對比佈景主題中，看不見 [屬性] 對話方塊中 'X' 按鈕的焦點樣式。 這個問題已經修正。 [#243](https://github.com/Microsoft/AzureStorageExplorer/issues/243)
* 協助工具：[動作] 和 [屬性] 索引標籤遺漏數個 aria 值，進而造成低於標準的螢幕助讀程式體驗。 現在已新增遺漏的 aria 值。 [#316](https://github.com/Microsoft/AzureStorageExplorer/issues/316)
* 協助工具：系統並未對左側摺疊的樹狀節點提供 aria-expanded 值 false。 這個問題已經修正。 [#352](https://github.com/Microsoft/AzureStorageExplorer/issues/352)

### <a name="known-issues"></a>已知問題
* 中斷連結透過 SAS URI 連結的資源 (例如 Blob 容器) 可能會導致錯誤，使得其他附件無法正確顯示。 若要解決此問題，只需重新整理群組節點。 請參閱[此問題](https://github.com/Microsoft/AzureStorageExplorer/issues/537)，以了解詳細資訊。
* 如果您使用 VS for Mac，而且曾建立自訂 AAD 設定，則您可能無法登入。 若要解決此問題，請刪除 ~/.IdentityService/AadConfigurations 的內容。 如果這麼做無法將您解除封鎖，請對[此問題](https://github.com/Microsoft/AzureStorageExplorer/issues/97)提出意見。
* Azurite 尚未完全實作所有的儲存體 API。 因此，對於開發儲存體使用 Azurite 時，可能出現未預期的錯誤或行為。
* 在少數情況下，樹狀焦點可能會固定在快速存取上。 若要取消固定焦點，您可以 [全部重新整理]。
* 由於 NodeJS 中的錯誤，造成無法從您的 OneDrive 資料夾上傳。 已修正該 Bug，但是尚未整合至 Electron。
* 當目標為 Azure Stack，以附加 Blob 方式上傳特定檔案會失敗。
* 按一下工作上的 [取消] 之後，該工作可能需要經過一段時間才會取消。 這是因為我們使用[這裡](https://github.com/Azure/azure-storage-node/issues/317)所述的取消篩選器因應措施。
* 如果您選擇錯誤的 PIN/智慧卡憑證，則必須重新啟動，才能使儲存體總管忘記該決定。
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案及實體的所有其他屬性和中繼資料在重新命名期間都會保留。
* Azure Stack 不支援下列功能，而且嘗試使用這些功能來搭配 Azure Stack 使用時，可能會導致非預期的錯誤：
   * 檔案共用
   * 存取層級
   * 虛刪除
* 儲存體總管使用的 Electron 殼層具有一些 GPU (圖形處理單元) 硬體加速的問題。 如果儲存體總管顯示空白 (空的) 主視窗，您可以嘗試從命令列啟動儲存體總管並透過新增 `--disable-gpu` 切換停用 GPU 加速：

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* 對於 Linux 使用者，您必須安裝 [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)。
* 針對使用 Ubuntu 14.04 的使用者，您必須確定 GCC 已是最新版本，做法是執行下列命令，然後重新啟動電腦即可：

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* 使用 Ubuntu 17.04 的使用者必須安裝 GConf，這可以透過執行下列命令並重新啟動電腦來完成：

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-120"></a>版本 1.2.0
2018 年 12 月 6 日

### <a name="new"></a>新增
* 如果 [儲存體總管] 只有在從一小部分租用戶載入訂用帳戶時發生失敗，系統將會顯示所有已成功載入的訂用帳戶，並特別針對失敗的租用戶顯示錯誤訊息。 [#159](https://github.com/Microsoft/AzureStorageExplorer/issues/159)
* 在 Windows 上，當有更新可用時，您現在可以選擇 [關閉時更新]。 選取此選項時，該項更新的安裝程式將會在您關閉 [儲存體總管] 之後執行。 [#21](https://github.com/Microsoft/AzureStorageExplorer/issues/21)
* 已將 [還原快照集] 新增至檢視檔案共用快照集時的檔案共用編輯器操作功能表。[#131](https://github.com/Microsoft/AzureStorageExplorer/issues/131)
* 現在一律啟用 [清除佇列] 按鈕。[#135](https://github.com/Microsoft/AzureStorageExplorer/issues/135)
* 已重新啟用對登入 ADFS Azure Stack 的支援。 需要 Azure Stack 1804 版或更新版本。 [#150](https://github.com/Microsoft/AzureStorageExplorer/issues/150)

### <a name="fixes"></a>修正
* 當您檢視某個檔案共用的快照集時，如果該檔案共用的名稱是相同儲存體帳戶中另一個檔案共用的前置詞，則系統也會列出該另一個檔案共用的快照集。 已修正此問題。 [#255](https://github.com/Microsoft/AzureStorageExplorer/issues/255)
* 當透過 SAS 連結時，從檔案共用快照集還原檔案會導致發生錯誤。 已修正此問題。 [#211](https://github.com/Microsoft/AzureStorageExplorer/issues/211)
* 當檢視某個 Blob 的快照集時，如果已選取基底 Blob 和單一快照集，就會啟用 [升階快照集] 動作。 現在只有在選取單一快照集的情況下，才會啟用此動作。 [#230](https://github.com/Microsoft/AzureStorageExplorer/issues/230)
* 如果啟動單一作業 (例如下載 Blob) 而後發生失敗，將不會自動重試，直到您啟動另一個相同類型的作業為止。 所有作業現在應該都會自動重試，不論您已將多少作業排入佇列。
* 針對在 GPV2 和「Blob 儲存體」帳戶中新建立的 Blob 容器，所開啟的編輯器沒有 [存取層] 資料行。 已修正此問題。 [#109](https://github.com/Microsoft/AzureStorageExplorer/issues/109)
* 透過 SAS 連結儲存體帳戶或 Blob 容器時，有時不會顯示 [存取層] 資料行。 現在一律會顯示此資料行，但如果未設定任何「存取層」，則值會空白。 [#160](https://github.com/Microsoft/AzureStorageExplorer/issues/160)
* 已停用為新上傳的區塊 Blob 設定「存取層」的功能。 已修正此問題。 [#171](https://github.com/Microsoft/AzureStorageExplorer/issues/171)
* 使用鍵盤來叫用 [索引標籤保持開啟狀態] 按鈕時，會失去鍵盤焦點。 現在，焦點會移至保持開啟的索引標籤。 [#163](https://github.com/Microsoft/AzureStorageExplorer/issues/163)
* 針對「查詢產生器」中的查詢，VoiceOver 不會提供目前運算子的有用描述。 現在已較具描述性。 [#207](https://github.com/Microsoft/AzureStorageExplorer/issues/207)
* 各種編輯器的分頁連結不具描述性。 現在已變更為較具描述性。 [#205](https://github.com/Microsoft/AzureStorageExplorer/issues/205)
* 在 [新增實體] 對話方塊中，VoiceOver 不會宣告輸入元素所屬的資料行。 目前資料行的名稱現在已包含在元素的描述中。 [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* 當焦點移至選項按鈕和核取方塊時，兩者沒有可見的框線。 已修正此問題。 [#237](https://github.com/Microsoft/AzureStorageExplorer/issues/237)

### <a name="known-issues"></a>已知問題
* 使用模擬器 (例如「Azure 儲存體模擬器」或 Azurite) 時，您將必須讓它們在其預設連接埠上接聽連線。 否則，「儲存體總管」將無法連線至這些模擬器。
* 如果您使用 VS for Mac，而且曾建立自訂 AAD 設定，則您可能無法登入。 若要解決此問題，請刪除 ~/.IdentityService/AadConfigurations 的內容。 如果這麼做無法將您解除封鎖，請對[此問題](https://github.com/Microsoft/AzureStorageExplorer/issues/97)提出意見。
* Azurite 尚未完全實作所有的儲存體 API。 因此，對於開發儲存體使用 Azurite 時，可能出現未預期的錯誤或行為。
* 在少數情況下，樹狀焦點可能會固定在快速存取上。 若要取消固定焦點，您可以 [全部重新整理]。
* 由於 NodeJS 中的錯誤，造成無法從您的 OneDrive 資料夾上傳。 已修正該 Bug，但是尚未整合至 Electron。
* 當目標為 Azure Stack，以附加 Blob 方式上傳特定檔案會失敗。
* 按一下工作上的 [取消] 之後，該工作可能需要經過一段時間才會取消。 這是因為我們使用[這裡](https://github.com/Azure/azure-storage-node/issues/317)所述的取消篩選器因應措施。
* 如果您選擇錯誤的 PIN/智慧卡憑證，則必須重新啟動，才能使儲存體總管忘記該決定。
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案及實體的所有其他屬性和中繼資料在重新命名期間都會保留。
* 雖然 Azure Stack 目前並不支援檔案共用，檔案共用節點仍然會出現在附加的 Azure Stack 儲存體帳戶之下。
* 儲存體總管使用的 Electron 殼層具有一些 GPU (圖形處理單元) 硬體加速的問題。 如果儲存體總管顯示空白 (空的) 主視窗，您可以嘗試從命令列啟動儲存體總管並透過新增 `--disable-gpu` 切換停用 GPU 加速：

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* 對於 Linux 使用者，您必須安裝 [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)。
* 針對使用 Ubuntu 14.04 的使用者，您必須確定 GCC 已是最新版本，做法是執行下列命令，然後重新啟動電腦即可：

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* 使用 Ubuntu 17.04 的使用者必須安裝 GConf，這可以透過執行下列命令並重新啟動電腦來完成：

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-110"></a>1.1.0 版
05/09/2018

### <a name="new"></a>新增
* 儲存體總管現在支援使用 Azurite。 注意：Azurite 的連線是硬式編碼至預設開發端點。
* 儲存體總管現在支援僅限 Blob 和 GPV2 儲存體帳戶的存取層。 在[此處](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)深入了解存取層。
* 產生 SAS 時，不再需要開始時間。

### <a name="fixes"></a>修正
* 美國政府帳戶的訂用帳戶擷取已中斷。 已修正此問題。 [#61](https://github.com/Microsoft/AzureStorageExplorer/issues/61)
* 未正確儲存存取原則的到期時間。 已修正此問題。 [#50](https://github.com/Microsoft/AzureStorageExplorer/issues/50)
* 在容器中產生項目的 SAS URL 時，項目的名稱不會附加至 URL。 已修正此問題。 [#44](https://github.com/Microsoft/AzureStorageExplorer/issues/44)
* 建立 SAS 時，過去的到期時間有時會成為預設值。 這是因為儲存體總管使用上次使用的開始和到期時間作為預設值。 現在，每當您開啟 SAS 對話方塊時，都會產生一組新的預設值。 [#35](https://github.com/Microsoft/AzureStorageExplorer/issues/35)
* 在儲存體帳戶之間進行複製時，則會產生 24 小時制 SAS。 如果複製持續 24 小時以上，複製會失敗。 我們已增加 SAS 持續 1 星期，以減少複製由於過期的 SAS 而失敗的機會。 [#62](https://github.com/Microsoft/AzureStorageExplorer/issues/62)
* 對於某些活動，按一下 [取消] 不一定有作用。 已修正此問題。 [#125](https://github.com/Microsoft/AzureStorageExplorer/issues/125)
* 對於某些活動，傳輸速度不正確。 已修正此問題。 [#124](https://github.com/Microsoft/AzureStorageExplorer/issues/124)
* [檢視] 功能表中的 [上一步] 拼字不正確。 現在拼字正確無誤。 [#71](https://github.com/Microsoft/AzureStorageExplorer/issues/71)
* Windows 安裝程式的最後一頁有 [下一步] 按鈕。 已變更為 [完成] 按鈕。 [#70](https://github.com/Microsoft/AzureStorageExplorer/issues/70)
* 使用 HC 黑色佈景主題時，對話方塊的按鈕不會出現索引標籤焦點。 現在會出現。[#64](https://github.com/Microsoft/AzureStorageExplorer/issues/64)
* 活動記錄檔中的動作 [自動解決] 大小寫不正確。 現在正確。 [#51](https://github.com/Microsoft/AzureStorageExplorer/issues/51)
* 從資料表刪除實體時，要求確認的對話方塊會顯示錯誤圖示。 對話方塊現在使用警告圖示。 [#148](https://github.com/Microsoft/AzureStorageExplorer/issues/148)

### <a name="known-issues"></a>已知問題
* 如果您使用 VS for Mac，而且曾建立自訂 AAD 設定，則您可能無法登入。 若要解決此問題，請刪除 ~/.IdentityService/AadConfigurations 的內容。 如果這麼做無法將您解除封鎖，請對[此問題](https://github.com/Microsoft/AzureStorageExplorer/issues/97)提出意見。
* Azurite 尚未完全實作所有的儲存體 API。 因此，對於開發儲存體使用 Azurite 時，可能出現未預期的錯誤或行為。
* 在少數情況下，樹狀焦點可能會固定在快速存取上。 若要取消固定焦點，您可以 [全部重新整理]。
* 由於 NodeJS 中的錯誤，造成無法從您的 OneDrive 資料夾上傳。 已修正該 Bug，但是尚未整合至 Electron。
* 當目標為 Azure Stack，以附加 Blob 方式上傳特定檔案會失敗。
* 按一下工作上的 [取消] 之後，該工作可能需要經過一段時間才會取消。 這是因為我們使用[這裡](https://github.com/Azure/azure-storage-node/issues/317)所述的取消篩選器因應措施。
* 如果您選擇錯誤的 PIN/智慧卡憑證，則必須重新啟動，才能使儲存體總管忘記該決定。
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案及實體的所有其他屬性和中繼資料在重新命名期間都會保留。
* 雖然 Azure Stack 目前並不支援檔案共用，檔案共用節點仍然會出現在附加的 Azure Stack 儲存體帳戶之下。
* 儲存體總管使用的 Electron 殼層具有一些 GPU (圖形處理單元) 硬體加速的問題。 如果儲存體總管顯示空白 (空的) 主視窗，您可以嘗試從命令列啟動儲存體總管並透過新增 `--disable-gpu` 切換停用 GPU 加速：

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* 對於 Linux 使用者，您必須安裝 [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)。
* 針對使用 Ubuntu 14.04 的使用者，您必須確定 GCC 已是最新版本，做法是執行下列命令，然後重新啟動電腦即可：

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* 使用 Ubuntu 17.04 的使用者必須安裝 GConf，這可以透過執行下列命令並重新啟動電腦來完成：

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-100"></a>1\.0.0 版
04/16/2018

### <a name="new"></a>新增
* 增強式驗證，可讓儲存體總管使用與 Visual Studio 2017 相同的帳戶存放區。 若要使用這項功能，您必須重新登入您的帳戶，並重新設定已篩選的訂用帳戶。
* 對於 AAD 所支援的 Azure Stack 帳戶，如果已啟用 [目標 Azure Stack]，則儲存體總管現在會擷取 Azure Stack 訂用帳戶。 您不再需要建立自訂登入環境。
* 已新增數個捷徑，以便進行更快速的導覽。 這些包括切換各種面板並在編輯器之間移動。 如需詳細資訊，請參閱 [檢視] 功能表。
* GitHub 上現在提供儲存體總管意見反應。 按一下左下方的 [意見反應] 按鈕或前往，即可觸達我們的問題頁面 [https://github.com/Microsoft/AzureStorageExplorer/issues](https://github.com/Microsoft/AzureStorageExplorer/issues) 。 請隨意提出建議、回報問題、詢問問題，或留下任何其他形式的意見反應。
* 如果您遇到 TLS/SSL 憑證問題，而且找不到有問題的憑證，您現在可以從命令列使用旗標啟動儲存體總管 `--ignore-certificate-errors` 。 使用此旗標啟動時，儲存體總管將會忽略 TLS/SSL 憑證錯誤。
* Blob 和檔案項目的內容功能表中現在有 [下載] 選項。
* 已改善協助工具和畫面讀取器支援。 如果您需依賴協助工具功能，請參閱[協助工具文件](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-accessibility)以取得詳細資訊。
* 儲存體總管現在使用 Electron 1.8.3

### <a name="breaking-changes"></a>重大變更
* 儲存體總管已切換到新的驗證程式庫。 切換到此程式庫時，您必須重新登入您的帳戶，並重新設定已篩選的訂用帳戶。
* 用來加密敏感性資料的方法已變更。 這可能會導致必須重新新增部分的快速存取項目及/或必須重新連結部分已連結的資源。

### <a name="fixes"></a>修正
* 位於 Proxy 後方的某些使用者會有因為「無法解析」錯誤訊息而中斷的群組 blob 上傳或下載。 已修正此問題。
* 如果使用直接連結時需要登入，按一下 [登入] 提示時會顯示空白的對話方塊。 已修正此問題。
* 在 Linux 上，如果儲存體總管因為 GPU 程序損毀而無法啟動，您現在會獲知損毀、得知要使用 '--disable-gpu' 參數，而儲存體總管會接著在已啟用此參數的情況下自動重新啟動。
* 在 [存取原則] 對話方塊中難以識別無效的存取原則。 無效的存取原則識別碼現在會以紅色標示，讓您更容易辨識。
* 活動記錄有時在活動的不同部分之間會有大量空白區域。 已修正此問題。
* 在表格查詢編輯器中，如果您讓時間戳記子句處於無效狀態，然後嘗試修改另一個子句，則編輯器會凍結。 如果偵測到另一個子句變更，編輯器現在會將時間戳記子句還原到其前次有效的狀態。
* 如果您在樹狀檢視中輸入搜尋查詢時暫停，則搜尋會開始並且從文字方塊竊取焦點。 現在，您必須按 'Enter' 鍵，或按一下 [開始搜尋] 按鈕，明確地開始搜尋。
* 在檔案共用中的檔案上按一下滑鼠右鍵時，有時會停用 [取得共用存取簽章] 命令。 已修正此問題。
* 如果在搜尋期間已篩選掉具有焦點的資源樹狀節點，您就無法用 Tab 鍵移入資源樹狀結構並使用方向鍵來瀏覽資源樹狀結構。 現在，如果取得焦點的資源樹狀節點隱藏起來，則資源樹狀結構中的第一個節點會自動取得焦點。
* 在編輯器工具列中有時可看到額外的分隔符號。 已修正此問題。
* 階層連結文字方塊有時會溢位。 已修正此問題。
* 一次上傳許多檔案時，有時會經常重新整理 Blob 和檔案共用編輯器。 已修正此問題。
* 在檔案共用快照集管理檢視中，[資料夾統計資料] 功能沒有任何用途。 它現在已停用。
* 在 Linux 上，[檔案] 功能表不會出現。 已修正此問題。
* 將資料夾上傳至檔案共用時，預設只會上傳資料夾的內容。 現在，預設行為是將資料夾的內容上傳到檔案共用中的相符資料夾。
* 數個對話方塊中的按鈕順序已反轉。 已修正此問題。
* 各種安全性相關修正程式。

### <a name="known-issues"></a>已知問題
* 在少數情況下，樹狀焦點可能會固定在快速存取上。 若要取消固定焦點，您可以 [全部重新整理]。
* 當目標為 Azure Stack，以附加 Blob 方式上傳特定檔案會失敗。
* 按一下工作上的 [取消] 之後，該工作可能需要經過一段時間才會取消。 這是因為我們使用這裡所述的取消篩選器因應措施。
* 如果您選擇錯誤的 PIN/智慧卡憑證，則必須重新啟動，才能使儲存體總管忘記該決定。
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案及實體的所有其他屬性和中繼資料在重新命名期間都會保留。
* 雖然 Azure Stack 目前並不支援檔案共用，檔案共用節點仍然會出現在附加的 Azure Stack 儲存體帳戶之下。
* 儲存體總管使用的 Electron 殼層具有一些 GPU (圖形處理單元) 硬體加速的問題。 如果儲存體總管顯示空白 (空的) 主視窗，您可以嘗試從命令列啟動儲存體總管並透過新增 `--disable-gpu` 切換停用 GPU 加速：

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* 對於 Linux 使用者，您必須安裝 [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0)。
* 針對使用 Ubuntu 14.04 的使用者，您必須確定 GCC 已是最新版本，做法是執行下列命令，然後重新啟動電腦即可：

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* 使用 Ubuntu 17.04 的使用者必須安裝 GConf，這可以透過執行下列命令並重新啟動電腦來完成：

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-096"></a>0.9.6 版
02/28/2018

### <a name="fixes"></a>修正
* 導致預期的 blob/檔案無法列在編輯器中的問題。 已修正此問題。
* 導致快照集檢視之間的切換無法正確顯示項目的問題。 已修正此問題。

### <a name="known-issues"></a>已知問題
* 儲存體總管不支援 ADFS 帳戶。
* 當目標為 Azure Stack，以附加 Blob 方式上傳特定檔案會失敗。
* 按一下工作上的 [取消] 之後，該工作可能需要經過一段時間才會取消。 這是因為我們使用[這裡](https://github.com/Azure/azure-storage-node/issues/317)所述的取消篩選器因應措施。
* 如果您選擇錯誤的 PIN/智慧卡憑證，則必須重新啟動，才能使儲存體總管忘記該決定。
* 帳戶設定面板可能會提示您需要重新輸入認證以篩選訂用帳戶。
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案及實體的所有其他屬性和中繼資料在重新命名期間都會保留。
* 雖然 Azure Stack 目前並不支援檔案共用，檔案共用節點仍然會出現在附加的 Azure Stack 儲存體帳戶之下。
* 儲存體總管使用的 Electron 殼層具有一些 GPU (圖形處理單元) 硬體加速的問題。 如果儲存體總管顯示空白 (空的) 主視窗，您可以嘗試從命令列啟動儲存體總管並透過新增 `--disable-gpu` 切換停用 GPU 加速：

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* 針對使用 Ubuntu 14.04 的使用者，您必須確定 GCC 已是最新版本，做法是執行下列命令，然後重新啟動電腦即可：

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* 使用 Ubuntu 17.04 的使用者必須安裝 GConf，這可以透過執行下列命令並重新啟動電腦來完成：

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-095"></a>0.9.5 版
02/06/2018

### <a name="new"></a>新增

* 檔案共用快照集的支援：
    * 建立和管理檔案共用的快照集。
    * 在瀏覽時輕易地在檔案共用快照集之間切換檢視。
    * 還原檔案的先前版本。
* Azure Data Lake Store 的預覽支援：
    * 連線到跨多個帳戶的 ADLS 資源。
    * 使用 ADL URI 連線並共用 ADLS 資源。
    * 以遞迴方式執行基本的檔案/資料夾作業。
    * 將個別資料夾釘選到快速存取。
    * 顯示資料夾統計資料。

### <a name="fixes"></a>修正
* 啟動效能改進。
* 多個錯誤修正。

### <a name="known-issues"></a>已知問題
* 儲存體總管不支援 ADFS 帳戶。
* 當目標為 Azure Stack，以附加 Blob 方式上傳特定檔案會失敗。
* 按一下工作上的 [取消] 之後，該工作可能需要經過一段時間才會取消。 這是因為我們使用這裡所述的取消篩選器因應措施。
* 如果您選擇錯誤的 PIN/智慧卡憑證，則必須重新啟動，才能使儲存體總管忘記該決定。
* 帳戶設定面板可能會提示您需要重新輸入認證以篩選訂用帳戶。
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案及實體的所有其他屬性和中繼資料在重新命名期間都會保留。
* 雖然 Azure Stack 目前並不支援檔案共用，檔案共用節點仍然會出現在附加的 Azure Stack 儲存體帳戶之下。
* 儲存體總管使用的 Electron 殼層具有一些 GPU (圖形處理單元) 硬體加速的問題。 如果儲存體總管顯示空白 (空的) 主視窗，您可以嘗試從命令列啟動儲存體總管並透過新增 `--disable-gpu` 切換停用 GPU 加速：

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* 針對使用 Ubuntu 14.04 的使用者，您必須確定 GCC 已是最新版本，做法是執行下列命令，然後重新啟動電腦即可：

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* 使用 Ubuntu 17.04 的使用者必須安裝 GConf，這可以透過執行下列命令並重新啟動電腦來完成：

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-094-and-093"></a>0.9.4 和 0.9.3 版
01/21/2018

### <a name="new"></a>新增
* 在下列情況下，將會重新使用您的現有儲存體總管視窗：
    * 開啟儲存體總管中所產生的直接連結。
    * 從入口網站開啟儲存體總管。
    * 從 Azure 儲存體 VS Code 延伸模組 (即將推出) 開啟儲存體總管。
* 已新增從儲存體總管內開啟新儲存體總管視窗的功能。
    * 針對 Windows，在 [檔案] 功能表下方以及工作列的操作功能表中會有 [開新視窗] 選項。
    * 針對 Mac，[應用程式功能表] 下方會有 [開新視窗] 選項。

### <a name="fixes"></a>修正
* 已修正安全性問題。 請盡早升級至 0.9.4 版。
* 舊的活動不適合進行清理。 這會影響長期執行作業的效能。 現在，會將它們正確地清除。
* 涉及大量檔案和目錄的動作偶而會導致儲存體總管凍結。 現在會針對向 Azure for File Shares 發出的要求進行節流控制，以限制系統資源使用。

### <a name="known-issues"></a>已知問題
* 儲存體總管不支援 ADFS 帳戶。
* 用於「檢視總管」及「檢視帳戶管理」的快速鍵應該分別是 Ctrl/Cmd+Shift+E 和 Ctrl/Cmd+Shift+A。
* 當目標為 Azure Stack，以附加 Blob 方式上傳特定檔案會失敗。
* 按一下工作上的 [取消] 之後，該工作可能需要經過一段時間才會取消。 這是因為我們使用這裡所述的取消篩選器因應措施。
* 如果您選擇錯誤的 PIN/智慧卡憑證，則必須重新啟動，才能使儲存體總管忘記該決定。
* 帳戶設定面板可能會提示您需要重新輸入認證以篩選訂用帳戶。
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案及實體的所有其他屬性和中繼資料在重新命名期間都會保留。
* 雖然 Azure Stack 目前並不支援檔案共用，檔案共用節點仍然會出現在附加的 Azure Stack 儲存體帳戶之下。
* 儲存體總管使用的 Electron 殼層具有一些 GPU (圖形處理單元) 硬體加速的問題。 如果儲存體總管顯示空白 (空的) 主視窗，您可以嘗試從命令列啟動儲存體總管並透過新增 `--disable-gpu` 切換停用 GPU 加速：

    ```
    ./StorageExplorer --disable-gpu
    ```

* 針對使用 Ubuntu 14.04 的使用者，您必須確定 GCC 已是最新版本，做法是執行下列命令，然後重新啟動電腦即可：

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* 使用 Ubuntu 17.04 的使用者必須安裝 GConf，這可以透過執行下列命令並重新啟動電腦來完成：

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-092"></a>0.9.2 版
11/01/2017

### <a name="hotfixes"></a>Hotfix
* 端視當地時區而定，編輯資料表實體的 Edm.DateTime 值時，可能出現未預期的資料變更。 編輯器目前使用純文字方塊，對於 Edm.DateTime 值提供精確、一致的控制。
* 無法在連接到名稱和金鑰時開始上傳/下載一組 Blob。 已修正此問題。
* 在過去，如果已選取一或多個帳戶的訂用帳戶，儲存體總管只會提示您重新驗證過期的帳戶。 而現在，即使完全篩選排除帳戶，儲存體總管也會提示您。
* Azure 美國政府的端點網域不正確。 已修正此問題。
* [管理帳戶] 面板的 [套用] 按鈕有時不容易按。 這應該不會再發生。

### <a name="new"></a>新增
* Azure Cosmos DB 預覽支援：
    * [線上檔](./cosmos-db/storage-explorer.md)
    * 建立資料庫和集合
    * 操作資料
    * 查詢、建立或刪除文件
    * 更新預存程序、使用者定義函式或觸發程序
    * 使用連接字串來連接和管理您的資料庫
* 改善上傳/下載許多小 Blob 的效能。
* 新增 Blob 上傳群組或 Blob 下載群組發生失敗時的「全部重試」動作。
* 如果儲存體總管偵測到您的網路連線已遺失，現在將在 Blob 上傳/下載期間暫停反覆項目。 然後在網路連線重新建立後，您可以繼續反覆項目。
* 新增透過捷徑功能表「全部關閉」、「關閉其他」和「關閉」索引標籤的能力。
* 儲存體總管現在會使用原生對話方塊和原生捷徑功能表。
* 儲存體總管現在更容易存取。 增強功能包括：
    * 改善的螢幕讀取器支援，Windows 上的 NVDA，以及 Mac 上的 VoiceOver
    * 改良的高對比佈景主題
    * 修正鍵盤定位停駐點和鍵盤焦點

### <a name="fixes"></a>修正
* 如果您嘗試使用無效的 Windows 檔案名稱開啟或下載 Blob，作業會失敗。 儲存體總管現在會偵測 Blob 名稱是否無效，並詢問您是否要加以編碼或略過該 Blob。 儲存體總管也會偵測檔案名稱是否已經過編碼，並詢問您是否要在上傳之前將它解碼。
* Blob 上傳期間，目標 Blob 容器的編輯器會有時不正確地重新整理。 已修正此問題。
* 數種形式的連接字串和 SAS URI 支援已回歸。 我們已經處理所有已知的問題，但如果您遇到其他問題，請傳送意見反應。
* 0.9.0 中有些使用者的更新通知已中斷。 已修正此問題，以及對於受此錯誤影響的使用者，可以在[這裡](https://azure.microsoft.com/features/storage-explorer/)手動下載最新版的儲存體總管。

### <a name="known-issues"></a>已知問題
* 儲存體總管不支援 ADFS 帳戶。
* 用於「檢視總管」及「檢視帳戶管理」的快速鍵應該分別是 Ctrl/Cmd+Shift+E 和 Ctrl/Cmd+Shift+A。
* 當目標為 Azure Stack，以附加 Blob 方式上傳特定檔案會失敗。
* 按一下工作上的 [取消] 之後，該工作可能需要經過一段時間才會取消。 這是因為我們使用這裡所述的取消篩選器因應措施。
* 如果您選擇錯誤的 PIN/智慧卡憑證，則必須重新啟動，才能使儲存體總管忘記該決定。
* 帳戶設定面板可能會提示您需要重新輸入認證以篩選訂用帳戶。
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案及實體的所有其他屬性和中繼資料在重新命名期間都會保留。
* 雖然 Azure Stack 目前並不支援檔案共用，檔案共用節點仍然會出現在附加的 Azure Stack 儲存體帳戶之下。
* 儲存體總管使用的 Electron 殼層具有一些 GPU (圖形處理單元) 硬體加速的問題。 如果儲存體總管顯示空白 (空的) 主視窗，您可以嘗試從命令列啟動儲存體總管並透過新增 `--disable-gpu` 切換停用 GPU 加速：

    ```
    ./StorageExplorer --disable-gpu
    ```

* 針對使用 Ubuntu 14.04 的使用者，您必須確定 GCC 已是最新版本，做法是執行下列命令，然後重新啟動電腦即可：

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* 使用 Ubuntu 17.04 的使用者必須安裝 GConf，這可以透過執行下列命令並重新啟動電腦來完成：

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-091-and-090"></a>0.9.1 和 0.9.0 版
10/20/2017
### <a name="new"></a>新增
* Azure Cosmos DB 預覽支援：
    * [線上檔](./cosmos-db/storage-explorer.md)
    * 建立資料庫和集合
    * 操作資料
    * 查詢、建立或刪除文件
    * 更新預存程序、使用者定義函式或觸發程序
    * 使用連接字串來連接和管理您的資料庫
* 改善上傳/下載許多小 Blob 的效能。
* 新增 Blob 上傳群組或 Blob 下載群組發生失敗時的「全部重試」動作。
* 如果儲存體總管偵測到您的網路連線已遺失，現在將在 Blob 上傳/下載期間暫停反覆項目。 然後在網路連線重新建立後，您可以繼續反覆項目。
* 新增透過捷徑功能表「全部關閉」、「關閉其他」和「關閉」索引標籤的能力。
* 儲存體總管現在會使用原生對話方塊和原生捷徑功能表。
* 儲存體總管現在更容易存取。 增強功能包括：
    * 改善的螢幕讀取器支援，Windows 上的 NVDA，以及 Mac 上的 VoiceOver
    * 改良的高對比佈景主題
    * 修正鍵盤定位停駐點和鍵盤焦點

### <a name="fixes"></a>修正
* 如果您嘗試使用無效的 Windows 檔案名稱開啟或下載 Blob，作業會失敗。 儲存體總管現在會偵測 Blob 名稱是否無效，並詢問您是否要加以編碼或略過該 Blob。 儲存體總管也會偵測檔案名稱是否已經過編碼，並詢問您是否要在上傳之前將它解碼。
* Blob 上傳期間，目標 Blob 容器的編輯器會有時不正確地重新整理。 已修正此問題。
* 數種形式的連接字串和 SAS URI 支援已回歸。 我們已經處理所有已知的問題，但如果您遇到其他問題，請傳送意見反應。
* 0.9.0 中有些使用者的更新通知已中斷。 已修正此問題，而且對於受到 bug 影響的使用者，您可以在[這裡](https://azure.microsoft.com/features/storage-explorer/)手動下載最新版本的儲存體總管

### <a name="known-issues"></a>已知問題
* 儲存體總管不支援 ADFS 帳戶。
* 用於「檢視總管」及「檢視帳戶管理」的快速鍵應該分別是 Ctrl/Cmd+Shift+E 和 Ctrl/Cmd+Shift+A。
* 當目標為 Azure Stack，以附加 Blob 方式上傳特定檔案會失敗。
* 按一下工作上的 [取消] 之後，該工作可能需要經過一段時間才會取消。 這是因為我們使用這裡所述的取消篩選器因應措施。
* 如果您選擇錯誤的 PIN/智慧卡憑證，則必須重新啟動，才能使儲存體總管忘記該決定。
* 帳戶設定面板可能會提示您需要重新輸入認證以篩選訂用帳戶。
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案及實體的所有其他屬性和中繼資料在重新命名期間都會保留。
* 雖然 Azure Stack 目前並不支援檔案共用，檔案共用節點仍然會出現在附加的 Azure Stack 儲存體帳戶之下。
* 儲存體總管使用的 Electron 殼層具有一些 GPU (圖形處理單元) 硬體加速的問題。 如果儲存體總管顯示空白 (空的) 主視窗，您可以嘗試從命令列啟動儲存體總管並透過新增 `--disable-gpu` 切換停用 GPU 加速：

    ```
    ./StorageExplorer --disable-gpu
    ```

* 針對使用 Ubuntu 14.04 的使用者，您必須確定 GCC 已是最新版本，做法是執行下列命令，然後重新啟動電腦即可：

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* 使用 Ubuntu 17.04 的使用者必須安裝 GConf，這可以透過執行下列命令並重新啟動電腦來完成：

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>0.8.16 版
8/21/2017

### <a name="new"></a>新增
* 您開啟 blob 時，如果偵測到變更，儲存體總管將提示您上傳已下載的檔案
* 增強的 Azure Stack 登入體驗
* 改善同時上傳/下載許多小檔案的效能


### <a name="fixes"></a>修正
* 對於某些 blob 類型，在上傳衝突時選擇取代有時會導致重新啟動上傳。
* 在 0.8.15 版中，上傳有時會延滯在 99%。
* 將檔案上傳到檔案共用時，如果您選擇上傳到尚未存在的目錄，上傳會失敗。
* 儲存體總管未正確產生共用存取簽章和資料表查詢的時間戳記。


### <a name="known-issues"></a>已知問題
* 目前無法使用名稱和金鑰連接字串。 這將在下一版中予以修正。 在此之前，您可以使用名稱和金鑰附加。
* 如果您嘗試開啟無效 Windows 檔案名稱的檔案，下載會導致找不到檔案的錯誤。
* 按一下工作上的 [取消] 之後，該工作可能需要經過一段時間才會取消。 此為 Microsoft Azure 儲存體節點程式庫的限制。
* 完成 Blob 上傳之後，系統會重新整理起始該上傳的索引標籤。 這和先前的行為不同，且會導致您返回所在容器的根。
* 如果您選擇錯誤的 PIN/智慧卡憑證，則必須重新啟動，才能使儲存體總管忘記該決定。
* 帳戶設定面板可能會提示您需要重新輸入認證以篩選訂用帳戶。
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案及實體的所有其他屬性和中繼資料在重新命名期間都會保留。
* 雖然 Azure Stack 目前並不支援檔案共用，檔案共用節點仍然會出現在附加的 Azure Stack 儲存體帳戶之下。
* 針對使用 Ubuntu 14.04 的使用者，您必須確定 GCC 已是最新版本，做法是執行下列命令，然後重新啟動電腦即可：

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* 使用 Ubuntu 17.04 的使用者必須安裝 GConf，這可以透過執行下列命令並重新啟動電腦來完成：

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>版本 0.8.14
06/22/2017

### <a name="new"></a>新增

* 將 Electron 版本更新至 1.7.2 以利用數個重要安全性更新
* 現已可從 [說明] 功能表快速存取線上疑難排解指南
* 儲存體總管疑難排解[指南][2]
* 連線至 Azure Stack 訂用帳戶的[指示][3]

### <a name="known-issues"></a>已知問題

* 在 Linux 上，刪除資料夾確認對話方塊上的按鈕不會註冊滑鼠點選。 因應措施為使用 Enter 鍵
* 如果您選擇錯誤的 PIN/智慧卡憑證，則必須重新啟動，才能使儲存體總管忘記該決定
* 同時上傳超過 3 個 Blob 或檔案群組可能會造成錯誤
* 帳戶設定面板可能會提示您需要重新輸入認證以篩選訂用帳戶
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案及實體的所有其他屬性和中繼資料在重新命名期間都會保留。
* 雖然 Azure Stack 目前並不支援檔案共用，檔案共用節點仍然會出現在附加的 Azure Stack 儲存體帳戶之下。
* Ubuntu 14.04 安裝需要更新或升級 gcc 版本，升級步驟如下：

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>0.8.13 版
05/12/2017

#### <a name="new"></a>新增

* 儲存體總管疑難排解[指南][2]
* 連線至 Azure Stack 訂用帳戶的[指示][3]

#### <a name="fixes"></a>修正

* 已修正︰檔案上傳先前很容易造成記憶體不足錯誤
* 已修正：您現在可以使用 PIN/智慧卡登入
* 已修正：在入口網站中開啟現在可搭配 Azure 中國的世紀、Azure 德國、Azure 美國政府和 Azure Stack
* 已修正：先前將資料夾上傳至 Blob 容器時，有時候會發生「作業不合法」錯誤
* 已修正：先前在管理快照集時會停用 [全選]
* 已修正：在檢視基底 Blob 的快照集屬性後，可能會覆寫該基底 Blob 的中繼資料

#### <a name="known-issues"></a>已知問題

* 如果您選擇錯誤的 PIN/智慧卡憑證，則必須重新啟動，才能使儲存體總管忘記該決定
* 在放大或縮小期間，縮放層級可能會暫時重設為預設層級
* 同時上傳超過 3 個 Blob 或檔案群組可能會造成錯誤
* 帳戶設定面板可能會提示您需要重新輸入認證以篩選訂用帳戶
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案及實體的所有其他屬性和中繼資料在重新命名期間都會保留。
* 雖然 Azure Stack 目前並不支援檔案共用，檔案共用節點仍然會出現在附加的 Azure Stack 儲存體帳戶之下。
* Ubuntu 14.04 安裝需要更新或升級 gcc 版本，升級步驟如下：

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812-and-0811-and-0810"></a>0.8.12、0.8.11 和 0.8.10 版
04/07/2017

#### <a name="new"></a>新增

* 儲存體總管現在會在您從更新通知安裝更新之後自動關閉
* 就地快速存取能針對您經常存取的資源提供加強的使用體驗
* 在 Blob 容器編輯器中，您現在可以查看租用 Blob 所屬的虛擬機器
* 現已能摺疊左側面板
* 探索現在會和下載同時執行
* 使用 Blob 容器、檔案共用及資料表編輯器中的 [統計資料] 來查看您資源或選取項目的大小
* 現已能登入以 Azure Active Directory (AAD) 為基礎的 Azure Stack 帳戶。
* 現已能將大小超過 32MB 的封存檔案上傳至進階儲存體帳戶
* 改善協助工具支援
* 您現在可以前往 [編輯- &gt; Ssl 憑證-匯入憑證]，新增信任的 Base-64 編碼的 X.509 TLS/ssl 憑證。 &gt;

#### <a name="fixes"></a>修正

* 已修正：先前在重新整理帳戶的認證之後，樹狀檢視有時候不會自動重新整理
* 已修正：先前針對模擬器佇列和資料表產生 SAS 會導致無效的 URL
* 已修正：進階儲存體帳戶現在可以在啟用 Proxy 時展開
* 已修正：先前在選取 1 或 0 個帳戶的情況下，帳戶管理頁面的 [套用] 按鈕會無法運作
* 已修正：上傳需要衝突解決的 Blob 可能會失敗 - 已在 0.8.11 中修正
* 已修正：傳送意見反應的功能在 0.8.11 中無法運作 - 已在 0.8.12 中修正

#### <a name="known-issues"></a>已知問題

* 升級至 0.8.10 之後，您必須重新整理所有認證。
* 在放大或縮小期間，縮放層級可能會暫時重設為預設層級。
* 同時上傳超過 3 個 Blob 或檔案群組可能會造成錯誤。
* 帳戶設定面板可能會提示您需要重新輸入認證以篩選訂用帳戶。
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案及實體的所有其他屬性和中繼資料在重新命名期間都會保留。
* 雖然 Azure Stack 目前並不支援檔案共用，檔案共用節點仍然會出現在附加的 Azure Stack 儲存體帳戶之下。
* Ubuntu 14.04 安裝需要更新或升級 gcc 版本，升級步驟如下：

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-089-and-088"></a>0.8.9 和 0.8.8 版
02/23/2017

>[!VIDEO https://www.youtube.com/embed/R6gonK3cYAc?ecver=1]

>[!VIDEO https://www.youtube.com/embed/SrRPCm94mfE?ecver=1]


#### <a name="new"></a>新增

* 儲存體總管 0.8.9 將會自動下載最新版本的更新。
* Hotfix：先前使用由入口網站所產生的 SAS URI 來附加儲存體帳戶將會導致錯誤。
* 現已能針對 Blob 快照集進行建立、管理及升階。
* 您現在已可以登入「Azure 中國 21Vianet」、「Azure 德國」及「Azure 美國政府」帳戶。
* 現已能變更縮放層級。 使用 [檢視] 功能表中的選項來放大、縮小及重設縮放。
* Blob 和檔案的使用者中繼資料現已支援 Unicode 字元。
* 協助工具改善。
* 可從更新通知中檢視下個版本的版本資訊。 您也可以從 [說明] 功能表檢視目前版本的資訊。

#### <a name="fixes"></a>修正

* 已修正：版本號碼現在已會正確顯示於 Windows 上的 [控制台] 中
* 已修正：搜尋已不再具有 50,000 個節點的限制
* 已修正：在目的地目錄不存在的情況下，上傳至檔案共用會卡在無限的迴圈之中
* 已修正：改善較長上傳及下載的穩定性

#### <a name="known-issues"></a>已知問題

* 在放大或縮小期間，縮放層級可能會暫時重設為預設層級。
* 快速存取僅適用於以訂用帳戶為基礎的項目。 此版本不支援透過金鑰或 SAS 權杖附加的本機資源或資源。
* 視您擁有的資源數量而定，快速存取可能需要幾秒鐘才能瀏覽至目標資源。
* 同時上傳超過 3 個 Blob 或檔案群組可能會造成錯誤。

12/16/2016
### <a name="version-087"></a>0.8.7 版

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>新增

* 在剛開始更新、下載或複製工作階段時，您可以在 [活動] 視窗中選擇如何解決衝突
* 將游標暫留在索引標籤上可以看到儲存體資源的完整路徑
* 按一下索引標籤之後，它會與左側導覽窗格中的位置同步

#### <a name="fixes"></a>修正

* 已修正：儲存體總管在 Mac 上現已是受信任的應用程式
* 已修正：再次支援 Ubuntu 14.04
* 已修正：「新增帳戶」UI 在載入訂用帳戶時有時會閃爍
* 已修正：左側導覽窗格中有時不會列出所有儲存體資源
* 已修正：[動作] 窗格有時會顯示空白動作
* 已修正：現已會保留上次工作階段關閉時的視窗大小
* 已修正：可以使用內容選單針對相同資源開啟多個索引標籤

#### <a name="known-issues"></a>已知問題

* 快速存取僅適用於以訂用帳戶為基礎的項目。 此版本不支援透過金鑰或 SAS 權杖附加的本機資源或資源
* 視您擁有的資源數量而定，快速存取可能需要幾秒鐘才能瀏覽至目標資源
* 同時上傳超過 3 個 Blob 或檔案群組可能會造成錯誤
* 搜尋功能在處理超過大約 50,000 個節點的搜尋作業之後，可能會影響效能或造成未處理的例外狀況
* 在 macOS 上第一次使用儲存體總管時，系統可能會多次要求使用者的許可，以存取鑰匙圈。 建議您選取 [一律允許] 來使系統不會再次顯示該提示

11/18/2016
### <a name="version-086"></a>0.8.6 版

#### <a name="new"></a>新增

* 現已可將常用的服務釘選到 Quick Access 以便瀏覽
* 現已可在多個索引標籤開啟多個編輯器。 按一下以開啟暫時索引標籤;按兩下以開啟 [永久] 索引標籤。您也可以按一下 [暫存] 索引標籤，使其成為永久索引標籤
* 我們已大幅改善上傳及下載的效能和穩定性，尤其是在高效能機器上處理大型檔案的情況
* 現可在 Blob 容器內建立空白的「虛擬」資料夾
* 我們已重新推出範圍搜尋，並搭配全新增強的子字串搜尋，因此您現在將會有兩種搜尋的選項：
    * 全域搜尋：請直接在搜尋文字方塊中輸入搜尋字詞
    * 範圍搜尋：按一下位於節點旁邊的放大鏡圖示，然後將搜尋字詞新增至路徑末端，或是以滑鼠右鍵按一下並選取 [從這裡搜尋]
* 已新增多種佈景主題：淺色 (預設)、深色、黑色高對比和白色高對比。 移至 [編輯] -&gt; [佈景主題] 來變更佈景主題喜好設定
* 您可以修改 Blob 和檔案的屬性
* 我們現已支援編碼 (base64) 及未編碼的佇列訊息
* 若使用 Linux，必須為 64 位元作業系統。 針對此版本，我們僅支援 64 位元的 Ubuntu 16.04.1 LTS
* 已更新我們的標誌！

#### <a name="fixes"></a>修正

* 已修正︰畫面凍結的問題
* 已修正：增強安全性
* 已修正：有時候可能會出現重複的附加帳戶
* 已修正：具有未定義內容類型的 Blob 可能會產生例外狀況
* 已修正：先前並無法在空白資料表上開啟 [查詢] 面板
* 已修正：數個搜尋功能中的錯誤
* 已修正：按一下 [載入更多] 時所載入資源的數目已從 50 提升至 100
* 已修正：首次執行時，如果已登入某個帳戶，現在預設會選取該帳戶的所有定用帳戶

#### <a name="known-issues"></a>已知問題

* 此版本的儲存體總管並無法在 Ubuntu 14.04 上執行
* 若要針對相同資源開啟多個索引標籤，請不要持續點選相同的資源。 請先按一下其他資源，然後再返回按一下原始資源，以在另一個索引標籤上再次開啟它
* 快速存取僅適用於以訂用帳戶為基礎的項目。 此版本不支援透過金鑰或 SAS 權杖附加的本機資源或資源
* 視您擁有的資源數量而定，快速存取可能需要幾秒鐘才能瀏覽至目標資源
* 同時上傳超過 3 個 Blob 或檔案群組可能會造成錯誤
* 搜尋功能在處理超過大約 50,000 個節點的搜尋作業之後，可能會影響效能或造成未處理的例外狀況

2016 年 10 月 3 日
### <a name="version-085"></a>0.8.5 版

#### <a name="new"></a>新增

* 現已能使用由入口網站所產生的 SAS 金鑰來附加儲存體帳戶和資源

#### <a name="fixes"></a>修正

* 已修正：先前在搜尋期間的競爭條件有時候會使節點無法展開
* 已修正：在以帳戶名稱和金鑰連線至儲存體帳戶時，[使用 HTTP] 會無法運作
* 已修正：SAS 金鑰 (特別是由入口網站所產生的 SAS 金鑰) 會傳回「結尾斜線」錯誤
* 已修正：資料表匯入問題
    * 資料分割索引鍵和列索引鍵有時候會反轉
    * 無法讀取 "null" 資料分割索引鍵

#### <a name="known-issues"></a>已知問題

* 搜尋功能在處理超過大約 50,000 個節點的搜尋作業之後，可能會影響效能
* Azure Stack 目前不支援 [檔案]，因此嘗試展開 [檔案] 將會顯示錯誤

09/12/2016
### <a name="version-084"></a>0.8.4 版

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>新增

* 產生針對儲存體帳戶、容器、佇列、資料表或檔案共用的直接連結，以便共用及輕鬆存取資源 - Windows 與 Mac 作業系統支援
* 從搜尋方塊搜尋 Blob 容器、資料表、佇列、檔案共用或儲存體帳戶
* 現已能在資料表查詢建立器中對子句進行群組
* 從 SAS 附加帳戶及容器內對 Blob 容器、檔案共用、資料表、Blob、Blob 資料夾、檔案及目錄進行重新命名和複製/貼上
* 對 Blob 容器和檔案共用進行重新命名和複製時，現在會保留屬性和中繼資料

#### <a name="fixes"></a>修正

* 已修正：無法編輯包含布林值或二進位屬性的資料表實體

#### <a name="known-issues"></a>已知問題

* 搜尋功能在處理超過大約 50,000 個節點的搜尋作業之後，可能會影響效能

08/03/2016
### <a name="version-083"></a>0.8.3 版

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>新增

* 對容器、資料表、檔案共用進行重新命名
* 改善查詢建立器體驗
* 能夠儲存及載入查詢
* 針對儲存體帳戶或容器、佇列、資料表或檔案共用的直接連結，可用來進行共用及輕鬆存取資源 (僅限 Windows，macOS 支援即將推出！)
* 能夠管理及設定 CORS 規則

#### <a name="fixes"></a>修正

* 已修正：Microsoft 帳戶每 8 至 12 小時便需要重新驗證

#### <a name="known-issues"></a>已知問題

* UI 有時候會呈現為凍結的情況，將視窗最大化能協助解決此問題
* macOS 安裝可能會需要較高的權限
* 帳戶設定面板可能會提示您需要重新輸入認證以篩選訂用帳戶
* 對檔案共用、Blob 容器及資料表進行重新命名，並不會保留中繼資料或容器上的其他屬性，例如檔案共用配額、公用存取層級或存取原則
* 重新命名 Blob (個別執行或在重新命名的 Blob 容器內) 不會保留快照集。 Blob、檔案和實體的所有其他屬性和中繼資料都會在重新命名期間保留
* 在 SAS 附加帳戶內並無法對資源進行複製或重新命名

07/07/2016
### <a name="version-082"></a>0.8.2 版

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>新增

* 儲存體帳戶是依訂用帳戶進行群組。透過金鑰或 SAS 附加的開發儲存體和資源會顯示在 (本機和附加的) 節點之下
* 在 [Azure 帳戶設定] 面板中登出帳戶
* 設定 Proxy 設定以啟用並管理登入
* 建立和中斷 Blob 租用
* 按一下便能開啟 Blob 容器、佇列、資料表及檔案

#### <a name="fixes"></a>修正

* 已修正：使用 .NET 或 Java 程式庫插入的佇列訊息沒有從 base64 正確解碼
* 已修正：$metrics 資料表不會針對 Blob 儲存體帳戶顯示
* 已修正：資料表節點無法用於本機 (開發) 儲存體

#### <a name="known-issues"></a>已知問題

* macOS 安裝可能會需要較高的權限

06/15/2016
### <a name="version-080"></a>0.8.0 版

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>新增

* 檔案共用支援：檢視、上傳、下載、複製檔案和目錄、SAS URI (建立和連線)
* 改善透過 SAS URI 或帳戶金鑰連線至儲存體的使用者體驗
* 匯出資料表查詢結果
* 針對資料表資料行的重新排列和自訂
* 搭配啟用的計量檢視儲存體帳戶的 $logs Blob 容器和 $metrics 資料表
* 改善匯出和匯入行為，現已包含屬性值類型

#### <a name="fixes"></a>修正

* 已修正：上傳或下載大型 Blob 可能會導致不完整的上傳/下載
* 已修正：編輯、新增或匯入具有數值字串值 ("1") 的實體會將它轉換為 double
* 已修正：無法在本機開發環境中展開資料表節點

#### <a name="known-issues"></a>已知問題

* $metrics 資料表不會針對 Blob 儲存體帳戶顯示
* 如果以程式設計方式新增的佇列訊息是使用 Base64 編碼方式進行編碼，則該訊息可能會無法正確顯示

05/17/2016
### <a name="version-07201605090"></a>0.7.20160509.0 版

#### <a name="new"></a>新增

* 改善應用程式當機的錯誤處理

#### <a name="fixes"></a>修正

* 已修正在需要登入認證時，InfoBar 訊息有時候不會顯示的錯誤

#### <a name="known-issues"></a>已知問題

* 資料表：新增、編輯或匯入具有不明確數值 (例如 "1" 或 "1.0") 的實體，且使用者嘗試將它傳送為 `Edm.String` 時，該值將會透過用戶端 API 以 Edm.Double 的形式傳回

03/31/2016

### <a name="version-07201603250"></a>0.7.20160325.0 版

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>新增

* 資料表支援：針對實體的檢視、查詢、匯出、匯入及 CRUD 作業
* 佇列支援：針對訊息進行檢視、新增、清除佇列
* 針對儲存體帳戶產生 SAS URI
* 透過 SAS URI 連線至儲存體帳戶
* 針對儲存體總管未來更新的更新通知
* 更新外觀與風格

#### <a name="fixes"></a>修正

* 改善效能和可靠性

### <a name="known-issues-amp-mitigations"></a>已知問題 &amp; 緩解方式

* 下載大型 Blob 檔案並無法正確運作。在我們解決此問題的期間，建議您使用 AzCopy
* 如果找不到主資料夾，或是無法寫入該資料夾，則不會擷取或快取帳戶認證
* 在新增、編輯或匯入具有不明確數值 (例如 "1" 或 "1.0") 的實體，且使用者嘗試將它傳送為 `Edm.String` 時，該值將會透過用戶端 API 以 Edm.Double 的形式傳回
* 匯入具有多行記錄的 CSV 檔案時，資料可能會變得破碎或變成亂碼

02/03/2016

### <a name="version-07201601291"></a>0.7.20160129.1 版

#### <a name="fixes"></a>修正

* 改善上傳、下載及複製 Blob 的整體效能

01/14/2016

### <a name="version-07201601050"></a>0.7.20160105.0 版

#### <a name="new"></a>新增

* Linux 支援 (和 OSX 相同的功能)
* 新增具有共用存取簽章 (SAS) 金鑰的 Blob 容器
* 新增 Azure 中國 21Vianet 的儲存體帳戶
* 新增具有自訂端點的儲存體帳戶
* 開啟及檢視內容文字和圖片 Blob
* 檢視及編輯 Blob 屬性和中繼資料

#### <a name="fixes"></a>修正

* 已修正：上傳或下載大量的 Blob (超過 500 個) 時，有時候可能會導致應用程式出現白色畫面
* 已修正：設定 Blob 容器公用存取層級時，在您重新將焦點設定在容器上之前，新的值將不會更新。 此外，對話方塊預設一律會顯示「無公用存取」，而非目前實際的值。
* 更佳的整體鍵盤/協助工具及 UI 支援
* 階層連結記錄在因大量空白字元而變得過長時便會自動換行
* SAS 對話方塊支援輸入驗證
* 本機存放區在使用者認證過期後仍會持續可用
* 刪除已開啟的 Blob 容器時，便會關閉位於右側的 Blob 總管

#### <a name="known-issues"></a>已知問題

* Linux 安裝需要更新或升級 gcc 版本，升級步驟如下：
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

11/18/2015
### <a name="version-07201511160"></a>0.7.20151116.0 版

#### <a name="new"></a>新增

* macOS 及 Windows 版本
* 使用您的「組織帳戶」、「Microsoft 帳戶」、2FA 等來登入以檢視「儲存體帳戶」。
* 本機開發儲存體 (使用儲存體模擬器，僅限 Windows)
* Azure Resource Manager 和傳統資源支援
* 建立及刪除 Blob、佇列或資料表
* 搜尋特定的 Blob、佇列或資料表
* 瀏覽 Blob 容器的內容
* 檢視並瀏覽目錄
* 上傳、下載及刪除 Blob 和資料夾
* 檢視及編輯 Blob 屬性和中繼資料
* 產生 SAS 金鑰
* 管理及建立儲存的存取原則 (SAP)
* 使用前置詞搜尋 Blob
* 拖放檔案以上傳或下載

#### <a name="known-issues"></a>已知問題

* 設定 Blob 容器公用存取層級時，在您重新將焦點設定在容器上之前，新的值將不會更新
* 當您開啟對話方塊以設定公用存取層級時，該對話方塊預設一律會顯示「無公用存取」，而非目前實際的值
* 無法對已下載的 Blob 進行重新命名
* 發生錯誤時，活動記錄項目有時候會卡在進行中的狀態，且該錯誤不會顯示
* 嘗試上傳或下載大量的 Blob 時，有時候會當機或是出現白色畫面
* 取消複製作業有時候會無法運作
* 在建立容器 (Blob/佇列/資料表) 的期間，如果您輸入無效的名稱，並繼續以不同的容器類型建立另一個容器，將無法把焦點設定在新的類型上
* 無法建立新資料夾或對資料夾進行重新命名




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md
