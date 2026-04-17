# 쿨메신저의 로컬 데이터베이스 구조에 대하여

## 데이터베이스 위치
%LocalAppData%\\CoolMessenger\\Memo\\{User ID}.udb

## 멤버 목록
멤버 목록은 `tbl_member`에서 확인할 수 있으며 아래와 같은 구조로 되어 있다.

| 컬럼명      | 타입    |
| ---------- | ------- |
| K_MemberID | INTEGER |
| MemberID   | TEXT    |
| MemberName | TEXT    |

## 메시지 내역
메시지 내역은 보낸 메시지와 받은 메시지로 구분되어 각각 `tbl_send`, `tbl_recv`에 나눠 저장된다.

두 테이블은 구조가 매우 비슷한데, 그 중에서도 공통적으로 메시지 내용은 아래와 같이 저장된다.

```text
MessageBody := "{COMP}" + base64(zlib(HTML))
```

### 리스트 표기
쿨메신저 내 리스트는 아래와 같은 방식으로 저장된다.
```
| (리스트 내 item 수) | (1번째 item의 1번째 필드) | (1번째 item의 2번째 필드) | ... | (n번째 item의 m번째 필드) |
```
예로 item의 필드 수가 2개이고 리스트 내에 item 수가 4개인 경우 | 로 구분했을 때 총 9개의 칸으로 구성된 리스트로 저장되며 그 첫 요소는 4이다.

### 수신 확인 기록
송신한 메시지가 언제 수신되었는지는 `tbl_send`의 `AnswerBack`에 다음과 같은 필드로 구성된 item들의 리스트로 저장된다.
```text
{ tbl_member.K_MemberID, Timestamp }
```

### CC 구분
`Receiver` 컬럼만으로는 누가 CC에 해당하는지를 알기 어려워 `CCList` 컬럼을 확인해야 하는데, 이는 아래와 같은 item들의 리스트로 저장된다.
```text
{ tbl_member.K_MemberID }
```
