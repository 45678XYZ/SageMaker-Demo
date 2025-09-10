# SageMakerDemoNote
> 本篇筆記主要為紀錄在 SageMaker 上訓練模型與建立環境時遇過的問題

### 建立VPC
需要取得外部連線的資源，因此必須建立一個有 NAT 閘道子網路的 VPC（付費）

<img width="1440" height="900" alt="截圖 2025-09-10 晚上10 15 34" src="https://github.com/user-attachments/assets/35a56b8a-a11a-43c9-a9a4-399dbed6ca35" />
<img width="1440" height="900" alt="截圖 2025-09-10 晚上10 16 04" src="https://github.com/user-attachments/assets/97fcb1f5-ab0c-48c7-a3df-924aca46c721" />


<br>
<br>

### 建立網域
大部分都可照預設，但要記得選取有 NAT 閘道的 **private** 子網路（若連接到 public 可能會無法載入資料）

<img width="1440" height="900" alt="截圖 2025-09-10 晚上10 23 39" src="https://github.com/user-attachments/assets/2d7106b3-d1be-45e7-bc1e-7a6410fc0a3f" />

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

<img width="1440" height="900" alt="截圖 2025-09-10 晚上11 46 04" src="https://github.com/user-attachments/assets/64f45d96-c177-48ff-99aa-729668d63655" />

<br>
<br>

`新建立的 project > New > Notebook`

<img width="1440" height="900" alt="截圖 2025-09-10 晚上11 49 37" src="https://github.com/user-attachments/assets/a898681e-50f9-4fb0-8daa-6384af2ac8df" />

<br>
<br>

`左側 git 圖示 > Clone a Repository > https://github.com/45678XYZ/SageMaker-Demo > Clone`

<img width="1440" height="900" alt="截圖 2025-09-10 晚上11 49 03" src="https://github.com/user-attachments/assets/f57d5569-635b-481c-8194-c4e5907c94ec" />

<br>
<br>

載入完畢即可執行 Notebook

<img width="1440" height="900" alt="截圖 2025-09-11 凌晨1 22 45" src="https://github.com/user-attachments/assets/7ff60274-8814-407b-b0ed-ee16a4a0d4fc" />

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

<img width="1440" height="900" alt="截圖 2025-09-10 晚上10 30 06" src="https://github.com/user-attachments/assets/1828237f-af64-47f8-9701-e5bc07cb44f8" />

<br>
<br>

替該 role 新增權限（會用到 **AmazonS3FullAccess**, **AmazonSageMakerFullAccess**）

`IAM > 角色 > 要新增權限的 role > 新增許可 > 連接政策`

<img width="1464" height="928" alt="截圖 2025-09-10 晚上10 33 02" src="https://github.com/user-attachments/assets/cd525f7a-49fb-4b23-8291-81d88959aa4f" />
<img width="1440" height="900" alt="截圖 2025-09-10 晚上10 34 17" src="https://github.com/user-attachments/assets/f3c9ac13-5630-48f0-8f78-1765b1274489" />


<br>
<br>

### 申請 Instance
要用到的 ml.p3.2xlarge 原本預設沒有配額，需要去申請

**錯誤訊息：**
account-level service limit 'ml.p3.2xlarge for training job usage' is 0 Instances, with current utilization of 0

**解決方法：**
`Amazon Service Quotas > 左側 AWS 服務 > 搜尋 SageMaker`

<img width="1440" height="900" alt="截圖 2025-09-10 晚上10 45 30" src="https://github.com/user-attachments/assets/08422a63-d833-4cfc-93a8-c2c3a4e75477" />

<br>
<br>

點選後再搜尋要用的 instance 名稱 + "for training job usage"，可以從右上角請求增加層級

<img width="1464" height="928" alt="截圖 2025-09-05 下午2 45 12" src="https://github.com/user-attachments/assets/6aebd9dc-7750-476d-b04c-bc70e3d8ccd3" />


> [!NOTE]
> 在 `SageMaker Unified Studio > space > configure space` 那邊設置的 instance 是用來執行環境的，不需要太強（預設即可）
>
> 而要用到其他比較強的 instance 則是在 code 裡呼叫的用來跑模型的 instance
