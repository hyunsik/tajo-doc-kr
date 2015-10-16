*******************************
JSON 함수
*******************************

.. function:: json_extract_path_text (string json, string xpath)

  ``json`` 으로부터 JSON 문자열을 추출하여 ``xPath`` 로 반환합니다.

  :param string:
  :param string:
  :rtype: text
  :example:

  .. code-block:: sql

    json_extract_path_text('{"test" : {"key" : "tajo"}}','$.test.key');
    > tajo
