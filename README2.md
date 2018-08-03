Google Cloud SDKコマンド

    インスタンスのリスト表示
        gcloud compute instances list
    
    インスタンスの起動
        gcloud compute instances start {INSTANCE_NAME}
        
    インスタンスへのSSH接続
        1. gcloud compute ssh NAME
        2. gcloud compute ssh {USER_NAME}@{INSTANCE_NAME}
        
    インスタンスの停止
        gcloud compute instances stop {INSTANCE_NAME}
        
Linux(ubuntu)コマンド覚書

    ユーザー一覧
        cat /etc/passwd
        
    ユーザー削除(対象ユーザーのhome以下も削除)
        sudo userdel -r {USER_NAME}
        
    Vimがインストールされているか確認
        dpkg -l vim
        
    Vimのインストール
        sudo apt-get install vim

ソフトウェアインストール
    
    ソフトウェア一覧の更新
        sudo apt-get update
        
    インストール(python3, postgressql, nginx)
        sudo apt-get install python3-pip python3-dev libpq-dev postgresql postgresql-contrib nginx
        
postgresqlコマンド
    
    postgresCLI起動
        sudo -u postgres psql
        (ユーザー名postresはpostgresqlインストール時に作られている)
        
    データベース作成
        CREATE DATABASE {DATABASE_NAME};
        
    ユーザー作成
        CREATE USER {USER_NAME} WITH PASSWORD '{PASSWORD}';
        
    ロールの設定(UTF-8の有効化)
        ALTER ROLE {USER_NAME} SET client_encoding TO 'utf8';
        
    ロールの設定(権限設定:実行結果の参照のみ)
        ALTER ROLE {USER_NAME} SET default_transaction_isolation TO 'read committed';
        
    ロールの設定(タイムゾーン:Tokyo)
        ALTER ROLE {USER_NAME} SET timezone TO 'UTC+9';
        
    ユーザーにデータベース参照権限を付与
        GRANT ALL PRIVILEGES ON DATABASE {DATABASE_NAME} TO {USER_NAME};
        
    データベース一覧表示(末尾にセミコロンは不要)
        \l
        
    ユーザー一覧表示
        SELECT * FROM pg_shadow;
        
    データベース選択
        \c {DATABASE_NAME};
        
    テーブル一覧表示
        \dt;
        
    テーブル構造表示
        \d {TABLE_NAME}
    
    postgresのCLIから出る
        \q
    
    ※postgres起動確認
        ps aux
        
virtualenvの設定
    
    pipのインストール
        sudo -H pip3 install --upgrade pip
        
    virtualenvのインストール
        sudo -H pip3 install virtualenv
        
    仮想環境の作成
        virtualenv {ENV_NAME}
            ls {ENV_NAME}/bin　でactivateが作成されていることを確認
    
    仮想環境の実行
        source {ENV_NAME}/bin/activate
        
必要なpythonライブラリのインストール
    
    djanog、gunicorn、postgres用ライブラリのインストール
        pip install django gunicorn psycopg2
        
    pillowのインストール
        pip install pillow
        
マイグレーション
    
    {APP_NAME}/{APP_NAME}/settings.pyを書きかえる。
       
       アプリ自体の実行可能設定
            ALLOWED_HOSTS = ['実行を許可するマシンのIPアドレス']
        
        postgresへの変更対応
            変更前
                DATABASES = {
                    'default': {
                        'ENGINE': 'django.db.backends.sqlite3',
                        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
                    }
                }
            
            変更後
                DATABASES = {
                    'default': {
                #        'ENGINE': 'django.db.backends.sqlite3',
                        'ENGINE': 'django.db.backends.postgresql_psycopg2',
                #        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
                        'NAME': '{DATABASE_NAME}',
                        'USER': '{USER_NAME}',
                        'PASSWORD': '{USER_PASSWORD}',
                        'HOST': 'localhost',
                        'PORT': '', #特に明記しなければデフォルトが適応される
                    }
                }
                
        pythonでのマイグレーション
            python3 manage.py makemigrations
            python3 manage.py migrate
            
        アプリの起動確認
            python3 manage.py runserver 0.0.0.0:8000
            ※GCPの場合、事前に
                ファイアウォールルート ルール＞default-allow-http プロトコル/ポート
            を tcp:8000 に変更するなどしてサーバーのポート開放を行う必要がある。


管理アカウント作成

    python3 manage.py createsuperuser