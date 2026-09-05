# Agent Skills

## 概要

このdirectoryには、TOEIC Projectの開発・検証・AI運用の過程で実際に繰り返し現れた失敗、判断ミス、実行上の問題を、再利用可能なAgent Skillとして整理したcollectionを収録しています。

収録数は72件です。

* General: 65
* TOEIC Project固有: 7

General Skillは、TOEIC Projectを離れても同種の作業で再利用できるように整理したものです。PowerShell、Git、runtime、検証、承認境界、再試行、作業scope、AI tool利用など、特定の教材生成処理だけには限定されない問題を扱います。

TOEIC Project Skillは、generator / validator間のcontract、問題生成、学習UI、semantic answer correctnessなど、このproject固有の構造や学習要件に強く依存するものです。

このrepositoryにあるSkillは公開用snapshotです。正本はprivate Knowledge Hubにあり、このcopyは正本から生成された派生成果物です。

## なぜこのcollectionを作ったか

このprojectでは、同じ種類の失敗が別の会話、別の作業単位、別のtool利用時に再発することがありました。

たとえば、実在しないpathを前提に処理を進める、承認されたscopeと実行scopeを混同する、成功後もtool callを繰り返す、validator側の欠陥を生成物の欠陥と誤認する、dirty worktree上で変更の来歴を失う、といった問題です。

個々の失敗をその場で直すだけでは、次の会話や別のagentが同じ失敗を繰り返す可能性があります。そのため、単発のincident reportとして残すのではなく、

* いつその問題が起きるのか
* 何が原因として確認されたのか
* 次回どう防ぐのか
* どの記録に基づくのか

を一つの再利用可能な単位にまとめました。

目的は「AIが失敗した記録」を集めることではありません。失敗と修正の履歴から、次の作業で直接使える判断材料と手順を残すことです。

## 何を抽出したか

元になったのは、TOEIC Projectの会話・実行記録から抽出された72件のreusable knowledge candidateです。各candidateは1件のSkillとして保持され、最終的に72 Skillとなっています。

各Skillには、少なくとも次の情報があります。

* `name`: Skillの識別名
* `description`: どの状況で使うSkillか
* `source-rko`: 元になったreusable knowledge candidate
* `source-occurrence-count`: そのcandidateに関連付けられたevidence association数
* `intended-hub-scope`: GeneralかTOEIC Project固有か
* `## いつ起きるか`
* `## 原因`
* `## どう防ぐか`
* `## 根拠`

collection全体を、内容の似ているSkill同士で統合したり、後から大きなcategoryへまとめたりはしていません。異なるfailure patternを一つに潰さず、元のcandidateとの対応を保つことを優先しています。

## Skill化の方法

Skill化では、単に会話の要約を作るのではなく、再利用可能なfailure-prevention unitに変換することを重視しました。

大まかな流れは次のとおりです。

1. 会話・実行記録から、繰り返し現れる失敗や修正パターンを候補として抽出する。
2. 各候補を一つのreusable knowledge unitとして識別する。
3. その候補が「どの状況で発火するか」「確認できた原因は何か」「どう防ぐか」に分解する。
4. 元記録とのassociationを保持したままSkill化する。
5. TOEIC Project固有の前提なしで成立するものをGeneral、project固有構造に依存するものをTOEIC Project-specificとして分ける。
6. General Skillについて、特定projectのpath、file名、schema keyなどが一般手順そのものへ混入していないかを別途監査する。
7. 公開時にはprivate identifierとmachine-local pathだけを決定的なruleで除去・置換し、Skillの技術的意味そのものは書き換えない。

特に公開copyの生成では、LLMにSkill本文を渡して再要約・再構成する方法は採っていません。canonicalとの対応を保つため、公開時の変換は限定されたsanitizationだけにしています。

## HumanとAIの分担

このcollectionはHumanだけ、またはAIだけで作ったものではありません。

AIは主に、長い作業履歴からfailure patternを抽出し、候補を構造化し、Skill本文へ変換し、evidence associationやmetadataを保持する作業を担当しました。また、件数、scope、metadata、hash、公開時のidentifier漏洩など、機械的に検証できる部分の検査にも利用しています。

Humanは、何を正本とするか、どの範囲を公開するか、Generalとproject-specificをどのように分けるか、どの変更を承認するかといった境界を決定しました。AIの提案が元の証拠より強い主張になっていないか、scopeを広げていないか、公開物として残すべき情報と非公開にすべき情報は何か、といった最終判断もHuman側で行っています。

したがって、AIが自動生成した72件をそのまま無審査で公開したcollectionでも、Humanが各技術内容を専門家として独立検証したcollectionでもありません。

HumanとAIで担当する判断の種類を分けながら作った成果物です。

## Evidenceとtraceability

private canonicalは、full source-message association、conversation/message identifier、`source-rko`、`source-occurrence-count`を保持します。

各Skillの`source-rko`によって、どのreusable knowledge candidateから作られたかを追跡できます。`source-occurrence-count`は、そのcandidateに関連付けられたevidence association数を保持するためのmetadataです。

public copyでは、conversation ID、message ID、private association、machine-local absolute pathを公開していません。一方で、`source-rko`と`source-occurrence-count`は維持しています。

そのため、公開repositoryからSkillの由来と相対的なevidence量は確認できますが、privateな元messageまで第三者が直接辿ることはできません。公開repository単独ではfull source-message associationを再検証できない、という境界があります。

## Repository構成

```text
agent-skills/
├─ README.md
├─ general/
│  └─ <skill-name>/
│     └─ SKILL.md
└─ toeic-project/
   └─ <skill-name>/
      └─ SKILL.md
```

`general/`には、特定のTOEIC Project構造を前提としなくても利用できる65 Skillを配置しています。

`toeic-project/`には、TOEIC Projectのgenerator、validator、学習UI、問題生成contractなど、project固有の構造に依存する7 Skillを配置しています。

このdirectoryはportfolio / documentation用です。実行時にagentが自動発見するSkill directoryとしては扱っていません。

## Canonical sourceとpublic copy

canonical sourceはprivate Knowledge Hubの`<AI_KNOWLEDGE_ROOT>`にあります。

このrepository copyは、canonicalから固定sanitization ruleで生成したderived publication snapshotです。

公開時に許可している変換は、原則として次の3種類だけです。

1. conversation ID / message IDなどprivate identifierの除去
2. privateなsource-message associationを公開用の固定文へ置換
3. machine-local absolute pathを意味の保たれるsemantic placeholderへ置換

これ以外の文章改善、要約、technical rewriteはpublication生成の一部として行いません。

したがって、このrepository側のSkillを直接修正してcanonicalとの差を埋める運用はしません。内容を変更する場合はcanonicalを先に変更し、その後public mirror全体を再生成します。

## Collectionの読み方・使い方

各Skillは、最初から最後まで規則集として読むよりも、現在起きている症状に対応するものを選んで使うことを想定しています。

まずfrontmatterの`description`と`## いつ起きるか`を確認してください。現在のfailure signatureや作業状況と一致する場合、そのSkillを適用する候補になります。

`## 原因`は、元のevidenceがどこまで原因を支持しているかを示します。Skillによっては直接原因まで確認できているものもあれば、結果は確認できてもroot causeまでは確定していないものもあります。記載以上の一般化をしないことが重要です。

`## どう防ぐか`には、同種の失敗を避けるために実際の作業へ持ち込める手順や判断基準があります。

`## 根拠`はpublic copyではprivate message identifierを除去しています。より詳細なsource-message associationはprivate canonical側に保持されています。

また、Skillは状況に応じて適用するものです。名前や一部のkeywordが一致しただけで機械的に全手順を適用するのではなく、現在の前提・tool・runtime・scopeがSkillの発火条件と一致しているかを先に確認してください。

## 実際に使った記録

## 限界

* **専門家裁定ではない:** 72 Skillをdomain expertが1件ずつ独立に技術認定したcollectionではありません。元の作業記録とその後の監査に基づいていますが、すべての内容について外部専門家による独立reviewが完了しているわけではありません。
* **occurrence count:** `source-occurrence-count`はevidence association数です。独立した再現実験数、独立利用者数、独立validation数を意味しません。同じ出来事やmessageが複数のknowledge unitと関係する場合もあります。
* **environment dependence:** 一部SkillはWindows、PowerShell、local AI tooling、harness、machine-local filesystem、specific project operationsに由来します。そのため、別OS、別shell、別toolchain、別agent環境へそのまま一般化できるとは限りません。
* **advisory / procedural mixture:** collectionにはadvisory guidanceとprocedural operational instructionが混在しています。すべてを同じ強度のhard rule、security boundary、industry best practiceとして扱うものではありません。
* **時間による陳腐化:** tool、OS、model、library、runtimeの挙動は変化します。特定versionや環境に由来するSkillは、将来の環境でも同じ前提が成立するかを確認してから使用する必要があります。
* **公開版だけでは完全なprovenanceを検証できない:** privacyとmachine-local情報の公開回避を優先しているため、public copyからprivate conversation/message associationまでは辿れません。

## Publication / synchronization policy

* publication Skillを直接編集しません。
* Skillの内容を変更する場合は、private canonicalを先に更新します。
* canonical Skillが1件でも変更された場合、public mirrorはstaleとして扱います。
* canonical Skill変更後は、live canonical全件からpublic mirrorを全量再生成します。
* publicationからcanonicalへ逆同期しません。
* partial hotfixを恒久的な同期方式にしません。
* canonicalとpublicationのdrift比較では、CRLF / LFの差だけをdriftと判定しないため、比較処理の内部だけでline endingをLFへ正規化してからhashを比較します。比較のために実ファイルのline endingを書き換えることはしません。
* bundleとpublication mirrorは別々のderived artifactです。一方を再生成しても、もう一方が自動的に最新になるわけではありません。
