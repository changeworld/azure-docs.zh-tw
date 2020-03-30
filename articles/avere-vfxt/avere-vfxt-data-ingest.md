---
title: 將資料移至適用於 Azure 的 Avere vFXT
description: 如何將資料新增至新的存放磁碟區以與「適用於 Azure 的 Avere vFXT」搭配使用
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: rohogue
ms.openlocfilehash: c2a38b20fff789faf370e3161a92a31ed5f04c57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153713"
---
# <a name="moving-data-to-the-vfxt-cluster---parallel-data-ingest"></a>將資料移至 vFXT 叢集 - 平行資料擷取

創建新的 vFXT 群集後，您的第一個任務可能是將資料移動到 Azure 中的新存儲卷。 不過，如果您一般移動資料的方法是從一個用戶端發出簡單的複製命令，您可能會發現複製效能很差。 單線程複製不是將資料複製到 Avere vFXT 群集後端存儲的好選擇。

由於 Azure 群集的 Avere vFXT 是可擴展的多用戶端緩存，因此將資料複製到該緩存的最快、最有效的方法是使用多個用戶端。 此技術會平行處理資料與物件的擷取。

![顯示多用戶端、多執行緒資料移動的圖表：左上方有一個內部部署硬體儲存體的圖示，其中有多個來自它的箭頭。 這些箭頭指向四部用戶端機器。 從每部用戶端機器都有三個箭頭指向 Avere vFXT。 從 Avere vFXT，有多個箭頭指向 Blob 儲存體。](media/avere-vfxt-parallel-ingest.png)

通常用來將資料從一個儲存體系統傳輸至另一個儲存體系統的 ``cp`` 或 ``copy`` 命令是只會一次複製一個檔案的單一執行緒處理序。 這意謂著檔案伺服器一次只會擷取一個檔案 - 這相當浪費叢集資源。

本文說明建立多用戶端、多執行緒檔案複製系統以將資料移至 Avere vFXT 叢集的策略。 它說明檔案傳輸概念和決策點，這些可用來以多個用戶端和簡單的複製命令進行有效率的資料複製。

此外，也說明一些能夠提供幫助的公用程式。 該``msrsync``實用程式可用於部分自動執行將資料集劃分為存儲桶和使用``rsync``命令的過程。 ``parallelcp`` 指令碼是另一個公用程式，可自動讀取來源目錄並發出複製命令。 此外，``rsync``該工具可以分兩個階段使用，以提供仍然提供資料一致性的更快副本。

按一下連結來跳至某個小節：

* [手動複製範例](#manual-copy-example) - 一個使用複製命令的完整說明
* [兩相 rsync 示例](#use-a-two-phase-rsync-process)
* [部分自動化 (msrsync) 範例](#use-the-msrsync-utility)
* [平行複製範例](#use-the-parallel-copy-script)

## <a name="data-ingestor-vm-template"></a>資料擷取器 VM 範本

GitHub 上有提供 Resource Manager 範本，此範本可藉由本文中所提到的平行資料擷取工具自動建立 VM。

![顯示多個分別來自 Blob 儲存體、硬體儲存體及 Azure 檔案來源之箭頭的圖表。 這些箭頭指向一個「資料擷取器 VM」，並從該處再有多個箭頭指向 Avere vFXT](media/avere-vfxt-ingestor-vm.png)

資料擷取器 VM 是一個教學課程的一部分，在該教學課程中，新建立的 VM 會掛接 Avere vFXT 叢集，並從該叢集下載其啟動程序指令碼。 如需詳細資料，請參閱[啟動載入資料擷取器 VM](https://github.com/Azure/Avere/blob/master/docs/data_ingestor.md) \(英文\)。

## <a name="strategic-planning"></a>策略規劃

在設計並行複製資料的策略時，應瞭解檔案大小、檔計數和目錄深度的權衡。

* 當檔案較小時，攸關的計量是每秒檔案數。
* 當檔案較大 (10 MiBi 或更大) 時，攸關的計量是每秒位元組數。

每個複製處理序都有輸送量速率和檔案傳輸速率，藉由對複製命令的長度進行計時及分解檔案大小和檔案計數，即可測量這些速率。 解釋如何測量費率不在本文檔的範圍之內，但瞭解是處理小檔還是大型檔非常重要。

## <a name="manual-copy-example"></a>手動複製範例

您可以藉由在背景中針對幾組預先定義的檔案或路徑，一次執行多個複製命令，在用戶端上手動建立多執行緒複製。

Linux/UNIX ``cp`` 命令包含可保留擁有權和 mtime 中繼資料的 ``-p`` 引數。 您可以視需要將此引數新增至下方的命令。 (如果新增此引數，將會增加從用戶端傳送到目的地檔案系統以進行中繼資料修改的檔案系統呼叫次數)。

此簡單範例會平行複製兩個檔案：

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

在發出此命令之後，`jobs` 命令會顯示有兩個執行緒正在執行。

### <a name="predictable-filename-structure"></a>可預測的檔案名稱結構

如果您的檔案名稱是可預測的，您可以使用運算式來建立平行複製執行緒。

例如，如果您的目錄包含從 `0001` 到 `1000` 依序編號的 1000 個檔案，您便可以使用下列運算式來建立 10 個平行執行緒，每個執行緒各複製 100 個檔案：

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination1/ & \
cp /mnt/source/file2* /mnt/destination1/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination1/ & \
cp /mnt/source/file5* /mnt/destination1/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination1/ & \
cp /mnt/source/file8* /mnt/destination1/ & \
cp /mnt/source/file9* /mnt/destination1/
```

### <a name="unknown-filename-structure"></a>未知的檔案名稱結構

如果您的檔案命名結構不可預測，您可以依目錄名稱將檔案分組。

此範例會收集整個目錄來傳送給以背景工作身分執行的 ``cp`` 命令：

```bash
/root
|-/dir1
| |-/dir1a
| |-/dir1b
| |-/dir1c
   |-/dir1c1
|-/dir1d
```

收集檔案之後，您可以執行平行複製命令，以遞迴方式複製子目錄及其所有內容：

```bash
cp /mnt/source/* /mnt/destination/
mkdir -p /mnt/destination/dir1 && cp /mnt/source/dir1/* mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ & # this command copies dir1c1 via recursion
cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
```

### <a name="when-to-add-mount-points"></a>新增掛接點的時機

在您有足夠的平行執行緒針對單一目的地檔案系統掛接點執行之後，會遇到一個新增更多執行緒也不會提升輸送量的瓶頸點。 （輸送量將以檔/秒或位元組/秒為單位進行測量，具體取決於資料類型。或者更糟的是，過執行緒有時會導致輸送量下降。

當發生這種情況時，您可以使用相同的遠端檔案系統掛接路徑，將用戶端掛接點新增至其他 vFXT 叢集 IP 位址：

```bash
10.1.0.100:/nfs on /mnt/sourcetype nfs (rw,vers=3,proto=tcp,addr=10.1.0.100)
10.1.1.101:/nfs on /mnt/destination1type nfs (rw,vers=3,proto=tcp,addr=10.1.1.101)
10.1.1.102:/nfs on /mnt/destination2type nfs (rw,vers=3,proto=tcp,addr=10.1.1.102)
10.1.1.103:/nfs on /mnt/destination3type nfs (rw,vers=3,proto=tcp,addr=10.1.1.103)
```

新增用戶端掛接點可讓您將額外的複製命令分岔至額外的 `/mnt/destination[1-3]` 掛接點，達成進一步的平行處理原則。

例如，如果您的檔案非常大，您可以將複製命令定義成使用不同的目的地路徑，以平行方式從執行複製的用戶端送出更多命令。

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination2/ & \
cp /mnt/source/file2* /mnt/destination3/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination2/ & \
cp /mnt/source/file5* /mnt/destination3/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination2/ & \
cp /mnt/source/file8* /mnt/destination3/ & \
```

在上述範例中，所有三個目的地掛接點都被用戶端檔案複製處理序當作目標。

### <a name="when-to-add-clients"></a>新增用戶端的時機

最後，當您已達到用戶端處理能力上限時，新增更多複製執行緒或額外的掛接點將無法產生任何額外的每秒檔案數或每秒位元組數增加。 在此情況下，您可以使用同一組掛接點來部署另一個用戶端，此用戶端將執行自己的一組檔案複製處理序。

範例：

```bash
Client1: cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
Client1: cp -R /mnt/source/dir2/dir2a /mnt/destination/dir2/ &
Client1: cp -R /mnt/source/dir3/dir3a /mnt/destination/dir3/ &

Client2: cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
Client2: cp -R /mnt/source/dir2/dir2b /mnt/destination/dir2/ &
Client2: cp -R /mnt/source/dir3/dir3b /mnt/destination/dir3/ &

Client3: cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ &
Client3: cp -R /mnt/source/dir2/dir2c /mnt/destination/dir2/ &
Client3: cp -R /mnt/source/dir3/dir3c /mnt/destination/dir3/ &

Client4: cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
Client4: cp -R /mnt/source/dir2/dir2d /mnt/destination/dir2/ &
Client4: cp -R /mnt/source/dir3/dir3d /mnt/destination/dir3/ &
```

### <a name="create-file-manifests"></a>建立檔案資訊清單

了解上述方法 (每個目的地多個複製執行緒、每個用戶端多個目的地、每個可透過網路存取之來源檔案系統多個用戶端) 之後，請考慮這項建議：建置檔案資訊清單，然後跨多個用戶端將它們與複製命令搭配使用。

此案例會使用 UNIX ``find`` 命令來建立檔案或目錄的資訊清單：

```bash
user@build:/mnt/source > find . -mindepth 4 -maxdepth 4 -type d
./atj5b55c53be6-01/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-01/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-01/support/trace/rolling
./atj5b55c53be6-03/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-03/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-03/support/trace/rolling
./atj5b55c53be6-02/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-02/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-02/support/trace/rolling
```

請將此結果重新導向到檔案：`find . -mindepth 4 -maxdepth 4 -type d > /tmp/foo`

接著，您可以藉由使用 BASH 命令來計算檔案及判斷子目錄的大小，逐一查看資訊清單：

```bash
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l` `du -sh ${i}`"; done
244    3.5M    ./atj5b5ab44b7f-02/support/gsi/2018-07-18T00:07:03EDT
9      172K    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.8M    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T01:01:00UTC
131    13M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    6.2M    ./atj5b5ab44b7f-02/support/gsi/2018-07-20T21:59:41UTC
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_vfxt_catchup
7      16K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:12:19UTC
8      83K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:17:17UTC
575    7.7M    ./atj5b5ab44b7f-02/support/cores/armada_main.2000.1531980253.gsi
33     4.4G    ./atj5b5ab44b7f-02/support/trace/rolling
281    6.6M    ./atj5b5ab44b7f-01/support/gsi/2018-07-18T00:07:03EDT
15     182K    ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-18T05:01:00UTC
244    17M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-19T01:01:01UTC
299    31M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T01:01:00UTC
256    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T21:59:41UTC_partial
889    7.7M    ./atj5b5ab44b7f-01/support/gsi/2018-07-20T21:59:41UTC
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_vfxt_catchup
11     248K    ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:12:19UTC
11     88K     ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:17:17UTC
645    11M     ./atj5b5ab44b7f-01/support/cores/armada_main.2019.1531980253.gsi
33     4.0G    ./atj5b5ab44b7f-01/support/trace/rolling
244    2.1M    ./atj5b5ab44b7f-03/support/gsi/2018-07-18T00:07:03EDT
9      158K    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.3M    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T01:01:00UTC
131    12M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    8.4M    ./atj5b5ab44b7f-03/support/gsi/2018-07-20T21:59:41UTC
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_vfxt_catchup
7      159K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:12:19UTC
7      157K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:17:17UTC
576    12M     ./atj5b5ab44b7f-03/support/cores/armada_main.2013.1531980253.gsi
33     2.8G    ./atj5b5ab44b7f-03/support/trace/rolling
```

最後您必須針對用戶端打造實際的檔案複製命令。

如果您有四個用戶端，請使用此命令：

```bash
for i in 1 2 3 4 ; do sed -n ${i}~4p /tmp/foo > /tmp/client${i}; done
```

如果您有五個用戶端，請使用類似以下的命令：

```bash
for i in 1 2 3 4 5; do sed -n ${i}~5p /tmp/foo > /tmp/client${i}; done
```

而對於六個...根據需要進行推斷。

```bash
for i in 1 2 3 4 5 6; do sed -n ${i}~6p /tmp/foo > /tmp/client${i}; done
```

您將會得到 *N* 個產生的檔案，您 *N* 個用戶端中的每一個都有一個檔案，此檔案會有從 `find` 命令輸出中取得之第四層目錄的路徑名稱。

請使用每個檔案來建置複製命令：

```bash
for i in 1 2 3 4 5 6; do for j in $(cat /tmp/client${i}); do echo "cp -p -R /mnt/source/${j} /mnt/destination/${j}" >> /tmp/client${i}_copy_commands ; done; done
```

上述範例會提供 *N* 個檔案，每個檔案的每一行都有一個複製命令，可在用戶端上當作 BASH 指令碼來執行。

目標是要在多個用戶端上以平行方式同時為每個用戶端執行這些指令碼的多個執行緒。

## <a name="use-a-two-phase-rsync-process"></a>使用兩相 rsync 過程

標準``rsync``實用程式不能很好地通過 Azure 系統的 Avere vFXT 填充雲存儲，因為它會生成大量檔創建和重命名操作，以確保資料完整性。 但是，如果按照第二次運行``--inplace``來檢查``rsync``檔完整性，則可以安全地使用 該選項跳過更仔細的複製過程。

標準``rsync``複製操作創建暫存檔案並填充資料。 如果資料傳輸成功完成，暫存檔案將重命名為原始檔案名。 此方法可確保一致性，即使檔在複製期間被訪問。 但是此方法會產生更多的寫入操作，這會減慢檔在緩存中的移動速度。

該選項``--inplace``直接將新檔寫入其最終位置。 檔在傳輸期間不保證一致，但如果準備存儲系統供以後使用，則這並不重要。

第二``rsync``個操作用作對第一個操作的一致性檢查。 由於檔已被覆制，因此第二階段是快速掃描，以確保目標上的檔與源上的檔匹配。 如果任何檔不匹配，則複製這些檔。

您可以在一個命令中同時發出兩個階段：

```bash
rsync -azh --inplace <source> <destination> && rsync -azh <source> <destination>
```

此方法是一種簡單且節省時間的方法，適用于資料集，最多包括內部目錄管理器可以處理的檔數。 （這通常是 3 節點群集的 2 億個檔，六節點群集的 5 億個檔，等等。

## <a name="use-the-msrsync-utility"></a>使用 msrsync 實用程式

該工具``msrsync``還可用於將資料移動到 Avere 群集的後端核心檔案伺服器。 此工具的設計目的是要藉由執行多個平行的 ``rsync`` 處理序，將頻寬使用情況最佳化。 您可以從 GitHub 取得它，網址為 <https://github.com/jbd/msrsync>。

``msrsync`` 會將來源目錄分解成個別的「貯體」，然後在每個貯體上執行個別的 ``rsync`` 處理序。

使用四核心 VM 的初步測試在使用 64 個處理序時所顯示的效率最佳。 請使用 ``msrsync`` 選項 ``-p`` 將處理序數目設定為 64。

您還可以使用 參數``--inplace``與``msrsync``命令。 如果使用此選項，請考慮運行第二個命令（如上文所述[的 rsync），](#use-a-two-phase-rsync-process)以確保資料完整性。

``msrsync``只能寫入本地卷和本地卷。 來源和目的地必須在叢集的虛擬網路中可作為本機掛接來存取。

要使用``msrsync``Avere 群集填充 Azure 雲卷，請按照以下說明操作：

1. 安裝``msrsync``及其先決條件（rsync 和 Python 2.6 或更高版本）
1. 決定要複製的檔案和目錄總數。

   例如，將 Avere 實用程式``prime.py``與參數```prime.py --directory /path/to/some/directory```一起使用（可通過下載<https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>URL 提供）。

   如果不使用``prime.py``，則可以使用 GNU``find``工具計算專案數，如下所示：

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. 將項目數除以 64 以決定每一處理序的項目數。 當您執行命令時，請將此數目與 ``-f`` 選項搭配使用來設定貯體的大小。

1. 發出複製``msrsync``檔的命令：

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   如果使用``--inplace``，則添加第二次執行，而不選擇檢查資料是否正確複製：

   ```bash
   msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH> && msrsync -P --stats -p 64 -f <ITEMS_DIV_64> --rsync "-ahv" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   例如，此命令的設計目的是要以 64 個處理序將 11,000 個檔案從 /test/source-repository 移至 /mnt/vfxt/repository：

   ``msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository && msrsync -P --stats -p 64 -f 170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/vfxt/repository``

## <a name="use-the-parallel-copy-script"></a>使用平行複製指令碼

該``parallelcp``腳本還可用於將資料移動到 vFXT 群集的後端存儲。

下方指令碼會新增 `parallelcp` 可執行檔。 (此指令碼是專為 Ubuntu 而設計的；如果使用另一個散發套件，則必須個別安裝 ``parallel``)。

```bash
sudo touch /usr/bin/parallelcp && sudo chmod 755 /usr/bin/parallelcp && sudo sh -c "/bin/cat >/usr/bin/parallelcp" <<EOM
#!/bin/bash

display_usage() {
    echo -e "\nUsage: \$0 SOURCE_DIR DEST_DIR\n"
}

if [  \$# -le 1 ] ; then
    display_usage
    exit 1
fi

if [[ ( \$# == "--help") ||  \$# == "-h" ]] ; then
    display_usage
    exit 0
fi

SOURCE_DIR="\$1"
DEST_DIR="\$2"

if [ ! -d "\$SOURCE_DIR" ] ; then
    echo "Source directory \$SOURCE_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -d "\$DEST_DIR" ] && ! mkdir -p \$DEST_DIR ; then
    echo "Destination directory \$DEST_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -w "\$DEST_DIR" ] ; then
    echo "Destination directory \$DEST_DIR is not writeable, or is not a directory"
    display_usage
    exit 3
fi

if ! which parallel > /dev/null ; then
    sudo apt-get update && sudo apt install -y parallel
fi

DIRJOBS=225
JOBS=225
find \$SOURCE_DIR -mindepth 1 -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$DIRJOBS -0 "mkdir -p \$DEST_DIR/{}"
find \$SOURCE_DIR -mindepth 1 ! -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$JOBS -0 "cp -P \$SOURCE_DIR/{} \$DEST_DIR/{}"
EOM
```

### <a name="parallel-copy-example"></a>平行複製範例

此範例會使用來自 Avere 叢集的來源檔案，利用平行複製指令碼來編譯 ``glibc``。
<!-- xxx what is stored where? what is 'the avere cluster mount point'? xxx -->

這些來源檔案儲存在 Avere 叢集掛接點上，而物件檔案則儲存在本機硬碟上。

此指令碼會使用上述平行複製指令碼。 ``-j`` 選項會與 ``parallelcp`` 和 ``make`` 搭配使用來進行平行處理。

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/node1 avere
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```
