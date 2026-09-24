# progress-report

[English](README.md) · [MIT 라이선스](LICENSE)

현재 요청에서 무엇을 마쳤고, 진행 중이며, 막혔거나 아직 시작하지 않았는지를 보고하는 스킬입니다. 각 단계에 구체적인 검증 방법과 근거가 있는 상태를 붙여 표로 보여줍니다. Codex와 Claude Code 플러그인이 같은 `skills/progress-report/`를 읽습니다.

## 왜 필요한가

대화로만 진행 상황을 전하면 최근 요청과 이전 작업이 섞이거나, 아직 검사하지 않은 수정이 완료로 표시되거나, 미뤄 둔 일이 사라지기 쉽습니다. `progress-report`는 보고 범위와 단계별 상태를 한 표에서 확인할 수 있게 합니다. 이 문제의식은 현재 스킬의 동작에서 읽은 것이며 제작 당시의 개인적 동기를 추측한 것은 아닙니다.

“어디까지 했어?”, “무엇이 남았어?”, “단계별로 진행 상황을 보여줘”, “이번 세션에서 하기로 한 일을 정리해줘” 같은 요청에 사용합니다. 확인된 작업을 보고하며 별도의 작업 데이터베이스를 관리하거나 검사를 자동 실행하지 않습니다.

## 플러그인 설치

플러그인과 마켓플레이스 이름은 모두 `progress-report`입니다. 이 저장소에 필요한 파일이 들어 있으며 `ai-workflow`를 받을 필요가 없습니다.

### Codex

```bash
codex plugin marketplace add insung/progress-report
codex plugin add progress-report@progress-report
```

필요하면 새 작업을 시작합니다. `$progress-report`로 명시 호출하거나 현재 요청의 진행 상황을 묻습니다.

### Claude Code

```bash
claude plugin marketplace add insung/progress-report
claude plugin install progress-report@progress-report
```

필요하면 새 세션을 시작합니다. 플러그인 스킬은 `/progress-report:progress-report`로 호출합니다.

## 어떻게 활용하나

**현재 요청**

```text
$progress-report 공개 스킬 저장소 작업은 어디까지 했어?
```

기본 범위는 가장 최근의 큰 요청과 그 후속 수정입니다. 답은 범위를 한 줄로 밝히고 `단계`, `할 일`, `검증 방법`, `상태` 네 열의 표를 보여줍니다. 작업에 단계 구분이 있으면 그 단계를 유지합니다.

**이번 세션 전체**

```text
$progress-report 이번 세션 전체에서 진행한 큰 요청을 모두 정리해줘.
```

“전체”를 명시하면 이전의 큰 요청도 포함합니다. 특정 단계를 지목하면 그 단계만 같은 형식으로 보고합니다.

상태 값은 `미착수`, `계획 중`, `진행 중`, `검토 중`, `막힘`, `완료`, `이번 범위에서 제외`로 제한됩니다. `완료`는 검증 방법을 실제로 실행해 통과한 근거가 있을 때만 사용합니다. 파일을 썼거나 검사를 계획했다는 사실만으로 완료가 되지 않습니다. 검토 상태에는 검토할 사람을, 막힘에는 장애 원인을 적고, 미룬 작업도 표에 남깁니다.

| 단계 | 할 일 | 검증 방법 | 상태 |
| --- | --- | --- | --- |
| 1 | 사용법 문서를 작성한다 | 두 언어 문서와 로컬 링크를 읽어 본다 | 진행 중 |
| 2 | 설치를 검증한다 | 두 도구의 깨끗한 설정에서 마켓플레이스 설치를 실행한다 | 미착수 |
| 3 | 결과를 검토한다 | 사용자가 공개 저장소 URL을 연다 | 검토 중 · 사용자 |

## 책임 경계

이 스킬은 현재 대화와 접근 가능한 산출물로 뒷받침되는 상태만 보고합니다. 로컬 커밋을 원격 게시로, 계획된 테스트를 통과로, 경과 시간을 사용자 승인으로 해석하지 않습니다. 검증하지 않은 단계는 진행 중 또는 미착수로 남깁니다. 미루기로 한 항목은 삭제하지 않고 이번 범위에서 제외된 항목으로 표시합니다.

이 스킬은 보고 형식이며 추적 시스템 연동은 아닙니다. spec-it을 쓰는 프로젝트의 정책이나 수용 절차를 대체하지 않습니다.

## 파일과 업데이트

정본은 [`skills/progress-report/SKILL.md`](skills/progress-report/SKILL.md)입니다. 현재 플러그인 버전은 `0.1.0`이며 새 버전을 낼 때 호스트 매니페스트를 함께 갱신해야 합니다.

Codex에서 갱신 후 다시 설치하려면 다음을 실행합니다.

```bash
codex plugin marketplace upgrade progress-report
codex plugin remove progress-report@progress-report
codex plugin add progress-report@progress-report
```

Claude Code에서는 다음을 실행합니다.

```bash
claude plugin marketplace update progress-report
claude plugin update progress-report@progress-report
```

제거하려면 `codex plugin remove progress-report@progress-report` 또는 `claude plugin uninstall progress-report@progress-report`을 실행합니다.
