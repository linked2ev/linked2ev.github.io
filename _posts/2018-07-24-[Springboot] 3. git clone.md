---
layout: post
title:  "[Springboot] 3. 프로젝트 git에 연동하기"
subtitle:   "[Springboot]"
categories: gitlog
tags: springboot
comments: true
---

git에 프로젝트 연동하기


## 6. git에 프로젝트 연동하기

6-1. 우선 상단 메뉴에 Window > Show View > Other > git검색 > Git Repositories 클릭

6-2. 아래 이미지에서 Clone Git repository 클릭

[![git clone STEP1](/assets/img/bmw/201807/2018-07-24-gitCloneStep1.png)]()  
<br>

6-3. 자기 자신 github에 가서 프로젝트 Repository URL 복사 

[![git clone STEP2](/assets/img/bmw/201807/2018-07-24-gitCloneStep2.png)]()  
<br>

6-4. git URL 붙여넣고 자신 git ID/PW 입력

[![git clone STEP3](/assets/img/bmw/201807/2018-07-24-gitCloneStep3.png)]()  
<br>

6-5. git 로컬 저장소(Local Repository) 지정  
**내 PC에 파일이 저장되는 개인 전용 저장소이며, git은 로컬저장소에 먼저 commit을 하고 git저장소에 push하는 형태이다.

[![git clone STEP4](/assets/img/bmw/201807/2018-07-24-gitCloneStep4.png)]()  
<br>

6-6. 프로젝트 git에 업로드  
프로젝트 우클릭 > Team > Share Project

[![git clone STEP5](/assets/img/bmw/201807/2018-07-24-gitCloneStep5.png)]() 
<br>

6-7. 로컬저장소를 선택하면 로컬저장소와 동기화가 된다.  
**실제 git저장소에는 commit을 한 뒤에 push를 해야 올라간다.

[![git clone STEP6](/assets/img/bmw/201807/2018-07-24-gitCloneStep6.png)]() 
<br>

6-8. git에 commit을 하기 위해, 프로젝트 우클릭 > Team > Commit을 누르면 아래와 같은 화면 을 볼수 있다.  
Staged Chages로 옮긴 후 Commit and Push를 클릭하면 Github저장소에 업로드가 된다.  
**Unstaged Chages: commit 제외 파일    
**Staged Chages  : commit 하는 파일

[![git clone STEP6](/assets/img/bmw/201807/2018-07-24-gitCloneStep7.png)]() 
<br>

(Git 관련해서도 역시 추후에 정리해서 포스팅 예정)
<br><br> 

---
스프링부트에 대해 이미 고견이 있는 고수님들의 블로그들이 많기에
해당 포스트에서는 개인프로젝트 개발 진행과정에 초점을 두겠습니다.