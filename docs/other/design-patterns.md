# 设计模式

> 参考1：[设计模式目录](https://refactoringguru.cn/design-patterns/catalog)
>
> 参考2：《设计模式——可复用面向对象软件的基础》
>
> 参考3：《人人都懂设计模式：从生活中领悟设计模式（Python实现）》


::: tip
部分代码可参考：[Linnzh/Util](https://github.com/Linnzh/Utils)
:::

## 监听模式/观察者模式 - Observer

概念：在对象间定义一种**一对多**的依赖关系，当这个对象状态发生改变时，所有依赖它的对象都会被通知并自动更新。

**观察者模式**是对象的行为模式，又叫**发布/订阅（Publish/Subscribe）模式**、**模型/视图（Model/View）模式**、**源/监听（Source/Listener）模式**或**从属者（Dependents）模式**。其核心思想就是*在被观察者与观察者之间建立一种自动触发的关系*。

### 适用性

- 一个抽象模型有两个方面，其中一个方面依赖于另一方面。将这二者封装在独立的对象中，以使它们可以各自独立地改变或复用。
- 对一个对象的改变需要同时改变其他对象，而不知道具体有多少对象待改变。
- 一个对象必须通知其他对象，而它又不能假定其他对象是谁，换言之，你不希望这些对象是紧密耦合的。

### 参与者

- Subject（目标）
  - 目标知道它的观察者，可以有任意多个观察者观察同一目标
  - 提供注册和删除观察者对象的接口
- Observer（观察者）
  - 为那些在目标发生改变时需要获得通知的对象，定义一个更新接口
- ConcreteSubject（具体目标）
  - 将有关状态存入各 ConcreteObserver 对象
  - 当它的状态发生改变时，向其各个观察者发出通知
- ConcreteObserver（具体观察者）
  - 维护一个指向 ConcreteSubject 对象的引用
  - 存储有关状态，这些状态应该与目标的状态保持一致
  - 实现 Observer 的更新接口，以使自身状态与目标的状态保持一致

### 案例

- MVC 的设计结构：Model 类担任目标角色即被观察者，View 则是观察者的基类。
- APP消息推送：APP是观察者，服务器数据如APP升级信息则是被观察者，一旦服务器数据有更新，就被推送到各个手机客户端。
- 状态异常邮件通知：常用登录状态是被观察者，短信和邮件的发送机制可认为是登录状态的监听者，一旦发生状态变更，就自动发送信息。

### 对现有业务的思考

观察者模式并不局限于一个更新方法，应对不同业务流程可抽象化多个接口，例如：

- 订单系统：订单作为被观察者
  - 在创建订单时，向短信系统（观察者）发送通知：创建了订单；取消订单等类同
  - 订单支付完成后，向生产系统（观察者）发送通知：收到订单，需要生产的产品信息；并向短信系统（观察者）发送通知：您的订单即将进入生产
- 生产系统：作为被观察者
  - 生产完成后，向物流系统（观察者）发送通知

### 问题

Q：对于不同模块（不在同一套代码中），如何运用观察者模式？

A：第一，可以使用 HTTP 进行通信（RESTful 接口）或使用 JSON-RPC 进行通信，有现成的框架和工具；第二使用配置中心，例如 Aliyun ACM、Zookeeper、Nacos 等进行服务注册、发现等。



## 状态模式 - State

概念：允许一个对象在其内部状态发生改变时改变其行为，使这个对象看上去就是改变了它的类型一样（其表现的行为和外在属性不一样）。

因此，状态模式属于对象的**行为**模式。又叫**状态对象（Object for State）**。其核心思想就是*一个事物（对象）有多种状态，在不同状态下所表现出来的行为和属性不一样。*

注意：表示状态的类应该只会有一个实例，所以状态类的实现要使用**单例**。

### 适用性

- 一个对象的行为取决于它的状态，并且它必须在运行时根据状态改变它的行为。
- 一个操作中含有庞大的多分支条件语句，且这些分支依赖于该对象的状态。这个状态通常用一个或多个枚举常量表示。通常，有多个操作包含这一相同的条件结构。State 模式将每个条件放入一个独立的类中，这使得你可以根据对象自身的情况将对象的状态作为一个对象，这一对象可以不依赖于其他对象而独立变化。

### 特点

- 将与特定状态相关的行为局部化，并且将不同状态的行为分割开。
- 使得状态转换显式化。
- State  对象可被共享。

### 参与者

- Context（环境，如 TCPConnection）
  - 定义客户感兴趣的接口
  - 维护一个 ConcreteState 子类的实例，这个实例定义当前状态
- State（状态，如 TCPState）
  - 定义一个接口以封装与 Context 的一个特定状态相关的行为
- ConcreteState Subclasses（具体状态子类，如 TCPEstablished、TCPListen、TCPClosed）
  - 每一个子类实现一个与 Context 的一个状态相关的行为

### 案例

- TCPConnection 对象：处于若干不同状态之一：连接已建立（Established）、正在监听（Listening）、连接已关闭（Closed）。当一个 TCPConnection 对象收到其他对象的请求时，它根据自身的当前状态做出不同的反应，例如，一个 Open 请求的结果依赖于该连接是处于连接已关闭状态还是连接已建立状态。状态改变时，对应实例被替换为新状态的实例。

### 对现有业务的思考

状态模式理解起来不难，但是带入到现实场景或者真正使用好像很难，对象间的关系以及处理方式过于复杂了。

> 通过在状态类中引用环境类的对象来回调环境类的 `setState()`方法实现状态的切换。在这种可以切换状态的状态模式中，增加新的状态类可能需要修改其他某些状态类甚至环境类的源代码，否则系统无法切换到新增状态。
>
> ——[状态模式](https://design-patterns.readthedocs.io/zh_CN/latest/behavioral_patterns/state.html#state)

可通过以下测试代码简单理解：

```php
public function testExample(): void
{
    $context = new TCPConnectionContext();

    $context->established();// 状态变更
    $stateName = $context->behavior();// 在 behavior() 方法中，调用当前状态的行为方法
    $this->assertEquals('established', $stateName);

    $context->listen();
    $stateName = $context->behavior();
    $this->assertEquals('listen', $stateName);

    $context->closed();
    $stateName = $context->behavior();
    $this->assertEquals('closed', $stateName);
}
```

## 中介模式 - Mediator

概念：用一个**中介对象**来封装一系列的对象交互。中介者使各对象不需要显式地相互引用，从而使其耦合松散，而且可以独立地改变它们之间的交互。

**中介模式**是对象行为型模式，又被称为**调停模式**。其作用是将网状结构类分离为星型结构，减少相互连接的数量，使得对象之间的结构更加简洁，交互更加顺畅。





### 适用性

- 一组对象以定义良好但复杂的方式进行通信，产生的相互依赖关系结构混乱且难以理解。
- 一个对象引用其他很多对象，并且直接与这些对象通信，导致难以复用该对象。
- 想定制一个分布在多个类中的行为，而又不想生成太多子类。

### 特点

- 通过中介者对象重定向调用行为，以间接的方式进行合作。 最终，组件仅依赖于一个中介者类，无需与多个其他组件相耦合。
- 只要组件使用**通用接口**与其中介者合作，就能将该组件与不同实现的中介者进行连接。
- **中介者**模式通过提供中心化的事件分发器来对观察者模式进行拓展。它允许任何对象无需依赖另一个对象所属的类就能跟踪和触发其事件。

### 参与者

- Mediator（中介者，如 DialogDirector）
  - 中介者定义一个接口，用于与各组件（Colleague）对象通信
- ConcreteMediator（具体中介者，如 FontDialogDirector）
  - 具体中介者通过协调各组件对象实现协作行为
  - 了解并维护它的各个组件
- Colleague Class（组件，即进行交互对象，如 ListBox、EntryField）
  - 每一个组件类都知道它的中介者对象
  - 每一个组件对象在需要与其他组件通信的时候，与它的中介者通信
  - 组件类可以是互不相干的多个类，也可以是具有继承关系的相似类

### 案例

- 设备管理器：QQ、钉钉、微信等通讯工具，作为 Colleague，需要与各种设备例如麦克风、扬声器、摄像头等进行交互，这些设备同作为交互对象 Colleague，其中**设备管理器**可以作为 ConcreteMediator 来保持两边的沟通交互。
- 对话框与各种表单元素之间的交互
- 事件分发器：事件分发器作为中介者，来跟踪和触发**组件/Colleague**（例如日志、用户数据仓）的事件。参考：[真实世界案例](https://refactoringguru.cn/design-patterns/mediator/php/example#example-1)，该案例配合了[监听模式](#监听模式/观察者模式 - Observer)

### 对现有业务的思考

同样理解起来不难，但目前想不到业务中适用的场景。

可通过以下测试代码简单理解：

```php
    public function testExample(): void
    {
        $mediator = new DeviceMediator();

        // 激活麦克风管理器中的第一个设备
        // 该过程全部交由中介者 mediator 做
        $colleague = $mediator->getDeviceListByType(DeviceMediator::SPEAKER);
        $mediator->activeByType(DeviceMediator::SPEAKER, $colleague[0]['id']);
        $this->assertEquals(1, $mediator->getCurrentDeviceIdByType(DeviceMediator::SPEAKER));

        // 激活麦克风管理器中的第二个设备
        // 该过程全部交由中介者 mediator 做
        $colleague = $mediator->getDeviceListByType(DeviceMediator::CAMERA);
        $mediator->activeByType(DeviceMediator::CAMERA, $colleague[1]['id']);
        $this->assertEquals(2, $mediator->getCurrentDeviceIdByType(DeviceMediator::CAMERA));
    }

```

#### 复杂理解

在事件分发器案例中，EventDispatcher 充当一个具体中介者，并定义了一个通用接口`ObserverInterface`（其定义了组件的行为`update()`）。然后在不同的组件如 User、Logger、UserRepository、OnBoardingNotification 实现这个通用接口，在客户端层面，所有行为通过事件分发器进行：

```php
    public function testExample(): void
    {
        $eventDispatcher = EventDispatcher::getInstance();
        echo PHP_EOL;

        $repository = new UserRepository();
        $eventDispatcher->attach($repository, 'facebook:update');

        $logger = new Logger(__DIR__ . '/log.txt');
        $eventDispatcher->attach($logger, '*');

        $onBoarding = new OnBoardingNotification('test@example.com');
        $eventDispatcher->attach($onBoarding, 'users:created');

        $repository->initialize(__DIR__ . 'users.csv');

        $user = $repository->createUser([
            'name' => 'Linnzh',
            'email' => 'linnzh@example.com',
        ]);

        $user->delete();

        $this->assertEquals(true, true);
    }

```



### 问题

- Colleague - Mediator 的通信方式？
  - 一种实现方法是使用 **Observer模式**，将 Mediator 实现为一个 Observer，各 Colleague 作为 Subject，一旦其状态改变就发送通知给 Mediator，Mediator 做出的响应是将状态改变的结果传播给其他的 Colleague。另一种实现方法是在 Mediator 中定义一个特殊的通知接口，各 Colleague 在通信时直接调用该接口。
- 中介模式的缺点？
  - 承接了所有的交互逻辑，交互的复杂度直接转变为中介者的复杂度，后续将变得难以维护
  - 中介者出问题会导致多个使用者同时出问题



## 装饰模式 - Decorator

概念：动态地给一个对象增加一些额外的职责。就**增加功能**来说，Decorator 模式相比于生成子类更为灵活。

装饰模式又称为**封装器（Wrapper）**，是对象结构型模式。其核心思想就是*动态地给类增加功能，而不改动原有的代码，来进行扩展。*将装饰器和被装饰的对象很好地进行解耦。



### 适用性

- 在不影响其他对象的情况下，以动态、透明的方式给单个对象添加职责。
- 处理那些可以撤销的职责。
- 当不能采用生成子类的方法进行扩充时。
  - 一种情况是，可能有大量独立的扩展，为支持每一种组合将产生大量的子类，使得子类数目呈爆炸性增长
  - 另一种情况是，类定义被隐藏，或者类定义不能用于生成子类（final 类）

### 特点

- 与对象的静态继承相比，Decorator 模式提供了更加灵活地向对象添加职责的方式。可以运用添加和分离的方式，用装饰在运行时增加和删除职责，同时也可以很容易地**重复添加一个特性**。
- 提供了一种“即用即付”的方式来添加职责，并不试图在一个复杂的可定制的类中支持所有可预见的特征，而是利用 Decorator 类逐步添加功能。
- 使用相同方法来完成其他行为（例如设置消息格式或者创建接收人列表）。只要所有装饰都遵循相同的接口，客户端就可以使用任意自定义的装饰来装饰对象。

### 参与者

- Component（部件）
  - 定义一个对象接口，可以给这些对象动态地添加职责
- ConcreteComponent（具体部件）
  - 定义一个对象，可以给这个对象添加一些职责
- Decorator（装饰器）
  - 维持一个**指向 Component 对象的指针**，并定义一个与 Component 接口一致的接口
- ConcreteDecorator（具体装饰器）
  - 定义了可动态添加到部件的额外行为。 具体装饰类会重写装饰基类的方法， 并在调用父类方法之前或之后进行额外的行为

### 案例

- 通知器：在消息通知示例中， 我们可以将简单邮件通知行为放在基类 `通知器`中， 但将所有其他通知方法放入装饰中。



- 敏感数据压缩和解密：**装饰**模式能够对敏感数据进行压缩和加密， 从而将数据从使用数据的代码中独立出来。



### 对现有业务的思考

装饰模式看起来很像 JS 中的原型链，可以不断地在一个对象上调用不同或相同的方法，例如过滤器。或者很适合在产品经理提出新功能时使用，例如想在订单流程中加一个评审环节，评审功能就可以使用装饰模式进行加载。

参考测试代码以理解：

```php
public function testExample(): void
{
    echo "\n正在创建订单……\n订单创建完成，即将发送通知……\n";

    $decorator = new SmsNotifierDecorator();
    $decorator->send('默认装饰器');

    echo "\n正在创建订单……\n订单创建完成，即将发送通知……\n";
    $decorator = new SmsNotifierDecorator(new WechatNotifierDecorator());
    $decorator->send('两个装饰器');


    $this->assertEquals(true, true);
}
```

其重点在于，抽象装饰器类，拥有一个指向 装饰器 对象的指针：

```php
abstract class AbstractNotifierDecorator implements NotifierInterface
{
    private ?NotifierInterface $wrapper;// 指针

    public function __construct(?NotifierInterface $notifier = null)
    {
        $this->wrapper = $notifier;
    }

    public function send(string $message): void
    {
        $this->wrapper && $this->wrapper->send($message);
    }
}
```

## 单例模式 - Singleton

概念：确保一个类只有一个实例，并且提供一个访问它的全局方法。

单例模式也称**单件模式**，属于对象创建型模式。其核心思想就是*让类自身负责保存它的唯一实例，并提供一个可访问该实例的方法。*

### 适用性

- 当类只能有一个实例而且客户可以从一个众所周知的访问点访问它时。
- 当这个唯一实例应该是通过子类化可扩展的，并且客户应该无需更改代码就能使用一个扩展的实例时。

### 特点

所有单例的实现都包含以下两个相同的步骤：

- 将默认构造函数设为私有， 防止其他对象使用单例类的 `new`运算符。
- 新建一个静态构建方法作为构造函数。该函数会 “偷偷” 调用私有构造函数来创建对象，并将其保存在一个静态成员变量中。 此后所有对于该函数的调用都将返回这一缓存对象。

### 参与者

- Singleton（类本身）
  - 定义一个 getInstance() 操作，允许客户访问它的唯一实例
  - 可能负责创建它自己的唯一实例

### 案例

- 数据库连接：避免多次创建数据库连接、断开连接等消耗。
- [状态模式](#状态模式 - State)中的具体状态类应设为单例

### 对现有业务的思考

参考代码：

```php
class Singleton
{
    private static ?self $instance = null;

    private function __construct()
    {
        // 构造函数私有化，使之无法被创建
    }

    public function __clone()
    {
        throw new \RuntimeException('禁止克隆一个单例对象！');
    }

    public function __wakeup()
    {
        throw new \RuntimeException('禁止序列化一个单例对象！');
    }

    public static function getInstance(): self
    {
        if (!self::$instance instanceof self) {
            self::$instance = new self();
        }
        return self::$instance;
    }
}
```

## 工厂模式 - Factory

概念：专门定义一个类来负责**创建其他类的实例**，根据参数的不同创建不同的实例，被创建的实例通常具有共同的父类。

工厂模式也被称为**简单工厂模式（Simple Factory Pattern）**、**虚拟构造函数（Virtual Contructor）**。Factory Method 使一个类的实例化延迟到其子类。



### 适用性

- 当一个类不知道它所必须创建的对象的类的时候。
- 当你在编写代码的过程中， 如果无法预知对象确切类别及其依赖关系时。
- 如果你希望用户能扩展你软件库或框架的内部组件， 可使用工厂方法。
- 当一个类希望由它的子类来指定它所创建的对象的时候。
- 当类将创建对象的职责委托给多个帮助子类中的某一个，并且希望将哪一个帮助子类使代理者这一信息局部化的时候。

### 特点

- 调用工厂方法的代码（通常被称为*客户端*代码）无需了解不同子类返回实际对象之间的差别。客户端将所有产品视为抽象的 `运输` 。客户端知道所有运输对象都提供 `交付`方法，但是并不关心其具体实现方式。

### 参与者

- Product
  - 定义工厂方法所创建的对象的接口
- ConcreteProduct
  - 实现 Product 接口的具体类
- Creator
  - 声明工厂方法，该方法返回一个 Product 类型的对象。Creator 也可以定义一个工厂方法的默认实现，并返回一个默认的 ConcreteProduct 对象
  - 可以调用工厂方法以创建一个 Product 对象
- ConcreteCreator
  - 重定义工厂方法以返回一个 ConcreteProduct 对象

### 案例

- 对话框组件：如果我们声明了一个在基本对话框类中生成按钮的工厂方法，那么我们就可以创建一个对话框子类，并使其通过工厂方法返回 Windows 样式按钮。子类将继承对话框基础类的大部分代码，同时在屏幕上根据 Windows 样式渲染按钮。
- 假设你使用开源 UI 框架编写自己的应用。你希望在应用中使用圆形按钮，但是原框架仅支持矩形按钮。你可以使用 `圆形按钮`Round­Button子类来继承标准的 `按钮`Button类。但是，你需要告诉 `UI框架`UIFramework类使用新的子类按钮代替默认按钮。 为了实现这个功能，你可以根据基础框架类开发子类 `圆形按钮 UI`UIWith­Round­Buttons ，并且重写其 `create­Button`创建按钮方法。基类中的该方法返回 `按钮`对象，而你开发的子类返回 `圆形按钮`对象。现在，你就可以使用 `圆形按钮 UI`类代替 `UI框架`类。
- 为创建社交网络连接器提供接口，可用于进行登录网络、发帖和潜在的其他行为，而实现所有这些功能都无需客户端代码与特定社交网络的特定类相耦合。

### 对现有业务的思考

该模式适合归纳一些具备类似特点的类，并提供一个快速创建不同对象的方法，而无需去了解具体对象的实现方式。比如说：

- 生产BOM中，子件、预组件、组件三者具备高度相似性
- 财务帐单中，收款单、付款单
- 物料管理中，原材料、产品、物料等

简单来说属于一些对象中，仅有类似 type 属性不同或特定行为的实现不同时，可使用工厂模式来进行创建。

从测试代码简单理解：

```php
public function testExample(): void
{
    $windowsDialog = DialogFactory::createWindowsDialog();
    $windowsDialog->render();
    $button = $windowsDialog->createButton();
    $button->render();
    $button->onClick();

    $webDialog = DialogFactory::createWebDialog();
    $webDialog->render();
    $button = $webDialog->createButton();
    $button->render();
    $button->onClick();

    $this->assertEquals(true, true);
}
```

这里的`$windowsDialog->createButton()`也可以使用工厂模式来创建不同的按钮。

一个关于社交网络连接器的案例：

```php
public function testExample(): void
{
    $socialNetworkConnector = SocialNetworkConnectorFactory::createFacebookConnector('facebook', 'facebook_pw');
    $socialNetworkConnector->logIn();
    $socialNetworkConnector->createPost('今天学习了工厂模式');
    $socialNetworkConnector->logOut();

    $socialNetworkConnector = SocialNetworkConnectorFactory::createLinkedInConnector('linkedin', 'linkedin_pw');
    $socialNetworkConnector->logIn();
    $socialNetworkConnector->createPost('今天学习了工厂模式噢');
    $socialNetworkConnector->logOut();

    $this->assertEquals(true, true);
}
```

## 抽象工厂模式 - Abstract Factory

概念：提供一个接口以创建一系列相关或相互依赖的对象，而无需指定他们具体的类。



抽象工厂模式建议为系列中的每件产品明确声明接口（例如椅子、 沙发或咖啡桌）。然后，确保所有产品变体都继承这些接口。 例如，所有风格的椅子都实现 `椅子`接口；所有风格的咖啡桌都实现 `咖啡桌`接口，以此类推。

### 适用性

- 一个系统要独立于它的产品的创建、组合和表示。
- 一个系统要由多个产品系列中的一个来配置。
- 要强调一系列相关的产品对象的设计以便进行联合使用。
- 提供一个产品类库，但只想显示它们的接口而不是实现。
- 如果代码需要与多个不同系列的相关产品交互， 但是由于无法提前获取相关信息， 或者出于对未来扩展性的考虑， 你不希望代码基于产品的具体类进行构建， 在这种情况下， 你可以使用抽象工厂。
- 如果你有一个基于一组[抽象方法](https://refactoringguru.cn/design-patterns/factory-method)的类， 且其主要功能因此变得不明确， 那么在这种情况下可以考虑使用抽象工厂模式。

即：抽象化多个工厂类。

### 特点

- 分离了具体的类，使之不出现在客户端代码
- 有利于产品的一致性
- 工厂应该是一个单例

### 参与者

- AbstractFactory（抽象工厂）
  - 声明一个创建抽象产品对象的操作接口
- ConcreteFactory（具体工厂）
  - 实现创建具体产品对象的操作
- AbstractProduct（抽象产品）
  - 为一类产品对象声明一个接口
- ConcreteProduct（具体产品）
  - 定义一个将被相应的具体工厂创建的产品对象
  - 实现 AbstractProduct 接口
- Client/Application（客户端）
  - 仅使用由 AbstractFactory 和 AbstractProduct 类声明的接口

AbstractFactory 将产品对象的创建延迟到它的 ConcreteFactory 子类。

### 案例

- 跨平台 GUI 组件系列及其创建方式：按钮和复选框将被作为产品。它们有两个变体：macOS 版和 Windows 版。抽象工厂定义了用于创建按钮和复选框的接口。而两个具体工厂都会返回同一变体的两个产品。

### 对现有业务的思考

依旧是从测试代码简单理解：

```php
public function testExample(): void
{
    $env = ['Windows', 'WEB', 'MacOS'];
    $config = $env[random_int(0, 2)];

    if ($config === 'Windows') {
        $factory = new WindowsFactory();
    } elseif ($config === 'WEB') {
        $factory = new WebFactory();
    } else {
        throw new \LogicException('不支持的操作系统！');
    }

    // $button = $factory->createButton();
    // $button->render();
    // $button->onClick();

    // $checkbox = $factory->createCheckbox();
    // $checkbox->print();

    // 无需关注具体的类
    $application = new Application($factory);
    $application->render();

    $this->assertEquals(true, true);
}
```

## 生成器模式 - Builder

概念：一种创建型设计模式，分步骤创建复杂对象，该模式允许你使用相同的创建代码生成不同类型和形式的对象。





假设有这样一个复杂对象，在对其进行构造时需要对诸多成员变量和嵌套对象进行繁复的初始化工作。这些初始化代码通常深藏于一个包含众多参数且让人基本看不懂的构造函数中；甚至还有更糟糕的情况，那就是这些代码散落在客户端代码的多个位置。

生成器模式建议将对象构造代码从产品类中抽取出来，并将其放在一个名为*生成器*的独立对象中。又称为“建造者模式（Builder）”。



你可以进一步将用于创建产品的一系列生成器步骤调用抽取成为单独的*主管*类。主管类可定义创建步骤的执行顺序，而生成器则提供这些步骤的实现。

### 适用性

- 想避免 “重叠构造函数 （telescopic constructor）” 的出现时
- 当你希望使用代码创建不同形式的产品 （例如石头或木头房屋） 时
- 想构造[组合](https://refactoringguru.cn/design-patterns/composite)树或其他复杂对象时

### 特点

生成器模式可以通过类来识别， 它拥有一个构建方法和多个配置结果对象的方法。 生成器方法通常支持方法链 （例如 `someBuilder->setValueA(1)->setValueB(2)->create()` ）。

### 参与者

- **生成器** （Builder）
  - 接口声明在所有类型生成器中通用的产品构造步骤。
- **具体生成器** （Concrete Builders）
  - 提供构造过程的不同实现。具体生成器也可以构造不遵循通用接口的产品。
- **产品** （Products）
  - 是最终生成的对象。由不同生成器构造的产品无需属于同一类层次结构或接口。
- **主管** （Director）
  - 类定义调用构造步骤的顺序，这样你就可以创建和复用特定的产品配置。
- **客户端** （Client）
  - 必须将某个生成器对象与主管类关联。一般情况下，你只需通过主管类构造函数的参数进行一次性关联即可。此后主管类就能使用生成器对象完成后续所有的构造任务。但在客户端将生成器对象传递给主管类制造方法时还有另一种方式。在这种情况下，你在使用主管类生产产品时每次都可以使用不同的生成器。

### 案例

- SQL 查询生成器：生成器接口定义了生成一般 SQL 查询所需的通用步骤。另一方面，对应不同 SQL 语言的具体生成器会去实现这些步骤，返回能在特定数据库引擎中执行的 SQL 查询语句。
- 分步构建一部汽车：汽车构建器、汽车手册生成器、汽车类型、汽车特征1……汽车特征n、主管控制生成器控制生成不同种的汽车。

### 对现有业务的思考

很适合用来生成不同种类的产品，以及一些初始化工作，例如：创建一家公司，需要部件：生成创始人、生成工厂地址/发货地址、生成默认配送方式、生成空仓库、生成初始账户、设定默认结算规则、设定默认结算货币、设定默认文件存储方式等。

简单测试代码以供理解：

```php
public function testExample(): void
{
    $director = new Director();
    echo "\n";

    $carBuilder = new CarBuilder();
    $director->buildSportsCar($carBuilder);
    echo $carBuilder . "\n\n";

    $manualBuilder = new ManualBuilder();
    $director->buildSUVCar($manualBuilder);
    echo $manualBuilder . "\n\n";

    $this->assertEquals(true, true);
}
```

> SQL 查询生成器示例：<https://refactoringguru.cn/design-patterns/builder/php/example#example-1>

## 原型模式 - Prototype

概念：用原型实例指定要创建对象的种类，并通过拷贝这些原型的属性来创建新的对象。又称为**克隆模式（clone）**。

其核心思想就是**一个`clone()`方法，用来拷贝父类的所有属性。**其中包括两个过程：

1. 分配一块新的内存空间给新的对象；
2. 拷贝父类对象的所有属性。

这个父类也就是**原型**。

> 浅拷贝：仅拷贝**引用类型对象**的指针（指向），而不拷贝引用类型对象指向的值；
>
> 深拷贝：同时拷贝引用类型对象及其指向的值。

在使用原型模式时，除非一些特殊情况（比如要求两个对象一起改变），尽量使用**深拷贝**的方式，这种也被称为“安全模式”。

### 适用性

- 当一个系统应该独立于它的产品创建、构成和表示时。
- 创建新对象的成本较高或初始化需要消耗过多资源时。
- 当要实例化的类是在运行时指定时，例如，通过动态装载。
- 为了避免创建一个与产品类层次平行的工厂类层次时。
- 当一个类的实例只能有几个不同状态组合的一种时，建立相应数目的原型并克隆它们可能比每次用合适的状态手工实例化该类更方便一些。
- 如果子类的区别仅在于其对象的初始化方式， 那么你可以使用该模式来减少子类的数量。 别人创建这些子类的目的可能是为了创建特定类型的对象。

### 特点

- 通过 clone 的方式创建对象，不会执行类的初始化函数。
- 你还可以创建一个中心化原型注册表，用于存储常用原型。
- 克隆包含循环引用的复杂对象可能会非常麻烦。

### 参与者

- Prototype（原型）
  - 声明一个克隆自身的接口。
- ConcretePrototype（具体原型）
  - 实现一个克隆自身的操作。
- Client（客户端）
  - 让一个原型克隆自身从而创建一个对象。

### 案例

- 应用程序的配置管理：应用程序在修改配置时，通常希望能备份以下原有的配置，以便能在原来的基础上进行修改，出问题时也可以切换回原来的配置。
- 文件复制：在 Windows 系统中，复制一份文件通常会在文件名后加一个 Copy + 数字。

### 对现有业务的思考

原型模式或者说克隆模式，让我想到项目里经常提到的一个需求：复制。

照例使用测试代码来简单理解：

```php
public function testExample(): void
{
    $author = new Author('Linn');
    $article = new Article('文章标题', "内容：{$author->getName()} 发表了她的第一篇文章~", $author);
    $article->addComment("恭喜");
    $article->addComment("恭喜+1");
    echo $article . "\n";

    $copyArticle = clone $article;
    $copyArticle->addComment("又有产出啦");
    echo $copyArticle . "\n";

    $copyArticle = clone $copyArticle;
    echo $copyArticle . "\n";

    $this->assertEquals(true, true);
}
```

## 适配器模式 - Adapter

概念：将一个类的接口变成客户端所期望的另一种接口，从而使原本因接口不匹配而无法一起工作的两个类能够在一起工作。

适配器模式又称为**包装器（Wrapper）**，其核心思想就是*将一个对象经过包装或转换后使它符合指定的接口，使得调用方可以像使用接口的一般对象一样使用它*，起到一个中间转换的作用。

注意：过多地使用适配器，容易使代码结构混乱，如明明看到调用的是A 接口，内部调用却是B接口的实现。

### 适用性

- 想使用一个已存在的类，但其接口不符合需求。
- 想创建一个可复用的类，该类可以和其他不相关的类或不可预见的类（即接口可能不兼容）协同工作。
- （仅适用于对象 Adapter）想使用一些已经存在的子类，但不可能对每一个都进行子类化以匹配它们的接口，对象适配器可以适配它的父类接口。
  - 将缺失功能添加到一个适配器类中是一种优雅得多的解决方案。然后你可以将缺少功能的对象封装在适配器中，从而动态地获取所需功能。

### 参与者

- Target（目标）
  - 定义 Client 使用的与特定领域相关的接口。
- Client（客户端）
  - 与符合 Target 接口的对象协同。
- Adaptee（被适配对象）
  - 定义一个已经存在的接口，该接口需要适配。
- Adapter（适配器）
  - 对 Adaptee 的接口与 Target 的接口进行适配。

### 案例

- 榫卯结构、电源适配器
- 数据格式兼容问题
- 方钉圆孔问题
- 第三方数据或遗留系统接口不兼容问题

### 对现有业务的思考

适配器模式可以解决很多数据接口不兼容的问题，尤其是新老接口对接时。也可以通过写适配器接口，来兼容多个不同类型的第三方接口，例如快递接口，对接快递鸟或者快递100、或者其他具体的快递公司。

简单示例代码：

```php
public function testExample(): void
{
    $emailNotification = new EmailNotification('reg.lynnzh@gmail.com');
    $emailNotification->send('一条通知', '你被清华大学录取啦！');

    $weiboNotification = new WeiboNotificationAdapter(new WeiboApi('Linn', md5('linn')), '张三');
    $weiboNotification->send('一条通知', '你被清华大学录取啦！');

    $this->assertEquals(true, true);
}
```

## 桥接模式 - Bridge

概念：**桥接模式**是一种结构型设计模式，可将一个大类或一系列紧密相关的类拆分为抽象和实现两个独立的层次结构，从而能在开发时分别使用。

别名**Handle**/**Body**。桥接模式可以通过一些控制实体及其所依赖的多个不同平台之间的明确区别来进行识别。









### 适用性

- 想要拆分或重组一个具有多重功能的庞杂类时，例如能与多个数据库服务器进行交互的类
  - 桥接模式可以将庞杂类拆分为几个类层次结构。此后，你可以修改任意一个类层次结构而不会影响到其他类层次结构。这种方法可以简化代码的维护工作，并将修改已有代码的风险降到最低。
- 希望在几个独立维度上扩展一个类时
  - 建议将每个维度抽取为独立的类层次。初始类将相关工作委派给属于对应类层次的对象，无需自己完成所有工作
- 不希望在抽象和它的实现部分之间有一个固定的绑定关系，需要在运行时切换不同实现方法时
  - 并不是说一定要实现这一点，桥接模式可替换抽象部分中的实现对象，具体操作就和给成员变量赋新值一样简单。
- 对不同的抽象接口和实现部分进行组合，并分别对它们进行扩充。

### 特点

- 分离接口及其实现部分。
- 提高可扩充性。
- 实现细节对客户透明。
- 桥接模式允许在不改动另一层次代码的前提下修改已有类，甚至创建新类。

### 参与者

- Abstraction（抽象部分）
  - 提供高层的逻辑控制，依赖于完成底层实际工作的实现对象
- Implementation（实现部分）
  - 为所有具体实现提供接口。抽象部分仅能通过在这里声明的方法与实现对象交互。
  - 抽象部分可以列出与实现部分一样的方法，但是抽象部分通常声明一些复杂行为，这些行为依赖于多种由实现部分声明的抽象操作。
- ConcreteImplementation（具体实现）
  - 包含特定于平台的代码。
- Refined Abstraction（精确抽象）
  - 提供控制逻辑的变体，与其父类一样，通过通用实现接口与不同的实现进行交互。

### 案例

- 在支持多种类型的数据库服务器或与多个特定种类 （例如云平台和社交网络等） 的 API 供应商协作时会特别有用。
- 远程控制器及其所控制的设备的类之间的分离：远程控制器是抽象部分，设备则是其实现部分。由于有通用的接口，同一远程控制器可与不同的设备合作，反过来也一样。

### 对现有业务的思考

暂时没有想到现有的可适配的业务场景。先做简单了解吧。

```php
public function testExample(): void
{
    $device = new Radio();
    echo $device;
    $remoteController = new BasicRemoteController($device);
    $remoteController->power();
    $remoteController->volumeUp();
    $remoteController->volumeUp();
    echo $device;
    $remoteController->volumeDown();
    echo $device;
    $remoteController->power();
    echo $device;

    $device = new TV();
    echo $device;
    $remoteController = new AdvancedRemoteController($device);
    $remoteController->power();
    $remoteController->mute();
    echo $device;
    $remoteController->volumeUp();
    echo $device;
    $remoteController->power();
    echo $device;

    $this->assertEquals(true, true);
}
```

## 组合模式 - Composite

概念：将对象组合成树形结构以表示“部分-整体”的层次结构，使用户对单个对象和组合对象的使用具有一致性。



### 适用性

- 想表示对象的“部分-整体”层次结构时。
- 希望用户忽略组合对象与单个对象的不同时，用户将统一地使用组合结构中的所有对象。
- 对象之间具有明显的“部分-整体”的关系，或具有层次关系时。
- 组合对象与单个对象具有相同或类似的行为（方法），用户希望统一地使用组合结构中的所有对象时。

### 特点

- 组合模式是一种具有层次关系的树形结构，不能再分的叶子节点是具体的组件，也就是最小的逻辑单元；具有子节点（由多个子组件构成）的组件被称为**复合组件**，也就是组合对象。

### 参与者

- Component（子件）
  - 为组合中的对象声明接口
  - 在适当情况下，实现所有类共有接口的缺省行为
  - 声明一个接口用于访问和管理 Component 的子组件
  - （可选）在**递归结构**中定义一个接口，用于访问一个父部件，在合适的情况下实现它。
- Leaf（叶节点）
  - 在组合中表示叶节点对象，叶节点没有子节点
  - 在组合中定义图元对象的行为
- Composite（Comtainer，容器）
  - 定义有子部件的那些部件的行为
  - 存储子部件
  - 在 Component 接口中实现与子部件有关的操作
- Client（客户端）
  - 通过 Component 接口操纵组合部件的对象



### 案例

- 财经应用领域里，一个资产组合聚合多个单个资产，为了支持复杂的资产聚合，资产组合可用一个 Composite 类实现，该类与单个资产的接口一致。
- 组织架构：各个部门或各个子公司的组织架构、学校各个学院与班级的关系、文件夹与文件的关系等。
- HTML 的 DOM 树。

### 对现有业务的思考

很明显该模式适合具有树状结构的业务场景，比如说组织机构。

```php
public function testExample(): void
{
    $tree = new CompanyComposite();

    $branchA = new CompanyComposite();
    $branchA->add(new CompanyLeaf('企业A1'));
    $branchA->add(new CompanyLeaf('企业A2'));
    $tree->add($branchA);
    echo $tree->execute();

    echo "\n";

    $branchB = new CompanyComposite();
    $branchB->add(new CompanyLeaf('企业B1'));
    $branchB->add(new CompanyLeaf('企业B2'));
    $tree->add($branchB);
    echo $tree->execute();

    $this->assertEquals(true, true);
}
```

## 外观模式 - Facade

概念：为子系统中的一组接口提供一个一致的界面，定义一个高层接口使这一子系统更加容易使用。

外观模式也被称为**门面模式**。将复杂的业务通过一个对接人来提供一整套统一的服务，让用户不用关心内部复杂的运行机制。

Laravel 框架中即采用了大量 Facade 类。

### 适用性

- 当需要为复杂子系统提供一个简单接口时。
  - 子系统往往因为不断演化而变得越来越复杂，，大多数模式使用时都会产生更多更小的类，这使得子系统更具有可复用性，也更容易对子系统进行定制，但也会给那些不需要定制子系统的用户带来一些使用上的困难。Facade 模式可以提供一个简单的默认视图，对大多数用户已经够用，需要更多可定制的用户可越过 Facade 层。
- 客户程序与抽象类的实现部分之间存在很大的依赖性时。
  - 引入 Facade 将这个子系统与客户以及其他子类分离，可以提高子系统的独立性和可移植性。
- 当需要构建一个层次结构的子系统时，使用 Facade 模式**定义子系统中每层的入口点**。
  - 如果子系统之间是相互依赖的，可以让它们仅通过 Facade 进行通信，从而简化它们之间的依赖关系。

### 特点

- 外观类通常可以转换为单例，因为在大部分情况下一个外观对象就足够了。
- 外观模式与中介模式很类似，它们都尝试在大量紧密耦合的类中组织起合作。

### 参与者

- Facade（外观）
  - 知道哪些子系统类负责处理请求
  - 将客户的请求**代理**给适当的子系统对象
- Subsystem Classes（子系统类）
  - 实现子系统的功能
  - 处理由 Facade 对象指派的任务
  - 没有 Facade 的任何相关信息，即没有指向 Facade 的指针

### 案例

- 当你通过电话给商店下达订单时，接线员就是该商店的所有服务和部门的外观。
  - 接线员为你提供了一个同购物系统、支付网关和各种送货服务进行互动的简单语音接口。
- 格式转换器：例如各大编辑器提供的 Markdown 文件转 PDF、HTML等格式文件。
- 文件的压缩与解压缩系统。

### 对现有业务的思考

根据一些浅薄的理解，外观模式是提供了一层*自动识别并转发请求*的壳，例如一个发送通知的功能，Facade 类将自动将通知发送给指定通知模块。

```php
public function testExample(): void
{
    $company = new CompanySubsystem('Linn集团');
    $company->addUser(new User('LA', 'test.la@example.com'));
    $company->addUser(new User('LB', 'test.lb@example.com'));

    $facade = new BroadcastFacade($company);
    $facade->send('愚人节全场九折！', '促销活动', 'email');
    $facade->send('愚人节全场九折！', '促销活动', 'weibo');

    $this->assertEquals(true, true);
}
```

## 享元模式 - Flyweight

概念：运用**共享技术**有效地支持大量细粒度对象的复用。

又称为**轻量级模式**，要求能够共享的对象必须是轻量级对象。享元对象能做到共享的关键是*区分内部状态和外部状态*。

- 内部状态（Intrinsic State）：存储在享元对象内部并且不会随环境改变而改变的状态，因此内部状态是可以共享的状态。
- 外部状态（Extrinsic State）：随环境改变而改变、不可共享的状态。享元对象的外部状态必须由客户端保存，并且在享元对象被创建之后，在需要时传入享元对象内部。

享元模式也被称为**缓存（Cache）模式**。

### 适用性

该模式仅在以下条件均成立时适用：

1. 使用了大量的相似对象
2. 由于 1 的存在造成了很大的**存储开销**
3. 对象的大多数状态都可变为外部状态
4. 如果删除对象的外部状态，那么可以用相对较少的共享对象取代很多组对象
5. 应用程序不依赖于对象标识：由于 Flyweight 对象可以被共享，因此对于概念上明显有别的对象，标识测试将返回真值

### 特点

- 由于 PHP 语言的特性，享元模式很少在 PHP 中使用。PHP 脚本通常仅需要应用的一部分数据，从不会同时将所有数据载入内存。
- 享元模式是一个考虑系统性能的设计模式，使用享元模式可以节约内存空间，提高系统的性能。

### 参与者

- Flyweight（享元）
  - 描述一个接口，通过这个接口 flyweight 可以接受并作用于外部状态
  - 包含原始对象中部分能在多个对象中共享的状态
  - 同一享元对象可在许多不同情景中使用
- Context（具体享元、情景上下文）
  - 实现 Flyweight 接口，并为内部状态（如果有的话）增加存储空间
  - 该对象必须是可共享的，所存储的状态也必须是内部的，即必须独立于 Context 对象的场景
- FlyweightFactory（享元工厂）
  - 创建并管理 Flyweight 对象
  - 确保合理地共享 Flyweight：当用户请求一个 Flyweight 时，FlyweightFactory 对象将提供一个已创建的实例或者新建一个实例（如果不存在的话）

### 案例

- 场景渲染：游戏场景渲染、粒子系统等。
  - 摒弃了在每个对象中保存所有数据的方式， 通过共享多个对象所共有的相同状态， 能在有限的内存容量中载入更多对象。
- 浏览器的缓存：浏览器会将已打开的页面、文件等缓存到本地，如果一个页面中多次出现相同的图片（即一个页面中多个 img 标签指向同一个图片地址），则只需创建一个图片对象，在解析到 img 标签的地方多次重复显示这个对象即可。

### 对现有业务的思考

从[PHP 享元模式讲解和代码示例](https://refactoringguru.cn/design-patterns/flyweight/php/example#example-1)中大概可以理解为：操作大量具有相同数据时，对这些相同数据进行缓存，而不是新建对象。

例如我有一张 10w 数据量的物料表，其中有品牌、厂商等信息具有高度一致性，这部分信息可以作为共享数据，而不是建立相关类创建实例。



```php
public function testExample(): void
{
    $pool = new MaterialPool();

    $suppliers = ['华为', 'OPPO', '小米', 'Apple'];
    $manufactureYears = [2020, 2021, 2022];

    $i = 0;
    while ($i < 1000) {
        $name = substr(str_shuffle('#ABCDEFGHILKMNOPQRSTUVWXYZ#abcdefghilkmnopqrstuvwxyz1234567890'), 1000 % 61, 10);
        $pool->addMaterial('物料' . $name, '简单描述' . $i, $suppliers[$i % 4], $manufactureYears[$i % 3]);
        $i++;
    }

    $material = $pool->findOneMaterial(['description' => '简单描述2']);
    if ($material) {
        $material->output();
    }

    $materials = $pool->findManyMaterial(['supplier' => '华为']);
    foreach ($materials as $material) {
        $material->output();
    }

    $this->assertEquals(true, true);
}
```



## 代理模式 - Proxy

概念：为其他对象提供一种代理，以控制对这个对象的访问。

别名**Surrogate**。其核心思想是：

- 使用一个额外的间接层来支持分散的、可控的、智能的访问
- 增加一个包装和委托来保护真实的组件，以避免过度复杂

代理对象可以在客户端和目标对象之间起到中间调和的作用，并且通过代理对象隐藏不希望被客户端看到的内容和服务，或添加客户需要的额外服务。



代理模式建议新建一个与原服务对象接口相同的代理类，然后更新应用以将代理对象传递给所有原始对象客户端。代理类接收到客户端请求后会创建实际的服务对象，并将所有工作委派给它。

### 适用性

- 远程代理（Remote Proxy）：本地执行远程服务，为一个对象在不同地址空间提供局部代表，类似“大使”。
- 虚拟代理（Virtual Proxy）：延迟初始化，根据需要创建开销很大的对象。
- 保护代理（Protection Proxy）：控制对原始对象的访问，用于对象应该有不同访问权限时。
- 智能引用（Smart Reference）：在访问对象时执行一些附加操作，典型用途包括：
  - 对指向实际对象的引用计数，当该对象没有引用时，可以自动释放它
  - 当第一次引用一个持久对象时，将其装入内存
  - 在访问一个实际对象前，检查是否已经锁定它，以确保其他对象不能改变它
- 缓存请求结果：适用于需要缓存客户请求结果并对缓存生命周期进行管理时，特别是当返回结果的体积非常大时。

### 特点

- 尽管代理模式在绝大多数 PHP 程序中并不常见，但它在一些特殊情况下仍然非常方便。当你希望在无需修改客户代码的前提下于已有类的对象上增加额外行为时，该模式是无可替代的。

### 参与者

- Proxy（代理）
  - 保存一个引用使得代理可以访问实体。如果 RealSubject 和 Subject 的接口相同，Proxy 将会引用 Subject。
  - 提供一个与 Subject 的接口相同的接口，这样代理就可以用来替代实体。
  - 控制对实体的存取，并可能负责创建和删除它。
- Subject（实体接口）
  - 定义 RealSubject 和 Proxy 的共用接口，这样就在任何使用 RealSubject 的地方都可以使用 Proxy
- RealSubject（实体）
  - 定义 Proxy 所代表的实体。

### 案例

- 火车票和机票的代售点。
- 信用卡是银行账户的代理，银行账户则是一大捆现金的代理。它们都实现了同样的接口，均可用于进行支付。
- HTML 的占位符。
- 各类特殊用途的代理：远程代理、虚拟代理、防火墙代理、同步化（Synchronization）代理、智能引用（Smart Reference）代理等。

### 对现有业务的思考

很明显该模式适用于缓存。这里可以模拟一个存入缓存、缓存命中、缓存失效的过程。

```php
public function testExample(): void
{
    $url = 'http://www.baidu.com';
    $realSubject = new Downloader();
    $realResult = $realSubject->download($url);

    $proxySubject = new CachingDownloader();
    $proxyResult = $realSubject->download($url);

    $this->assertEquals($realResult, $proxyResult);
}
```



## 责任链模式 - Chain of Responsibility

概念：使多个对象都有机会处理请求，从而避免请求的发送者和接收者之间的耦合关系。将这些对象连成一条链，并沿着这条链传递该请求，直到有一个对象处理它为止。





### 适用性

- 有多个对象可以处理一个请求，哪个对象处理该请求运行时自动确定。
- 想在不明确指定接收者的情况下，向多个对象中的其中一个，提交一个请求。
- 可处理一个请求的对象集合应被动态指定。
- 当程序需要使用不同方式处理不同种类请求，而且请求类型和顺序预先未知时，可以使用责任链模式。
  - 该模式能将多个处理者连接成一条链。接收到请求后，它会 “询问” 每个处理者是否能够对其进行处理。这样所有处理者都有机会来处理请求。
- 当必须按顺序执行多个处理者时，可以使用该模式。
  - 无论你以何种顺序将处理者连接成一条链，所有请求都会严格按照顺序通过链上的处理者。
- 如果所需处理者及其顺序必须在运行时进行改变，可以使用责任链模式。
  - 如果在处理者类中有对引用成员变量的设定方法，你将能动态地插入和移除处理者，或者改变其顺序。

### 特点

- 该模式使得一个对象无需知道是其他哪一个对象处理其请求，只知道这个请求将会被正确地处理，简化对象之间的相互连接。
- 增强了给对象指派职责的灵活性，可通过在运行时对该链进行动态的增加或修改来增加或改变处理一个请求的那些职责。
- 无法保证被接受，一个请求也可能因该链没有被正确配置而得不到处理。

### 参与者

- Handler（请求处理者）
  - 定义一个处理请求的接口
  - （可选）实现**后继链**
- ConcreteHandler（具体请求处理者）
  - 处理它所负责的请求
  - 可访问它的后继者
  - 如果可处理该请求，则处理；反之则将该请求转发给它的后继者
- Client（提交请求的客户端）
  - 向链上的具体处理者对象提交请求

### 案例

- 订购系统中的各项检查：多项检查使得代码愈发臃肿不堪
  - 将这些处理者连成一条链。链上的每个处理者都有一个成员变量来保存对于下一处理者的引用。除了处理请求外，处理者还负责沿着链传递请求。请求会在链上移动，直至所有处理者都有机会对其进行处理。
  - 处理者会在进行请求处理工作后决定是否继续沿着链传递请求。如果请求中包含正确的数据，所有处理者都将执行自己的主要行为，无论该行为是身份验证还是数据缓存。

### 对现有业务的思考

这很像框架中的“**HTTP请求中间件**”，也是一层一层处理请求。类似地，可以将一些非业务向的**中间步骤**作为一个责任链，例如订单下单时的一系列动作处理：生成账单、转到调度中心等，或者是一些检查过滤类工作。

```php
public function testExample(): void
{
    $server = new Server();
    $server->register('admin@example.com', 'admin_pass');
    $server->register('user@example.com', 'user_pass');

    $middleware = new ThrottlingMiddleware(2);
    $middleware
        ->linkWith(new UserExistsMiddleware($server))
        ->linkWith(new RoleCheckMiddleware());

    $server->setMiddleware($middleware);


    $success = $server->logIn('123', '123_pass');
    $this->assertFalse($success);

    $success = $server->logIn('1234', '1234_pass');
    $this->assertFalse($success);

    $server->logIn('user@example.com', 'user_pass');

    $success = $server->logIn('admin@example.com', 'admin_pass');
    $this->assertTrue($success);
}

```



## 命令模式 - Command

概念：将一个请求封装为一个对象，从而使你可用不同的请求对客户进行参数化，延迟请求执行或将其放入队列中，对请求排队或者记录请求日志，以及支持可撤销的操作。

又名**动作模式（Action）**、**事务模式（Transaction）**。

### 适用性

- 抽象出待执行的动作以参数化某对象，可使用**回调函数**表达这种参数化机制。Command 模式是回调机制的一个面向对象的替代品。
- 在不同的时刻指定、排列和执行请求。一个 Command 对象可以有一个与初始请求无关的生存期。如果一个请求的接收者可用一种与地址空间无关的方式表达，那么就可将负责该请求的命令对象传送给另一个不同的进程，并在那里实现该请求。
- 支持取消操作。
  - Command 的 Execute 操作可在实施前将状态存储起来，在取消操作时，这个状态用来消除该操作的影响。
  - Command 接口必须添加一个 Unexecute 操作，该操作取消上一次 Execute 调用的效果。
  - 执行的命令被存储在一个历史列表中，可通过“向前”和“向后”遍历这一列表并分别调用 Unexecute 和 Execute 来实现重数不限的“撤销”和“重做”。
- 支持修改日志，这样当系统崩溃时，这些修改可以被重做一遍[^1]。
  - 在Command 接口中添加**装载**和**存储**操作，可以用来保持一个一致的修改日志。
  - 从崩溃中恢复的过程包括从磁盘中重新读入记录下的命令并用 Execute 操作重新执行它们。
- 用构建在抽象操作上的高层操作构造一个系统。这样一种结构在支持**事务**（Transaction）的信息系统中很常见。
  - Command 有一个公共接口，使得你可以用同一种方式调用所有的事务。
  - 同时使用该模式也易于添加新事务以扩展系统。

### 特点

- 将调用操作的对象与知道如何实现该操作的对象解耦。
- Command 是头等对象，他们可像其他对象一样被操纵和扩展。
- 可将多条命令装配成一个组合命令。一般来说，组合命令是 Composite 模式的一个实例。
- 增加新的 Command 很容易，因为这无需改变已有的类。



### 参与者

- Command（命令接口）
  - 声明执行操作的接口。
- ConcreteCommand（具体命令）
  - 将一个接收者对象绑定于一个动作。
  - 调用接收者相应的操作，以实现 Execute。
- Invoker（发送者/触发者）
  - 要求该命令执行这个请求。
- Receiver（接收者）
  - 知道如何实施与执行一个请求相关的操作，任何类都可能作为一个接收者。
- Client（客户端）
  - 创建一个具体命令对象，并指定它的接收者。

### 案例

- 用于对任务进行排序、 记录任务执行历史以及执行 “撤销” 操作。

### 对现有业务的思考

根据其概念和适用性，可用于各种流程的**回退/撤销**操作。这个实现需要记录每一个步骤的执行参数及顺序。

## 迭代器模式 - Iterator

概念：提供一种方法顺序访问一个聚合对象中的各个元素，而又不需要暴露该对象的内部表示。

又名**游标（Cursor）模式**。该模式的关键思想是**将对列表的访问和遍历从列表对象中分离出来，并放入一个迭代器（iterator）对象中**。迭代器类定义了一个访问该列表元素的接口。迭代器对象负责跟踪当前元素，即它已经知道哪些元素已经被遍历过了。



### 适用性

- 访问一个聚合对象的内容而无需暴露它的内部表示。
- 支持对聚合对象的多种遍历：深度优先遍历、广度优先遍历等。
- 为遍历不同的聚合结构提供一个统一的接口（即支持多态迭代）。

### 特点

- 支持以不同的方式遍历一个聚合。例如代码生成和语义检查要遍历语法分析树。
- 简化了聚合的接口。
- 在同一个聚合上可以有多个遍历，每个迭代器保持它自己的遍历状态，可以同时进行多个遍历。

### 参与者

- Iterator（迭代器接口）
  - 定义访问和遍历元素的接口。
- ConcreteIterator（具体迭代器）
  - 实现迭代器接口。
  - 对该聚合遍历时，跟踪当前位置。
- Aggregate（聚合/集合）
  - 定义创建相应迭代器对象的接口。
- ConcreteAggregate（具体聚合/集合）
  - 实现创建相应迭代器的接口，该操作返回 ConcreteIterator 的一个适当的实例。

### 案例

- 各类列表，包括堆、栈、队列、树、图等。

### 对现有业务的思考

目前没有太多用到遍历的地方。看看一些网上示例吧😭并且 PHP 已有 Iterator 接口。

```php
public function testExample(): void
{
    $csv = new CsvIterator(__DIR__ . '/cats.csv');

    foreach ($csv as $key => $row) {
        print_r($row);
    }

    $this->assertIsIterable($csv);
}
```

再复杂一点，可以给多种不同类型的集合对象发送通知，该场景需要遍历。例如当前登录用户的所有好友，包括微信好友、QQ好友等。（但通常这会使用数据库来记录完成）

## 备忘录模式 - Memento

概念：**在不破坏封装性的前提下**，捕获一个对象的内部状态，并在该对象之外保存这个状态，这样以后就可将该对象恢复到原先保存的状态。

别名：Token。有时有必要记录一个对象的内部状态。为了允许用户取消不确定的操作或从错误中恢复过来，需要实现**检查点**和**取消**机制，而要实现这些机制，必须事先将状态信息保存在某处，这样才能将对象恢复到它们先前的状态。

但是对象通常封装了其部分或所有状态信息，使得其状态不能被其他对象访问，也就不可能在该对象之外保存其状态。而暴露其内部状态又将违反封装的原则，可能有损应用的可靠性和可扩展性。

一个备忘录是一个对象，它存储另一个对象在某个瞬间的内部状态，而后者称为备忘录的**原发器（Originator）**。当需要设置原发器的检查点时，取消操作机制会向原发器请求一个备忘录。原发器用描述当前状态的信息初始化该备忘录（创建快照）。只有原发器可以向备忘录中存取信息，备忘录对其他对象“不可见”。



### 适用性

- 必须保存一个对象在某一时刻的（部分）状态，这样以后需要时它才能恢复到先前的状态。
- 如果一个接口让其他对象直接得到这些状态，将会暴露对象的实现细节并破坏对象的封装性。

### 特点

- 保持封装边界。可避免暴露一些只应由原发器管理却又必须存储在原发器之外的信息，把可能很复杂的 Originator 内部信息对其他对象屏蔽起来，从而保持了封装边界。
- 简化了原发器。
- 开销可能很大。如果原发器在生成备忘录时必须拷贝并存储大量的信息，或者客户非常频繁地创建备忘录和恢复原发器状态，可能会导致非常大的开销。除非封装和恢复 Originator 状态的开销并不大，否则该模式可能并不适合。

### 参与者

- Memento（备忘录）
  - 备忘录存储原发器对象的内部状态。
  - 原发器根据需要决定备忘录存储原发器的哪些内部状态。
  - 防止原发器以外的其他对象访问备忘录。备忘录实际上有两个接口，管理者（Caretaker）只能看到备忘录的**窄接口**——它只能将备忘录传递给其他对象。相反，原发器能够看到一个**宽接口**，允许它访问返回到先前状态所需的所有数据。理想情况是只允许生成本备忘录的那个原发器访问本备忘录的内部状态。
- Originator（原发器）
  - 原发器创建一个备忘录，用以记录当前时刻它的内部状态。
  - 使用备忘录恢复内部状态。
- Caretaker（管理者）
  - 负责保存好备忘录。
  - 不能对备忘录的内容进行操作或检查。

### 案例

- 各种含有历史记录、快照等概念的结构。
- 各类编辑器的**撤销**、**恢复**等功能。

### 对现有业务的思考

PHP语言里基本上可以使用**序列化**来生成对象的副本（以保存快照或状态），并且由于绝大部分 PHP 脚本是单线程运行，且会话时间非常有限， 通常会将对象的状态保存在比 RAM 更持久的存储设备中（例如关系型数据库、NoSQL等）。

## 策略模式 - Strategy

概念：定义一系列算法，把它们一个个封装起来，并且使它们可相互替换。本模式使得算法可独立于使用它的客户而变化。

别名**政策（Policy）模式**。

### 适用性

- 许多相关的类仅仅是行为有异。策略模式提供了一种用多个行为中的一个行为来配置一个类的方法。
- 需要使用一个算法的不同变体。
- 算法使用客户不应该知道的数据，避免暴露复杂的与算法相关的结构数据。
- 一个类定义了多种行为，并且这些行为在这个类的操作中以多个条件语句的形式出现。将相关的条件分支移入它们各自的Strategy 类中以代替这些条件语句。

### 特点

- 策略模式为上下文（Context）定义了一系列的可供复用的算法或行为，继承有助于析取出这些算法中的公共功能。
- 取消了一些条件语句。
- 为相同行为提供不同的实现选择。

### 参与者

- Strategy（策略）
  - 定义所有支持的算法的公共接口，Context 使用该接口来调用某 ConcreteStrategy 定义的算法。
- ConcreteStrategy（具体策略算法）
  - 以 Strategy 接口实现某具体算法。
- Context（上下文）
  - 用一个 ConcreteStrategy 对象来配置。
  - 维护一个对 Strategy 对象的引用。
  - 可定义一个接口来让 Strategy 访问它的数据。

### 案例

- ET++SwapsManager 计算引擎框架为不同的金融设备计算价格。
- Java 8 开始支持 lambda 方法，它可作为一种替代策略模式的简单方式。
  - 策略模式可以通过允许嵌套对象完成实际工作的方法以及允许将该对象替换为不同对象的设置器来识别。
- 各种不同选项导致的结果，例如支付方式、订单类型等。

### 对现有业务的思考

可以看出来有很多地方可使用策略模式。例如根据类型的不同所做出的一系列决策、某个参数的存在性导致的流程走向等。

```php
public function testExample(): void
{
    $order = new Order([
        'price' => 100.00,
        'products' => [
            [
                'name' => '商品1',
                'price' => 30,
                'quantity' => 3
            ],
            [
                'name' => '商品2',
                'price' => 70,
                'quantity' => 2
            ]
        ],
    ]);
    $order->setPaymentMethod(PaymentFactory::getPaymentMethod('alipay'));
    $order->pay();
    $this->assertTrue(true, true);
}
```



## 模板方法模式 - Template Method

概念：定义一个操作中的算法的骨架，而将一些步骤延迟到子类中。模板方法使得子类可以不改变一个算法的结构即可重新定义该算法的某些特定步骤。



工厂模式是模板方法模式的一种特殊形式，同时，工厂模式可以作为一个大型模板方法中的一个步骤。

### 适用性

- 一次性实现一个算法的不变部分，并将可变的行为留给子类实现。
- 各子类中公共的行为应被提取出来并集中到一个公共父类中，以避免代码重复。
  - 首先识别现有代码中的不同之处，并且将不同之处分离为新的操作。
  - 最后用一个调用新的操作的模板方法来替换不同的代码。
- 控制子类扩展。模板方法只在特定点调用钩子操作，这样就只允许在这些特定点进行扩展。

### 参与者

- AbstractClass（抽象类）
  - 定义抽象的**原语操作**（Primitive Operation），具体的子类将重定义它们以实现一个算法的各步骤。
  - 实现一个模板方法，定义一个算法的骨架。该模板方法不仅调用原语操作，也调用定义在 AbstractClass 或其它对象中的操作。
- ConcreteClass（具体类）
  - 实现原语操作已完成算法中与特定子类相关的步骤。

### 案例

- 各种继承模型。

### 对现有业务的思考

模板方法模式在 PHP 框架中较为常见，用以简化通过类继承对**默认行为**进行扩展的工作，将**继承机制**发挥得炉火纯青。

在业务中可以将订单分为多种不同类型的订单，但抽象订单中定义了基本行为，扩展订单则根据其特色进行重写。

```php
public function testExample(): void
{
    $data = [
        'price' => 100.00,
        'products' => [
            [
                'name' => '商品1',
                'unit_price' => 30,
                'quantity' => 3,
                'pickup_quantity' => 1,
            ],
            [
                'name' => '商品2',
                'unit_price' => 70,
                'quantity' => 2,
                'pickup_quantity' => 0,
            ]
        ],
    ];

    $pickUp = false;
    foreach ($data['products'] as $product) {
        if (isset($product['pickup_quantity']) && $product['pickup_quantity'] > 0) {
            $pickUp = true;
            break;
        }
    }

    $order = OrderFactory::createOrder($pickUp, $data);
    $order->commit();

    $this->assertInstanceOf(PickupOrder::class, $order);
}
```

## 访问者模式 - Visitor

概念：表示一个作用于某对象结构中的各元素的操作。它使你可以在不改变各元素的类的前提下定义作用于这些元素的新操作，将算法与其所作用对象隔离开。

访问者模式建议将新行为放入一个名为*访问者*的独立类中，而不是试图将其整合到已有类中。

### 适用性

- **一个对象结构包含很多类对象**，它们有不同的接口，而你想对这些对象是是一些依赖于其具体类的操作。
- 需要对一个对象结构中的对象进行很多不同并且不相关的操作，而你想避免让这些操作“污染”这些对象的类。Visitor 可将这些操作集中起来定义在一个类中。当该对象结构被很多应用共享时，用 Visitor 模式让每个应用仅包含需要用到的操作。
- 定义对象结构的类很少改变，但经常需要在此结构上定义新的操作。改变对象结构类需要重新定义对所有访问者的接口，这可能需要很大的代价。如果对象结构经常改变，那么可能还是在这些类中定义这些操作比较好。
- 清理辅助行为的业务逻辑。将所有非主要行为抽取到一组访问者类中，使得程序的主要类更专注于主要的工作。

### 特点

- 访问者模式使得更易于增加新的操作。仅需增加一个新的访问者即可在一个对象结构中定义一个新的操作。相反，如果每个功能都分散在多个类之上的话，定义新的操作时必须修改每一个类。
- 访问者集中相关的操作而分离无关的操作。

### 参与者

- Visitor（访问者）
  - 为该对象结构中 ConcreteElement 的每一个类声明一个 Visit 操作。该操作名字和特征标识了发送 Visit 请求给该访问者的类。这使得访问者可以确定正被访问元素的具体的类。这样访问者就可以通过该元素的特定接口直接访问它。
- ConcreteVisitor（具体访问者）
  - 实现每个由 Visitor 声明的操作。每个操作实现本算法的一部分，而该算法片段时对应于结构对象中的类。
  - ConcreteVisitor 为该算法提供了上下文并存储它的局部状态。这一状态常常在遍历该结构的过程中累计结果。
- Element（元素）
  - 定义一个 Accept 操作接口，它以一个访问者为参数。
- ConcreteElement（具体元素）
  - 实现 Accept 操作，该操作以一个访问者为参数。
- ObjectStructure（对象结构）
  - 能够枚举它的元素。
  - 可以提供一个高层的接口以允许该访问者访问它的元素。
  - 可以是一个组合（Composite）或是一个集合，如一个列表或一个无序集合。

### 案例

- 抽象语法树
- **访问者**模式为几何图像层次结构添加了对于 XML 文件导出功能的支持。

### 对现有业务的思考

该模式比较明确，就是不妨碍已有类结构的情况下，得到访问类属性的权限，并对其进行一系列操作。

访问者模式在 PHP 代码中不太常用，因为它不仅复杂，应用范围也比较狭窄。有个案例是给已有的复杂结构（例如组织机构）增加**报表导出**功能，可能是整个公司的财务报表、某个部门的财务报表，或者是具体某个雇员的薪资表。

```php
public function testExample(): void
{
    $mobileDev = new Department("Mobile Development", [
        new Employee("Albert Filmore", "designer", 100000),
        new Employee("Ali Hallway", "programmer", 100000),
        new Employee("Sarah Kronor", "programmer", 90000),
        new Employee("Monica Ronaldo", "QA engineer", 31000),
        new Employee("James Smith", "QA engineer", 30000),
    ]);
    $techSupport = new Department("Tech Support", [
        new Employee("Larry Albrecht", "supervisor", 70000),
        new Employee("Elton Pale", "operator", 30000),
        new Employee("Rajeev Kumar", "operator", 30000),
        new Employee("John Burnoose", "operator", 34000),
        new Employee("Sergey Korolyov", "operator", 35000),
    ]);
    $company = new Company("SuperStarDevelopment", [$mobileDev, $techSupport]);

    $report = new SalaryReport();

    echo "Client: I can print a report for a whole company:\n\n";
    echo $company->accept($report);

    echo "\nClient: ...or just for a single department:\n\n";
    echo $techSupport->accept($report);

    $this->assertTrue(true);
}
```
