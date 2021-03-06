﻿---
title: 'メール フローのルールを構成するためのベスト プラクティス: Exchange 2013 Help'
TOCTitle: メール フローのルールを構成するためのベスト プラクティス
ms:assetid: abd863c3-c0ce-42f3-9470-a573adc3cbba
ms:mtpsurl: https://technet.microsoft.com/ja-jp/library/Dn960147(v=EXCHG.150)
ms:contentKeyID: 65218744
ms.date: 05/23/2018
mtps_version: v=EXCHG.150
ms.translationtype: MT
---

# メール フローのルールを構成するためのベスト プラクティス

 

_**適用先:** Exchange Online, Exchange Online Protection, Exchange Server 2013_

_**トピックの最終更新日:** 2016-12-09_

Exchange トランスポート ルールに関する以下のベスト プラクティス推奨に従って、一般的な構成エラーを防いでください。各推奨事項は、例と詳細な手順を示すトピックにリンクしています。

## ルールをテストする

予期せぬ事態がユーザーの電子メールに生じることを防ぐため、またルールが持つビジネス、法律、法令遵守上の意図を間違いなく実現するため、テストを徹底的に行います。多数のオプションがあり、ルールどうしが相互に関係し合うことがあるため、ルールに一致するはずのメッセージと、一致しないはずのメッセージの両方に対してテストを実施し、不用意に一般的すぎるルールを作成してしまった場合に備えることが重要です。ルールをテストするためのすべてのオプションについては、「[メール フロー ルールのテスト](test-a-mail-flow-rule-exchange-2013-help.md)」を参照してください。

## ルールのスコープを決める

意図したメッセージだけにルールが適用されることを確認してください。例を挙げます。

  - **組織が受信するメッセージと、送信するメッセージのどちらか一方にルールを制限する**
    
    既定では、新しいルールは組織内のユーザーが送信または受信するメッセージのどちらか一方に適用されます。したがって、ルールを一方向だけに適用したい場合は、そのことをルールの条件に必ず指定します。例については「[添付ファイル ブロックの一般的なシナリオ](common-attachment-blocking-scenarios-for-mail-flow-rules-exchange-2013-help.md)」を参照してください。

  - **送信者または受信者のドメインに基づいてルールを制限する**
    
    既定では、新しいルールは送信元または受信先のドメインに関係なくメッセージに適用されます。1 つのドメインを除く全ドメインにルールを適用したい場合もあれば、1 つのドメインだけにルールを適用したい場合もあります。例については、「[Office 365 で組織全体の信頼できる差出人またはブロックする差出人の一覧を作成する](https://technet.microsoft.com/ja-jp/library/dn198251\(v=exchg.150\))」を参照してください。

トランスポート ルールに当てはまるすべての条件と例外を網羅した一覧については、以下を参照してください。

  - [Exchange Online でのメール フロー ルールの条件と例外 (述語)](https://technet.microsoft.com/ja-jp/library/jj919235\(v=exchg.150\))

  - [トランスポート ルールの条件 (述語)](mail-flow-rule-conditions-and-exceptions-predicates-in-exchange-2013-exchange-2013-help.md)

  - [Exchange Online Protection でのメール フロー ルールの条件と例外 (述語)](https://technet.microsoft.com/ja-jp/library/jj919234\(v=exchg.150\))

## 2 つのルールが必要な状況を見分ける

2 つのルールを使わないと実現できないこともあります。トランスポート ルールは順番に処理されます。したがって、同じメッセージに複数のルールを適用することができます。たとえば、メッセージをブロックするというアクションに加えて、適用したいアクションがもう 1 つある場合 (送信者の上司にメッセージをコピーする、あるいは通知メッセージ用に件名を変えるなど)、2 つのルールが必要です。最初のルールで送信者の上司にメッセージをコピーすると共に件名を変更し、2 番目のルールでメッセージをブロックできます。

このような 2 つのルールを使用する場合は、条件が同一であることを確認します。例を確認するには、「[一般的なメッセージの承認シナリオ](common-message-approval-scenarios-exchange-2013-help.md)」の例 3、「[添付ファイル ブロックの一般的なシナリオ](common-attachment-blocking-scenarios-for-mail-flow-rules-exchange-2013-help.md)」の例 3、および「[組織全体の免責事項、署名、フッター、またはヘッダー](organization-wide-disclaimers-signatures-footers-or-headers-exchange-online-help.md)」を参照してください。

## スレッド内のすべての電子メールで同じアクションを繰り返さない

スレッド内の一連の電子メールには多数のメッセージが含まれており、スレッド内の各メッセージにアクションを繰り返し実行するのは煩わしい場合もあります。たとえば、免責事項を追加するなどのアクションなら、スレッドの最初のメッセージだけに適用すれば十分と考えられるかもしれません。その場合は、既に免責事項のテキストが含まれているメッセージのための例外を追加します。 例については「[組織全体の免責事項、署名、フッター、またはヘッダー](organization-wide-disclaimers-signatures-footers-or-headers-exchange-online-help.md)」を参照してください。

## ルール処理を中止すべき時を判断する

1 つのルールが一致した時点でルール処理を停止するのが合理的な場合があります。たとえば、添付ファイル付きのメッセージをブロックするルールと、パターンに一致するメッセージに免責事項を挿入するルールがある場合、メッセージがブロックされた時点でルール処理を停止すべきと考えられます。さらにアクションを実行する必要はありません。

1 つのルールがトリガーされたらルール処理を停止するには、ルールで **\[これ以上の処理を中止する\]** チェック ボックスをオンにします。

## 照合するキーワードまたはパターンが多い場合は、ファイルから読み込む

たとえば、許容できない、あるいは不適切な単語のリストが含まれている電子メールの送信を禁止したいとします。それらの単語または語句を記したテキスト ファイルを作成し、それらの単語または語句を使用しているメッセージをブロックするトランスポート ルールを Windows PowerShell でセットアップできます。

テキスト ファイルにはパターンの正規表現を含めることができます。これらの式では、大文字と小文字は区別されません。一般的な正規表現には、次のものが含まれます。


<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td><p><strong>式</strong></p></td>
<td><p><strong>一致</strong></p></td>
</tr>
<tr class="even">
<td><p><strong>.</strong></p></td>
<td><p>任意の 1 文字</p></td>
</tr>
<tr class="odd">
<td><p><strong>*</strong></p></td>
<td><p>任意の追加文字</p></td>
</tr>
<tr class="even">
<td><p><strong>\d</strong></p></td>
<td><p>任意の 10 進数字</p></td>
</tr>
<tr class="odd">
<td><p>[<em>文字グループ</em>]</p></td>
<td><p><em>文字グループ</em>に含まれる任意の 1 文字。</p></td>
</tr>
</tbody>
</table>


正規表現を含んだテキスト ファイルと、使用する Exchange モジュール Windows PowerShell コマンドとを示す例については、「[単語、語句、パターンの一覧に基づいてメールをルーティングするメール フロー ルールの使用](use-mail-flow-rules-to-route-email-based-on-a-list-of-words-phrases-or-patterns-exchange-2013-help.md)」を参照してください。

正規表現を使用してパターンを指定する方法については、「[正規表現言語 - クイック リファレンス](https://go.microsoft.com/fwlink/p/?linkid=532394)」を参照してください。

