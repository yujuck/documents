deploy 오픈소스를 만드는 중에 pm2 startup과 save 커맨드를 사용할 수 있도록 추가하는 부분이 생겼다. <br />
pm2 설치하는 부분을 맡으면서 pm2 공식문서 읽고 TIS 작성도 했었는데 9월 13일에 했던 내용이 딱 startup과 save 관련 내용이었다.<br />
(와.. 오픈소스 만들기 시작한지 벌써 두달 되어가네..)<br />
이번에 startup과 save에 대해서 다시 보게 되었는데 보면서 그때 이해가 안된다고 적었던 부분들이 어느 정도 이해가 되어서 다시 한번 정리해본다.<br />


### 다른 user로 실행

그때 적었던 걸 보면 다른 user로 실행시킨다는 게 무슨 의미인지 잘 모르겠다고 했는데,<br />
말그대로 pm2를 사용하고 있는 user를 말하는 것이었다.<br />

aws ec2 서버를 사용하고 있어서 원격 서버 접속할 때 ec2-user로 접속을 하고 있다.<br />
그래서 접속하면 user는 ec2-user로 되어있어서 `pm2 startup`을 실행하면 user는 ec2-user로 실행이 된다.<br />
이때 다른 user로 startup 스크립트를 실행하도록 하려면
```bash
pm2 startup ubuntu -u www --hp /home/ubuntu
```
이런식으로 `-u <username>` 과 `--hp <user_home>`를 변경하면 된다고 했다.<br />
`pm2 startup` 바로 뒤에 오는건 플랫폼이라고 했는데 이건 아직 잘 모르겠다..

### startup 실행 이후
`pm2 startup`을 실행하면 출력되는 커맨드를 복사해서 다시 실행하면 끝인줄 알았는데 아니었다.<br />

```bash
sudo su -c "env PATH=$PATH:/home/unitech/.nvm/versions/node/v14.3/bin pm2 startup <distribution> -u <user> --hp <home-path>
```

위의 커맨드를 실행하고 나서 `systemctl status <service name>.service`를 입력해보면 inactive 상태로 되어있다.<br />
`sudo systemctl start <service name>.service` 이렇게 해야 active 상태가 되었던 것 같다..<br />
오늘 했는데 쓰려니까 또 헷갈리네..;;;

startup 스크립트가 잘 등록되었는지 확인할 수 있는 방법은 서버를 reboot 시킨 후 다시 들어갔을 때 process가 있는지를 확인해보면 된다.<br />
`sudo reboot` 하면 리부팅할 수 있고,<br />
다시 들어가서 `pm2 ls`를 했을 때, startup 스크립트가 동작했으면 process들이 그대로 있고 startup 스크립트가 없으면 아무것도 나오지 않는다.<br />
지금 약간 헷갈리는게 테스트할 때 startup을 제대로 안해놨으면 reboot 시에 없어지는거랑 제대로 했으면 남아있는걸 확인하긴 했는데<br />
`제대로 안했을 때`가 systemctl start를 안해서 인지(inactive 상태) 아니면 아예 커맨드를 복사해서 실행을 안해서였는지가 기억이 안난다ㅠㅠ<br />
너무 이렇게 저렇게 해서.. 그런 듯.. 이거는 내일 다시 해보지 뭐..!

내일 다시 해볼 때 unstartup도 써보고 깔끔하게 이전 startup 스크립트를 지우려면 save를 한번 해주고 startup을 해야한다는 내용도 봤었는데 같이 확인해볼 수 있을 것 같다.<br />

### save 커맨드
save는 별다른건 없는 것 같고 그냥 dump 파일을 만들어주는 것 같은데<br />
save 한 내용을 다시 가져올 때는 `resurrect` 커맨드를 사용해야한다고 했는데 이거는 언제 하는건지 잘 모르겠다.<br />
뭔가.. 실수로 프로세스를 지웠을 때 사용하려나..?

리부팅 이후에는 어차피 원래 저장되어있던 프로세스를 가져오는거니까 필요없을 것 같긴 한데 맞는진 모르겠음..


==========================

11/2 내용 확인
1. startup 하고 출력된 커맨드를 실행하면 inactive 상태가 맞는데 굳이 `systemctl status <service name>.service`를 실행하지 않아도 reboot하면 프로세스 살아있고 status를 확인해봐도 active로 바뀌어있다. 등록만 하면 될 듯!
2. unstartup을 하고 나면 관리하고 있던 프로세스가 아예 다 사라져버린다.. 이럴 때 `pm2 resurrect` 쓰나봄.. resurrect 커맨드로 다시 살려놨다.
