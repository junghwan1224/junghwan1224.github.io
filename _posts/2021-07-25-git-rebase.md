---
layout: post
title: What is git rebase??
date: 2021-07-25 12:00:00 +0900
category: Git
---

정리할 내용

## git rebase

- 커밋의 base를 다시 정하는 작업
- 한 브랜치에서 다른 브랜치로 합치는 방법 중 하나(다른 하나는 `git merge`)
- 깔끔한 커밋 이력 관리가 가능
- [Git rebase 설명](https://git-scm.com/book/ko/v2/Git-%EB%B8%8C%EB%9E%9C%EC%B9%98-Rebase-%ED%95%98%EA%B8%B0)
- `git merge` 를 사용하면, 두 브랜치의 마지막 커밋과 공통 조상을 사용하는 `3-way merge` 로 새로운 커밋을 만들어낸다.
- 반면 `git rebase` 를 사용하면 한 브랜치에서 변경된 사항을 다른 브랜치에 적용할 수 있다.
- 동작 원리: 두 브랜치가 나뉘기 전인 공통 커밋으로 이동하고 나서 그 커밋부터 지금 `checkout` 한 브랜치가 가리키는 커밋까지 diff를 만들어 차례로 어딘가에 임시로 저장해 놓는다. rebase할 브랜치(checkout한 브랜치)가 합칠 브랜치(master)가 가리키는 커밋을 가리키게 하고, 아까 저장해 놓았던 변경사항을 차례대로 적용한다. 그리고나서 master 브랜치를 `fast-forward` 시킨다.

## fast-forward merge

- 빨리감기 방식이라고도 하며, 별도의 커밋을 생성하지 않고 마스터가 가리키는 곳을 바꾼다.

## non fast-forward merge

- `fast-forward` 방식이어도 반드시 머지 커밋 생성

