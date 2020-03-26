# Ansible Role: SqlServer\_preinstall
===============================

## Description

本Ansible RoleはMicrosoftのリレーショナルデータベース管理ソフトウェアSQLServerをインストールする前のチェックと準備を行います。<br/>
対象バージョンは以下のバージョンです。  

~~~
	SQLServer 2016 SP1はExpress edition版やDeveloper edition版などがあるので、  
	インストールしたいもののみダウンロードしてください。  

	Microsoft Data platfrom  
	https://www.microsoft.com/en-us/sql-server/sql-server-2016  
~~~

設定内容は以下のとおりです。

- OSに関する情報のチェックおよび設定
	- SQLServerインストール環境のチェック
		- OSバージョン
		- SQLServerインスタンスが既に存在するか
	- 環境情報出力（別途実行可能）  
		- OS  
		- Hardware  
		- Firewall  
- インストール前の環境設定
	- SQLServerインストール環境の設定
	- インストールファイルの転送・解凍

## Supports

- 管理マシン(Ansibleサーバ)
 * Linux系OS（RHEL7）
 * Ansible バージョン 2.4 以上 (動作確認バージョン 2.4, 2.9)
 * Python バージョン 2.x, 3.x  (動作確認バージョン 2.6, 2.7, 3.6)

- 管理対象マシン(構築対象マシン)
 * Windows Server 2016
 * Framework バージョン 3.0 以上
 * Management Framework バージョン 3.0 以上

## Requirements

- 管理マシン(Ansibleサーバ)
  * 管理対象マシンと管理者権限でPowershell通信可能であること。
- 管理対象マシン(構築対象マシン)
  * 管理対象マシンにアクセスできるように配置

## Role Variables

Role の変数値について説明します。

### Mandatory variables

以下の変数は、実行時に、必ず指定します。

- SQLServer共通変数設定
	* VAR\_SQLServer\_OS\_Version：ターゲットホストのOSバージョン
		* 例： Windows Server 2016
	* VAR\_Tmp\_Folder：インストール用一時フォルダ
		* 例： C:\Temp\AnsibleforSQLServer
	* VAR\_Installer\_Type：インストール物件の類型を指定する
		* 例：ISO
	* VAR\_Installer\_Unzip\_Path：展開されたインストール物件のフルパスを指定する
		* 例： E:\
	* VAR\_AllowSkip\_SQLServerSWExist:
        既にSQLServerインスタンスがインストールされた場合、
        処理を中止しないで、事前準備ロールとSQLServerインストールロールをスキップする/しない：  
        true/false（デフォルトはfalse）  
		* 例：true
	* VAR\_INI\_Path：SQLServerインストール用INIファイルの格納パス
		* 例：C:\Temp\AnsibleforSQLServer  
	* VAR\_INSTANCENAME：INIファイルINSTANCENAME変数  
		* 例：MSSQLSERVER
</br>
- インストールの前準備 Role変数
	* VAR\_Sysinfo\_Path：ターゲットホストのシステム情報を確認するパス
		* 例：C:\Temp\AnsibleforSQLServer  
	*  VAR\_Installer\_Name：SQLServerインストールパッケージのファイル名
		*  例：  
		   \-  SW\_DVD9\_SQL\_Svr\_Enterprise\_Edtn\_2016\_64Bit\_Japanese\_MLF\_X20-97280.exe  
           \-  SW\_DVD9\_SQL\_Svr\_Enterprise\_Edtn\_2016\_64Bit\_Japanese\_MLF\_X20-97280.box
		*  例：SW\_DVD9\_SQL\_Svr\_Enterprise\_Edtn\_2016\_64Bit\_Japanese\_MLF\_X20-97280.iso  
	* VAR\_Installer\_Path：SQLServerインストールパッケージのファイル格納パス
		* 例：C:\Tem\AnsibleforSQLServer   
	* VAR\_Installer\_URL：SQLServerインストールパッケージのファイルのWeb格納URL、設定されない場合、直接に変数VAR\_Installer\_Pathから、インストールパッケージのファイルを取得する。
		* 例: http://192.168.1.1:8080
	* VAR\_WinCopy：trueの場合、win\_copyで利用して物件をコピーする。ただVAR\_Installer\_URLが設定された場合、win\_get\_urlで利用して物件をダウンロードする。
		* 例: false
	* VAR\_FireWall\_Rule：SQLServer用ポートを通信可能に設定する
		* 例：	  
		     &nbsp;  &nbsp;- Name: For SQLServer-Database Engine Rule  
             &nbsp;  &nbsp;  &nbsp;Port: 1433  
             &nbsp;  &nbsp;  &nbsp;Protocol: tcp  
             &nbsp;  &nbsp;  &nbsp;Profiles: public  
		* 備考：  
			ファイアウォールを経由して SQL Server にアクセスしたい場合は、ファイアウォールを構成いただく必要があります。  
			どのような設定が必要かは以下をご参照願います。  
			　- SQL Server のアクセスを許可するための Windows ファイアウォールの構成 | Microsoft Docs  
　　			https://docs.microsoft.com/ja-jp/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access  

### Optional variables


### Unchanging variables


## Dependencies

特にありません。

## 制限事項


## 注意事項

* 変数関連
 * 必須変数は必ず指定してください。
 * AnsibleでWebからインストールパッケージのファイルをダウンロードする時、フルパスは{{ VAR\_Installer\_URL }}/{{ VAR\_Installer\_Name }}となります。たとえば、ダウンロードパスは http://download.microsoft.com/sqlserver/win/win\_database.zip の場合、変数VAR\_Installer\_URL＝"http://download.microsoft.com/sqlserver/win" 、変数VAR\_Installer\_Name＝"win\_database.zip"となる必要です。
 * 変数に特殊符号を含める場合（例：「:」など）、あるいは頭が「{」という文字がある場合、両端に「"」を付けることが必要。
 * 変数VAR\_SQLServer\_OS\_Versionの書き方は：Windows Server 2016 大小文字区別、半角スペースあり。OSバージョンを検証する時、完全一致のチェックではなく、部分一致となります。つまり、実際のOSはWindows Server 2016、変数VAR\_SQLServer\_OS\_VersionにWindows Serverが設定されても一致する結論となります。ただ、Win Serverのような値は一致しないと判断されます。
 * VAR\_Installer\_TypeがISOあるいはCMPの場合、Role：SQLServerインストールのみ実施する時、AnsibleでISOファイルあるいはCMPファイルを展開していないため、手動的にそのISOファイルあるいはCMPファイルを展開する必要。そのとき、展開されたフルパスを事前に設定する必要（例：E:\）。VAR\_Installer\_TypeがPATHの場合、直接に展開されたインストール物件のフルパスを設定してください。
* 事前準備
 * 変数VAR\_Installer\_Pathで指定されたフォルダに十分のディスク容量があること（インストール物件および解凍されたもの）。　　

## 備考

* WebでSQLServerインストールパッケージのファイルを共有する方法：  
  Ansibleホストにコマンドラインで以下のように実行して、 http://Ansibleホスト:8080/ でアクセスして、ファイルダウンロードできます。  

~~~sh
	>cd {{本ソースのフォルダ}}/Win2016/playbook/roles/SqlServers_preinstall/files  
	>nohup python -m SimpleHTTPServer 8080 &  
~~~

## Usage  

1. 本Roleを用いたPlaybookを作成します。  
2. SQLServerインスールバイナリは下記のMirosoftサイトからダウンロードして、Ansible管理マシンに配置します。SQLServer 2016 SP1はExpress edition版やDeveloper edition版などがあるので、インストールしたいもののみダウンロードしてください。  
	Microsoft Data platfrom  
	https://www.microsoft.com/en-us/sql-server/sql-server-2016  
3. 必須変数は必ず指定してください。  
4. 任意変数を必要に応じて指定します。  
5. Playbookを実行します。  

## Example Varible

## Example Playbook

今回既にPlaybookの形式を提供したが、もし自分でPlaybookを作成する時、以下の記述を参照してください。  
インストールとすべての設定をする場合は、提供したRoleを"roles"ディレクトリに配置したうえで、
以下のようなPlaybookを作成できます。

- フォルダ構成
~~~
  - group_vars/
      * server1
      * server2
  - host_vars/
      * host1
      * host2
  - roles/
      * SqlServer_install/
      * SqlServer_preinstall/
  - sqlserverinstall.yml
  - AllVariable.yml
  - hosts
~~~

- マスターPlaybook サンプル「sqlserverinstall.yml」

~~~
# sqlserverinstall.yml
---
  - hosts: sql
    roles:
      - role: SqlServer_preinstall
        tags:
          - SqlServer_preinstall   
      - role: SqlServer_install
        tags:
          - SqlServer_install
~~~

## Running Playbook

- extra-varsを利用する場合の実行例

~~~sh
> ansible-playbook sqlserverinstall.yml --extra-vars="@./AllVariable.yml" -i ./hosts  
~~~
- 本roleのみを実行する場合は --tags "SqlServer\_preinstall"を付け加える

~~~sh
> ansible-playbook sqlserverinstall.yml --extra-vars="@./AllVariable.yml" --tags "SqlServer_preinstall" -i ./hosts  
~~~
- 本roleの情報収集のみを実行する場合は --tags  SqlServer\_preinstall\_gathersysinfo"を付け加える

~~~sh
> ansible-playbook sqlserverinstall.yml --extra-vars="@./AllVariable.yml" --tags "SqlServer_preinstall_gathersysinfo" -i ./hosts  
~~~


# Copyright
---------
Copyright (c) 2017 NEC Corporation

# Author Information
------------------
NEC Corporation
