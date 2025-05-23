# **IoC (Inversion of Control) — 控制反转**

**IoC**（控制反转）是一个设计原则，指的是将程序的控制权从传统的“程序控制流”反转给外部框架或容器。在传统的应用程序中，程序通过创建对象、管理对象生命周期来控制程序的执行流程。而在 IoC 中，控制权被反转，外部框架或容器负责管理对象的创建、生命周期、依赖关系等。

## **IoC 的核心思想：**

- 传统开发中，应用程序负责控制对象的创建和管理。

- **IoC** 反转了这一点，外部框架（如 Spring、Angular 等）负责对象的创建、管理和依赖关系。

换句话说，IoC 是一种**设计模式**，旨在解耦程序的各个组件，使得各个部分的工作职责更加明确。

  

## **IoC 的常见形式：**

###  **事件驱动**
事件循环（例如 Node.js 的事件模型）。

### **回调函数**
函数调用交给外部控制，如 JavaScript 中的异步回调。

### [ DI (Dependency Injection) — 依赖注入](#**DI%20(Dependency%20Injection)%20—%20依赖注入**)
通过外部传入依赖对象，来消除内部控制。

### **服务定位器**
通过服务定位器模式查找服务


## **DI (Dependency Injection) — 依赖注入**

**DI**（依赖注入）是实现 **IoC** 的一种技术方式。它的核心思想是：**将对象的依赖关系交给外部容器来管理，而不是由对象自己创建这些依赖。**

### **依赖注入的核心理念：**

- 类不再自己创建依赖对象，而是通过外部传递（注入）这些依赖对象。

- 这样一来，类的依赖关系被管理和注入，不必自己负责这些依赖的创建和管理。

### **依赖注入的工作方式：**

#### **构造函数注入**
通过构造函数注入依赖项。

#### **属性注入**
通过 setter 方法注入依赖项。

#### **接口注入**
通过接口的实现类来注入依赖项。


  

### **例子：依赖注入**

假设有一个 Car 类依赖于 Engine 类，如果不使用 DI，Car 类可能自己创建一个 Engine 对象，这样就会导致高耦合。通过 DI，Car 类不再关心如何创建 Engine，而是依赖外部注入的 Engine 对象。

##### 不使用 DI
```java
class Engine {
  start() {
    console.log('Engine started');
  }
}

class Car {
  constructor() {
    this.engine = new Engine();  // 创建了自己的 Engine 实例
  }

  drive() {
    this.engine.start();
    console.log('Car is driving');
  }
}

const car = new Car();
car.drive();
```

##### 使用 DI
```java
class Engine {
  start() {
    console.log('Engine started');
  }
}

class Car {
  constructor(engine) {
    this.engine = engine;  // Engine 对象由外部传入
  }

  drive() {
    this.engine.start();
    console.log('Car is driving');
  }
}

const engine = new Engine();
const car = new Car(engine);  // Engine 由外部创建并传入
car.drive();
```

### **DI 的好处：**
1. **解耦**：对象不再直接控制其依赖的创建，使得它们更容易测试和维护。
2. **可测试性**：依赖可以在单元测试中轻松地替换为 mock 对象，从而提升测试的效率。
3. **灵活性**：通过外部配置，可以轻松替换依赖对象，例如替换不同的数据库连接、日志系统等。