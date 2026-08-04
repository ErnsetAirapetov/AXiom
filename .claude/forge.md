Фордж: GitHub (gh CLI)

| действие | команда |
|---|---|
| список меток | gh label list |
| создать метку | gh label create "<имя>" -c <hex без #> -d "<описание>" |
| удалить метку | gh label delete "<имя>" --yes |
| список задач | gh issue list --state open --limit 50 --json number,title,labels |
| прочитать задачу | gh issue view <N> |
| создать задачу | gh issue create --title "<t>" --body-file <f> --label <l> |
| комментарий к задаче | gh issue comment <N> -b "<текст>" |
| пометить решением | gh issue edit <N> --add-label needs-decision |
| закрыть задачу | gh issue close <N> -c "<итог>" |
| создать милстоун | gh api repos/{owner}/{repo}/milestones -f title="<t>" |
| создать задачу с милстоуном | gh issue create --title "<t>" --body-file <f> --label <l> --milestone "<t>" |
| закрыть милстоун | gh api -X PATCH repos/{owner}/{repo}/milestones/<num> -f state=closed |
| список PR | gh pr list --json number,title,headRefName,statusCheckRollup |
| прочитать PR / дифф | gh pr view <N> / gh pr diff <N> |
| создать PR | gh pr create --base main --title "<t>" --body-file <f> (в теле: Closes #N) |
| добавить метку к PR | gh pr edit <N> --add-label <l> |
| комментарий к PR | gh pr comment <N> -b "<текст>" |
| смержить PR | gh pr merge <N> --squash --delete-branch |

Нюанс: `--delete-branch` падает, если ветку ещё держит worktree, хотя PR к
этому моменту уже `MERGED` — код возврата в этом случае лжёт про провал.
Ритуал `/land` поэтому снимает worktree до мержа; признак успеха — состояние
PR на фордже, не код возврата команды.

Windows:
- команды форджа и ритуалы запускать из PowerShell;
- перед первой командой форджа в сессии:
  `[Console]::OutputEncoding = [System.Text.Encoding]::UTF8`.
  Без этого UTF-8 вывод gh/glab приходит кракозябрами на кодовой странице
  cp1251/cp866, и русские титулы задач нечитаемы;
- длинный русский текст (тело задачи, тело PR) подавать файлом:
  `--body-file <f>` у gh, `-d (Get-Content -Raw -Encoding UTF8 <f>)` у glab
  (без `-Encoding UTF8` файл без BOM прочитается в ANSI и текст манглится
  молча). В аргументе командной строки он тоже манглится;
- путь `gh api` брать в кавычки: `gh api "repos/{owner}/{repo}/milestones"`.
  Без кавычек PowerShell разбирает `{owner}` как скриптблок, и gh падает с
  `The command parameter was already specified`;
- если всё же работаете из Git Bash: аргумент, начинающийся со слэша,
  MSYS превратит в путь (`/land` → `C:/Program Files/Git/land`) ещё до
  запуска программы. Обход — `MSYS_NO_PATHCONV=1` или двойной слэш `//land`.
