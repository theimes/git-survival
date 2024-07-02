# Git

Ein git Repository ist aus praktischer Sicht eine Abfolge von aufgezeichneten
Zuständen eines Ordners mit Textdateien 

> wobei vorrangig Softwareprogramme verwaltet werden ;-)

## Commit

Ein `commit` speichert grundsätzlichen einen Zustand aller Dateien eines Ordners.

Sollen Dateien ignoriert werden, sind entsprechende Einträge in der Datei [`.gitignore`](https://git-scm.com/docs/gitignore)
zu hinterlegen.

Beispiel:

```
# wild-card
*.py[cod]

# einzelne Dateien order Ordner
.Python
build/
```

## Repository clonen

```shell
git clone <remote url>
```

Legt im aktuellen Ordner einen Unterordner mit den Dateien des Repositories an.

## Änderungen vornehmen

Geänderte Dateien mit `git add <FILE_PATH>` in der Versionsverwaltung vormerken
und mit `git commit` speichern.

Dabei öffnet  sich ein Editor, damit eine Beschreibung des commits eingegeben wird.
Diese ist zwingend, d.h. eine leere Beschreibung bricht den Commitvorgang ab.

### Commit message

Je nach Grad der Zusammenarbeit im Team, sollte die Commitbeschreibung eine
kurze und prägnante Zusammenfassung der vorgenommenen Änderungen enthalten.

Commits sollten *eine* atomare Änderung enthalten. Es ist besser, viele kleine
commits zu haben, als einen großen. Zudem sollten die Änderungen zur Sicherung
der eigenen Arbeit möglichst oft nach `remote` gepusht werden.

Wie es nicht geht: [tutorial](https://www.linkedin.com/pulse/how-write-very-bad-commit-messages-ran-bar-zik), [whatthecommit](https://whatthecommit.com/), [codelord](https://www.codelord.net/2015/03/16/bad-commit-messages-hall-of-shame/)

Ein pragmatischer Ansatz ist auf [dev.to](https://dev.to/thawkin3/how-to-write-awful-commit-messages-and-good-ones-too-1f8m)
beschrieben.

Mit `git commit -m` läßt sich direkt eine commit message übergeben, die dann allerdings nicht mehrzeilig sein kann.

### Commit `--amend`

Mit `git commit --amend` lassen sich weitere commits zu einem früheren commit hinzufügen.

Aber **Achtung**: das ist nur solange sicher, wie der commit noch nicht gepusht wurde. 
Ansonsten wird die git Historie zerstört.

## Branches

Ein `Branch` ist ein benannter `commit`.

```shell
git checkout -b <branch-name>
```

Wenn der Name des Branches mit `feat/` beginnt und die Jira Ticketnummer folgt (z.B. `DATA-42`),
werden Information zum Entwicklungsfortschritt im Ticket angezeigt.

![git info im jira ticket](git-info-in-jira-ticket.png)

### Tags

Eine Sonderform von benannten commits sind `tags`. Diese sind geeignet, um auf dem Repository
einen Zustand zu kennzeichnen, damit dieser wiederholbar abgerufen werden kann.

Wird verwendet um produktiv deploybaren Code zu kennzeichnen.

Beispiel `git tag v0.0.1` erstellt einen Tag lokal, mit `git push origin v0.0.1` wird dieser
nach remote übertragen.

Vorhandene tags werden mit `git tag --list` angezeigt.

## Synchronisierung mit `remote`

```shell
git fetch [--all]
```

Mit der Option `--all` werden Informationen über alle Branches abgeholt.

Hilfreich um zu sehen, ob eventuell ein Kollege bereits einen neuen Branche
für ein Ticket angelegt hat.

### Pull

Schreibt die Änderungen des `remote` Branches in die lokale Kopie.

Dabei können Konflikte auftreten, weil Änderungen von `remote` mit
lokalen Änderungen kolidieren.

In diesem Fall hilft ```git stash``` womit die aktuellen Änderungen in einen
*quasi* Branch geschrieben werden. 

Einzelne stashes werden nach dem Muster `stash@{<NUMMER>}` referenziert.

Nützliche commands sind:

- `git stash list` zum Auflisten aller stashes.
- `git stash pop stash@{<NUMMER>}` zum Anwenden der im stash gespeicherten Änderungen und anschließendem Löschen des stashes.
- `git stash drop stash@{<NUMMER>}` zum Löschen eines stashes.

Achtung: ein `git apply stash@{<NUMMER>}` beläßt den stash im Verzeichnis, so daß sich im Laufe der
Zeit eine ganze Menge ansammeln kann.

### `Merge` Konflikte

Wichtigste Regel: *Don't panic*.

1. Das lokale Repository aktualisieren.
1. Zum zu mergenden Branch wechseln und den Konflikt-Branch (also `main` oder `develop`)
   in diesen mergen.
   ```shell
   git merge main
   ```
1. Die Konflikte in `Visual Studio Code` lösen. Der merge-Editor bietet dazu eine
   transparente Ansicht.
1. Die Änderungen in den remote Branch pushen.

### Push

Beim pushen eines branches wird der lokale branch nicht mit `remote` verknüpft.

D.h. weitere `push` oder `pull` laufen ins leere.

```shell
$ git push origin feat/updated 

$ git pull
There is no tracking information for the current branch.
Please specify which branch you want to merge with.
See git-pull(1) for details.

    git pull <remote> <branch>

If you wish to set tracking information for this branch you can do so with:

    git branch --set-upstream-to=origin/<branch> feat/updated
```

Ein `push` ohne verknüpften `remote` branch gibt einen freundlichen Hinweis,
was zu tun ist, um das Probelem zu beseitigen.

```shell
$ git push
fatal: The current branch feat/updated has no upstream branch.
To push the current branch and set the remote as upstream, use

    git push --set-upstream origin feat/updated
```

## Diff

Struktur

```shell
git diff [commit#1..commit#2]
```

Dabei könen die commits als hash oder als Name eingegeben werden.

Werden keine Argumente übergeben, werden die Änderungen im aktuellen Ordner gegenüber
den gespeicherten Daten angezeigt.

## `Merge` | `Rebase` | `Squash` Strategie

Letzlich ist `merge` die sicherste Strategie. Nachteilig ist das Aufblähen der Commit Historie,
was bei sehr großen Repositories (z.B. Linux) zu Problemen führen kann in unserer alltäglichen
Arbeit jedoch kaum eine Rolle spielt (sh. [Offizielle `merge` Doku](https://git-scm.com/docs/git-merge)).

Manchmal hört man den Vorschlag, doch einfach einen `git rebase` zu machen. 

Dazu eine kleine Einordnung: wenn "normale" git Befehle so etwas wie Pflaster aufkleben sind, ist
ein `git rebase` eher eine [Operation am offenen Herzen](https://medium.com/@dirk.avery/the-definitive-git-rebase-guide-dbd7717f9437).

> quidquid agis, prudenter agas et respice finem

## Disaster Recovery

Sollten Dinge mal wirklich schlecht laufen ist [oshitgit](https://ohshitgit.com/de) erste Anlaufstelle.
