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
    
Gunicornの導入
    
    単体で動かす
        gunicornがインストールされているか確認
            which gunicorn
        
        gunicornの起動
            gunicorn --bind 0.0.0.0:8000 {APP_NAME}.wsgi
        
    単体での自動起動設定
    
        システムファイルの確認
            ls /etc/systemd/system
            
        参考:sshd.serviceファイルの内容を確認
            vi /etc/systemd/system/sshd.service
            
        権限確認
            ls -la /etc/systemd/system
            
        gunicorn.serviceファイルを作成
            sudo vi /etc/systemd/system/gunicorn.service
                
                [Unit]
                Description=gunicorn daemon
                After=network.target
                
                [Service]
                User=cjx_open_d
                Group=www-data
                WorkingDirectory=/home/{USER_NAME}/{APP_NAME}
                ExecStart={virtualenv's directory}/bin/gunicorn --access-logfile - --wokers 3  --bind unix:/home/{USER_NAME}/{APP_NAME}/{APP_NAME}.sock {APP_NAME}.wsgi:application
                
                [Install]
                WantedBy=multiuser.target
        
        gunicornのシステムコントロールへ登録
            sudo systemctl start gunicorn
            
        gunicornの(自動)起動
            sudo systemctl enable gunicorn
            
        gunicorn.serviceの存在確認
            ls /etc/systemd/system/
            
        gunicorn.serviceの存在確認
            ls /etc/systemd/system/multi-user.target.wants
            
        sockファイルの存在確認
            ls /home/{USER_NAME}/{APP_NAME}
            
        gunicornの動作確認
            sudo systemctl status gunicorn
            (logファイルとして確認)
            sudo journalctl -u gunicorn

nginxの設定
    
    アプリ用設定ファイル作成先ディレクトリ
        cd /etc/nginx/sites-available/
    
    ファイル作成
        sudo vi {APP_NAME}
            
            server {
                    listen 80;
                    server_name {PUBLIC_IP};
                    
                    location = /favicon.ico {access_log off; log_not_found off;}
                    location /static/ {
                                root /home/{USER_NAME}/{APP_NAME};
                    }
                    
                    location / {
                            include proxy_params;
                            proxy_pass http://unix:/home/{USER_NAME}/{APP_NAME}/{APP_NAME}.sock;
                    }
            }
            
    シンボリックリンクの作成
        sudo ln -s /etc/nginx/sites-available/{APP_NAME} /etc/nginx/sites-enabled/
        
    確認
        ls -la /etc/nginx/sites-enabled/
        
    設定のテスト
        sudo nginx -t
        
    nginxの再起動
        sudo systemctl restart nginx
        
    8000番ポートの無効化
        sudo ufw delete allow 8000
    
    80番ポートの有効化
        sudo ufw allow 'Nginx Full'
            (GCPコンソールで80番を開放)
    
    gunicornの再起動
        sudo systemctl restart gunicorn
        
    staticフォルダの有効化
        sudo vi /etc/nginx/sites-available/{APP_NAME}
            
            location /static/admin {
                    root /home/{USER_NAME}/{virtualenv}/lib/python3.5/site-packages/django/contrib/admin;

            }
            
    nginxの再起動
        sudo systemctl restart nginx