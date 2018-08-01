Google Cloud SDKコマンド

    インスタンスのリスト表示
        gcloud compute instances list
    
    インスタンスの起動
        gcloud compute instances start INSTANCE_NAME
        
    インスタンスへのSSH接続
        1. gcloud compute ssh NAME
        2. gcloud compute ssh USER_NAME@INSTANCE_NAME
        
    インスタンスの停止
        gcloud compute instances stop INSTANCE_NAME