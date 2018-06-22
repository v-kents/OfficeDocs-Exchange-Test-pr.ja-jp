﻿---
title: 'ハイブリッド展開でのシングル サインオン: Exchange 2013 Help'
TOCTitle: ハイブリッド展開でのシングル サインオン
ms:assetid: 050606f9-718d-4a1f-b7a6-50b08c6e9e07
ms:mtpsurl: https://technet.microsoft.com/ja-jp/library/Hh563846(v=EXCHG.150)
ms:contentKeyID: 48267598
ms.date: 01/11/2018
mtps_version: v=EXCHG.150
ms.translationtype: HT
---

# ハイブリッド展開でのシングル サインオン

 

_<strong>適用先:</strong>Exchange Online, Exchange Server 2013, Exchange Server 2016_

_<strong>トピックの最終更新日:</strong>2016-01-29_

シングル サインオンにより、ユーザーは 1 つのユーザー名とパスワードで、オンプレミスと Office 365 の両方の組織にアクセスできます。シングル サインオンは、ユーザーにとってなじみのあるサインオン エクスペリエンスであり、これにより管理者は、オンプレミスの Active Directory 管理ツールを使用して Exchange Online 組織のメールボックスのアカウント ポリシーを簡単に制御できます。シングル サインオンが有効になっている場合は、ハイブリッド展開を構成する必要はありませんが、ハイブリッド展開を構成するよう強くお勧めします。シングル サインオンを使用しないユーザーは、オンプレミス組織と Office 365 の 2 組の別々の資格情報を記憶する必要があります。シングル サインオンの他のいくつかのメリットを以下に示します。

  - **Exchange Online Archiving**   シングル サインオンが導入されている場合、オンプレミスの Outlook ユーザーは、Exchange Online 組織内のアーカイブされたコンテンツに初めてアクセスする際に資格情報の入力を要求されます。ただし、ユーザーは、\[パスワードを保存する\] を選択することで一時的にそれ以降の資格情報の入力要求を回避できますが、オンプレミスのアカウントのパスワードが変更されると再び資格情報の入力が要求されます。シングル サインオンが Exchange 組織に導入されておらず、Exchange Online Archiving が有効になっている場合は、オンプレミスのユーザー プリンシパル名 (UPN) がそのユーザーの Exchange Online アカウントと一致する必要があります。また、ユーザーがアーカイブにアクセスするたびにオンプレミスの資格情報の入力が要求されます。

  - **ポリシーの制御**Active Directory からアカウント ポリシーを制御できます。これにより、パスワード ポリシー、ワークステーションの制限、ロックアウトの制御などを管理できます。Office 365 組織で追加の作業を行う必要はありません。

  - **サポートへの問い合わせの削減**   どの企業でも、パスワードを忘れたという問い合わせはよくあります。覚えなければならないパスワードが少なくなれば、パスワードを忘れる可能性も低くなります。

シングル サインオンを展開する場合、パスワード同期と Active Directory フェデレーション サービス (AD FS) の 2 つのオプションがあります。Azure Active Directory Connect では、両方のオプションが用意されています。AD FS を使用する必要がなければ、パスワード同期を使用することを強くお勧めします。パスワード同期には、AD FS と同じ利点の多くがあります。展開の複雑さを避けることもできます。各オプションの一般的なメリットとデメリットのいくつかを、次の表に示します。


> [!NOTE]
> 既定では、AD FS を展開していてオンプレミスの AD FS サーバーに何らかの理由でインターネットから到達できない場合、Office 365 はユーザーを認証するためにパスワード同期を再確立します。これにより、Office 365 メールボックスのユーザーは、オンプレミスのサーバーを使用できない場合でも、作業を中断しないですみます。



各オプションに関する詳細については、「[Azure AD Connect ユーザーのサインオン オプション](http://go.microsoft.com/fwlink/p/?linkid=723514)」をご覧ください。


<table>
<colgroup>
<col style="width: 33%" />
<col style="width: 33%" />
<col style="width: 33%" />
</colgroup>
<thead>
<tr class="header">
<th><p>シングル サインオン メソッド</p></th>
<th><p>メリット</p></th>
<th><p>デメリット</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>パスワード同期 (推奨)</p></td>
<td><ul>
<li><p>AD FS よりも大幅に簡単。</p></li>
<li><p>オンプレミスの Active Directory が使用できない場合でも、ユーザーは Office 365 にログインできる。</p></li>
<li><p>その他の少数のサーバーは、パスワード同期を展開する必要がある。</p></li>
<li><p>サード パーティ証明書は不必要。</p></li>
<li><p>AD FS によるオンプレミスの Active Directory への外部アクセスは不要。</p></li>
<li><p>展開には、わずか数時間しかかからない。</p></li>
</ul></td>
<td><ul>
<li><p>オンプレミスの Active Directory 内のユーザー アカウントを無効にしても、Office 365 内では無効にならない。Office 365 管理ポータルでアカウントを手動で無効にする必要がある。</p></li>
<li><p>オンプレミスの Active Directory が必要。他のディレクトリ サービスはサポートされていない。</p></li>
</ul></td>
</tr>
<tr class="even">
<td><p>AD FS</p></td>
<td><ul>
<li><p>パスワードの変更は、即時。</p></li>
<li><p>オンプレミスの Active Directory のユーザーを無効にすると、オンプレミスのネットワーク アクセスと Office 365 アカウントの両方が無効になる。</p></li>
<li><p>Active Directory 以外のディレクトリ サービスをサポート。</p></li>
<li><p>大規模かつ多様な展開をサポート。</p></li>
<li><p>2 要素認証をサポート。</p></li>
</ul></td>
<td><ul>
<li><p>必要なサーバーが多い。そのうち少なくとも 1 つは、境界ネットワーク内に存在する必要がある。</p></li>
<li><p>パブリック IP アドレスと TCP ポート 443 がファイアウォール上で開かれている必要がある。</p></li>
<li><p>アカウントのパスワードの変更を検出したりアカウントが最近有効または無効にされたかどうかを検出するには、オンプレミスの Active Directory に接続する必要がある。</p></li>
</ul></td>
</tr>
</tbody>
</table>
