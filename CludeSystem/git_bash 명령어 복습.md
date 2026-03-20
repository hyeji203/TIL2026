# 클라우드시스템구축 수업

## git bash 명령어 
1. **git 계정 정보 세팅**
Git 전역으로 사용자 이름과 이메일 주소 설정
   - $ git config --global user.name ＂(본인 이름)＂
   - $ git config --global user.email ＂(본인 이메일)＂

1. **$git status:** 현재 작업 중인 디렉터리와 스테이징 영역의 상태를 확인
   * Untracked files: 새로 만들었지만 한 번도 git add를 하지 않은 파일들
   * Changes not staged for commit: 기존에 관리하던 파일인데 내용이 수정되었지만, 아직 스테이징 영역에 올리지(add) 않은 상태
   * Changes to be committed: git add를 통해 스테이징 영역에 올라간 파일들

2. **$git add:** 신규 파일 추가

3. **$git commit 파일 -m "로그메세지":** 변경 사항을 Git 저장소에 영구적으로 기록, 
Staging Area에서 Repository로 이동하는 것

4. **reset**: 원하는 시점으로 돌아간 뒤 이후 내역들 삭제.
   **$git reset --soft [commit ID]:** commit된 파일들을 staging area로 돌려놓는다. (commit 하기 전 상태로)
   **$git reset --mixed [commit ID]:** commit된 파일들을 working directory로 돌려놓는다. (add 하기 전 상태로)
   **$git reset --hard [commit ID]:** commit된 파일들 중 tracked 파일들을 working directory에서 삭제.

5. **$git revert [commit id]:** 되돌리기 원하는 시점의 커밋을 거꾸로 실행.
   - revert를 사용하면 중간에 무슨 문제가 있었는지, 왜 돌아갔는지 등의 기록이 가능하다는 장점이 있다.
   - 다른 사람과 같은 브랜치에서 함께 작업할 때 코드 충돌을 최소화 가능.

6. **계정 초기화**
Git bash에서 아래 git명령어 입력: 설정정보 초기화
   - $ git config --global --unset user.name
   - $ git config --global --unset user.email
   - $ git config --global --list 로 확인!