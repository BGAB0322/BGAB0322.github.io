---
layout:   post
title:    Visual Studio Code에서 WSL Extentions 설치 에러 해결
category: ros
image:    /assets/img/blog/2023-09-26/thumnail_ros.png
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

![그림1](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-09-26/extensions_error_1.png?raw=true)

ROS 개발을 할 때 저는 Window에서 WSL1을 가상환경으로 사용하고 Microsoft Stroe를 통해 Ubuntu의 여러 버전을 설치한 채로 Visual Studio(이하 VSCode)에서 remote로 접속합니다.

코드의 시각적인 요소나 파일을 다룰 때도 전반적인 개발에 있어 VSCode로 작업하는게 익숙해져서 Window 운영체제에서 코드를 수정할 때는 VSCode에서 사용합니다.

그리고 작업 간에 편의성을 Remote Extensions에 있는 기능들을 Ubuntu 로컬에 설치하는게 기본적인 환경 설정인데 다른 장치를 오가면서 환경설정을 새로 하다 보면, 아래와 비슷한 에러 문구로 골머리를 앓아왔습니다. 

`Permission denied`

경로는 .vscode-server/extensions의 어딘가라고 하는데 경고 알림에서 언급하는 디렉토리가 존재하지 않고 도대체 이를 어떻게 해결해야 하나 싶었습니다.

보통 많이 거론되는 내용이 chmod 777 명령어를 이용해 경고에서 언급하는 디렉토리의 Permission을 변경하는 것인데 이것도 소용이 없었습니다. 그러다가 문득 방법을 찾은 듯 하여 글을 남겨두려고 합니다.

## F1 → WSL:Connect to WSL

![그림2](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-09-26/extensions_error_2.png?raw=true)

VSCode를 실행하여 F1 키를 눌러보면 위와 같이 커맨드 입력란(>)이 뜹니다. 거기에 WSL: Connect to WSL 또는 WSL: Connect to WSL using Distro를 사용하여 WSL을 Remote에 연결해보면 이전에 이루어지지 않던 Extentions 설치가 정상적으로 이루어졌습니다.

![그림3](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-09-26/extensions_error_3.png?raw=true)

보통 Remote를 사용하려고 한다면 Extensions에서 Remote를 설치하고 나서, 왼쪽 하단에 생성되는 초록창을 통해서 연결을 해왔습니다.

![그림4](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-09-26/extensions_error_4.png?raw=true)

그러면 SSH 접속, WSL 접속 등 한 눈에 카테고리 종류가 들어왔기에 이렇게 접속해왔는데, 이런 접속이 Ubuntu 버전마다 Extensions 설치에 제한이 생길 수도 있는 건 몰랐군요.​

다른 장치에도 설치가 불가능했던 내용도 이 방법으로 Ubuntu를 연결하여 Extensions를 설치하니 정상적으로 설치가 이루어졌습니다. 원인까지는 명확하게 무엇이라고 규정하질 못하겠지만, 접속 방법에 따른 권한 설정이 다른 것으로 보입니다.

이것저것 검색해보았는데, 이왕이면 원인도 명확히 짚고 넘어가고 싶은데 말이죠. 해결 방법은 알았으니 이 키워드로 원인도 확실하게 찾아봐야겠습니다.