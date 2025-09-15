# SageMakerDemoNote
> 本篇筆記主要為紀錄在 SageMaker 上訓練模型與建立環境時遇過的問題

### 建立VPC
需要取得外部連線的資源，因此必須建立一個有 NAT 閘道子網路的 VPC（付費）

<img width="1440" height="846" alt="487932037-35a56b8a-a11a-43c9-a9a4-399dbed6ca35" src="https://github.com/user-attachments/assets/9e493ef1-8f95-41b4-995f-8655354906e0" />
<img width="1439" height="847" alt="487932068-97fcb1f5-ab0c-48c7-a3df-924aca46c721" src="https://github.com/user-attachments/assets/556154c3-3dfe-488f-b965-27afa93666f0" />

<br>
<br>

### 建立網域
大部分都可照預設，但要記得選取有 NAT 閘道的 **private** 子網路（若連接到 public 可能會無法載入資料）

<img width="1440" height="847" alt="489632526-1d8428f9-2007-4754-be3c-6c4b8c8e07cd" src="https://github.com/user-attachments/assets/8a90079f-cd8d-4752-856c-0a1d29d088ac" />

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

<img width="1440" height="808" alt="487932337-a898681e-50f9-4fb0-8daa-6384af2ac8df" src="https://github.com/user-attachments/assets/cddb5102-386c-4d7e-a1d8-5ff52ad83e12" />

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
ClientError: An error occurred (AccessDenied) when calling the CreateMultipartUpload operation: User: \<UserName\> is not authorized to perform: s3:PutObject on resource: \<ResourceName\> because no identity-based policy allows the s3:PutObject action


**解決方法：**
在 Studio 找到該 Project 的 role ARN

<img width="1440" height="823" alt="487932594-1828237f-af64-47f8-9701-e5bc07cb44f8-2" src="https://github.com/user-attachments/assets/d0ea839c-a465-4d41-a824-fd146ecad797" />

<br>
<br>

替該 role 新增權限（會用到 **AmazonS3FullAccess**, **AmazonSageMakerFullAccess**）

`IAM > 角色 > 要新增權限的 role > 新增許可 > 連接政策`

<img width="1440" height="875" alt="489634412-75ea7024-363d-410b-83ff-7fc88bfebe50" src="https://github.com/user-attachments/assets/445921d4-c84e-45d3-81af-d835ac4cee0e" />
<img width="1440" height="847" alt="487932761-f3c9ac13-5630-48f0-8f78-1765b1274489" src="https://github.com/user-attachments/assets/9ab41b5e-61d2-4325-9112-cb02dc237405" />

<br>
<br>

### 申請 Instance
要用到的 ml.p3.2xlarge 原本預設沒有配額，需要去申請

**錯誤訊息：**
account-level service limit 'ml.p3.2xlarge for training job usage' is 0 Instances, with current utilization of 0

**解決方法：**
`Amazon Service Quotas > 左側 AWS 服務 > 搜尋 SageMaker`

<img width="1438" height="846" alt="487932912-08422a63-d833-4cfc-93a8-c2c3a4e75477" src="https://github.com/user-attachments/assets/00cf3982-452a-4ece-ab91-b5ba6ba31a9f" />


<br>
<br>

點選後再搜尋要用的 instance 名稱 + "for training job usage"，可以從右上角請求增加層級

<img width="1464" height="874" alt="487932978-6aebd9dc-7750-476d-b04c-bc70e3d8ccd3" src="https://github.com/user-attachments/assets/cb2d882c-bfaf-45c2-8de2-76adec1bf34a" />


> [!NOTE]
> 在 `SageMaker Unified Studio > space > configure space` 那邊設置的 instance 是用來執行環境的，不需要太強（預設即可）
>
> 而要用到其他比較強的 instance 則是在 code 裡呼叫的用來跑模型的 instance
