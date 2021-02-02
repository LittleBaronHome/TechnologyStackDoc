# javaFX

## 启动方式

1. 继承`javafx.application.Application`
2. 重写`start(Stage primaryStage)`方法
3. 调用`launch(String... args)`方法

> 若调用launch的方法和重写的start方法不在同一类中，调用launch时可调用其重载方法`launch(Class<? extends Application> appClass, String... args)`传入重写start方法的类
>
> Application父类中也提供了`init()`和`stop()`生命周期的钩子函数
>
> `start()`,`init()`,`stop()`都是单开一个线程做处理。

```java
public class Main extends Application {
    public static void main(String[] args) {
        launch(args);
    }
    @Override
    public void init() throws Exception {
    }
    @Override
    public void start(Stage primaryStage) {
        primaryStage.show(); // 窗口展示
    }
    @Override
    public void stop() throws Exception {
    }
}
```



## Stage常用设置

```java
public void start(Stage primaryStage) {
    primaryStage.setTitle("JavaFX测试案例"); // 设置标题
    primaryStage.getIcons().add(new Image("")); // 设置图标

    primaryStage.setWidth(500);
    primaryStage.setHeight(1000); // 设置窗口大小
    primaryStage.setMinHeight(100);
    primaryStage.setMinWidth(100); // 设置窗口最小大小

    primaryStage.setFullScreen(true);
    primaryStage.setScene(new Scene(new Group())); // 全屏展示（不是最大化）

    primaryStage.setResizable(false); // 设置窗口大小不可调节

    primaryStage.setX(100);
    primaryStage.setY(100); // 设置初始位置

    primaryStage.setOpacity(1); // 设置透明度
    primaryStage.setAlwaysOnTop(true); // 窗口置顶

    primaryStage.show(); // 窗口展示
    primaryStage.close(); // 窗口关闭

    // 屏幕信息
    Screen screen = Screen.getPrimary();
    Rectangle2D rec1 = screen.getBounds(); // 获取屏幕大小
    Rectangle2D rec2 = screen.getVisualBounds(); // 获取屏幕可视化区域大小

    /**
         * 窗口类型
         * DECORATED 白色背景 + 平台样式 【默认】
         * UNDECORATED 白色背景 
         * TRANSPARENT 透明背景
         * UTILITY 白色背景 + 关闭按钮
         * UNIFIED 白色背景 + 平台样式 （消除顶部样式，有些平台不支持会被降为DECORATED）
         */
    primaryStage.initStyle(StageStyle.DECORATED);

    /**
         * 模态框
         * NONE 非模态 不阻塞
         * WINDOW_MODAL 模态 + 阻塞 仅对拥有者窗口 
         				可通过primaryStage.initOwner(Window owner);设置拥有者
         * APPLICATION_MODAL 模态 + 阻塞 对所有窗口
         */
    primaryStage.initModality(Modality.APPLICATION_MODAL);

    // Stage工具类
    Platform.setImplicitExit(false); // 若关闭所有窗口，程序不结束
    platform.exit(); // 自动关闭所有窗口，关闭虚拟机，退出程序
}
```



## 放置控件

> 容器（Stage） - 场景（Scene） - 控件组（Group） - 控件（node）

```java
public void start(Stage primaryStage) {
    Button button = new Button("button"); // 初始化控件
    button.setPrefWidth(10); 
    button.setPrefHeight(10); // 设置控件大小
    
    button.setLayoutX(10);
    button.setLayoutY(10); // 设置控件位置
    
    Group group = new Group();
    group.getChildren().add(button); // 添加控件到控件组
    group.setAutoSizeChildren(true); // 自动设置子控件大小，默认true
    
    Scene screen = new Scene(group); // 添加控件组到场景
    
    primaryStage.setScene(screen); // 设置场景到容器
    primaryStage.show(); // 窗口展示
}
```



## Button

```java
public void start(Stage primaryStage) {
    Button button = new Button("button");
    Button button2 = new Button("button2");

    // 原生设置样式
    Background bg = new Background(new BackgroundFill(
        Paint.valueOf("#F8F8F8"), // 背景色
        new CornerRadii(15), // 圆角
        Insets.EMPTY // padding
    ));
    Border bo = new Border(new BorderStroke(
        Paint.valueOf("#CCCCCC"), // 边框色 
        BorderStrokeStyle.SOLID, // 线类型
        new CornerRadii(15), // 圆角
        new BorderWidths(1) // 线宽度
    ));
    button.setBackground(bg);
    button.setBorder(bo);
    button.setTextFill(Paint.valueOf("#000000")); // 字体颜色

    // CSS设置样式
    button2.setStyle(
        "-fx-background-color: #F8F8F8;" +
        "-fx-background-radius: 15;" +
        "-fx-border-color: #CCCCCC;" +
        "-fx-border-radius: 15;" +
        "-fx-border-width: 1;" +
        "-fx-text-fill: #000000"
    );

    // 监听事件
    // 单击事件
    button.setOnAction(event -> {
        System.out.println("单击了：" + ((Button)event.getSource()).getText());
    });
    // 双击事件
    button.addEventHandler(MouseEvent.MOUSE_CLICKED, event -> {
        if (event.getClickCount() == 2 && 
            (MouseButton.PRIMARY.name()).equals(event.getButton().name()) // 左键
           ) {
            System.out.println("双击了：" + ((Button)event.getSource()).getText());
        }
    });

    Group group = new Group();
    group.getChildren().add(button);
    group.getChildren().add(button2);
    button2.setLayoutX(100);

    Scene screen = new Scene(group);

    primaryStage.setScene(screen);
    primaryStage.setWidth(500);
    primaryStage.setHeight(300);
    primaryStage.show();
}
```



## 快捷键

```java
public void start(Stage primaryStage) {
    Button button = new Button("button");

    Group group = new Group();
    group.getChildren().add(button);

    Scene screen = new Scene(group);

    // 监听键盘事件
    button.setOnKeyPressed(event -> {
        System.out.println("摁下了：" + event.getCode().getName());
    });
    
    // 设置快捷键
    // 将Alt + C 绑定到button上 可能会有平台兼容性问题，不建议使用
    KeyCombination k1 = new KeyCodeCombination(KeyCode.C, KeyCombination.ALT_DOWN);
    Mnemonic m1 = new Mnemonic(button, k1);
    screen.addMnemonic(m1);

    // 自定义快捷键事件
    KeyCombination k2 = new KeyCodeCombination(KeyCode.B, KeyCombination.ALT_DOWN);
    screen.getAccelerators().put(k2, () -> {
        System.out.println("自定义快捷键事件");
        // button.fire(); // 执行button的setOnAction函数
    });

    primaryStage.setScene(screen);
    primaryStage.setWidth(500);
    primaryStage.setHeight(300);
    primaryStage.show();
}
```



## 标签、输入框、密码框

```java
public void start(Stage primaryStage) {
    Group group = new Group();

    // 标签
    Label label1 = new Label("用户名:");
    label1.setLayoutX(10);
    label1.setLayoutY(30);
    Label label2 = new Label("密码:");
    label2.setLayoutX(10);
    label2.setLayoutY(80);

    // 输入框
    TextField textField1 = new TextField();
    textField1.setLayoutX(60);
    textField1.setLayoutY(30);
    textField1.setTooltip(new Tooltip("用户名")); // 光标悬浮提示
    textField1.setPromptText("用户名"); // 失焦框内提示

    // 密码框
    PasswordField passwordField1 = new PasswordField();
    passwordField1.setLayoutX(60);
    passwordField1.setLayoutY(80);
    passwordField1.setTooltip(new Tooltip("密码")); // 光标悬浮提示
    passwordField1.setPromptText("密码"); // 失焦框内提示

    group.getChildren().addAll(label1, label2, textField1, passwordField1);

    Scene screen = new Scene(group);
    primaryStage.setScene(screen);
    primaryStage.setWidth(500);
    primaryStage.setHeight(300);
    primaryStage.show();
}
```



## 布局

> Group是组件组，并不是布局管理，布局管理可以管理Group，可以直接管理控件，也可以管理另一个布局管理对象

### AnchorPane布局

> 绝对定位布局

```java
public void start(Stage primaryStage) {
    AnchorPane ac = new AnchorPane();

    // 标签
    Label label1 = new Label("用户名:");
    AnchorPane.setLeftAnchor(label1, 10.0);
    AnchorPane.setTopAnchor(label1, 30.0);
    Label label2 = new Label("密码:");
    AnchorPane.setLeftAnchor(label2, 10.0);
    AnchorPane.setTopAnchor(label2, 80.0);

    // 输入框
    TextField textField1 = new TextField();
    AnchorPane.setLeftAnchor(textField1, 60.0);
    AnchorPane.setTopAnchor(textField1, 30.0);
    textField1.setTooltip(new Tooltip("用户名")); // 光标悬浮提示
    textField1.setPromptText("用户名"); // 失焦框内提示

    // 密码框
    PasswordField passwordField1 = new PasswordField();
    AnchorPane.setLeftAnchor(passwordField1, 60.0);
    AnchorPane.setTopAnchor(passwordField1, 80.0);
    passwordField1.setTooltip(new Tooltip("密码")); // 光标悬浮提示
    passwordField1.setPromptText("密码"); // 失焦框内提示

    ac.getChildren().addAll(label1, label2, textField1, passwordField1);

    Scene screen = new Scene(ac);
    primaryStage.setScene(screen);
    primaryStage.setWidth(500);
    primaryStage.setHeight(300);
    primaryStage.show();
}
```

### HBox布局

> 水平布局

```java
public void start(Stage primaryStage) {
    AnchorPane ac = new AnchorPane();
    HBox box = new HBox();
    box.setPrefWidth(500);
    box.setPrefHeight(500);
    box.setPadding(new Insets(10)); // 内边距
    box.setSpacing(10.0); // 组件间隔
    box.setAlignment(Pos.CENTER); // 对齐方式

    Button button1 = new Button("1");
    Button button2 = new Button("2");
    Button button3 = new Button("3");
    Button button4 = new Button("4");

    HBox.setMargin(button1, new Insets(10)); // 外边距
    box.getChildren().addAll(button1, button2, button3, button4);
    ac.getChildren().add(box);

    Scene screen = new Scene(ac);
    primaryStage.setScene(screen);
    primaryStage.setWidth(500);
    primaryStage.setHeight(300);
    primaryStage.show();
}
```

### VBox布局

> 垂直布局

```java
public void start(Stage primaryStage) {
    AnchorPane ac = new AnchorPane();
    HBox box = new HBox();
    box.setPrefWidth(500);
    box.setPrefHeight(500);

    Button button1 = new Button("1");
    Button button2 = new Button("2");
    Button button3 = new Button("3");
    Button button4 = new Button("4");

    box.getChildren().addAll(button1, button2, button3, button4);
    ac.getChildren().add(box)

    Scene screen = new Scene(ac);
    primaryStage.setScene(screen);
    primaryStage.setWidth(500);
    primaryStage.setHeight(300);
    primaryStage.show();
}
```

### BorderPanel布局

> 方位布局

```java
public void start(Stage primaryStage) {
    BorderPane ac = new BorderPane();
    Scene scene = new Scene(ac);

    AnchorPane a1 = new AnchorPane();
    AnchorPane a2 = new AnchorPane();
    AnchorPane a3 = new AnchorPane();
    AnchorPane a4 = new AnchorPane();
    AnchorPane a5 = new AnchorPane();
    Border border = new Border(new BorderStroke(
        Paint.valueOf("#CCCCCC"), // 边框色
        BorderStrokeStyle.SOLID, // 线类型
        new CornerRadii(15), // 圆角
        new BorderWidths(1) // 线宽度
    ));
    a1.setBorder(border);
    a2.setBorder(border);
    a3.setBorder(border);
    a4.setBorder(border);
    a5.setBorder(border);

    a1.setPrefHeight(100);
    a2.setPrefWidth(100);
    a3.setPrefHeight(100);
    a4.setPrefWidth(100);

    ac.setTop(a1);
    ac.setRight(a2);
    ac.setBottom(a3);
    ac.setLeft(a4);
    ac.setCenter(a5);

    primaryStage.setScene(screen);
    primaryStage.setWidth(500);
    primaryStage.setHeight(300);
    primaryStage.show();

}
```

### FlowPane布局

> 流式布局

```java
public void start(Stage primaryStage) {
    FlowPane ac = new FlowPane();
    Scene scene = new Scene(ac);

    Button button1 = new Button("button1");
    Button button2 = new Button("button2");
    Button button3 = new Button("button3");
    Button button4 = new Button("button4");
    Button button5 = new Button("button5");
	ac.setHgap(10); // 水平间距
    ac.setVgap(10); // 垂直间距
    ac.setOrientation(Orientation.VERTICAL); // 默认展示形式
    ac.getChildren().addAll(button1, button2, button3, button4, button5);

    primaryStage.setScene(screen);
    primaryStage.setWidth(500);
    primaryStage.setHeight(300);
    primaryStage.show();

}
```

### GridPane布局

> 网格布局

```java
public void start(Stage primaryStage) {
    GridPane ac = new GridPane();
    Scene scene = new Scene(ac);

    Button button1 = new Button("button1");
    Button button2 = new Button("button2");
    Button button3 = new Button("button3");
    Button button4 = new Button("button4");
    Button button5 = new Button("button5");

    ac.setHgap(10); // 水平间距
    ac.setVgap(10); // 垂直间距
    ac.add(button1, 0, 0);
    ac.add(button2, 1, 0);
    ac.add(button3, 2, 0);
    ac.add(button4, 0, 1);
    ac.add(button5, 1, 1);

    primaryStage.setScene(screen);
    primaryStage.setWidth(500);
    primaryStage.setHeight(300);
    primaryStage.show();

}
```

### StackPane布局

> 栈布局

### TextFlow布局

> 文本流布局

