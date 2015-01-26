# DDBJパイプラインとGalaxyによるデータ解析

情報・システム研究機構  
ライフサイエンス統合データベースセンター  
大田達郎 t.ohta@dbcls.rois.ac.jp

prepared for AJACS岩手  
December 5, 2014

----

これは統合データベース講習会 AJACS岩手「DDBJパイプラインとGalaxyによるデータ解析」の資料です。プログラムは[こちら](http://events.biosciencedbc.jp/training/ajacs51)

----

## 概要

本講習は、主に新型DNAシーケンサー(High-Throughput Sequencer, HTS)から得られる塩基配列データを対象に、データ解析に必要な基本的な知識と、公共のウェブツールを利用した解析の手順について学びます。デモデータを利用し、DDBJパイプラインを用いたデータ処理と、Galaxyシステムを利用したデータ解析を行います。

----

## 講習の流れ

今回の講習では、コンピュータを使って以下の内容について説明します。

-	データについて
-	データ処理について
	-	データ処理の種類
		-	マッピング
		-	アセンブル
	-	DDBJパイプラインの使い方
-	データ解析について
	-	データ解析の種類
		-	ゲノム多型解析
	-	Galaxyの使い方

----

## データについて

今回の講習では新型DNAシーケンサーから得られる塩基配列データを取り扱います。塩基配列データは様々な形式でファイルに保存されます。今回のデータ解析に関連するデータ形式(フォーマット)は以下のようなものです。

-	FASTQ
- FASTA
- SAM
- VCF

その他の主要なフォーマットについては、[こちら](http://genome.ucsc.edu/FAQ/FAQformat.html)をご参照ください。

いずれもファイル形式はプレーンテキストです。しかし、ファイルが大きすぎるとアプリケーションがクラッシュしてしまうので、メモ帳やMicrosoft Office Excelのようなアプリケーションで開くことはお勧めしません。

----

### FASTQ

新型シーケンサから得られる塩基配列断片は「リード」と呼ばれます。1回のシーケンス(1Run)からは機械によって数百〜数十億本のシーケンスリードが得られます。FASTQフォーマットは、1本1本のリードの塩基配列と1塩基ごとのクオリティを記述します。FASTQフォーマットは、4行で1リードの情報を記述します。

```
@SRR975299.998 HWI-ST1347:124:H0G13ADXX:1:1101:7299:3013 length=101
ACTGTTAGAAACAATATTCCTTCTGAAGTACCATGATAGAAATTACTATTAAAGCACTATCCTCTACCCCTTACCGTGCATGGTTCTGATGCATTCAAAGC
+SRR975299.998 HWI-ST1347:124:H0G13ADXX:1:1101:7299:3013 length=101
==?D=BDDFFDADGH?EHEHHHBFABHF:<FHAHGAGCGHIHHHGGIIICHFHHGEHIIIGGIGEHHCHGCFFD@E;DECEEA?BCB@>A;AACDCC@CCC
@SRR975299.999 HWI-ST1347:124:H0G13ADXX:1:1101:7473:3197 length=101
TCTTCACTGGCTTTCCATGATCAATGTCAAGAATCAGACTGAGAGCTTGGGAACATTCTTTTGCTCTTTGCCGTACCTAATATAATTATTAGAAGAAGGAA
+SRR975299.999 HWI-ST1347:124:H0G13ADXX:1:1101:7473:3197 length=101
CCCFFFFFHHHHHJJJJJHJJJJJJJFIHIHJIIJJJHJJJJIIIJHIJJIJJJJJJJJJJJJJJJJJJJJIJIHHHFFFFFFFEEFEEFFDDDDDDDDDD
@SRR975299.1000 HWI-ST1347:124:H0G13ADXX:1:1101:7557:3113 length=101
CCATGATTCTGTCCAGCCAGCAGGATGCAGCCTTTGCCTTTGCCTCTCTTGCCATAGTTTTCTCCTCCTATATCACTCTTGTTGTGCTCTTTGTGCCCAAG
+SRR975299.1000 HWI-ST1347:124:H0G13ADXX:1:1101:7557:3113 length=101
CCCFFFFFHHHHHIJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJIJJJJJJJJJHIJJJJIJJJJIJJJJJHJHHHHHHFFFFEEEEEEEDDDDDDBD
```

|||
|:--:|:--:|:--:|
|1行目|リードID|
|2行目|塩基配列|
|3行目|+から始まるコメント|
|4行目|クオリティスコア|

[参考: FASTQ format / Wikipedia](http://en.wikipedia.org/wiki/FASTQ_format)

----

#### Phred Quality Score

FASTQフォーマットで記述される塩基配列のクオリティはPhred Quality Scoreを示す記号で表されます。リードの1塩基ごとに、「その塩基を読み取った(BaseCall)精度」を記述します。

|Phred Quality Score|エラー確率|Base Callの精度|
|:-----------------:|:------:|:------------:|
|10|1/10|90%|
|20|1/100|99%|
|30|1/1000|99.9%|
|40|1/10000|99.99%|

[参考: Phred quality score / Wikipedia](http://en.wikipedia.org/wiki/Phred_quality_score)

----

### FASTA

FASTAはDNA塩基配列を記述するために最もシンプルで一般的なフォーマットです。塩基配列だけでなくアミノ酸配列もこのフォーマットで記述されます。BLASTなどのtradな塩基配列解析においてもよく用いられます。2行で1本の塩基配列を記述します。1行目は">"から始まるコメント、2行目は塩基配列です。

```
>gi|129295|sp|P01013|OVAX_CHICK GENE X PROTEIN (OVALBUMIN-RELATED)
QIKDLLVSSSTDLDTTLVLVNAIYFKGMWKTAFNAEDTREMPFHVTKQESKPVQMMCMNNSFNVATLPAE
KMKILELPFASGDLSMLVLLPDEVSDLERIEKTINFEKLTEWTNPNTMEKRRVKVYLPQMKIEEKYNLTS
VLMALGMTDLFIPSANLTGISSAESLKISQAVHGAFMELSEDGIEMAGSTGVIEDIKHSPESEQFRADHP
FLFLIKHNPTNTIVYFGRYWSP
```

[参考: FASTA format description](http://genetics.bwh.harvard.edu/pph/FASTA.html)

----

### SAM/BAM

新型シーケンサのデータ解析では、得られたリードがゲノムDNAのどの領域に由来するのかを調べるために「マッピング」という操作を行います。これは「リファレンス・マッピング」とも呼ばれます。リファレンス、つまり既に読まれたゲノム配列と、リードのデータをマッピング・ツールに与えると、どのリードがどの領域にアラインメントされたかを示すデータが得られます。SAM(Sequence Alignment/Map format)フォーマットは、このデータを記述するためのもので、BAMフォーマットは、SAMをバイナリ形式に変換したものです。

[参考: Hts-specs by samtools](http://samtools.github.io/hts-specs/)

----

### VCF

VCF(Variant Call Format)は、ゲノム上の塩基配列多型の位置とそのIDや精度を記述するためのフォーマットです。データの他に、日付やデータを生成したプログラム名、リファレンス配列などを記述するメタ情報を含みます。リファレンス配列にマッピングされたリードの情報を多型解析プログラムに入力すると、この形式のファイルが生成されます。

[参考: File Specifications - The Global Alliance Data Working Group File Formats Task Team](http://ga4gh.org/#/fileformats-team)

----

## データ処理について

新型シーケンサから得られる一次データ（シークエンスリードデータ）は、そのままでは何の情報も持たないため、情報解析を行うことができません。そのため、データ解析を行う前にデータ処理を行う必要があります。代表的なものは以下の2つです。

- マッピング
    - ゲノム塩基配列やCDSなどを参照配列（リファレンス）として、それぞれのリードがどの領域から得られたかを調べる
- アセンブル
    - 参照配列がない場合などに、リード同士の端の部分の相同性を元にリードを繋ぎあわせコンティグ配列を得る

どちらの処理も、リードの長さや数、リファレンス配列の大きさ、計算機の性能や並列化などに応じて計算時間が変わります。

----

### DDBJパイプラインの使い方

マッピングやアセンブルのようなデータ処理には大きな計算機が必要でしたが、静岡県三島市にある遺伝学研究所のDDBJが提供する「[DDBJ Read Annotation Pipeline](http://p.ddbj.nig.ac.jp/)」では、DDBJが保有するスーパーコンピュータを用いて、データ処理をウェブブラウザ上の操作のみで行うことができます。

![パイプラインログイン画面](http://gyazo.com/1223f87a21ff578f7ef1dc2a5af1b4f9.png)

----

#### ログイン

アカウントを持っていない場合は「New Account」をクリックして必要な情報を入力、届いたEmailを確認してアカウントを作成してください。今回は「Login as "guest"」を利用して、実際の流れを体験してみます（guestアカウントでは解析の実行はできません）。既にアカウントをお持ちの方は、ユーザIDとパスワードでログインしてください。ログインすると、以下のような画面が表示されます。

![パイプラインログイン後画面](http://gyazo.com/57d015285aea25fba5d816f05c93042e.png)

----

#### データのインポート

解析するデータのインポートには2つの方法があります。

1. 手元のデータをアップロードする
2. 公共DBのデータを利用する

手元のデータをアップロードする場合は、「FTP Upload」をクリックして、「Add New files」をクリックします。

![データアップロード1](http://gyazo.com/e5b8ac1ce1f7faf35ee4860645fb87d5.png)

----

今回は、デモデータをHTTP経由でアップロードします。ページ下部の「Browse and Upload」をクリックして、[こちらのページ](https://www.dropbox.com/sh/vj019i2zwf653t3/AADhYOSz3HRi2GBlDFH_0Sn2a?dl=0)からダウンロードした「demo.fastq」を選択します。今回は軽量なデータなので、すぐに完了しますが、実際のシーケンスデータはFTPでの転送を行ってください。インポートが完了したら「Next Step」をクリックします。

![データアップロード2](http://gyazo.com/72ade75293908c4dc224ca297b617f3f.png)

----

Read Layoutは「Single-end」を選択して、先ほどアップロードしたデータが選択されているのを確認して、「Next Step」をクリックします。

![データアップロード3](http://gyazo.com/48e6373e377cd1906c7bd710a187b470.png)

----

今回はイルミナ社のシーケンサーのデータなので、Instrument Modelは「ILLUMINA」を選択して、Study Titleを入力します。例では「ddbj pipeline demo」と入力しています。入力したら「SUBMIT」をクリックして、確認ダイアログでOKをクリックします。これでデータのインポートは完了です。

![データアップロード4](http://gyazo.com/c2cf2bc93581e45afd87bfee04235bb4.png)

----

公共の新型シーケンスデータ・レポジトリであるDDBJ Sequence Read Archive (DRA)で公開されているデータを利用する場合は、「Import public DRA」を選択して、「Input DRA/ERA/SRA Accession Number」にアクセッション番号を入力し、「Add my DRA entry」をクリックします。今回のデモデータのオリジナルデータの番号は「SRA073646」です。Confirmationのダイアログで「Send a mail when completed importing」をチェックしておくと、アカウントで登録したEmailにインポート終了後、Emailが届きます。（guestアカウントではメールアカウントが登録されていないのでメールは受け取れません）。

![DRAインポート](http://gyazo.com/6c97ca429c34ff65e3d3a66437fbaf3f.png)

----

#### クオリティ値によるフィルタリング

シークエンスエラーの多いリードをそのまま処理すると、誤ったマッピング結果の原因となり、その後の解析での結果解釈に大きな影響があります。ここでは、QV=19を基準としてリードをフィルタリングします。画面左の「Analysis」中の「Step-1」から「Preprocessing」を選択し、「FTP Upload」で先ほどインポートしたデータを選択します。「Next」をクリックします。

![プリプロセス1](http://gyazo.com/b5031e9d4f19b896be1603436aa4d858.png)

----

#### クオリティ値によるフィルタリング

次の画面で、フィルタリングの条件を設定します。Step1では「Phred+33」を、Step2ではQV Thresholdに「19」を入力します。Step3では、QV Thresholdに「14」を、Percentage Thresholdに「30」を入力します。「Next」をクリックして、完了を待ちます。進行状況は、画面左の「JOB STATUS」から確認できます。

![プリプロセス2](http://gyazo.com/16694cd0889a0f4173f3961da15afb9b.png)

----

#### マッピング

続いてマッピングの処理を行います。左側のAnalysisから「Mapping / de novo Assembly」をクリックして、先ほどと同様に「FTP upload」をクリックして、データを選択し、Nextをクリックします。

![マッピング1](http://gyazo.com/83ff6f769cbbdec3c14f58d45a2cf32f.png)

----

「Reference Genome Mapping」が選択されていることを確認して、利用するツールを選択します。今回は代表的なマッピングツールである「BWA」を選択します。Nextをクリックします。

![マッピング2](http://gyazo.com/34c4ef71aae85a2d2960952eea959c72.png)

----

今回はSingle-Endのリードとしてマッピングを行うので、「demo.fastq」を選択して、「confirm」をクリックします。項目が追加されたら、「Next」をクリックします。

![マッピング3](http://gyazo.com/d5f90c311c3bce0e8552e59902cd7027.png)

----

次にリードをマップするリファレンスゲノムを選択します。今回は「Homo sapiens」の「Homo sapiens Feb. 2009 (hg19)」の「All.fa」を選択して、「Next」をクリックします。

![マッピング4](http://gyazo.com/78e21d85eaf70dbe1e05c96fb5701713.png)

----

次にマッピングを行うbwaとその後のファイル操作を行うツールのオプションを設定します。今回は「Step 1) Convert reference sequence」で「-a bwtsw (for large-size reference 2GB~)」を選択する以外は、デフォルトのまま実行します。

![BWAセットアップ](http://gyazo.com/04f0ba3162cfff78c7c6f832daf85403.png)

![samtools](http://gyazo.com/58233afd1cd1868ac3fb20401c0d23cc.png)

----

設定した内容を確認した後、ジョブ終了通知用のメールアドレスを入力し、「Run」をクリックします。（guestアカウントではRUNボタンは表示されません）

![confirm](http://gyazo.com/7e7c7501eef3b5fa704c9dc5d43d7cf3.png)

----

実行したジョブは画面左の「JOB STATUS」から確認することができます。完了したら、「View」をクリックします。

![status](http://gyazo.com/df4588a8895b5cce1ac660cde87c610b.png)

![status-finished](http://gyazo.com/1302cc0e8a0c782f080b40a4cbaafa61.png)

----

「View」をクリックしたあとの画面です。実行されたプロセスとその結果が表示され、結果ファイルのダウンロードができます。

![view](http://gyazo.com/f9d700d2f43691eb53d09e4df77de9b7.png)

----

### その他の資料

DDBJパイプラインの使い方について、[Pipeline Tutorial](http://www.ddbj.nig.ac.jp/search/help/pipeline-tutorial-j.html)をDDBJが提供しています。また、DBCLSでは[今日からはじめるDDBJ Read Annotation Pipeline](http://togotv.dbcls.jp/20100617.html)、[DDBJ Read Annotation Pipelineによるde novo Assembly解析](http://togotv.dbcls.jp/20110226.html#p01)といった[統合TV](http://togotv.dbcls.jp/ja/)も公開しておりますので、ご覧下さい。

----

## データ解析について

新型シーケンサから得られたデータは、マッピング、アセンブルなどのデータ処理を行ったあと、それぞれの目的に応じた解析を行うことで、サンプルの持つ特徴的なゲノム領域をリストアップしたり、サンプル間の差を比べたりすることができます。新型シーケンサを用いた実験は、どのようなシーケンサを使うか（リードの数や長さ）、どのようにサンプルDNAを得るかによって区別することができます。サンプルDNAの抽出方法を工夫することで様々な情報をハイスループットに得られることが新型シーケンサが強力なツールとして用いられる理由ですが、そのシーケンシングの種類は非常に多岐にわたります([*-Seq](http://applications.illumina.com/applications/sequencing/ngs-library-prep/library-prep-methods.html#.VIEmdL6Hq2w))。それぞれの目的に応じた解析の手順と、それに用いられる代表的なツールは、既存研究の論文やオンラインフォーラムで調べることができます。

![*-Seq](http://gyazo.com/9fa560a57003974dbd21ab0b74f48654.png)

----

### データ解析の種類

それぞれのシーケンシングにおける解析については、[次世代シークエンス解析スタンダード〜NGSのポテンシャルを活かしきるWET&DRY](http://www.amazon.co.jp/dp/4758101914/ref=cm_sw_r_tw_dp_40sGub0Q8C567)という書籍としてもまとめられています。非常に参考になります。おすすめです。

![dritoshi-book](http://gyazo.com/783282872dfc90d3b03226a7da1f1596.png)

----

### NGS現場の会

[NGS現場の会](http://www.ngs-field.org)は新型シーケンサを利用した研究に関わる人が集まる研究会です。アカデミア、企業、研究者、テクニシャン、学生などなどの垣根のいっさいない情報交換のための会を目指しています。データ解析で困ったときに助けてくれる人もいます。

![ngs-genbanokai](http://gyazo.com/9587ff44815076cb7b8a17d0ab000064.png)

----

### Galaxyの使い方

Galaxyは複数のツールを組み合わせたデータ解析ワークフローを構築するためのオープンソースでウェブ・ベースの解析プラットフォームです。新型シーケンサのデータ解析に限らず、さまざまな生命科学のデータ解析のために、世界中で広く利用されています。

Galaxyには2つの使い方があります。1つは、Public Galaxy Serverと呼ばれる、世界中で様々な機関によって公開されているサーバを使う方法です。日本ではDBCLS, DDBJ, Pitagora-Galaxyプロジェクトなどによって誰でも利用できるサーバが公開されています。

もう1つの使い方は、自分でGalaxyのサーバを起動する方法です。大量のデータを生み出し大規模にデータを解析する研究室では、専用のGalaxyを立ち上げて利用しています。

- [Galaxy Project](https://usegalaxy.org)
- [Galaxy 101: The first thing you should try](https://usegalaxy.org/u/aun1/p/galaxy101)

----

### 本家Galaxy

今回の講習では本家である[usegalaxy.org](https://usegalaxy.org)を利用します。

![honke](http://gyazo.com/9fde98c4f2f9f271aeddc6697fc30b81.png)

----

### Galaxyの基本的な使い方

Galaxyの画面は常に左サイドバー、中央のブラウザ、右サイドバーに三分割されています。左サイドバーは利用できるツールのリストです。中央のブラウザは選択中のツールをコントロールしたり結果を見るために使います。右サイドバーでは、ツールを実行するごとにプロセスがスタックして、履歴として機能します。履歴表示だけでなく、それぞれのプロセスの情報を見たり、再実行したりできます。

![galaxy-ss](http://gyazo.com/2e7c840ca43c4aacfec29c1cfd7809d6.png)

----

### アカウントの作成

[本家Galaxy]のウェブサイトの右上の「User」をクリックして、「Register」をクリックします。Emailアドレス、パスワード、名前を入力してから「Submit」をクリックします。しばらく待つとメールが届くので、メールの中のアクティベーションのURLをクリックして、confirmしたあと、作成したアカウントでログインします。

![create-account](http://gyazo.com/0a94312f2302baff73fc133f5db3170b.png)

----

### データのインポート

先ほどと同じく[こちらのページ](https://www.dropbox.com/sh/vj019i2zwf653t3/AADhYOSz3HRi2GBlDFH_0Sn2a?dl=0)から「demo.fastq」というデータをダウンロードします。Galaxyの左パネルの「Get Data」をクリックして、開いたリストの一番上にある「Upload File」をクリックするとダイアログが開くので、「Choose local file」をクリックしてダウンロードしたdemo.fastqファイルを選択します。リストの「Genome」をクリックして、「Human Feb. 2009 (hg19)」を選択したら、「Start」をクリックします。しばらく待つと完了するので、「Close」をクリックします。

![import-data](http://gyazo.com/baef9e108d341998b57e920bad879a5b.png)

----

目玉ボタンをクリックしてみましょう。インポートが完了したデータの様子です。

![imported](http://gyazo.com/e4ccd26ba42ae96206c75f5a31a59a48.png)

----

### FastQCを実行する

インポートしたFASTQデータのクオリティを確認します。今回は、最もポピュラーな新型シーケンサデータ用クオリティ・コントロールのツールである[FastQC](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/)を利用します。左パネルの検索ボックスに「fastqc」と入力して出る「FastQC: Read QC」をクリックします。「Short read data from your current history」でdemo.fastqを選んでから「Execute」をクリックすると、プロセスが右パネルの履歴に追加され、しばらくすると実行されます。

![fastqc](http://gyazo.com/3285d55b88827aba4d2949eafbc0de54.png)

----

実行が完了したら、目玉ボタンをクリックします。FastQCの結果を見ることができます。

![fastqc-done](http://gyazo.com/be7b677e1d0e9be85fc917003386b269.png)

----

### ワークフローのインポート

Galaxyではデータのインポート、ツールの選択、実行を繰り返すことでワークフローを作ることができます。ここでは、公開されているワークフローを利用してデータ解析を行ってみます。まず、画面上部の「Shared Data」をクリックして「Published Workflow」をクリックします。

![published-workflow](http://gyazo.com/5a32a7ad6a9eb347fe24da8c357710f8.png)

----

### Exome Sequencing Workflow

左上の検索ボックスに「exome」と入力してエンターキーを押します。表示された「Exome Analysis」をクリックします。ワークフローの概要が表示されるので、右上のフロッピーディスクアイコンの隣にある緑色の+ボタンをクリックします。この操作は、ログインしていないとできないため、失敗した場合はログインできているかを画面上部の「User」から確認してください。

![workflow-detail](http://gyazo.com/dcd266543e799aafad092967662f1044.png)

----

完了しました。「start using this workflow」をクリックします。

![done](http://gyazo.com/7b90fd4fa9396c54f8055972658bcfbe.png)

----

### ワークフローを編集する

表示されたページで再び「Exome Analysis」をクリックして「View」をクリックすると、詳細を見ることができます。このワークフローでは、FASTQデータを入力として、BWAによるマッピングを行ったのち、多型を検出するツールを実行しています。しかし、BWAの操作は時間がかかるので、先ほどDDBJのパイプラインでの結果を使って、この工程をはぶきます。まず、[こちらのページ](https://www.dropbox.com/sh/vj019i2zwf653t3/AADhYOSz3HRi2GBlDFH_0Sn2a?dl=0)から、demo.samをダウンロードします。これを先ほどと同じ手順でgalaxyにインポートしてから、画面上部の「Workflow」をクリックして再度ワークフローのページを表示します。「Imported: Exome Analysis」をクリックして、「Edit」をクリックします。

![workflow](http://gyazo.com/6f4552370aee627688a88d2cc240703c.png)

----

ワークフロー・エディターが表示されます。表示されている「Map with BWA for Illumina」のボックスの✕ボタンをクリックしてこのプロセスを消します。

![workflow-editor](http://gyazo.com/888a21b61ed6623764575d0b443cad1b.png)

----

消えた様子です。

![eliminated](http://gyazo.com/3ff991ff9cb03aeb3b9d936abcc9437b.png)

----

次に、ワークフローを「SAM-to-BAM」からスタートさせるために、2つある「Input dataset」のどちらか片方を消して、残った「Input datase」のボックス右側のボタンをドラッグ＆ドロップで「SAM-to-BAM」のボックスに繋げます。

![connect](http://gyazo.com/ad64ec157767d2bb3e277e45336747f9.png)

----

エディタ右上の歯車ボタンを押して「save」をクリックします。そのあと、同じく歯車ボタンを押して「Run」をクリックします。

![save](http://gyazo.com/876e9066f1abed8afd91a678d90389aa.png)

----

表示されたワークフローの入力ファイルに「demo.sam」が表示されているのを確認したら、中央ページ下部の「Run Workflow」をクリックします。

![edited-workflow](http://gyazo.com/8b6bfcf88cdff6136cdac6e38583e657.png)

----

ワークフローが実行されました。あとは待つだけです。

![running](http://gyazo.com/8973d4fdcece303fc1a63cca24cd0ee6.png)

----

### Pitagora-Galaxy

Galaxyはこのように、ウェブブラウザで誰でも簡単にデータ解析ができるようになっています。しかし、自分のラボで独自のGalaxyを使いたいという場合には、インストールや、ワークフローの構築が難しいという難点があります。そこで、日本国内のGalaxyユーザが集まってお互いのワークフローを持ち寄って、簡単にインストールできる形で配るというプロジェクトが始まりました。それが[Pitagora Galaxy](http://www.pitagora-galaxy.org)です。誰でも使えるテストサイトもありますが、「Oracle VirtualBox」と呼ばれるフリーのソフトウェアを使って簡単にGalaxyを自分のコンピュータにインストールすることができます。また、Amazon Web Service上で動くクラウド版もあります。

![pg](http://gyazo.com/2ff7ed946475c60484d3004a781a69c5.png)

----

## 以上

おつかれさまでした！
