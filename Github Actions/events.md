github actions에서 트리거되는 events 종류와 각각의 특징들

#### push

- 커밋이 push되면 트리거됨

#### pull_request

- pull request의 default activity type은 open, synchronize, reopen여서 pr을 생성할 떄, 해당 pr에 새로운 커밋이 푸시될 때, 다시 열었을 때 트리거됨.
- 특정 activity 시에만 트리거되도록 하고 싶다면 명시적으로 activity type을 지정해줘야 함.

#### issue, issue_comment

- issue는 default branch로 설정된 브랜치에서만 트리거됨. 즉, default branch에 issue 이벤트를 트리거하는 workflow가 있을 때 동작한다는 의미. 다른 브랜치의 workflow에 있으면 동작 안함
- issue_comment는 issue에 코멘트를 남길 때 뿐만 아니라 pull request에 코멘트를 남길 때에도 트리거 됨. 마찬가지로 default branch에서만 동작함

#### schedule

- 특정 시간에 워크플로우를 실행할 수 있도록 해주는 이벤트
- cron 표현식을 사용해 실행 시간을 설정할 수 있음
- default branch에서만 동작
- 경우에 따라서는 지정한 시간에 정확히 실행도지 않을 수 있음.
  - github actions 사용에 높은 부하가 있으면 딜레이가 발생하거나 실행이 아예 안될 수도 있다고 함
  - 그렇기 때문에 정확한 시간에 반드시 실행되어야 하는 경우에는 사용하지 않는 것이 좋음

#### workflow-dispatch

- 사용자가 원할 때 수동으로 실행시킬 수 있도록 해주는 이벤트
- default branch에서만 동작
- 인풋 값을 넣을 수 있음. 인풋으로 받을 키를 정의하면 github actions 탭에서 직접 입력할 수 있음
  - choice라는 타입이 있는데, options로 선택지를 정의해두고 그 중 하나를 선택해서 실행시킬 수 있음

#### 여러 개의 이벤트 처리

- 하나의 워크플로우 파일에서 여러 이벤트에 대해 정의할 수 있음
- on 키워드에 트리거할 이벤트를 나열하여 정의하면 해당 이벤트들이 발생했을 때 트리거됨

#### needs 키워드

- 하나의 워크플로우에서 여러 개의 잡을 사용하는 경우가 많은데, job은 기본적으로 **병렬**로 실행됨
- needs 키워드를 사용하면 **순차적**으로 실행할 수 있음
- needs는 하나의 job이 다른 job 또는 여러 job이 완료될 때 실행되도록 할 때 사용
  - 즉 여러 job의 실행 순서를 조절 가능
  - 테스트 job이 실행되고, 그 job이 성공할 때 배포 job을 실행하도록 설정 가능

#### re-run

- github actions 탭에서 과거에 실행한 워크플로우를 재실행 시킬 수 있음
- 해당 워크플로우의 성공, 실패 여부와 상관없이 재실행할 수 있음.
- 다만 트리거된 그 시점을 다시 실행하기 때문에 yaml 파일을 수정하고 다시 실행시켜도 수정된 내용은 반영되지 않음
  - 수정한 파일이 push된 이후, 워크플로우가 트리거되어야 반영이 됨
