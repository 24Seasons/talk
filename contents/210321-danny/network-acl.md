# 네트워크 ACL을 사용하는 이유

발표([영상](https://www.youtube.com/watch?v=ZDJuoDiMGC4&t=782s)) 중에 나왔던 네트워크 ACL 질문에 대한 답변입니다.

-> [질문 부분 링크](https://www.youtube.com/watch?t=932&v=ZDJuoDiMGC4&feature=youtu.be)

---

발표에서는 반드시 인바운드, 아웃바운드 규칙을 모두 열어두어야 한다는 느낌으로 이야기를 했었는데 모두 열어두어야 하는 것은 아닙니다.

네트워크 ACL은 서브넷 범위에서 규칙을 적용할 수 있고, 보안 그룹과 마찬가지로 회사 내부 네트워크에서의 SSH 접근만을 허용하거나 특정 아이피의 접근을 차단하는 규칙을 인바운드 규칙으로 적용할 수 있습니다.

다만 ACL은 Stateless 하기 때문에 반드시 아웃바운드 규칙도 신경써서 적용을 해주어야 합니다. 아래 이미지를 예로 들어보겠습니다.

![21](https://github.com/24Seasons/talk/blob/main/contents/210321-danny/images/21.png?raw=true)

사용자가 SSH 접근을 하기 위해 22번 포트를 목적지로 합니다. 출발지의 포트는 임시포 트를 사용하게 됩니다. 사용자별로 사용하는 임시 포트가 다르고 랜덤하게 사용됩니다. 그렇기 때문에 아웃바운드 규칙을 [임시 포트](https://whackur.tistory.com/98) (Ephemeral Port) 1024 ~ 65535로 범위를 지정해 주어야 합니다.

일반적인 접근 제어는 보안 그룹을 통해서 대부분 가능하지만 IP 대역 레벨에서 차단이 필요할 경우 네트워크 ACL을 사용한다고 합니다. 그리고 네트워크 성능이 Stateful 한 방화벽 보다 빠르게 처리가 된다고 합니다.

참고했던 [책](http://www.yes24.com/Product/Goods/93887402)에서는 ACL은 위에서도 설명했듯이 Stateless 하기 때문에 인바운드 규칙과 아웃바운드 규칙을 모두 설정해 주어야 하는 규칙 설정이 민감하여 잘 안 쓰는 편이라고 합니다.

---

### 참고

- [ACL 네트워크 란 무엇입니까?](https://www.netinbag.com/ko/internet/what-is-an-acl-network.html)
- [ACL(Aceess Control List)](https://m.blog.naver.com/printf7/10171744397)
- [ACL과 NAT](http://cocotp10.blogspot.com/2018/01/acl-access-control-lists-nat-network.html)
- 따라하며 배우는 AWS 네트워크 입문
