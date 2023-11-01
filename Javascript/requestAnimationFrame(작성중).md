# window.requestAnimationFrame()

window객체 내장 메서드인 `requestAnimationFrame`는 브라우저가 화면을 repaint 하기 직전에 호출되어야 하는 코드를 등록해두는 함수.

바로 실행되는 것이 아니라. 브라우저가 화면을 다시 그리기 직전에 콜백으로 전달받은 함수를 실행하는 것이다.



## 애니메이션 최적화
일반적으로 화면을 그리는 속도는 60FPS를 목표로 한다 . 60FPS가 인간이 인식할 수 있는 화면 깜빡임 수준인것 같다.

