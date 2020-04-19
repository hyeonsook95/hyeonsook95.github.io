---
layout: post
title:  "이메일 용어 및 보안"
date:   2020-04-19
excerpt: "이메일과 관련된 용어 및 보안"
categories: 
 - CS
tag:
 - cs
 - email
 - dns
 - webmail
---

__이 글은 개인적으로 여러 글들을 조사하여 편집하고 정리한 내용이므로 오류가 있을 수 있습니다.__

## 용어

### Email Server
* 서버는 이메일 주소에 대응하는 mailbox(메일 사서함)을 가짐.
* 서버는 클라이언트를 통해 받은 email을 목적지 주소로 보내는 기능을 함.
* 서버간의 통신은 SMTP 프로토폴을 사용하여 email을 주고 받음.
* MTA, MSA, MDA 기능을 수행.

#### MTA
* Mail Transport Agent
* 메일을 전달해주는 서버.
* 흔히 말하는 메일서버.
* MUA에서 작성되고 전송된 이메일을 처리하는 우체국. 
* Sendmail, postfix, Exim, qmail, Apache James, ...

#### MSA
* Mail Submission Agent
* MUA로부터 메일을 수신하고 MTA로 메일을 전달하는 역할을 하는 일종의 프로그램 또는 소프트웨어.

#### MDA
* Mail Delivery Agent
* MTA로부터 이메일을 받아 받는 사람의 MUA로 메일을 전달해주는 역할
* MUA에서 전송된 이메일을 MTA로부터 넘겨받아 다시 수신자가 MUA를 통해서 받기 전까지 이메일을 저장해주거나 MUA로 전송해 주는 역할. 
* daum.net과 paran.com의 메일서버들은 계정사용자들에 대한 MDA를 가지고 있어 사용자들이 MUA를 사용하여 메일을 받아가지 않는다면 이를 보관해둠.

### Email Client
* 서버의 mailbox에 저장된 email을 가져와서 읽는 기능을 함.
* 사용자가 email을 작성하면 서버에 보내는 기능도 수행.
* 클라이언트는 서버와의 통신에서 SMTP, POP, IMAP 프로토콜을 사용함.
* SMTP는 서버에게 email을 보낼 때 사용함.
* POP, IMAP는 서버에게서 email을 받을 때 사용함.
* MUA, MTA-Client, MSA-Client 기능을 수행.

#### MUA
* Mail User Agent
* 사용자가 이메일을 읽고 답장하고 삭제하는 등의 행위를 할 때 이용하는 컴퓨터 프로그램. 
* Outlook, Windows live mail, ...

### 프로토폴

#### SMTP
* Simple Mail Transfer Protocol
* 이메일을 전달해주는 프로토콜.
* TCP/IP에서 이메일을 전달시켜주는 프로토콜.
* 기본으로 TCP 25 포트를 사용.
* PC에서 메일서버로 메일을 보낼 때, 메일서버끼리 메일을 주고 받을 때 사용.
* MTA-Server와 MTA-Client 간의 프로토콜.

#### POP
* Post Office Protocol
* 이메일 수신을 위한 프로토콜.
* MUA에서 메일을 수신하면 메일서버에서 메일이 삭제됨(옵션으로 서버에 보존 가능).
* 메일 전체를 MUA로 한 번에 다운로드.

#### IMAP
* 이메일 수신을 위한 프로토콜.
* MUA에서 메일을 수신하더라도 메일서버에 메일이 존재함. 수신받은 email을 자신의 디바이스로 copy 함.
* 메일의 일부 내용만 다운로드(순차적으로 메일의 내용을 수신함).

## 이메일 동작

1. 사용자는 __MUA__ 를 사용하여 메일을 작성하고 보내기 버튼을 누른다.
2. 메일을 보내면, __MUA__ 는 __SMTP 프로토콜__ 을 사용하여(MTA-Client 기능을 하여) __MSA__ 로 메일을 전달한다. __MSA__ 는 __SMTP 프로토콜__ 을 사용하여(MTA-Server 기능을 하여) MUA에게서 전달받는다.
3. MTA에서는 받는 사람의 도메인을 확인하고 받는 사람의 메일서버로 __SMTP 프로토콜__ 을 사용하여 메일을 전달한다.
4. MTA는 수신된 메일이 있으면 __MDA__ 를 통해 메일을 전달한다. 받는 사람의 __MUA__ 에서 __POP 프로토콜__ 또는 __IMAP 프로토콜__ 을 사용하여 __MDA__ 에 있는 메일을 수신한다.

## Webmail (Web-based mail)
* Webmail Service Provider(Google, Daum, Naver, ...)가 제공하는 이메일 서비스.
* 이메일 클라이언트가 Web Server 상에서 Web Application으로 동작함.
* 웹 브라우저를 사용하여 Webmail Server에 접속하여 email 작업을 함.
  
* Webmail Hosting Service 사업자인 경우 유료로 email Service를 제공.
* 도메인을 소유하고 있는 경우, 사용자 도메인에 속한 이메일 주소를 사용하도록 해줌.

## 이메일 보안

### SSL/TLS
* Email Client에서 Email Server에 접속할 때, UserName과 Password를 입력하여 접속 인증을 받음.
* SMTP는 이 값을 암호문으로 전달함.
* POP의 경우, 이 값을 평문으로 전달함.
    * SSL/TLS를 사용하지 않으면 평문으로 노출되어 Network 상에 돌아다니게 됨.
* Email Client S/W에서 SSL/TLS를 사용하려면 Email Server에 SSL Certificate가 설치되어 있어야 함.
    * 대부분 설치되어 있음.
* SMTP와 POP에 SSL/TLS 보안 설정을 하면, Network Traffic이 전부 암호화되므로, Network Traffic이 capture되더라도 내용을 볼 수 없음.
    * Web Server에도 설치되어 Web Browser과의 Network Traffic을 암호화시키는 데 사용하고 있음.
* SSL -> ver3.0
* TLS -> SSL ver3.0 이상

### 이메일 내용 암호화
* S/MIME: 세계적으로 공인된 인증기관에서 개인의 신원에 대한 증명을 해주는 "개인인증서"를 발급받아 설치하여 사용.
* PGP: "Web of Trust" 방식의 개인인증서를 사용. 공인된 기고나에서의 인증이 아닌 Web 상의 서로를 신뢰해줌.
    * 이메일 통신을 위해서는 상대방의 인증서 정보(공개키 정보)를 확보해야 하고,
    * 나의 인증서를 상대에게 전달해주어야 함.
    * 특정 Email Client S/W에서만 지원.

### DNSSEC
* IP 네트워크상의 DNS에서 정보를 보호하기 위해 사용되는 기술.
* DNS 데이터의 위조-변조 기능을 차단하기 위해 공개키 암호화방식의 전자서명 기술을 도입 적용함.



출처: https://noviceany.tistory.com/58 [novice in anything]

---
* [이메일 프로토콜과 보안](https://blog.naver.com/PostView.nhn?blogId=aepkoreanet&logNo=221502428053&parentCategoryNo=&categoryNo=1&viewDate=&isShowPopularPosts=true&from=search)
* [메일 서버 구동의 이해](https://webdir.tistory.com/164)
* [메일서버 SMTP 이해하기](https://noviceany.tistory.com/58)
* [DNSSEC란?](https://xn--3e0bx5euxnjje69i70af08bea817g.xn--3e0b707e/jsp/resources/dns/dnssecInfo/dnssecProcess.jsp)