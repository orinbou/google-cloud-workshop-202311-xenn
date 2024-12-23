# Cloud Run Test

以下の

* google-cloud-workshop-202311-xenn  
  https://github.com/zenn-dev/google-cloud-workshop-202311-xenn
* Cloud Run のハンズオンで Cloud Shell と teachme コマンドが便利だった  
  https://zenn.dev/waddy/articles/google-cloud-handson-with-teachme

## プロジェクト情報
* 名称：My Project 1021 Cloud Run
* 番号：720475705308
* ＩＤ：my-project-1021-cloud-run

## 前準備
以下のコマンドを実行する
```
gcloud config set project my-project-1021-cloud-run
```
実行結果
```
Updated property [core/project].
sarubobo_com@cloudshell:~ (my-project-1021-cloud-run)$
```

https://github.com/zenn-dev/google-cloud-workshop-202311-xenn
をclone（もしくはZIPダウンロード＆解凍）してルートディレクトリに配置する。

## セットアップ（API）
以下のコマンドを実行する
```
gcloud services enable \
  run.googleapis.com \
  artifactregistry.googleapis.com \
  cloudbuild.googleapis.com \
  cloudresourcemanager.googleapis.com \
  pubsub.googleapis.com \
  sqladmin.googleapis.com \
  sql-component.googleapis.com \
  resourcesettings.googleapis.com \
  iam.googleapis.com
```
実行結果
```
Operation "operations/acf.p2-720475705308-f0853e09-2170-4c78-827e-58f3c297e977" finished successfully.
```

## セットアップ（設定）

### gcloud コマンドの初期化
以下のコマンドを実行する
```
gcloud config set run/region asia-northeast1
gcloud config set run/platform managed
```
実行結果
```
Updated property [run/region].
Updated property [run/platform].
```

### 環境変数の設定
以下のコマンドを実行する
```
export RAILS_MASTER_KEY=e1bcaa95519afaa1c20c33e25a846f4a
export GITHUB_REPOSITORY_NAME=google-cloud-workshop-202311-xenn
export TF_VAR_gcp_project_id=$GOOGLE_CLOUD_PROJECT
export TF_VAR_primary_region="asia-northeast1"
export CLOUD_SQL_CONNECTION_HOST="/cloudsql/${GOOGLE_CLOUD_PROJECT}:$TF_VAR_primary_region:xenn-db"
export CLOUD_SQL_INSTANCE_NAME=$GOOGLE_CLOUD_PROJECT:$TF_VAR_primary_region:xenn-db
export XENN_CLOUD_RUN_SERVICE_ACCOUNT="xenn-cloud-run-runner@${GOOGLE_CLOUD_PROJECT}.iam.gserviceaccount.com"
```

## Terraform の利用開始

### 初期化
以下のコマンドを実行する
```
cd ~/$GITHUB_REPOSITORY_NAME/infra
terraform init
```
実行結果
```
Initializing the backend...

Successfully configured the backend "local"! Terraform will automatically
use this backend unless the backend configuration changes.
Initializing modules...
- artifact-registry in modules/artifact-registry
- cloud-run in modules/cloud-run
- db in modules/cloud-sql

Initializing provider plugins...
- Finding latest version of hashicorp/google...
- Installing hashicorp/google v6.7.0...
- Installed hashicorp/google v6.7.0 (signed by HashiCorp)

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

### Cloud Run 用 サービスアカウントの設定
以下のコマンドを実行する
```
cd ~/$GITHUB_REPOSITORY_NAME/infra
terraform apply -target module.cloud-run
```
実行結果
```
Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # module.cloud-run.google_project_iam_member.backend_runner_cloudsql_client will be created
  + resource "google_project_iam_member" "backend_runner_cloudsql_client" {
      + etag    = (known after apply)
      + id      = (known after apply)
      + member  = (known after apply)
      + project = "my-project-1021-cloud-run"
      + role    = "roles/cloudsql.client"
    }

  # module.cloud-run.google_project_iam_member.secret_accessor_member will be created
  + resource "google_project_iam_member" "secret_accessor_member" {
      + etag    = (known after apply)
      + id      = (known after apply)
      + member  = (known after apply)
      + project = "my-project-1021-cloud-run"
      + role    = "roles/secretmanager.secretAccessor"
    }

  # module.cloud-run.google_service_account.backend_runner will be created
  + resource "google_service_account" "backend_runner" {
      + account_id   = "xenn-cloud-run-runner"
      + disabled     = false
      + display_name = "Cloud Run Service Account"
      + email        = (known after apply)
      + id           = (known after apply)
      + member       = (known after apply)
      + name         = (known after apply)
      + project      = "my-project-1021-cloud-run"
      + unique_id    = (known after apply)
    }

Plan: 3 to add, 0 to change, 0 to destroy.
╷
│ Warning: Resource targeting is in effect
│ 
│ You are creating a plan with the -target option, which means that the result of this plan may not represent all of the changes requested by the current configuration.
│ 
│ The -target option is not for routine use, and is provided only for exceptional situations such as recovering from errors or mistakes, or when Terraform specifically suggests to use it as part of an error
│ message.
╵

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

module.cloud-run.google_service_account.backend_runner: Creating...
module.cloud-run.google_service_account.backend_runner: Still creating... [10s elapsed]
module.cloud-run.google_service_account.backend_runner: Creation complete after 13s [id=projects/my-project-1021-cloud-run/serviceAccounts/xenn-cloud-run-runner@my-project-1021-cloud-run.iam.gserviceaccount.com]
module.cloud-run.google_project_iam_member.secret_accessor_member: Creating...
module.cloud-run.google_project_iam_member.backend_runner_cloudsql_client: Creating...
module.cloud-run.google_project_iam_member.secret_accessor_member: Creation complete after 10s [id=my-project-1021-cloud-run/roles/secretmanager.secretAccessor/serviceAccount:xenn-cloud-run-runner@my-project-1021-cloud-run.iam.gserviceaccount.com]
module.cloud-run.google_project_iam_member.backend_runner_cloudsql_client: Creation complete after 10s [id=my-project-1021-cloud-run/roles/cloudsql.client/serviceAccount:xenn-cloud-run-runner@my-project-1021-cloud-run.iam.gserviceaccount.com]
╷
│ Warning: Applied changes may be incomplete
│ 
│ The plan was created with the -target option in effect, so some changes requested in the configuration may have been ignored and the output values may not be fully updated. Run the following command to
│ verify that no other changes are pending:
│     terraform plan
│ 
│ Note that the -target option is not suitable for routine use, and is provided only for exceptional situations such as recovering from errors or mistakes, or when Terraform specifically suggests to use it
│ as part of an error message.
╵

Apply complete! Resources: 3 added, 0 changed, 0 destroyed.
```

### 他のリソースの作成
以下のコマンドを実行する
```
cd ~/$GITHUB_REPOSITORY_NAME/infra
terraform apply
```
実行結果
```
module.cloud-run.google_service_account.backend_runner: Refreshing state... [id=projects/my-project-1021-cloud-run/serviceAccounts/xenn-cloud-run-runner@my-project-1021-cloud-run.iam.gserviceaccount.com]
module.cloud-run.google_project_iam_member.secret_accessor_member: Refreshing state... [id=my-project-1021-cloud-run/roles/secretmanager.secretAccessor/serviceAccount:xenn-cloud-run-runner@my-project-1021-cloud-run.iam.gserviceaccount.com]
module.cloud-run.google_project_iam_member.backend_runner_cloudsql_client: Refreshing state... [id=my-project-1021-cloud-run/roles/cloudsql.client/serviceAccount:xenn-cloud-run-runner@my-project-1021-cloud-run.iam.gserviceaccount.com]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # module.artifact-registry.google_artifact_registry_repository.xenn-repo will be created
  + resource "google_artifact_registry_repository" "xenn-repo" {
      + create_time      = (known after apply)
      + description      = "XennアプリケーションのDockerリポジトリ"
      + effective_labels = {
          + "goog-terraform-provisioned" = "true"
        }
      + format           = "DOCKER"
      + id               = (known after apply)
      + location         = "asia-northeast1"
      + mode             = "STANDARD_REPOSITORY"
      + name             = (known after apply)
      + project          = "my-project-1021-cloud-run"
      + repository_id    = "xenn-repo"
      + terraform_labels = {
          + "goog-terraform-provisioned" = "true"
        }
      + update_time      = (known after apply)
    }

  # module.db.google_sql_database_instance.xenn-db will be created
  + resource "google_sql_database_instance" "xenn-db" {
      + available_maintenance_versions = (known after apply)
      + connection_name                = (known after apply)
      + database_version               = "POSTGRES_14"
      + deletion_protection            = false
      + dns_name                       = (known after apply)
      + encryption_key_name            = (known after apply)
      + first_ip_address               = (known after apply)
      + id                             = (known after apply)
      + instance_type                  = (known after apply)
      + ip_address                     = (known after apply)
      + maintenance_version            = (known after apply)
      + master_instance_name           = (known after apply)
      + name                           = "xenn-db"
      + private_ip_address             = (known after apply)
      + project                        = "my-project-1021-cloud-run"
      + psc_service_attachment_link    = (known after apply)
      + public_ip_address              = (known after apply)
      + region                         = "asia-northeast1"
      + self_link                      = (known after apply)
      + server_ca_cert                 = (sensitive value)
      + service_account_email_address  = (known after apply)

      + settings {
          + activation_policy     = "ALWAYS"
          + availability_type     = "ZONAL"
          + connector_enforcement = (known after apply)
          + disk_autoresize       = true
          + disk_autoresize_limit = 0
          + disk_size             = 10
          + disk_type             = "PD_SSD"
          + edition               = "ENTERPRISE"
          + pricing_plan          = "PER_USE"
          + tier                  = "db-f1-micro"
          + user_labels           = (known after apply)
          + version               = (known after apply)

          + ip_configuration {
              + ipv4_enabled   = true
              + server_ca_mode = (known after apply)
              + ssl_mode       = (known after apply)
            }
        }
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

module.artifact-registry.google_artifact_registry_repository.xenn-repo: Creating...
module.db.google_sql_database_instance.xenn-db: Creating...
module.artifact-registry.google_artifact_registry_repository.xenn-repo: Still creating... [10s elapsed]
module.db.google_sql_database_instance.xenn-db: Still creating... [10s elapsed]
module.artifact-registry.google_artifact_registry_repository.xenn-repo: Creation complete after 11s [id=projects/my-project-1021-cloud-run/locations/asia-northeast1/repositories/xenn-repo]
module.db.google_sql_database_instance.xenn-db: Still creating... [20s elapsed]
module.db.google_sql_database_instance.xenn-db: Still creating... [30s elapsed]
module.db.google_sql_database_instance.xenn-db: Still creating... [40s elapsed]
:
module.db.google_sql_database_instance.xenn-db: Still creating... [10m30s elapsed]
module.db.google_sql_database_instance.xenn-db: Still creating... [10m40s elapsed]
module.db.google_sql_database_instance.xenn-db: Still creating... [10m50s elapsed]
module.db.google_sql_database_instance.xenn-db: Creation complete after 10m55s [id=xenn-db]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

### データベースユーザーの作成
以下のコマンドを実行する
```
gcloud sql users create postgres \
  --instance=xenn-db \
  --password=handson
```
実行結果
```
Creating Cloud SQL user...done.                                                                                                                                                                                
Created user [postgres].
```

### DBに接続できることを確認
以下のコマンドを実行する
```
gcloud sql connect xenn-db --user=postgres --database=postgres
# password: handson
# postgres => \dt
```
実行結果
```
Allowlisting your IP for incoming connection for 5 minutes...done.                                                                                                                                             
Connecting to database with SQL user [postgres].Password: 
psql: error: connection to server at "35.221.100.109", port 5432 failed: FATAL:  password authentication failed for user "postgres"
connection to server at "35.221.100.109", port 5432 failed: FATAL:  password authentication failed for user "postgres"
```
以下のコマンドを実行する
```
exit
```

## APIアプリケーションのデプロイ
以下のコマンドを実行する
```
cd ~/$GITHUB_REPOSITORY_NAME/api && \
gcloud builds submit . \
--tag asia-northeast1-docker.pkg.dev/$GOOGLE_CLOUD_PROJECT/xenn-repo/xenn-api && \

gcloud run jobs deploy rails-command \
--quiet \
--project=$GOOGLE_CLOUD_PROJECT \
--image=asia-northeast1-docker.pkg.dev/$GOOGLE_CLOUD_PROJECT/xenn-repo/xenn-api \
--service-account=$XENN_CLOUD_RUN_SERVICE_ACCOUNT \
--set-cloudsql-instances=$CLOUD_SQL_INSTANCE_NAME \
--cpu=1 \
--task-timeout=60m \
--max-retries=0 \
--parallelism=1 \
--set-env-vars=RAILS_ENV=production \
--set-env-vars=RAILS_MASTER_KEY=$RAILS_MASTER_KEY \
--set-env-vars=CLOUD_SQL_CONNECTION_HOST=$CLOUD_SQL_CONNECTION_HOST \
--command=bundle,exec,rails \
--args=db:migrate,db:migrate:status && \

gcloud run deploy xenn-api \
--image=asia-northeast1-docker.pkg.dev/$GOOGLE_CLOUD_PROJECT/xenn-repo/xenn-api \
--service-account=$XENN_CLOUD_RUN_SERVICE_ACCOUNT \
--add-cloudsql-instances=$CLOUD_SQL_INSTANCE_NAME \
--allow-unauthenticated \
--set-env-vars=RAILS_ENV=production \
--set-env-vars=RAILS_MASTER_KEY=$RAILS_MASTER_KEY \
--set-env-vars=CLOUD_SQL_CONNECTION_HOST=$CLOUD_SQL_CONNECTION_HOST
```
実行結果
```
Creating temporary archive of 68 file(s) totalling 55.0 KiB before compression.
Some files were not included in the source upload.

Check the gcloud log [/tmp/tmp.s0iKKyh5WV/logs/2024.10.21/11.11.30.296318.log] to see which files and the contents of the
default gcloudignore file used (see `$ gcloud topic gcloudignore` to learn
more).

Uploading tarball of [.] to [gs://my-project-1021-cloud-run_cloudbuild/source/1729509090.723672-9f2bba2d4fbc47b9a5e9fde517511802.tgz]
Created [https://cloudbuild.googleapis.com/v1/projects/my-project-1021-cloud-run/locations/global/builds/ec5f10ac-f441-4b45-bb55-9cafad3421aa].
Logs are available at [ https://console.cloud.google.com/cloud-build/builds/ec5f10ac-f441-4b45-bb55-9cafad3421aa?project=720475705308 ].
Waiting for build to complete. Polling interval: 1 second(s).
--------------------------------------------------------------------------------------------- REMOTE BUILD OUTPUT ----------------------------------------------------------------------------------------------
starting build "ec5f10ac-f441-4b45-bb55-9cafad3421aa"

FETCHSOURCE
Fetching storage object: gs://my-project-1021-cloud-run_cloudbuild/source/1729509090.723672-9f2bba2d4fbc47b9a5e9fde517511802.tgz#1729509092758915
Copying gs://my-project-1021-cloud-run_cloudbuild/source/1729509090.723672-9f2bba2d4fbc47b9a5e9fde517511802.tgz#1729509092758915...
/ [1 files][ 25.6 KiB/ 25.6 KiB]                                                
Operation completed over 1 objects/25.6 KiB.                                     
BUILD
Already have image (with digest): gcr.io/cloud-builders/docker
Sending build context to Docker daemon  126.5kB
Step 1/20 : FROM ruby:3.2.2-slim
3.2.2-slim: Pulling from library/ruby
:
（長いので省略）
:
349729a34532: Pushed
bc7ab3c53adb: Pushed
d349c55f562a: Pushed
571ade696b26: Pushed
latest: digest: sha256:c16de09fa182e60e721dd879cf2bfa5a4edb9f1be3d0794476297a22f1e9b534 size: 4292
DONE
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
ID: ec5f10ac-f441-4b45-bb55-9cafad3421aa
CREATE_TIME: 2024-10-21T11:11:33+00:00
DURATION: 3M2S
SOURCE: gs://my-project-1021-cloud-run_cloudbuild/source/1729509090.723672-9f2bba2d4fbc47b9a5e9fde517511802.tgz
IMAGES: asia-northeast1-docker.pkg.dev/my-project-1021-cloud-run/xenn-repo/xenn-api (+1 more)
STATUS: SUCCESS
Deploying container to Cloud Run job [rails-command] in project [my-project-1021-cloud-run] region [asia-northeast1]
OK Creating job... Done.                                                                                                                                                                                       
Done.                                                                                                                                                                                                          
Job [rails-command] has successfully been deployed.

To execute this job, use:
gcloud run jobs execute rails-command
Deploying container to Cloud Run service [xenn-api] in project [my-project-1021-cloud-run] region [asia-northeast1]
OK Deploying new service... Done.                                                                                                                                                                              
  OK Creating Revision...                                                                                                                                                                                      
  OK Routing traffic...                                                                                                                                                                                        
  OK Setting IAM Policy...                                                                                                                                                                                     
Done.                                                                                                                                                                                                          
Service [xenn-api] revision [xenn-api-00001-xhb] has been deployed and is serving 100 percent of traffic.
Service URL: https://xenn-api-720475705308.asia-northeast1.run.app
```

## DBマイグレーションの実行
以下のコマンドを実行する
```
gcloud run jobs execute rails-command --wait
```
実行結果
```
OK Creating execution... Done.                                                                                                                                                                                 
  OK Provisioning resources...                                                                                                                                                                                 
  OK Starting execution...                                                                                                                                                                                     
  OK Running execution... 1 / 1 complete                                                                                                                                                                       
Done.                                                                                                                                                                                                          
Execution [rails-command-5pgt6] has successfully completed.

View details about this execution by running:
gcloud run jobs executions describe rails-command-5pgt6

Or visit https://console.cloud.google.com/run/jobs/executions/details/asia-northeast1/rails-command-5pgt6/tasks?project=720475705308
```

### DBテーブルが作成されたことを確認
以下のコマンドを実行する
```
gcloud sql connect xenn-db --user=postgres --database=postgres
# password: handson
# postgres => \dt
```
実行結果
```
Allowlisting your IP for incoming connection for 5 minutes...done.                                                                                                                                             
Connecting to database with SQL user [postgres].Password: 
psql (16.4 (Ubuntu 16.4-1.pgdg24.04+2), server 14.13)
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, compression: off)
Type "help" for help.
```
以下のコマンドを実行する
```
exit
```

### curl リクエスト実行
以下のコマンドを実行する
```
XENN_API_ROOT_URL=$(gcloud run services describe xenn-api --region asia-northeast1 --format json | jq -r '.status.url')
curl $XENN_API_ROOT_URL/articles
```
実行結果
```
{"articles":[]}
```

### DBデータSEEDの実行
以下のコマンドを実行する
```
gcloud run jobs execute rails-command --wait \
--args=db:seed
```
実行結果
```
OK Creating execution... Done.                                                                                                                                                                                 
  OK Provisioning resources...                                                                                                                                                                                 
  OK Starting execution...                                                                                                                                                                                     
  OK Running execution... 1 / 1 complete                                                                                                                                                                       
Done.                                                                                                                                                                                                          
Execution [rails-command-jfxsn] has successfully completed.

View details about this execution by running:
gcloud run jobs executions describe rails-command-jfxsn

Or visit https://console.cloud.google.com/run/jobs/executions/details/asia-northeast1/rails-command-jfxsn/tasks?project=720475705308
```

### curl リクエスト実行（再）
以下のコマンドを実行する
```
XENN_API_ROOT_URL=$(gcloud run services describe xenn-api --region asia-northeast1 --format json | jq -r '.status.url')
curl $XENN_API_ROOT_URL/articles
```
実行結果
```
{"articles":[{"id":4,"title":"Google Cloud Next Tokyo ’23 で アプリ・生成AI・BigQueryあたりのセッションに参加したレポート","slug":"google-cloud-next-tokyo-2023-report","created_at":"2024-10-21T11:23:22.292Z"},{"id":3,"title":"ビストロ 大塚","slug":"c8bca1bb1e508f","created_at":"2024-10-21T11:23:22.279Z"},{"id":2,"title":"カフェ ブルー中野","slug":"a1200070fa2b1b","created_at":"2024-10-21T11:23:22.263Z"},{"id":1,"title":"バー ハングリー","slug":"de24dc1bcef6a2","created_at":"2024-10-21T11:23:22.246Z"}]}
```

## Webアプリケーションのデプロイ

### バックエンドアプリのURLを.envへ反映
以下のコマンドを実行する
```
XENN_API_ROOT_URL=$(gcloud run services describe xenn-api --region asia-northeast1 --format json | jq -r '.status.url')
cd ~/$GITHUB_REPOSITORY_NAME/web && \
cat << EOF > .env.production
NEXT_PUBLIC_API_ROOT=$XENN_API_ROOT_URL
EOF
```

### デプロイ
以下のコマンドを実行する
```
cd ~/$GITHUB_REPOSITORY_NAME/web && \
gcloud builds submit . \
--tag asia-northeast1-docker.pkg.dev/$GOOGLE_CLOUD_PROJECT/xenn-repo/xenn-web && \

gcloud run deploy xenn-web \
--quiet \
--image=asia-northeast1-docker.pkg.dev/$GOOGLE_CLOUD_PROJECT/xenn-repo/xenn-web \
--service-account=$XENN_CLOUD_RUN_SERVICE_ACCOUNT \
--no-use-http2 \
--allow-unauthenticated
```
実行結果
```
Creating temporary archive of 27 file(s) totalling 183.5 KiB before compression.
Uploading tarball of [.] to [gs://my-project-1021-cloud-run_cloudbuild/source/1729510017.217774-33d3638c26674076a06799c97265f090.tgz]
Created [https://cloudbuild.googleapis.com/v1/projects/my-project-1021-cloud-run/locations/global/builds/f9f92305-8389-4fc5-9b80-ef3908a5bc86].
Logs are available at [ https://console.cloud.google.com/cloud-build/builds/f9f92305-8389-4fc5-9b80-ef3908a5bc86?project=720475705308 ].
Waiting for build to complete. Polling interval: 1 second(s).
--------------------------------------------------------------------------------------------- REMOTE BUILD OUTPUT ----------------------------------------------------------------------------------------------
starting build "f9f92305-8389-4fc5-9b80-ef3908a5bc86"

FETCHSOURCE
Fetching storage object: gs://my-project-1021-cloud-run_cloudbuild/source/1729510017.217774-33d3638c26674076a06799c97265f090.tgz#1729510018267080
Copying gs://my-project-1021-cloud-run_cloudbuild/source/1729510017.217774-33d3638c26674076a06799c97265f090.tgz#1729510018267080...
/ [1 files][ 75.6 KiB/ 75.6 KiB]                                                
Operation completed over 1 objects/75.6 KiB.
BUILD
Already have image (with digest): gcr.io/cloud-builders/docker
Sending build context to Docker daemon  215.6kB
Step 1/15 : FROM node:20.9-slim AS builder
20.9-slim: Pulling from library/node
1f7ce2fa46ab: Pulling fs layer
be65943961fd: Pulling fs layer
1a8f879c1134: Pulling fs layer
644a1ba376b1: Pulling fs layer
19a23cad7be5: Pulling fs layer
644a1ba376b1: Waiting
19a23cad7be5: Waiting
be65943961fd: Verifying Checksum
be65943961fd: Download complete
644a1ba376b1: Verifying Checksum
644a1ba376b1: Download complete
1f7ce2fa46ab: Verifying Checksum
1f7ce2fa46ab: Download complete
19a23cad7be5: Verifying Checksum
19a23cad7be5: Download complete
1a8f879c1134: Verifying Checksum
1a8f879c1134: Download complete
1f7ce2fa46ab: Pull complete
be65943961fd: Pull complete
1a8f879c1134: Pull complete
644a1ba376b1: Pull complete
19a23cad7be5: Pull complete
Digest: sha256:d272d96f3ad3a4e5bb2b6c36ea7427b4e83d1b23fb24b9df8b71d01aa59951b1
Status: Downloaded newer image for node:20.9-slim
 32ba799eca0f
Step 2/15 : WORKDIR /app
 Running in 462e18939194
Removing intermediate container 462e18939194
 77fc26679338
Step 3/15 : COPY package.json ./
 efbad56984e4
Step 4/15 : COPY yarn.lock ./
 c0a87d08ccc8
Step 5/15 : RUN yarn install --frozen-lockfile --production=false
 Running in 89c7e3281a92
yarn install v1.22.19
[1/4] Resolving packages...
[2/4] Fetching packages...
[3/4] Linking dependencies...
warning "zenn-markdown-html > markdown-it-anchor@8.6.7" has unmet peer dependency "@types/markdown-it@*".
[4/4] Building fresh packages...
Done in 52.80s.
Removing intermediate container 89c7e3281a92
 c4f8cfaa5f79
Step 6/15 : COPY . .
 623462ec0a96
Step 7/15 : RUN yarn build
 Running in 8b1b218d66df
yarn run v1.22.19
$ next build
Attention: Next.js now collects completely anonymous telemetry regarding usage.
This information is used to shape Next.js' roadmap and prioritize features.
You can learn more, including how to opt-out if you'd not like to participate in this anonymous program, by visiting the following URL:
https://nextjs.org/telemetry
   ▲ Next.js 14.0.2
   - Environments: .env.production

   Creating an optimized production build ...
Browserslist: caniuse-lite is outdated. Please run:
  npx update-browserslist-db@latest
  Why you should do it regularly: https://github.com/browserslist/update-db#readme
Browserslist: caniuse-lite is outdated. Please run:
  npx browserslist@latest --update-db
  Why you should do it regularly: https://github.com/browserslist/browserslist#browsers-data-updating
 ✓ Compiled successfully
   Linting and checking validity of types ...
   Collecting page data ...
   Generating static pages (0/7) ...
   Generating static pages (1/7) 
   Generating static pages (3/7)
   Generating static pages (5/7) 
 ✓ Generating static pages (7/7)
   Finalizing page optimization ...
   Collecting build traces ...

Route (app)                              Size     First Load JS
┌ ○ /                                    141 B          84.6 kB
├ ○ /_not-found                          876 B          85.3 kB
├ λ /articles                            141 B          84.6 kB
├ λ /articles/[slug]                     180 B          84.6 kB
├ λ /articles/[slug]/edit                1.81 kB         197 kB
└ λ /articles/new                        1.81 kB         197 kB
+ First Load JS shared by all            84.5 kB
  ├ chunks/472-5c02fff33de24029.js       29.2 kB
  ├ chunks/fd9d1056-02d435321a393d23.js  53.3 kB
  ├ chunks/main-app-ce281d8555633725.js  219 B
  └ chunks/webpack-b0e24464cc2141d7.js   1.74 kB


○  (Static)   prerendered as static content
λ  (Dynamic)  server-rendered on demand using Node.js

Done in 57.87s.
Removing intermediate container 8b1b218d66df
 5225fa8744aa
Step 8/15 : FROM node:20.9-slim AS runner
 32ba799eca0f
Step 9/15 : ENV NODE_ENV=production
 Running in 4c9b5c905240
Removing intermediate container 4c9b5c905240
 01a96677d208
Step 10/15 : WORKDIR /app
 Running in 538fcefe8203
Removing intermediate container 538fcefe8203
 f340456f2b76
Step 11/15 : COPY --from=builder /app/next.config.js ./
 f02e55245a1a
Step 12/15 : COPY --from=builder /app/public ./public
 40553f3983a3
Step 13/15 : COPY --from=builder /app/.next/static ./.next/static
 8e53cdbebd63
Step 14/15 : COPY --from=builder /app/.next/standalone ./
 cc8be09ba15c
Step 15/15 : CMD ["node", "server.js"]
 Running in bce3dd84c521
Removing intermediate container bce3dd84c521
 931092864deb
Successfully built 931092864deb
Successfully tagged asia-northeast1-docker.pkg.dev/my-project-1021-cloud-run/xenn-repo/xenn-web:latest
PUSH
Pushing asia-northeast1-docker.pkg.dev/my-project-1021-cloud-run/xenn-repo/xenn-web
The push refers to repository [asia-northeast1-docker.pkg.dev/my-project-1021-cloud-run/xenn-repo/xenn-web]
989f250e09f0: Preparing
95b46dcf692c: Preparing
dd9092658792: Preparing
7403c9d6c2cf: Preparing
03e43670cbb0: Preparing
c3505f9ccbec: Preparing
29dfa9898e17: Preparing
63c5ccbc8d4f: Preparing
9547708d2625: Preparing
92770f546e06: Preparing
c3505f9ccbec: Waiting
29dfa9898e17: Waiting
63c5ccbc8d4f: Waiting
92770f546e06: Waiting
9547708d2625: Waiting
03e43670cbb0: Pushed
7403c9d6c2cf: Pushed
dd9092658792: Pushed
95b46dcf692c: Pushed
989f250e09f0: Pushed
c3505f9ccbec: Pushed
9547708d2625: Pushed
29dfa9898e17: Pushed
92770f546e06: Pushed
63c5ccbc8d4f: Pushed
latest: digest: sha256:e29c62b66735bd095e57e0ac902a6f47dbae20421927fd51d6ee86982c22ab91 size: 2410
DONE
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
ID: f9f92305-8389-4fc5-9b80-ef3908a5bc86
CREATE_TIME: 2024-10-21T11:26:59+00:00
DURATION: 3M4S
SOURCE: gs://my-project-1021-cloud-run_cloudbuild/source/1729510017.217774-33d3638c26674076a06799c97265f090.tgz
IMAGES: asia-northeast1-docker.pkg.dev/my-project-1021-cloud-run/xenn-repo/xenn-web (+1 more)
STATUS: SUCCESS
Deploying container to Cloud Run service [xenn-web] in project [my-project-1021-cloud-run] region [asia-northeast1]
OK Deploying new service... Done.                                                                                                                                                                              
  OK Creating Revision...                                                                                                                                                                                      
  OK Routing traffic...                                                                                                                                                                                        
  OK Setting IAM Policy...                                                                                                                                                                                     
Done.                                                                                                                                                                                                          
Service [xenn-web] revision [xenn-web-00001-gls] has been deployed and is serving 100 percent of traffic.
Service URL: https://xenn-web-720475705308.asia-northeast1.run.app
```

## いろいろためす

https://xenn-web-720475705308.asia-northeast1.run.app

![img_Xenn_Top.png](images/img_Xenn_Top.png)

Xennの機能をひととおり触ってみましょう。

* 記事の一覧
* 記事の詳細
* 新しい記事の作成

* Google Cloud Console で作成されたリソースを画面からみてみましょう。

* Google Cloud Console で Cloud SQL をのぞいてみる
* Google Cloud Console で Cloud Run をのぞいてみる

* このようなことをやってみましょう。


## Terraform の利用終了（リソースの削除）

### 他のリソースの削除
以下のコマンドを実行する
```
cd ~/$GITHUB_REPOSITORY_NAME/infra
terraform destroy
```
実行結果
```
module.cloud-run.google_service_account.backend_runner: Refreshing state... [id=projects/my-project-1021-cloud-run/serviceAccounts/xenn-cloud-run-runner@my-project-1021-cloud-run.iam.gserviceaccount.com]
module.artifact-registry.google_artifact_registry_repository.xenn-repo: Refreshing state... [id=projects/my-project-1021-cloud-run/locations/asia-northeast1/repositories/xenn-repo]
module.db.google_sql_database_instance.xenn-db: Refreshing state... [id=xenn-db]
module.cloud-run.google_project_iam_member.backend_runner_cloudsql_client: Refreshing state... [id=my-project-1021-cloud-run/roles/cloudsql.client/serviceAccount:xenn-cloud-run-runner@my-project-1021-cloud-run.iam.gserviceaccount.com]
module.cloud-run.google_project_iam_member.secret_accessor_member: Refreshing state... [id=my-project-1021-cloud-run/roles/secretmanager.secretAccessor/serviceAccount:xenn-cloud-run-runner@my-project-1021-cloud-run.iam.gserviceaccount.com]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # module.artifact-registry.google_artifact_registry_repository.xenn-repo will be destroyed
  - resource "google_artifact_registry_repository" "xenn-repo" {
      - cleanup_policy_dry_run = false -> null
      - create_time            = "2024-10-21T10:51:53.007790Z" -> null
      - description            = "XennアプリケーションのDockerリポジトリ" -> null
      - effective_labels       = {
          - "goog-terraform-provisioned" = "true"
        } -> null
      - format                 = "DOCKER" -> null
      - id                     = "projects/my-project-1021-cloud-run/locations/asia-northeast1/repositories/xenn-repo" -> null
      - labels                 = {} -> null
      - location               = "asia-northeast1" -> null
      - mode                   = "STANDARD_REPOSITORY" -> null
      - name                   = "xenn-repo" -> null
      - project                = "my-project-1021-cloud-run" -> null
      - repository_id          = "xenn-repo" -> null
      - terraform_labels       = {
          - "goog-terraform-provisioned" = "true"
        } -> null
      - update_time            = "2024-11-05T02:23:09.566789Z" -> null
    }

  # module.cloud-run.google_project_iam_member.backend_runner_cloudsql_client will be destroyed
  - resource "google_project_iam_member" "backend_runner_cloudsql_client" {
      - etag    = "BwYk+nBFfyA=" -> null
      - id      = "my-project-1021-cloud-run/roles/cloudsql.client/serviceAccount:xenn-cloud-run-runner@my-project-1021-cloud-run.iam.gserviceaccount.com" -> null
      - member  = "serviceAccount:xenn-cloud-run-runner@my-project-1021-cloud-run.iam.gserviceaccount.com" -> null
      - project = "my-project-1021-cloud-run" -> null
      - role    = "roles/cloudsql.client" -> null
    }

  # module.cloud-run.google_project_iam_member.secret_accessor_member will be destroyed
  - resource "google_project_iam_member" "secret_accessor_member" {
      - etag    = "BwYk+nBFfyA=" -> null
      - id      = "my-project-1021-cloud-run/roles/secretmanager.secretAccessor/serviceAccount:xenn-cloud-run-runner@my-project-1021-cloud-run.iam.gserviceaccount.com" -> null
      - member  = "serviceAccount:xenn-cloud-run-runner@my-project-1021-cloud-run.iam.gserviceaccount.com" -> null
      - project = "my-project-1021-cloud-run" -> null
      - role    = "roles/secretmanager.secretAccessor" -> null
    }

  # module.cloud-run.google_service_account.backend_runner will be destroyed
  - resource "google_service_account" "backend_runner" {
      - account_id   = "xenn-cloud-run-runner" -> null
      - disabled     = false -> null
      - display_name = "Cloud Run Service Account" -> null
      - email        = "xenn-cloud-run-runner@my-project-1021-cloud-run.iam.gserviceaccount.com" -> null
      - id           = "projects/my-project-1021-cloud-run/serviceAccounts/xenn-cloud-run-runner@my-project-1021-cloud-run.iam.gserviceaccount.com" -> null
      - member       = "serviceAccount:xenn-cloud-run-runner@my-project-1021-cloud-run.iam.gserviceaccount.com" -> null
      - name         = "projects/my-project-1021-cloud-run/serviceAccounts/xenn-cloud-run-runner@my-project-1021-cloud-run.iam.gserviceaccount.com" -> null
      - project      = "my-project-1021-cloud-run" -> null
      - unique_id    = "112615968347065308688" -> null
    }

  # module.db.google_sql_database_instance.xenn-db will be destroyed
  - resource "google_sql_database_instance" "xenn-db" {
      - available_maintenance_versions = [
          - "POSTGRES_14_13.R20240910.01_17",
        ] -> null
      - connection_name                = "my-project-1021-cloud-run:asia-northeast1:xenn-db" -> null
      - database_version               = "POSTGRES_14" -> null
      - deletion_protection            = false -> null
      - first_ip_address               = "35.221.100.109" -> null
      - id                             = "xenn-db" -> null
      - instance_type                  = "CLOUD_SQL_INSTANCE" -> null
      - ip_address                     = [
          - {
              - ip_address     = "35.221.100.109"
              - time_to_retire = ""
              - type           = "PRIMARY"
            },
          - {
              - ip_address     = "34.84.21.200"
              - time_to_retire = ""
              - type           = "OUTGOING"
            },
        ] -> null
      - maintenance_version            = "POSTGRES_14_13.R20240910.01_04" -> null
      - name                           = "xenn-db" -> null
      - project                        = "my-project-1021-cloud-run" -> null
      - public_ip_address              = "35.221.100.109" -> null
      - region                         = "asia-northeast1" -> null
      - self_link                      = "https://sqladmin.googleapis.com/sql/v1beta4/projects/my-project-1021-cloud-run/instances/xenn-db" -> null
      - server_ca_cert                 = (sensitive value) -> null
      - service_account_email_address  = "p720475705308-rokscu@gcp-sa-cloud-sql.iam.gserviceaccount.com" -> null

      - settings {
          - activation_policy            = "ALWAYS" -> null
          - availability_type            = "ZONAL" -> null
          - connector_enforcement        = "NOT_REQUIRED" -> null
          - deletion_protection_enabled  = false -> null
          - disk_autoresize              = true -> null
          - disk_autoresize_limit        = 0 -> null
          - disk_size                    = 10 -> null
          - disk_type                    = "PD_SSD" -> null
          - edition                      = "ENTERPRISE" -> null
          - enable_dataplex_integration  = false -> null
          - enable_google_ml_integration = false -> null
          - pricing_plan                 = "PER_USE" -> null
          - tier                         = "db-f1-micro" -> null
          - user_labels                  = {} -> null
          - version                      = 17 -> null

          - backup_configuration {
              - binary_log_enabled             = false -> null
              - enabled                        = false -> null
              - point_in_time_recovery_enabled = false -> null
              - start_time                     = "18:00" -> null
              - transaction_log_retention_days = 7 -> null

              - backup_retention_settings {
                  - retained_backups = 7 -> null
                  - retention_unit   = "COUNT" -> null
                }
            }

          - ip_configuration {
              - enable_private_path_for_google_cloud_services = false -> null
              - ipv4_enabled                                  = true -> null
              - server_ca_mode                                = "GOOGLE_MANAGED_INTERNAL_CA" -> null
              - ssl_mode                                      = "ALLOW_UNENCRYPTED_AND_ENCRYPTED" -> null
            }

          - location_preference {
              - zone = "asia-northeast1-b" -> null
            }
        }
    }

Plan: 0 to add, 0 to change, 5 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes 

module.cloud-run.google_project_iam_member.backend_runner_cloudsql_client: Destroying... [id=my-project-1021-cloud-run/roles/cloudsql.client/serviceAccount:xenn-cloud-run-runner@my-project-1021-cloud-run.iam.gserviceaccount.com]
module.cloud-run.google_project_iam_member.secret_accessor_member: Destroying... [id=my-project-1021-cloud-run/roles/secretmanager.secretAccessor/serviceAccount:xenn-cloud-run-runner@my-project-1021-cloud-run.iam.gserviceaccount.com]
module.artifact-registry.google_artifact_registry_repository.xenn-repo: Destroying... [id=projects/my-project-1021-cloud-run/locations/asia-northeast1/repositories/xenn-repo]
module.db.google_sql_database_instance.xenn-db: Destroying... [id=xenn-db]
module.artifact-registry.google_artifact_registry_repository.xenn-repo: Destruction complete after 1s
module.cloud-run.google_project_iam_member.secret_accessor_member: Destruction complete after 9s
module.cloud-run.google_project_iam_member.backend_runner_cloudsql_client: Destruction complete after 9s
module.cloud-run.google_service_account.backend_runner: Destroying... [id=projects/my-project-1021-cloud-run/serviceAccounts/xenn-cloud-run-runner@my-project-1021-cloud-run.iam.gserviceaccount.com]
module.db.google_sql_database_instance.xenn-db: Still destroying... [id=xenn-db, 10s elapsed]
module.cloud-run.google_service_account.backend_runner: Destruction complete after 2s
module.db.google_sql_database_instance.xenn-db: Still destroying... [id=xenn-db, 20s elapsed]
module.db.google_sql_database_instance.xenn-db: Still destroying... [id=xenn-db, 30s elapsed]
module.db.google_sql_database_instance.xenn-db: Still destroying... [id=xenn-db, 40s elapsed]
module.db.google_sql_database_instance.xenn-db: Still destroying... [id=xenn-db, 50s elapsed]
module.db.google_sql_database_instance.xenn-db: Still destroying... [id=xenn-db, 1m0s elapsed]
module.db.google_sql_database_instance.xenn-db: Still destroying... [id=xenn-db, 1m10s elapsed]
module.db.google_sql_database_instance.xenn-db: Still destroying... [id=xenn-db, 1m20s elapsed]
module.db.google_sql_database_instance.xenn-db: Still destroying... [id=xenn-db, 1m30s elapsed]
module.db.google_sql_database_instance.xenn-db: Still destroying... [id=xenn-db, 1m40s elapsed]
module.db.google_sql_database_instance.xenn-db: Still destroying... [id=xenn-db, 1m50s elapsed]
module.db.google_sql_database_instance.xenn-db: Destruction complete after 1m55s

Destroy complete! Resources: 5 destroyed.
```

### Cloud Run 用 サービスアカウントの削除
以下のコマンドを実行する
```
cd ~/$GITHUB_REPOSITORY_NAME/infra
terraform destroy -target module.cloud-run
```
実行結果
```
No changes. No objects need to be destroyed.

Either you have not created any objects yet or the existing objects were already deleted outside of Terraform.
╷
│ Warning: Resource targeting is in effect
│ 
│ You are creating a plan with the -target option, which means that the result of this plan may not represent all of the changes requested by the current configuration.
│ 
│ The -target option is not for routine use, and is provided only for exceptional situations such as recovering from errors or mistakes, or when Terraform specifically suggests to use it as part of an error
│ message.
╵
╷
│ Warning: Applied changes may be incomplete
│ 
│ The plan was created with the -target option in effect, so some changes requested in the configuration may have been ignored and the output values may not be fully updated. Run the following command to
│ verify that no other changes are pending:
│     terraform plan
│ 
│ Note that the -target option is not suitable for routine use, and is provided only for exceptional situations such as recovering from errors or mistakes, or when Terraform specifically suggests to use it
│ as part of an error message.
╵

Destroy complete! Resources: 0 destroyed.
```

# 参考

* Google Cloud PlatformのCloud Runに入門してみる  
  https://www.mof-mof.co.jp/tech-blog/cloud-run-sample_copy


