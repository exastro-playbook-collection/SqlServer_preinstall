# SQLServerのインストール
===============================
# Trademarks
-----------
* Linuxは、Linus Torvalds氏の米国およびその他の国における登録商標または商標です。
* RedHat、RHEL、CentOSは、Red Hat, Inc.の米国およびその他の国における登録商標または商標です。
* Windows、PowerShell、SQL Server、.NET Frameworkは、Microsoft Corporation の米国およびその他の国における登録商標または商標です。
* Ansibleは、Red Hat, Inc.の米国およびその他の国における登録商標または商標です。
* pythonは、Python Software Foundationの登録商標または商標です。
* NECは、日本電気株式会社の登録商標または商標です。
* その他、本ロールのコード、ファイルに記載されている会社名および製品名は、各社の登録商標または商標です。

## Description

本プロジェクトのリポジトリで SQL Server データベースに対する、インストールの前準備ロール、SQL Server のインストールおよびインスタンス作成ロールを公開しています。

- SQLServer の対象バージョンは以下のバージョンとなります。  
**・SQLServer 2016 SP1** 
 
- 対応OSは以下となります。  
**・Windows Server 2016**

各Roleの説明はREADME_role.mdをご参照ください。

## Remarks

本プロジェクトのリポジトリで公開している Role には SQLServer のインストールファイルは含まれません。  
利用者がインストールパッケージを準備して、「../roles/SqlServer\_preinstall/files」に登録していただく必要があります。  

## Usage

本Playbookを利用するためには、以下の手順で実施してください。

1.事前の環境構築  
　①管理マシンの準備（RHEL7、Ansible2.4.1）  
　②管理対象マシンの準備（Windows Server 2016）  
　③ SQLServer のインストールパッケージの準備  

2.Playbookの作成  
　①公開のリポジトリからPlaybookを管理マシンにコピーしてください。  
　②パラメータファイルの編集  
　　・各Roleの「defaults/main.yml」にデフォルト値が定義されています。特殊な値を指定したい場合、AllVariable.ymlに設定できます。  
　　　変数の詳細は各RoleのREADME_role.mdファイルを参照してください。  
　　・必須変数は必ず指定する必要があります。  
　　・任意変数は必要に応じて指定してください。  
　③hostsファイルを編集して、[sql]グループの下に管理対象マシンのIPアドレスやホスト名を記入してください。  
　④「../Win2016/playbook/roles/SqlServer\_preinstall/files」に SQLServer のインストール物件をコピーしてください。  
　⑤Webでインストール物件を管理対象マシンに転送したい場合、変数VAR\_Installer\_URLを設定して、以下の例のようにWebサーバを構築してください。  

~~~
	  WebでSQLServerインストールパッケージを共有する方法：  
	  管理マシンにコマンドラインで以下のように実行して、http://管理マシン:8080/でアクセスして、ファイルをダウンロードできます。
	
	    >cd {{本ソースのフォルダ}}/Win2016/playbook/roles/SqlServer_preinstall/files
	    >nohup python -m SimpleHTTPServer 8080 &
~~~

3.ロール実施手順  
　①管理対象マシンの情報採取、採取情報の確認のみの実施<br>
　　インストールの前準備で管理対象マシン情報採取のみを実施して、取得したホストOS情報を確認する。  
　　何か不備があれば、設定を変更してOS情報を再採取する。  

　　``` >ansible-playbook sqlserverinstall.yml -t SqlServer_preinstall_gathersysinfo -e "@./AllVariable.yml" -i ./hosts  ```

　②一括でのロール実行  
　　インストールの前準備、SQLServer インストールおよびインスタンス作成を実施する。  

　　``` >ansible-playbook sqlserverinstall.yml -e "@./AllVariable.yml" -i ./hosts  ```

　③他の実施方法について  
　　下記の「Running Playbook」の記載を参照して、他の実行方法を選択してください。  

## Running Playbook

- 一括でインストールの前準備、SQLServer　インストールおよびインスタンス作成を実施したい場合、下記のとおりで実行する。  

　　①指定のSQLServerのインスタンスが存在しても、処理をスキップしたくない場合  

　　``` >ansible-playbook sqlserverinstall.yml -e "@./AllVariable.yml" -i ./hosts  ```

　　②指定の　SQLServer　のインスタンスが既にインストールされていて、インストールの前準備と SQLServer インストールおよびインスタンス作成をスキップしたい場合  

　　``` >ansible-playbook sqlserverinstall.yml -e "@./AllVariable.yml" -e "VAR_AllowSkip_SQLServerSWExist=true" -i ./hosts  ```

- インストールの前準備のみを実施したい場合、下記のとおりで実行する。  

　　①指定の　SQLServer　のインスタンスが既にインストールされたら、インストールの前準備をスキップしたくない場合  

　　``` >ansible-playbook sqlserverinstall.yml -t SqlServer_preinstall -e "@./AllVariable.yml" -i ./hosts  ```

　　②指定の　SQLServer　のインスタンスが既にインストールされていて、インストールの前準備をスキップしたい場合  

　　``` >ansible-playbook sqlserverinstall.yml -t SqlServer_preinstall -e "@./AllVariable.yml" -e "VAR_AllowSkip_SQLServerSWExist=true" -i ./hosts  ```

- インストールの前準備で管理対象マシン情報採取のみを実施したい場合、下記のとおりで実行する。  

　　``` >ansible-playbook sqlserverinstall.yml -t SqlServer_preinstall_gathersysinfo -e "@./AllVariable.yml" -i ./hosts  ```

- SQLServer　インスタンス作成のみを実施したい場合、下記のとおりで実行する。  

　　①指定の　SQLServer　のインスタンスが存在しても、処理をスキップしたくない場合  

　　``` >ansible-playbook sqlserverinstall.yml -t SqlServer_install -e "@./AllVariable.yml" -i ./hosts  ```

　　②指定の　SQLServer　のインスタンスが既にインストールされていて、処理をスキップしたい場合  

　　``` >ansible-playbook sqlserverinstall.yml -t SqlServer_install -e "@./AllVariable.yml" -e "VAR_AllowSkip_SQLServerSWExist=true" -i ./hosts  ```


## 注意事項

* 変数関連
 * 「VAR\_」で始まる変数は利用者が変更可能のものです。「VAR\_」で始まっていない変数は通常に変更する必要ないが、必要に応じて指定することもできます。<br>
 * 変数 VAR\_SQLServer\_OS\_Version の書き方は：Windows Server 2016 (大小文字区別、半角スペースあり)。  
 OSバージョンを検証する時、完全一致のチェックではなく、部分一致となります。つまり、実際のOSはWindows Server 2016、変数 VAR\_SQLServer\_OS\_Version に Windows Server が設定されても一致する結論となります。ただ、Win Server のような値は一致しないと判断されます。  

<!--
 * 変数に特殊符号を含める場合（例：「:」など）、或は頭が「{」という文字がある場合、両端に「"」を付けることが必要。
 * INIファイルの変数にtrue / falseがある場合は、 ""（二重引用符）を使用してブール変数としてエスケープされないようにします。
 * Role：SQLServerインストール（インスタンス作成も含む）のみ実施する時、AnsibleでISOファイルを展開していないため、手動的にそのISOファイルを展開する必要です。その時、展開されたVAR\_Installer\_Drive\_Letterを事前に設定する必要です。
-->

* 事前準備
 * 変数VAR\_Installer\_Pathに指定されたファイルシステムには十分のディスク容量に空きがあることを確認してください。（インストール物件および解凍されたものがコピーされます）<br>

<!--
 * なるべく以前のインストールで設定された環境変数の内容を残らないように更新すること。
-->

## 構築結果取得の利用について

このRoleには、構築情報取得の定義を行っています。以下のエビデンス情報を取得することが可能です。  

### エビデンス一覧 ##
~~~
#エビデンス構成
.
└── ターゲットホストIPアドレス
    └── command
        ├── 0 		# パッケージ格納先のファイルリスト
        ├── 1 		# Firewallポート情報
        ├── 2 		# ini格納先のファイルリスト
        ├── 3 		# iniファイル
        ├── 4 		# SQL Server関連サービス情報
        ├── 5 		# SQL Server関連レジストリ情報
        ├── 6 		# SQL Server関連ディレクトリのリスト
        ├── 7 		# SQL Server関連ディレクトリのリスト
        ├── 8 		# SQL Server関連ディレクトリのリスト
        └── results.json 	# 各コマンドの情報を格納するJSONファイル
~~~

## 備考

SQLServer インスールバイナリ（ISOファイル）は下記の Mirosoft サイトから試用版がダウンロード可能です。</br>

    Microsoft Data platfrom 
    ​https://www.microsoft.com/en-us/sql-server/sql-server-2016 

	その他  
	SSMS(SQL Server Management Studio) は別途以下サイトからダウンロードしてインストールする必要があります。  
	https://docs.microsoft.com/ja-jp/sql/ssms/download-sql-server-management-studio-ssms (2018年1月現在)  
	※SSMSは今回Ansible対応の対象外とします。必要の場合、手動でインストールしてください。


# Copyright
---------
Copyright (c) 2017 NEC Corporation

# Author Information
------------------
NEC Corporation
