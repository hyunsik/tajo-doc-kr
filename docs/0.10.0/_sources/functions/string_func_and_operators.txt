******************************
문자열 함수와 연산자들
******************************

.. function:: str1 || str2

  입력된 두개의 문자열 str1과 str2를 붙여 하나의 문자열로 만듭니다.

  :인자 str1: 첫번째 문자열
  :인자 str2: 두번째 문자열
  :리턴타입: text
  :예제:

  .. code-block:: sql

    select ‘Ta’ || ‘jo’; 
    > 'Tajo'
  

.. function:: char_length (string text)

  입력된 문자열에 포함된 문자의 갯수를 돌려줍니다.

  :인자 string: 입력 문자열
  :리턴타입: int4
  :동일한 함수: character_length
  :예제:

  .. code-block:: sql

    select char_length(‘Tajo’);
    > 4


.. function:: trim([leading | trailing | both] [characters] from string)

  입력된 문자열중 앞, 뒤, 혹은 양쪽을 지정해 특정 문자를 지웁니다. 특정 문자를 지정하지 않는다면, 기본적으로 공백을 제거한 문자열을 돌려줍니다.

  :인자 string: 입력 문자열
  :인자 characters: 입력 문자열에서 지우고자 하는 특정 문자
  :리턴타입: text
  :예제:

  .. code-block:: sql

    select trim(both ‘x’ from ‘xTajoxx’);
    > Tajo   


.. function:: btrim(string text, [characters text])

  입력된 문자열 양 끝에서 특정한 문자를 제거한 문자열을 돌려줍니다. 특정 문자를 지정하지 않는다면, 기본적으로 공백을 제거한 문자열을 돌려줍니다.
  
  :인자 string: 입력 문자열
  :인자 characters: 입력 문자열에서 지우고자 하는 특정 문자
  :리턴타입: text
  :동일한 함수: trim
  :예제:

  .. code-block:: sql

    select btrim(‘xTajoxx’, ‘x’);
    > Tajo 


.. function:: ltrim(string text, [characters text])

  입력된 문자열의 앞에서 특정한 문자를 제거한 문자열을 돌려줍니다. 특정 문자를 지정하지 않는다면, 기본적으로 공백을 제거한 문자열을 돌려줍니다.
  
  :인자 string: 입력 문자열
  :인자 characters: 입력 문자열에서 지우고자 하는 특정 문자
  :리턴타입: text
  :예제:

  .. code-block:: sql

    select ltrim(‘xxTajo’, ‘x’);
    > Tajo 


.. function:: rtrim(string text, [characters text])

  입력된 문자열의 뒤에서 특정한 문자를 제거한 문자열을 돌려줍니다. 특정 문자를 지정하지 않는다면, 기본적으로 공백을 제거한 문자열을 돌려줍니다.
  
  :인자 string: 입력 문자열
  :인자 characters: 입력 문자열에서 지우고자 하는 특정 문자
  :리턴타입: text
  :예제:

  .. code-block:: sql

    select rtrim('Tajoxx', 'x');
    > Tajo 


.. function:: split_part(string text, delimiter text, field int)

  구분기호를 기준으로 쪼개진 문자열 중 지정한 특정 문자열을 돌려줍니다. 기본적으로 문자열의 위치는 0이 아닌 1부터 매겨집니다.

  :인자 string: 구분기호를 포함한 입력 문자열
  :인자 delimiter: 입력 문자열을 쪼개는 기준이 되는 구분기호
  :인자 field: 원하는 문자열의 위치
  :리턴타입: text
  :예제:

  .. code-block:: sql

    select split_part(‘ab_bc_cd’,‘_’,2);   
    > bc 


.. function:: regexp_replace(string text, pattern text, replacement text)

  입력된 정규식 패턴과 일치하는 입력문자열의 일부분을 특정 문자열로 치환합니다.

  :인자 string: 입력 문자열
  :인자 pattern: 치환하고자 하는 정규식 패턴
  :인자 replacement: 일치하는 정규식 패턴과 치환될 문자열
  :리턴타입: text
  :예제:

  .. code-block:: sql

    select regexp_replace(‘abcdef’, ‘(ˆab|ef$)’, ‘–’); 
    > –cd–


.. function:: upper(string text)

  입력된 문자열을 대문자로 치환해 돌려줍니다.

  :인자 string: 입력 문자열
  :리턴타입: text
  :예제:

  .. code-block:: sql

    select upper('tajo');
    > TAJO


.. function:: lower(string text)

  입력된 문자열을 소문자로 치환해 돌려줍니다.

  :인자 string: 입력 문자열
  :리턴타입: text
  :예제:

  .. code-block:: sql

    select lower('TAJO');
    > tajo
