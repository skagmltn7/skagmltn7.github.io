---
title: "Permission denied to {}. The requested URL returned error: 403 해결방안"
author: heesu
date: 2023-05-27 10:46:00+0900
categories: [Studying,error]
tags: [github]
---


<img src="https://user-images.githubusercontent.com/133394749/241348248-b87e0b06-d8f1-42f5-9aaf-6d2e192dda32.PNG" width="600" height="auto" alt="">


깃프로필에 productive-box를 사용하려했는데 액션에서 자꾸 `unable to access 'https://github.com/skagmltn7/productive-box/': The requested URL returned `에러가 떴다. 



<br>

이러한 에러가 뜬다면 productive-box를 fork한 `레파지토리- Settings - Action - General - Workflow permissions`에서 `read and write permissions`으로 바꿔 준 후 productive-box에서 파일 아무거나 수정하면 정상작동된다!! 정상작동되었는지는 Action탭에서 확인 가능하다.<br><br>


<img src="https://user-images.githubusercontent.com/133394749/241348490-7f568fb5-99e5-4b31-814a-fe1dd7d309ae.PNG" alt="">

<img src="https://user-images.githubusercontent.com/133394749/241348486-ae4658b9-cf4b-432c-b551-ebe170a8947b.PNG" alt="">


<br><br><br><br>

<center>
<img src="https://user-images.githubusercontent.com/133394749/241348540-ca050024-8a53-40b7-8e58-e30540f7a3e3.gif" width="300" height="auto" alt=""/>
<br>
<a href="https://stackoverflow.com/questions/73687176/permission-denied-to-github-actionsbot-the-requested-url-returned-error-403">stackoverflow</a>에서 참고하였습니다.
</center>

