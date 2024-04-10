# CAPEv2-Build
惡意軟體沙箱 [CAPEv2](https://github.com/kevoreilly/CAPEv2) Sandbox 的建置步驟。  
因為是一個相當新而且還在不停更新的專案，安裝過程可能隨時會改變。  
因此除了過程外，還會介紹主要的幾個元件跟需要設定的東西，以防未來專案改變時能夠知道怎麼更改步驟  


## Outline
- [0. Intro](#0-intro-)
- [1. 架設與設定Host環境](#1-架設與設定host環境)
  - [1.1 Ubuntu安裝設定](#11-ubuntu安裝設定)
  - [1.2 透過官方腳本安裝QEMU-KVM](#12-透過官方腳本安裝qemu-kvm)
  - [1.3 透過官方腳本安裝CAPEv2本體](#13-透過官方腳本安裝capev2本體)
  - [1.4 安裝額外套件](#14-安裝額外套件)
- [2. 架設與設定Guest環境](#2-架設與設定guest環境)
  - [2.1 Windows安裝設定](#21-windows安裝設定)
  - [2.2 安裝Python3 32bit](#22-安裝python3-32bit)
  - [2.3 關閉Windows Defender與防火牆](#23-關閉windows-defender與防火牆)
  - [2.4 設定GPE](#24-設定gpe)
  - [2.5 關閉其他系統雜訊](#25-關閉其他系統雜訊)
  - [2.6 設置agent.py](#26-設置agentpy)
- [3. 設定虛擬網路介面與CAPEv2設定檔](#3-設定虛擬網路介面與capev2設定檔)
  - [3.1 設定虛擬網路介面](#31-設定虛擬網路介面)
  - [3.2 固定Guest IP](#32-固定guest-ip)
  - [3.3 修改CAPEv2設定檔](#33-修改capev2設定檔)
- [4. 開始使用](#4-開始使用)

    
## 0. Intro .
CAPEv2 沿襲自 [Cuckoo](https://github.com/cuckoosandbox)，Cuckoo的原始版本已經從2019開始就不再維護，語言還是Python2，隨著套件等等的更新只會變得越來越難用。  
而CAPEv2沿襲了Cuckoo的架構，將整體程式碼以Python3重寫過，也能夠支援各式各樣的分析，是十分強大的惡意軟體分析沙箱。
但整體安裝過程非常繁瑣，需要下載與設定的東西很多，就算熟悉流程，安裝一次也大約需要花費2~3小時。

在開始前，我們需要準備以下東西
* Linux Ubuntu 22 iso (Host)
* Windows 7 or 10 iso (Guest)
* VMware (或其他VMM)  

這是我的配置，CAPEv2的Guest主要是Windows，並且是在QEMU-KVM上運行。
如果想要換成Linux或是實體機請自行參考 [官方文件](https://capev2.readthedocs.io/en/latest/)。  
如果你想要在原生Ubuntu OS上安裝就不用 VMM，但要注意必須是**Ubuntu 22**以上的版本，CAPEv2不支援Ubuntu 22以下的版本  
如果是Windows透過VMM安裝，**請確保Windows是企業或是專業版**，且VMM支援巢狀虛擬化功能。  

## 1. 架設與設定Host環境
在這邊我們首先要來架設CAPEv2負責分析和收集惡意軟體資料的Host端虛擬機，因為官方有提供安裝腳本所以整體過程不算太複雜，但還是有些需要注意的地方。  

- ### 1.1 Ubuntu安裝設定
  基本上依照正常的安狀流程進行安裝即可，需要注意的是在使用VMware進行安裝時要配置的硬體需求如下
  * Memory >= 8GB
  * 硬碟 >= 100GB
  * 勾選開啟 **Virtual Intel VT-x/EPT or AMD-V/RVI (巢狀虛擬化)**  
  
  ![image](https://github.com/chikenGhost/CAPEv2-Build/assets/83065453/f1792a1d-974a-4afc-aac2-efd3bc43e1dd)

  接著設定使用者，這邊需要設定使用者名稱和密碼為 **cape**  

  ![image](https://github.com/chikenGhost/CAPEv2-Build/assets/83065453/5cb88cd4-f20b-40cf-ad39-e92302ced73c)

  其餘安裝步驟皆依照預設安裝流程進行即可  

- ### 1.2 透過官方腳本安裝QEMU-KVM
  
  接下來先透過官方提供的sh腳本安裝QEMU-KVM　　
  ```sh
  wget https://raw.githubusercontent.com/kevoreilly/CAPEv2/3b303a4419ce341a121363774ea666ea47befd60/installer/kvm-qemu.sh  
  chmod +x ./kvm-qemu.sh  
  sudo ./kvm-qemu.sh all cape | tee kvm-qemu.log  
  ```
  整體安裝需約15分鐘，完成後重啟cape虛擬機。  
  接著再安裝QEMU-KVM的GUI介面：QEMU-KVM-virt-manager　　
  ```sh
  sudo ./kvm-qemu.sh virtmanager cape | tee kvm-qemu-virt-manager.log
  ```
  安裝需約5分鐘，完成後重啟cape虛擬機。　　
  
- ### 1.3 透過官方腳本安裝CAPEv2本體

  再來是CAPEv2的本體，官方亦有提供sh腳本來協助安裝  
  ```sh
  wget https://raw.githubusercontent.com/kevoreilly/CAPEv2/3b303a4419ce341a121363774ea666ea47befd60/installer/cape2.sh 
  chmod +x ./cape2.sh
  sudo ./cape2.sh all cape | tee ./cape.log
  ```
  整體安裝需約20分鐘，完成後重啟cape虛擬機  
  
- ### 1.4 安裝額外套件

  確認安裝完成後，CAPEv2的目錄應該會位於 /opt/CAPEv2 中，我們需要在這個資料夾進行一些額外套件的安裝
  
  ![image](https://github.com/chikenGhost/CAPEv2-Build/assets/83065453/c5a32cf5-f12c-44b9-8143-c66a3f71e5c8)

  在這邊我們先調整一下相依元件的設定，檔案位於 /CAPEv2/extra/optional_dependencies.txt 中  
  這邊會引用到pyattck作為attck分析的依賴模組，但語法有誤。雖然目前這個功能貌似還沒有實裝，但為了不讓他報錯還是修改一下  
  pyattck == "7.1.2" -> pyattck == 7.1.2
  
  ![image](https://github.com/chikenGhost/CAPEv2-Build/assets/83065453/3f932ea4-9531-48d0-8c47-7e06772356a0)

  修改完後，就可以在 /opt/CAPEv2 目錄下執行以下指令來進行虛擬環境依賴套件的安裝  
  ```sh
  sudo apt install -y libgraphviz-dev
  sed -ie "s/flask-sqlalchemy/flask-sqlalchemy==3.0.5/" extra/optional_dependencies.txt
  poetry run pip install -r extra/optional_dependencies.txt
  ```

  最後我們再確認QEMU-KVM-virt-manager是否可以正常執行
  ```sh
  virt-manager
  ```
  輸入上面指令後，應該會出現QEMU-KVM-virt-manager的GUI，確認完成後，重啟cape虛擬機  

  ![image](https://github.com/chikenGhost/CAPEv2-Build/assets/83065453/0b38f358-9795-4611-b62e-5fbeef5092d8)  


## 2. 架設與設定Guest環境
- ### 2.1 Windows安裝設定
   ```sh
  virt-manager
  ```
  開啟QEMU-KVM的GUI介面，點選「Create a new virtual machine」  
 ![image](https://github.com/chikenGhost/CAPEv2-Build/assets/83065453/8b65e9a3-ec05-4341-8a32-f019e9a79623)

在選擇ISO檔時，Browse -> Browse Local -> 選擇Win10 映像檔  
![image](https://github.com/chikenGhost/CAPEv2-Build/assets/83065453/49d5bed7-9226-423a-80a2-478e6a0c46da)

Memory/Disk/CPU大小與數量可任意，依據你要分析的樣本需求而定。  
但要注意使用者名稱的設定，使用者名稱會與之後CAPEv2的設定檔有關，我這邊把Guest的名稱叫做cape1  
![image](https://github.com/chikenGhost/CAPEv2-Build/assets/83065453/59de2b3e-12fc-461b-98cd-791500f60aad)

因為Guest機器是用來運行病毒的，設定帳戶時請注意**不要使用自己的Microsoft帳號進行登入**，並且將其他隱私設定全都關閉  
![image](https://github.com/chikenGhost/CAPEv2-Build/assets/83065453/d085f1e8-56da-41ec-99ba-7f89d58cc833)
![image](https://github.com/chikenGhost/CAPEv2-Build/assets/83065453/9234cbbe-05ff-4ab7-943d-e0c66ca3f2b8)

- ### 2.2 安裝Python3 32bit
  CAPEv2的agent.py需要透過**Python3 32bit**執行（注意是32bits），並將Python加入到PATH  
  ![image](https://github.com/chikenGhost/CAPEv2-Build/assets/83065453/df5e36cb-fa3b-4d13-9859-98b0d135f53e)  
  安裝完成後，重啟Guest，並且安裝截圖用的套件Pillow  
  ```bash
  pip install Pillow == 9.5.0
  ```
  
- ### 2.3 關閉Windows Defender與防火牆
- ### 2.4 設定GPE
- ### 2.5 關閉其他系統雜訊
- ### 2.6 設置agent.py

## 3. 設定虛擬網路介面與CAPEv2設定檔
- ### 3.1 設定虛擬網路介面
- ### 3.2 固定Guest IP
- ### 3.3 修改CAPEv2設定檔

## 4. 開始使用

