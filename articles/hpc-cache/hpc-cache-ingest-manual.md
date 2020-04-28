---
title: Azure HPC 快取資料內嵌-手動複製
description: 如何在 Azure HPC 快取中使用 cp 命令將資料移至 Blob 儲存體目標
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 1d5f8e6b59a4ae0149f219738952b47ce399c2ff
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82194987"
---
# <a name="azure-hpc-cache-data-ingest---manual-copy-method"></a>Azure HPC 快取資料內嵌-手動複製方法

本文提供詳細的指示，說明如何手動將資料複製到 Blob 儲存體容器，以與 Azure HPC 快取搭配使用。 它會使用多執行緒平行作業來優化複製速度。

若要深入瞭解如何將資料移至 Azure HPC 快取的 Blob 儲存體，請參閱[將資料移至 Azure blob 儲存體](hpc-cache-ingest.md)。

## <a name="simple-copy-example"></a>簡單複製範例

您可以藉由在背景中針對幾組預先定義的檔案或路徑，一次執行多個複製命令，在用戶端上手動建立多執行緒複製。

Linux/UNIX ``cp`` 命令包含可保留擁有權和 mtime 中繼資料的 ``-p`` 引數。 您可以視需要將此引數新增至下方的命令。 （新增引數會增加從用戶端傳送至目的地檔案系統以進行中繼資料修改）的檔案系統呼叫數目。

此簡單範例會平行複製兩個檔案：

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

在發出此命令之後，`jobs` 命令會顯示有兩個執行緒正在執行。

## <a name="copy-data-with-predictable-file-names"></a>以可預測的檔案名複製資料

如果您的檔案名是可預測的，您可以使用運算式來建立平行複製執行緒。

例如，如果您的目錄包含依序編號為`0001` `1000`的1000檔案，則您可以使用下列運算式來建立10個平行線程，其中每個都會複製100個檔案：

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

## <a name="copy-data-with-unstructured-file-names"></a>以非結構化的檔案名複製資料

如果您的檔案命名結構不是可預測的，您可以依目錄名稱將檔案分組。

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

## <a name="when-to-add-mount-points"></a>新增掛接點的時機

當您有足夠的平行線程進入單一目的地檔案系統掛接點之後，將會有一個點，讓新增更多執行緒不會提供更多的輸送量。 （輸送量將根據您的資料類型，以每秒的檔案數或位元組/秒來測量）。或更糟的是，過度執行緒有時可能會導致輸送量降低。

發生這種情況時，您可以使用相同的遠端檔案系統掛接路徑，將用戶端掛接點新增至其他 Azure HPC 快取裝載位址：

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

## <a name="when-to-add-clients"></a>新增用戶端的時機

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

## <a name="create-file-manifests"></a>建立檔案資訊清單

瞭解上述方法（每個目的地有多個複製執行緒、每個用戶端的多個目的地、每個網路可存取的來源檔案系統的多個用戶端）時，請考慮下列建議：組建檔案資訊清單，然後在多個用戶端上使用複製命令。

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
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
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
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
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
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_hpccache_catchup
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

還有六個 ...。視需要推斷。

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
