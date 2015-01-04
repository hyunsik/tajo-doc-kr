*****************************
수학함수와 연산자들
*****************************

.. function:: abs (number int|float)

  입력된 수의 절대값을 돌려줍니다.

  :인자 number: 입력된 수
  :리턴타입: 입력된 인자와 동일한 타입
  :예제:
  
  .. code-block:: sql

    select abs(-9); 
    > 9

.. function:: acos (number float)

  입력된 수의 아크 코사인 값을 돌려줍니다.

  :인자 number: 입력된 라디안 (radian) 값
  :rtype: float8
  :예제:

  .. code-block:: sql

    select acos(0.3); 
    > 1.2661036727794992 

.. function:: asin (number float)

  입력된 수의 아크 사인 값을 돌려줍니다.

  :인자 number: 입력된 라디안 (radian) 값
  :리턴타입: float8
  :예제:

  .. code-block:: sql

    select acos(0.8); 
    > 0.9272952180016123

.. function:: atan (number float8)

  입력된 수의 아크 탄젠트 값을 돌려줍니다.

  :인자 number: 입력된 라디안 (radian) 값
  :리턴타입: float8
  :예제:

  .. code-block:: sql

    select atan(0.8); 
    > 0.6747409422235527

.. function:: atan2 (y float, x float)

  입력된 직교좌표계 (x,y)를 극좌표계(r, theta)로 변환하고, 각 theta 의 값을 돌려줍니다.

  :인자 y: 세로좌표 (y축)
  :인자 x: 가로좌표 (x축)
  :리턴타입: float8
  :예제:

  .. code-block:: sql

    select atan2(2.7, 0.3);
    > 1.460139105621001

.. function:: cbrt (number float)

  입력된 수의 세제곱근 계산하여 돌려줍니다.

  :인자 number: 입력된 값
  :리턴타입: float8
  :예제:

  .. code-block:: sql

    select cbrt(27.0); 
    > 3.0

.. function:: ceil (number float)

  입력된 값보다 큰 정수들 중 가장 작은 값을 돌려줍니다.

  :인자 number: 입력된 값
  :리턴타입: int8
  :예제:

  .. code-block:: sql

    select ceil(-42.8); 
    > -42

.. function:: cos (number float)

  입력된 값의 코사인 값을 돌려줍니다.

  :인자 number: 입력된 라디안 (radian) 값
  :리턴타입: float8
  :예제:

  .. code-block:: sql

    select cos(0.7);
    > 0.7648421872844885

.. function:: degrees (number float)

  입력된 라디안 (radian) 값을 각도로 변환해 돌려줍니다.
  
  :인자 number: 라디안 (radian) 값
  :리턴타입: float8
  :예제:

  .. code-block:: sql

    select degrees(0.8);
    > 45.83662361046586

.. function:: div (num1 int, num2 int)

  입력된 두개의 정수를 나누고 (num1 / num2), 그 몫을 돌려줍니다.

  :인자 num1: 피제수
  :인자 num2: 제수
  :리턴타입: int8
  :예제:

  .. code-block:: sql

    select div(8,3);
    > 2

.. function:: exp (number float)

  오일러 상수 e에 대해 입력된 수의 지수승을 돌려줍니다.

  :예제 number: 입력된 값
  :리턴타입: float8
  :예제:

  .. code-block:: sql

    select exp(1.0);
    > 2.718281828459045

.. function:: floor (number float)

  입력된 값보다 작은 정수 중 가장 큰 수를 반환합니다.

  :인자 number: 입력된 값
  :리턴타입: int8
  :예제:

  .. code-block:: sql

    select floor(53.1); 
    > 53

.. function:: mod (num1 int, num2 int)

  입력된 두 수를 나누고 (num1 / num2), 그 나머지를 반환합니다.

  :인자 num1: 제수
  :인자 num2: 피제수
  :리턴타입: int8
  :예제:

  .. code-block:: sql

    select mod(10,3);
    > 1

.. function:: pi ()

  원주율을 돌려줍니다.

  :리턴타입: float8
  :예제:

  .. code-block:: sql

    select pi();
    > 3.141592653589793

.. function:: pow (x float, y float)
  
  입력된 값 x의 y 거듭제곱을 돌려줍니다.

  :인자 x: 밑
  :인자 y: 지수
  :리턴타입: float8
  :예제:

  .. code-block:: sql

    select pow(2.0, 10.0);
    > 1024.0

.. function:: radians (number float)
 
  입력된 각도 값을 라디안 (radian) 값으로 변환해 돌려줍니다.

  :인자 number: 각도 값
  :리턴타입: float8
  :예제:

  .. code-block:: sql

    select radians(45.0);
    > 0.7853981633974483

.. function:: round (number int|float)

  입력된 값의 반올림 값을 돌려줍니다.

  :인자 number: 입력 값
  :리턴타입: int8
  :예제:

  .. code-block:: sql

    select round(5.1); 
    > 5

.. function:: sign (number int|float)

  입력된 값의 부호를 돌려줍니다.

  :인자 number: 입략 깂
  :리턴타입: float8
  :예제:

  .. code-block:: sql

    select sign(-8.4); 
    > -1.0

.. function:: sin (number float)

  입력 값의 사인 값을 돌려줍니다.

  :인자 number: 입력 값
  :리턴타입: float8
  :예제:

  .. code-block:: sql

    select sin(1.0); 
    > 0.8414709848078965

.. function:: sqrt (number float8)

  입력된 값의 제곱근을 돌려줍니다.

  :인자 number: 입력 값
  :리턴타입: float8
  :예제:

  .. code-block:: sql

    select sqrt(256.0); 
    > 16.0

.. function:: tan (number float)

  입력된 값의 탄젠트 값을 돌려줍니다.

  :인자 number: 입력 값
  :리턴타입: float8
  :예제:

  .. code-block:: sql

    select tan(0.2); 
    > 0.2027100355086725
