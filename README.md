

![logo](css-modules-logo.png)

## CSS Modules

A CSS Module is a CSS file in which all class names and animation names are scoped locally by default. All URLs (url(...)) and @imports are in module request format (./xxx and ../xxx means relative, xxx and xxx/yyy means in modules folder, i. e. in node_modules).

CSS Modules是所有的类名和动画名称都默认是只在某个命名空间内生效的CSS文件书写方式。所有的URL和@imports 都是模块化的，在引用的时候需要添加路径或者命名空间才能够调用到，无论是相对路径，绝对路径。

CSS Modules compile to a low-level interchange format called ICSS or Interoperable CSS, but are written like normal CSS files:

CSS Modules会被编译成一个内聚的，格式化的被称为ICSS的或者说内部可操作的CSS的格式，但是在编写的时候，和通常的CSS是一致的：

```css
/* style.css */
.className {
  color: green;
}
```

When importing the CSS Module from a JS Module, it exports an object with all mappings from local names to global names.

当我们需要引入一个模块化的CSS文件的时候，我们只需要像引入一个JS模块一样引入这个文件即可，CSS Modules将文件内部的样式都映射地暴露到全局中，这个过程中带着他们的命名空间，或者说路径。

```css
import styles from "./style.css";
// import { className } from "./style.css";

element.innerHTML = '<div class="' + styles.className + '">';
```



### Naming

For local class names camelCase naming is recommended, but not enforced.

在文件中的类名，推荐使用驼峰命名，但是这也不是强制的。（个人建议，还是使用BEM比较符合国内开发者的习惯）。

### Exceptions

:global switches to global scope for the current selector resp. identifier. :global(.xxx) resp. @keyframes :global(xxx) declares the stuff in parenthesis in the global scope.

:global这个伪选择器是用来将局部作用于的规则直接映射到全局作用域，变为全局可用的规则的。

Similar :local and :local(...) for local scope.

:local也类似，尽管你默认是局部作用域的，但是这个伪类可以在全局作用域中生命一个只在局部有效的规则。

If the selector is switched into global mode, global mode is also activated for the rules. (this allows to make animation: abc; local)

Example: .localA :global .global-b .global-c :local(.localD.localE) .global-d

### Composition

It's possible to compose selectors.

```css 
.className {

  color: green;

  background: red;

}

.otherClassName {

  composes: className;

  color: yellow;

}

```


There can be multiple composes rules, but composes rules must be before other rules. Extending works only for local-scoped selectors and only if the selector is a single class name. When a class name composes another class name, the CSS Module exports both class names for the local class. This can add up to multiple class names.

It's possible to compose multiple classes with 

```css
composes: classNameA classNameB;
```

CSS Modulesk可以由很多类型的规则组成，换句话说，可以继承和重写，不过继承的规则必须是子啊之前出现过的。

继承的特性只能是在本地，本模块的，而且被继承的选择器需要符合一个条件，即这个选择器对应唯一的一组规则，而不是可以对应很多组规则。

另外，也可以实现多重继承，继承两个独立的类。

### Dependencies

It's possible to compose class names from other CSS Modules.

从其他的CSS Modules中继承规则也是允许的。

```css
.otherClassName {

  composes: className from "./style.css";

}
```

Note that when composing multiple classes from different files the order of appliance is undefined. Make sure to not define different values for the same property in multiple class names from different files when they are composed in a single class.

从不同的模块引入的样式，他们之间的顺序是不确定的，因此尽量保证继承的每一个选择器，他们定义的央视之间应该保持良好的独立性，防止出现污染。

Note that composing should not form a circular dependency. Elsewise it's undefined whether properties of a rule override properties of a composed rule. The module system may emit an error.

不要出现循环继承，当然这会引起一些编译上的错误。

Best if classes do a single thing and dependencies are hierarchic.

一个类最好之负责一种样式，以保证所有的依赖都是层次分明的。

### Usage with preprocessors

Preprocessors can make it easy to define a block global or local.

也可以使用一些预编译器。

i. e. with less.js

```css
:global {
  .global-class-name {
    color: green;
  }
}

```



### Why?

modular and reusable CSS!

1. No more conflicts. 减少了冲突。
2. Explicit dependencies. 依赖变得很有层次。
3. No global scope. 不再污染全局。