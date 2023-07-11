## Github에서 Issue
이제까지 혼자서 프로젝트를 하거나, 소규모로 토이프로젝트를 진행할 때는 Issue를 쓰지 않고, pull request를 보내고 merge하는 것만 주로 경험했었는데, 이번 SWS에서 Issue를 이용하여 진행 해보자고 하셔서 이슈에 대해 공부해보았다!

이슈(Issue)는 새로운 추가될 가능, 개선 해야할 가능, 버그 등등이 있다. 모든 활동 내역에 대해서 이슈를 등록하고 그 이슈기반으로 작업을 진행하게 된다.

### Issue Template
- Setting ➡️ Option ➡️ Custom issue template을 통하여 템플릿을 생성할 수 있고, 이것을 이용하여 이슈를 등록하면 편리하게 이슈를 작성할 수 있음. 

백엔드를 담당하면 bug template에서 서버로그, response body 값을 주로 등록한다.

### Issue 작업
**등록된 이슈 살펴보기**
- Assignees: 해당 작업의 담당자
- Lables: 해당 작업의 성격
- Milestone: 해당 작업이 속한 파트

### Issue 기반 Branch 생성
Task의 갖는 가장 큰 기능은 깃허브 이슈 기반으로 브랜치 생성을 쉽게 도와주는 것으로 생각한다. 즉, 깃허브에서 생성된 이슈를 기반으로 브랜치를 생성하는 것이 핵심이다. 깃허브 이슈는 각자의 유니크한 값인 Issue Number를 갖는다. 그 Issue Number를 기반으로 브랜치 이름을 갖게하여서 브랜치의 명확한 작업의 의도를 갖게 할 수 있다.

## PR
### Github Pull Request
- Reviewers 톱니바퀴 버튼을 클릭하여 리뷰어를 지정한다.
- resolved: #1(해당 Issue Number) PR 요청하는 이유, 즉 무슨 이슈에 대한 작업인지 명시하기

resolved 키워드를 입력하면 해당 PR이 master Branch에 반영되면 자동으로 close된다. 자동으로 closed되는 것이 싫다면 issue:#[해당 Issue Number]를 작성한다.

이렇게 PR이 생성되면 새로운 Issue Number가 부여된다. 즉, PR도 Issue이다.
반드시 해당 PR이 무슨 이슈에 따른 요청인지 명시하는 것이 좋다. 그래야 해당 이슈에 PR이 연결되어 해당 이슈가 무슨 코드로 인하여 진행되었는지 추적하기에 좋다.

## Code Review
리뷰어가 요청받은 PR로 가서 Add your review 버튼을 클릭! 소스코드에 대한 질문 등 다양한 코멘트를 남기는 방식으로 PR이 진행된다.
- Approve: 코드에 대해 의문점이 없다면 승인
- Comment: 간단한 피드백 제출
- Request changes: 해당 코드에 문제가 있다고 판단되면 코드 수정 요구

comment가 해결되었다면 Merge Pull Request 버튼을 눌러서 해당 PR을 반영한다. 반영이 완료되고 해당 브랜치가 더는 필요없다고 판단시에 Delete Branch 버튼을 통해서 remote에 있는 브랜치를 삭제할 수 있고, **위에서 작성한 resolved: #1 키워드 덕분에 소스코드가 해당 Branch에 적용됐으니 자동으로 #1에 대한 이슈는 close 처리된다!**

