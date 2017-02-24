Apache Tajo 한글 문서 프로젝트 (tajo-doc-kr)
=========================================================
Apache Tajo의 [공식 메뉴얼] (http://tajo.apache.org/docs/devel/)을 한글로 번역 및 작성 합니다. 또한 Apache Tajo 커미터의 도움으로 한글로 잘 작성된 문서는 Apache Tajo 프로젝트에 영문으로 번역되어 반영될 수 있습니다.

*현재 0.11.0 릴리즈 문서가 번역되고 있습니다.*

번역 문서
=========
* Tajo (개발 버전) 사용자 메뉴얼 (http://hyunsik.github.io/tajo-doc-kr/docs/0.11.0/) (진행중)
* Tajo (완료) 사용자 메뉴얼 (http://hyunsik.github.io/tajo-doc-kr/docs/0.10.0/)

문서포맷
=========
Apache Tajo의 공식 문서와 똑같은 reStructuredText 포맷 (http://docutils.sourceforge.net/rst.html) 을 사용합니다.
소스는 Sphinx (http://sphinx-doc.org/index.html) 를 이용해 문서를 생성하게 되어 있습니다. 

Sphinx나 reStructuredText은 위지웍(WYSIWYG) 도구를 제공하거나 markdow과 같이 쉽지는 않지만 문서의 계층구조를 가지는 체계적인 문서를 작성하는데 아주 강력한 도구입니다. Python의 공식문서도 이 도구를 이용하여 문서를 만듭니다.

reStructuredText 포맷에 대한 레퍼런스는 아래 링크에서 참고 하세요.
 * reStructuredText reference (http://docutils.sourceforge.net/rst.html)
 * reStructuredText Primer (http://sphinx-doc.org/rest.html)

문서 빌드 하기
==============
Python의 ```easy_install```를 이용해서 Sphinx를 설치하세요.
```
$ easy_install -U Sphinx
```
설치가 완료되면 문서를 빌드하기 위해서는 ```make html```만 실행해주시면 됩니다.
```
$ make html
$ ls -l tajo-doc-kr/target/html
index.html
....
....
```

Mailing List
============
 * tajo-doc-kr at googlegroups.com (https://groups.google.com/forum/#!forum/tajo-doc-kr/new)
 
공헌하기
===========
 * 이슈 트래커 (https://github.com/hyunsik/tajo-doc-kr/issues)
 * https://github.com/hyunsik/tajo-doc-kr/wiki/HowToContribute 페이지를 읽어주세요!

공헌한 분들
===========
참여를 원하시는 분들은 메일로 연락 주세요.

 * 최현식 (Hyunsik Choi) (https://github.com/hyunsik)
 * 남윤민 (Yoonmin Nam) (https://github.com/RonyMin)
 * 장정식 (Jeongshik Jang) (https://github.com/jsjang0070)
 * 현동준 (Dongjoon Hyun) (https://github.com/dongjoon-hyun)
 * 강정화 (JungHwa Kang) (https://github.com/KangJungHwa)
 
License
===========
Apache License 2.0 (http://www.apache.org/licenses/)
