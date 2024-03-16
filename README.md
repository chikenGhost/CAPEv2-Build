# CAPEv2-Build
惡意軟體沙箱 [CAPEv2](https://github.com/kevoreilly/CAPEv2) Sandbox 的建置步驟  
但因為是一個相當新而且還在滾動式更新的專案，安裝過程可能隨時會改變，這邊會介紹主要的幾個元件跟需要設定的東西。  


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
CAPEv2 沿襲自 [Cuckoo](https://github.com/cuckoosandbox)，Cuckoo的原始版本已經從2019開始就不再維護，語言還是Python2，隨著套件等等的更新只會變得越來越難用  
而CAPEv2沿襲了Cuckoo的架構，將整體程式碼以Python3重寫過，也能夠支援ATTCK等分析，是十分強大的惡意軟體分析沙箱。
但整體安裝過程非常繁瑣，需要下載、準備的東西很多，安裝一次大約需要花費2~3小時。

在開始前，你需要準備以下東西
* Linux Ubuntu 22 iso (Host)
* Windows 7 or 10 iso (Guest)
* VMware (或其他VMM)  

這是我的配置，CAPEv2的Guest主要是Windows，並且是在QEMU-KVM上運行。
如果想要換成Linux或是實體機請自行參考 [官方文件](https://capev2.readthedocs.io/en/latest/)。  
如果你想要在原生Ubuntu OS上安裝就不用 VMM，但要注意必須是**Ubuntu 22**以上的版本，CAPEv2不支援Ubuntu 22以下的版本  
如果是Windows透過VMM安裝，**請確保Windows是企業或是專業版**，且VMM支援巢狀虛擬化功能。  

## 1. 架設與設定Host環境
- ### 1.1 Ubuntu安裝設定
- ### 1.2 透過官方腳本安裝QEMU-KVM
- ### 1.3 透過官方腳本安裝CAPEv2本體
- ### 1.4 安裝額外套件

## 2. 架設與設定Guest環境
- ### 2.1 Windows安裝設定
- ### 2.2 安裝Python3 32bit
- ### 2.3 關閉Windows Defender與防火牆
- ### 2.4 設定GPE
- ### 2.5 關閉其他系統雜訊
- ### 2.6 設置agent.py

## 3. 設定虛擬網路介面與CAPEv2設定檔
- ### 3.1 設定虛擬網路介面
- ### 3.2 固定Guest IP
- ### 3.3 修改CAPEv2設定檔

## 4. 開始使用

