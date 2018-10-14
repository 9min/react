# React와 JSX의 탄생 배경

React의 개발사인 페이스북은 자사 제품들에 PHP를 사용하기로 알려져 있습니다.  
페이스북의 PHP사랑은 유명한데, PHP를 C++로 컴파일한 후 빌드하는 Hiphop for PHP라는 프로젝트를 진행하여(현재는 HHVM으로 대체) PHP 자체의 성능을 개선하기도 했고, PHP 엔진 자체를 수정한 HHVM(hack과 그 어플리케이션을 실행하는 힙합가상머신)을 개발하여 발표하기도 했습니다.  
페이스북은 PHP라는 언어 자체에 대해 고민하고 Hack이라는 PHP의 문법 개정판과 같은 언어를 개발하기도 했는데, 2010년에 발표한 XHP역시 PHP언어의 문법 구조를 바꾸려는 노력의 하나였습니다.  
XHP는 HTML 출력이 많은 PHP 언어 특성에 맞춰 XML또는 HTML을 더 빠르고 편하게 출력하는 데 목적이 있는데, 바로 아래와 같이 사용이 가능합니다. 

```php
<?php
  if ($_POST['name']) {
?>
    <span>this is <?=$_POST['name']?> block.</span>
<?php
  } else {
?>
    <span>this is else block</span>
<?php
  }
?>
```

위 코드가 일반적인 php를 사용할 때의 코드라면 XHP를 사용하게 되면 아래와 같이 표현이 가능하게 됩니다.

```php
<?php
  if ($_POST['name']) {
    echo <span>this is {$_POST['name']}</span>;
  } else {
    echo <span>this is else block</span>
  }
?>
```

echo 명령어를 통해 간단하게 HTML 출력이 가능합니다.  
페이스북은 이 아이디어를 브라우저로 옮기고자했고, 이것이 바로 React가 된 것입니다.  

JSX라는 자바스크립트의 확장 문법을 통해 XHP와 유사한 기능을 React라는 이름으로써 제공하게 된 것이 React의 탄생배경입니다.


참고 사이트 출처  
[http://wiki.sys4u.co.kr/pages/viewpage.action?pageId=8553032](http://wiki.sys4u.co.kr/pages/viewpage.action?pageId=8553032)