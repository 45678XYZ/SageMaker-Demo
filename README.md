<<<<<<< HEAD
# SageMaker-Demo
=======
# SageMakerDemoNote
> 本篇筆記主要為紀錄在 SageMaker 上訓練模型與建立環境時遇過的問題

### 建立VPC
需要取得外部連線的資源，因此必須建立一個有 NAT 閘道子網路的 VPC（付費）

<img width="1440" height="900" alt="截圖 2025-09-10 晚上10 15 34" src="https://github.com/user-attachments/assets/69a35276-e1cc-4a01-a756-3abd89f0e3c4" />
<img width="1440" height="900" alt="截圖 2025-09-10 晚上10 16 04" src="https://github.com/user-attachments/assets/d30fff01-03b9-499f-b4af-31445bd9e2d5" />

<br>
<br>

### 建立網域
大部分都可照預設，但要記得選取有 NAT 閘道的 **private** 子網路（若連接到 public 可能會無法載入資料）

<img width="1440" height="900" alt="截圖 2025-09-10 晚上10 23 39" src="https://github.com/user-attachments/assets/12b50ace-54a6-4ee3-af2f-5c614b814f19" />

<br>
<br>

### 開啟 SageMaker Unified Studio
從網域開啟 Unified Studio 時，Safari 瀏覽器可能因預設防止跨網站追蹤無法順利開啟

**錯誤訊息：**
Invalid or expired auth token. This could be due to third party cookies being disabled for your browser. Check troubleshooting steps at Amazon SageMaker Unified Studio Troubleshooting

**解決方法：**
`Safari > 設定 > 隱私權 > 取消防止跨網站追蹤`

<br>

### 程式執行說明
`SageMaker Unified Studio > Create project` 建立一個新的 project（設定皆照預設）

<img width="1440" height="900" alt="截圖 2025-09-10 晚上11 46 04" src="https://github.com/user-attachments/assets/d18aceac-f1b2-4e2b-bb86-eb8acc0c4cd8" />

<br>
<br>

`新建立的 project > New > Notebook`

<img width="1440" height="900" alt="截圖 2025-09-10 晚上11 49 37" src="https://github.com/user-attachments/assets/cc99740e-b4a2-4fe0-ae24-cb8f4c95dc61" />

<br>
<br>

`左側 git 圖示 > Clone a Repository > https://github.com/45678XYZ/SageMakerDemo > Clone`

<img width="1440" height="900" alt="截圖 2025-09-10 晚上11 49 03" src="https://github.com/user-attachments/assets/b25f69d8-9d54-4127-941d-cc0b3b96cc94" />

<br>
<br>

載入完畢即可執行 Notebook

<img width="1440" height="900" alt="截圖 2025-09-11 凌晨12 13 16" src="https://github.com/user-attachments/assets/1ee0a42f-3fa7-4f5b-8b34-7dc825d6bbe6" />

<br>
<br>

### IAM 權限
部分功能需要額外開啟權限

**錯誤訊息：**
ClientError: An error occurred (AccessDenied) when calling the CreateMultipartUpload operation: User: 
arn:aws:sts::029448392342:assumed-role/datazone_usr_role_4ysmzknfyhv547_b0z6p40qutfk13/SageMaker is not authorized 
to perform: s3:PutObject on resource: 
"arn:aws:s3:::amazon-sagemaker-029448392342-us-east-1-64d07b55dc34/samples/datasets/imdb/train/data-00000-of-00001.
arrow" because no identity-based policy allows the s3:PutObject action


**解決方法：**
在 Studio 找到該 Project 的 role ARN

<img width="1440" height="900" alt="截圖 2025-09-10 晚上10 30 06" src="https://github.com/user-attachments/assets/9d33bb89-488a-4691-9997-8f8ce4e1ff36" />


<br>
<br>

替該 role 新增權限（會用到 **AmazonS3FullAccess**, **AmazonSageMakerFullAccess**）

`IAM > 角色 > 要新增權限的 role > 新增許可 > 連接政策`

<img width="1464" height="928" alt="截圖 2025-09-10 晚上10 33 02" src="https://github.com/user-attachments/assets/6c51b380-e263-4c06-ace6-c48c21c9763f" />
<img width="1440" height="900" alt="截圖 2025-09-10 晚上10 34 17" src="https://github.com/user-attachments/assets/9ea9d71d-7b59-4985-894d-55377dc36dba" />

<br>
<br>

### 申請 Instance
要用到的 ml.p3.2xlarge 原本預設沒有配額，需要去申請

**錯誤訊息：**
account-level service limit 'ml.p3.2xlarge for training job usage' is 0 Instances, with current utilization of 0

**解決方法：**
`Amazon Service Quotas > 左側 AWS 服務 > 搜尋 SageMaker`

<img width="1440" height="900" alt="截圖 2025-09-10 晚上10 45 30" src="https://github.com/user-attachments/assets/46be34d2-4269-439b-866c-2fdd9ca24548" />

<br>
<br>

點選後再搜尋要用的 instance 名稱 + "for training job usage"，可以從右上角請求增加層級

<img width="1464" height="928" alt="截圖 2025-09-05 下午2 45 12" src="https://github.com/user-attachments/assets/ec62eaa0-ed79-49ea-a817-7befae25784f" />

> [!NOTE]
> 在 `SageMaker Unified Studio > space > configure space` 那邊設置的 instance 是用來執行環境的，不需要太強（預設即可）
>
> 而要用到其他比較強的 instance 則是在 code 裡呼叫的用來跑模型的 instance




>>>>>>> 793c792 (Initial project upload)
