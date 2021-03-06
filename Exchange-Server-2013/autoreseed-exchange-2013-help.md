﻿---
title: 'AutoReseed: Exchange 2013 Help'
TOCTitle: AutoReseed
ms:assetid: 61f9a8be-070e-4c62-b505-52644fcff0c5
ms:mtpsurl: https://technet.microsoft.com/ja-jp/library/Dn789209(v=EXCHG.150)
ms:contentKeyID: 62523710
ms.date: 05/23/2018
mtps_version: v=EXCHG.150
ms.translationtype: MT
---

# AutoReseed

 

_**適用先:** Exchange Server 2013 SP1_

_**トピックの最終更新日:** 2015-03-09_

自動再シード、または AutoReseed は、ディスクの障害、データベース破損の発生、またはデータベース コピーの再シードが必要なその他の問題に対応するために通常は管理者によって行われる操作の代わりとなる機能です。AutoReseed は、ディスクの障害発生後にシステムでプロビジョニング済みの予備のディスクを使用して、自動的にデータベースの冗長性を復元するように設計されています。

## AutoReseed の概要

AutoReseed 構成では、標準化されたストレージの表示構造が使用され、管理者が開始位置を選択します。AutoReseed は、ドライブ障害の発生後にできる限り早く冗長性の回復を試みます。これには、マウント ポイントを使用したボリュームのセット (予備のボリュームを含む) およびデータベースの事前マッピングが含まれます。ディスクがオペレーティング システムで使用できない、もしくはこれ以上書き込みが行えないといったディスク障害が発生すると、予備のボリュームがシステムによって割り当てられ、影響をうけたデータベース コピーは自動的に再シードされます。

1.  Microsoft Exchange Replication サービスは定期的に、状態が FailedAndSuspended のコピーをスキャンします。AutoReseed 用に構成されたボリューム上のすべてのデータベース コピーが連続して 15 分間 FailedandSuspended の状態である場合、AutoReseed ワークフローが開始します。

2.  AutoReseed は、失敗して中断されたコピーの再開を最大 3 回試行し、各試行間に 5 分間の休止が置かれます。場合によっては、FailedandSuspended データベース コピーが再開した後、コピーが障害状態のままになることがあります。これはさまざまな理由で発生する可能性があるため、これらのケースに対処する目的で以下のステップが設計されています。すなわち、AutoReseed は、連続して 10 分間エラーになっているデータベース コピーを自動的に中断してワークフローの実行を維持します。中断と再開の操作を実行しても、結果としてデータベース コピーが正常にならない場合、ワークフローを続行します。

3.  この状態のコピーが見つかると、いくつかの前提条件の確認が実行されます。たとえば、予備ディスクがあるかどうか、データベースとそのログ ファイルが同じボリューム上に構成され、必須の命名規則に一致する適切な場所にあるかどうかを確認します。

4.  前提条件の確認に成功すると、Microsoft Exchange Replication サービスに含まれる Disk Reclaimer 機能が、以下の表のタイムラインに従って、予備ディスクを割り当てて再マップし、フォーマットします。AutoReseed は、予備ディスクの割り当てを最大 5 回試行し、各試行間に 1 時間の休止が置かれます。

5.  予備の割り当てが完了すると、AutoReseed は SafeDeleteExistingFiles シード スイッチを使用して InPlaceSeed 操作を実行します。データベースのアクティブ コピーをシード ソースとして使用して、影響を受けたディスク上にあったすべてのデータベースが再シードされます。

6.  シード操作が完了した後、Microsoft Exchange Replication サービスは新しくシードされたコピーが正常であることを確認します。

再試行がすべて完了すると、ワークフローが停止します。3 日後に、データベースのコピーが FailedandSuspended のままである場合、ワークフローの状態がリセットされてステップ 1 から再開します。障害が発生したディスク、コントローラーなどの交換には数日かかる場合があるため、このリセットと再開の動作は有効 (かつ意図的) です。

この時点で、障害がディスク障害によるものであればオペレーターまたは管理者の手動による介入を必要とし、障害の発生したディスクを取り外して交換し、交換したディスクを予備として再構成します。

AutoReseed は DAG の 3 つのプロパティを使用して構成されています。プロパティのうち 2 つは使用中のマウント ポイントを示します。Exchange 2013 では、Windows Server がボリュームごとに複数のマウント ポイントを許可するという仕様を利用します。*AutoDagVolumesRootFolderPath* プロパティは、利用できるすべてのボリュームを含むマウント ポイントを示します。これには、データベースおよび予備のボリュームをホストしているボリュームが含まれます。*AutoDagDatabasesRootFolderPath* プロパティはデータベースを含むマウント ポイントを示します。3 番目の DAG プロパティ、*AutoDagDatabaseCopiesPerVolume* は、ボリュームごとのデータベースの数を構成するのに使用します。

AutoReseed の構成の例を次に図示します。

**AutoReseed の構成の例**

![自動再シード構成の例](images/Dn789209.e3af7306-f5b4-4ec4-9ccf-222ec452699b(EXCHG.150).gif "自動再シード構成の例")

この例では、3 つのボリュームがあり、そのうち 2 つにはデータベース (VOL1 および VOL2) が含まれ、1 つは空でフォーマット済みの予備 (VOL3) です。

AutoReseed を構成するには:

1.  3 つのボリュームはすべて 1 つのマウント ポイントにマウントされます。この例では、マウント ポイント C:\\ExchVols が使用されています。これは、Exchange データベースのストレージの取得に使用するディレクトリを表します。

2.  メールボックス データベースのルート ディレクトリが別のマウント ポイントとしてマウントされます。この例では、マウント ポイント C:\\ExchDBs が使用されています。次に、データベースの親ディレクトリを作成し、その親ディレクトリの下に、1 つはデータベース ファイル用で、もう 1 つはログ ファイル用の 2 つのサブディレクトリがあるようにディレクトリ構造を作成します。

3.  データベースが作成されます。上の例は、ボリュームごとに 1 つのデータベースを使用したシンプルな設計を示しています。したがって、VOL1 には 3 つのディレクトリがあります。親ディレクトリと 2 つのサブディレクトリ (1 つは MDB1 のデータベース ファイル用で、もう 1 つはそのログ用) です。この例には示していませんが、VOL2 上にも 3 つのディレクトリがあります。親ディレクトリおよびその下に MDB2 のデータベース ファイル用と、もう 1 つはそのログ用のディレクトリです。

この構成では、MDB1 または MDB2 に障害が発生した場合、障害が発生したデータベースのコピーが自動的に VOL3 に再シードされます。

## Disk Reclaimer

予備ディスクを割り当ててフォーマットする AutoReseed コンポーネントは、*Disk Reclaimer* と呼ばれます。Disk Reclaimer コンポーネントは、自動再シード操作に備えて、予備ディスクを自動的にフォーマットします。フォーマットの間隔は、ディスクの状態によって異なります。Disk Reclaimer でディスクをフォーマットするには、特定の条件を満たす必要があります。

  - Disk Reclaimer が有効にされている必要があります。既定では有効にされますが、[Set-DatabaseAvailabilityGroup](https://technet.microsoft.com/ja-jp/library/dd297934\(v=exchg.150\)) を使用して無効にすることもできます。

  - ボリュームのマウント ポイントが、ボリュームのルート パス (既定では、C:\\ExchangeVolumes) にある必要があります。

  - ボリュームのどのマウント パスもデータベース ボリューム パス (既定では、C:\\ExchangeDatabases) に設定することはできません。

  - ボリュームにファイルが格納されている場合、それらのファイルはいずれも、24 時間アクセスされていない状態でなければなりません。

上記の条件に加え、Disk Reclaimer が特定のボリュームをフォーマットしようと試みるのは、1 日に一度だけです。次の表は、Disk Reclaimer のフォーマット動作について説明しています。


<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<thead>
<tr class="header">
<th>ディスクの状態とデータベース コピー</th>
<th>フォーマット間隔</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>ディスクはフォーマットされていない状態であるか、フォーマット済みであっても空であるか、またはフォーマット済みで、24 時間アクセスされていないファイルが格納されている状態になっている。また、ローカル Active Directory サイトに、シード元として使用できる正常なアクティブ データベース コピーがある。</p></td>
<td><p>1 日</p></td>
</tr>
<tr class="even">
<td><p>ディスクはフォーマットされていない状態であるか、フォーマット済みであっても空であるか、またはフォーマット済みで、24 時間アクセスされていないファイルが格納されている状態になっている。ただし、ローカル Active Directory サイトに、シード元として使用できる正常なアクティブ データベース コピーがない。</p></td>
<td><p>2 日</p></td>
</tr>
<tr class="odd">
<td><p>ディスクはフォーマットされていない状態であるか、フォーマット済みであっても空であるか、またはフォーマット済みで、24 時間アクセスされていないファイルが格納されている状態になっている。また、ローカル Active Directory サイトに、シード元として使用できる正常なアクティブ データベース コピーがあるが、データベース ファイル (EDB ファイル) およびログ ファイルとは別に、不明のファイルが存在する。</p></td>
<td><p>2 週間</p></td>
</tr>
<tr class="even">
<td><p>ディスクはフォーマットされていない状態であるか、フォーマット済みであっても空であるか、またはフォーマット済みで、24 時間アクセスされていないファイルが格納されている状態になっている。また、ローカル Active Directory サイトに、シード元として使用できる正常なアクティブ データベース コピーがあるが、Active Directory 内には存在しないデータベースのデータベース ファイル (EDB ファイル) が 1 つ以上ある。</p></td>
<td><p>2 週間</p></td>
</tr>
</tbody>
</table>

