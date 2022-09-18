# 들어가기

코드 예시를 통해 객체 지향 설계의 장점을 보여준다.

# 예시 UI

![화면 구성](./asset/%ED%99%94%EB%A9%B4%EA%B5%AC%EC%84%B1.png)

# 초기구현

## 기능

메뉴를 누르면 화면 영역에 알맞은 내용이 출력된다.  
공통 버튼을 누르면 메뉴에 따라 화면 영역의 데이터가 변경된다.

```Java
public class Application implements OnClickListener {
    private Menu menu1 = new Menu("menu1");
    private Menu menu2 = new Menu("menu2");
    private Button button1 = new Button("button1");
    // private Button button2 = new Button("button2");

    private String currentMenu = null;

    public Application() {
        menu1.setOnClickListener(this);
        menu2.setOnClickListener(this);
        button1.setOnClickListener(this);
        // button2.setOnClickListener(this);
    }

    public void clicked(Component eventSource) {
        if (eventSource.getId().equals("menu1")) {
            changeUIToMenu1();
        } else if (eventSource.getId().equals("menu2")) {
            changeUIToMenu2();
        } else if (eventSource.getId().equals("button1")) {
            if (currentMenu == null) {
                return;
            }

            if (currentMenu.equals("menu1")) {
                processButton1WhenMenu1();
            } else if (currentMenu.equals("menu2")) {
                processButton1WhenMenu2();
            }
        }
        // else if (eventSource.getId().equals("button2")) {
        //     if (currentMenu == null) {
        //         return;
        //     }

        //     if (currentMenu.equals("menu1")) {
        //         processButton2WhenMenu1();
        //     } else if (currentMenu.equals("menu2")) {
        //         processButtonWhenMenu2();
        //     }
        // }
    }

    private void changeUIToMenu1() {
        currentMenu = "menu1";
        System.out.println("메뉴1 화면으로 전환");
    }
    private void changeUIToMenu2() {
        currentMenu = "menu2";
        System.out.println("메뉴2 화면으로 전환");
    }
    private void processButton1WhenMenu1() {
        System.out.println("메뉴1 화면의 버튼1 처리");
    }
    private void processButton1WhenMenu2() {
        System.out.println("메뉴2 화면의 버튼1 처리");
    }
    // private void processButton2WhenMenu1() {
    //     System.out.println("메뉴1 화면의 버튼1 처리");
    // }
    // private void processButton2WhenMenu2() {
    //     System.out.println("메뉴2 화면의 버튼1 처리");
    // }
}

```

## 문제점

기능이 추가 / 수정 될 때 마다 변경되어야 하는 코드가 많다.  
코드가 복잡하여 수정하기 쉽지 않다.
책임이 많다.

- 클릭된 메뉴, 버튼을 구분하여 함수를 실행 (컨트롤러 성격)
- 각 메뉴 및 버튼이 선택되었을 때에 대한 처리 로직 실행

# 수정하기 좋은 구조

추상화와 다형성을 이용해서 변화되는 부분을 관리한다.

- 추상화 - 공통 적인 부분만을 추출하여 만든 개념
- 다형성 - 행동은 같지만 실제 표현이 다름 / 짖다 - 강아지 멍멍, 오리 꽥꽥

공통적인 부분

- 메뉴가 선택되면 해당 화면을 보여준다.
- 버튼을 클릭하면 선택된 메뉴에 따라 알맞은 처리를한다.

```Java
public interface ScreenUI {
    public void show();
    public void handleButton1Click();
}

public class Menu1ScreenUI implements ScreenUI {
    public void show() {
        System.out.println("메뉴1 화면으로 전환");
    }
    public void handleButton1Click() {
        System.out.println("메뉴1 화면의 버튼1 처리");
    }
}
public class Menu2ScreenUI implements ScreenUI {
    public void show() {
        System.out.println("메뉴2 화면으로 전환");
    }
    public void handleButton1Click() {
        System.out.println("메뉴2 화면의 버튼1 처리");
    }
}

public class Application implements OnClickListener {
    private Menu menu1 = new Menu("menu1");
    private Menu menu2 = new Menu("menu2");
    private Button button1 = new Button("button1");

    private ScreenUI currentScreen = null;

    public Application() {
        menu1.setOnClickListener(this);
        menu2.setOnClickListener(this);
        button1.setOnClickListener(this);
    }

    public void clicked(Component eventSource) {
        String sourceId = eventSource.getId();
        if (sourceId.equals("menu1")) {
            currentScreen = new Menu1ScreenUI();
            currentScreen.show()
        } else if (sourceId.equals("menu2")) {
            currentScreen = new Menu2ScreenUI();
            currentScreen.show()
        } else if (sourceId.equals("button1")) {
            if (currentScreen == null) {
                return;
            }
            currentScreen.handleButton1Click();
        }
    }
}
```

# 추가 개선

clicked method의 변경 원인이 여러가지다.  
menu의 추가, 수정 / button의 추가, 수정  
따라서 clicked method가 서로 다른 책임을 가지고 있다.

```Java
public interface ScreenUI {
    public void show();
    public void handleButton1Click();
}

public class Menu1ScreenUI implements ScreenUI {
    public void show() {
        System.out.println("메뉴1 화면으로 전환");
    }
    public void handleButton1Click() {
        System.out.println("메뉴1 화면의 버튼1 처리");
    }
}
public class Menu2ScreenUI implements ScreenUI {
    public void show() {
        System.out.println("메뉴2 화면으로 전환");
    }
    public void handleButton1Click() {
        System.out.println("메뉴2 화면의 버튼1 처리");
    }
}

// public class Menu3ScreenUI implements ScreenUI {
//     public void show() {
//         System.out.println("메뉴2 화면으로 전환");
//     }
//     public void handleButton1Click() {
//         System.out.println("메뉴2 화면의 버튼1 처리");
//     }
// }

public class Application{
    private Menu menu1 = new Menu("menu1");
    private Menu menu2 = new Menu("menu2");
    // private Menu menu2 = new Menu("menu3");
    private Button button1 = new Button("button1");

    private ScreenUI currentScreen = null;

    public Application() {
        menu1.setOnClickListener(menuListener);
        menu2.setOnClickListener(menuListener);
        // menu3.setOnClickListener(menuListener);
        button1.setOnClickListener(buttonListener);
    }
    private OnClickListener menuListener = new OnClickListener() {
        public void clicked(Component eventSource) {
            String sourceId = eventSource.getId();
            if (sourceId.equals("menu1")) {
                currentScreen = new Menu1ScreenUI();
            } else if (sourceId.equals("menu2")) {
                currentScreen = new Menu2ScreenUI();
            }
            // else if (sourceId.equals("menu3")) {
            //     currentScreen = new Menu3ScreenUI();
            // }
            currentScreen.show()
        }
    }

    private OnClickListener buttonListener = new OnClickListener() {
        public void clicked(Component eventSource) {
            if (currentScreen == null) {
                    return;
                }
            String sourceId = eventSource.getId();
            if (sourceId.equals("button1")) {
                currentScreen.handleButton1Click();
            }

        }
    }
}
```

# 구조 수정을 통해 얻게된 이점

- 새로운 메뉴 추가 시, **버튼 처리 코드가 영향을 받지 않음**
- 한 메뉴 관련 코드가 한 개의 클래스로 모여서 코드 **분석/수정이 용이함**
- 서로 다른 메뉴에 대한 처리 코드가 섞여 있지 않아 수정이 용이함

즉, 코드 수정이 용이해졌고 수정에 대한 영향력이 최소화되었다.
