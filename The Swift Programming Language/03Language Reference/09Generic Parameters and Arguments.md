#泛型形参和泛型实参
这章节讲述在泛型、函数和初始化函数中的形参和实参。当您声明一个泛型、函数或初始化函数时，必须先明确哪些类型参数是可操作的。这些泛型参数充当占位符，当一个泛型被泛型函数或者初始化函数创建时，该实例的实际参数类型将取代泛型。

关于Swift的泛型概述，请查看泛型章节。


##泛型形参子句
一个泛型形参子句指定一个泛型或函数的类型参数，以及与这些参数相关的任何约束和限制。泛型参数子句包含在尖括号中(`<>`)，并具有下列形式之一:

    <generic parameter list>
    <generic parameter list where requirements>

泛型形参列表是一个用逗号分隔的列表，每个泛型形参具有以下形式:

    type parameter: constraint

一个泛型形参包含一个类型参数后跟一个可选的约束。 一个类型参数的名称只是一个占位符（例如，`T`，`U`，`V`，`KeyType`，`ValueType`等等）。您可以访问该类型参数（及其任何相关类型），在其余的类型、函数或初始化函数的声明，其中包括在函数或初始化函数的签名中。

该约束指定类型参数继承自一个特定的类或者是符合一种协议或协议组成。例如下面的例子中，泛型形参`T: Comparable`意味着替代类型参数T的任何类型参数必须符合Comparable协议。
    
    func simpleMin<T: Comparable>(x: T, y: T) -> T {
        if x < y {
       		return y
        }
    	return x
    }

又如`int`和`double`类型，都符合`Comparable`协议，该函数可接受这两种类型的参数。与泛型类型相比，当您使用一个泛型函数或初始化函数时，无需指定一个泛型实参子句。因为可从传递给函数或初始化函数的参数类型来推断类型参数。

    simpleMin(17, 42) // T is inferred to be Int
    simpleMin(3.14159, 2.71828) // T is inferred to be Double

##Where子句
您可以通过包含泛型形参列表后面的`where`子句中指定类型参数和其相关类型的附加限制条件。`where`子句由关键字`where`后跟限制条件组成，并用逗号分隔多个限制条件。

在`where`子句中的要求指定类型参数继承自一个类或是符合一种协议或协议组成。虽然在`where`子句中提供的糖衣语法能缩写类型参数的约束（例如，`T: Comparable`等于`T where T: Comparable`），您也可以使用它来编写更复杂的类型参数约束。例如，您可以描述该限制为泛型类型T继承自`C`类和符合`P`协议，采用`<T where T: C, T: P`>。

正如上面所提，您可以约束类型参数的相关类型以符合协议。例如，泛型形参子句`<T: Generator where T.Element: Equatable>`指定T符合`Generator`协议以及泛型类型`T`、`T.Element`都符合`Equatable`协议（T具有相关联的类型`Element`，因为`Generator`声明`Element`和`T`符合`Generator`协议）。

您也可以指定两种类型是相同的限制条件，使用`==`操作符。例如，泛型参数子句`<T: Generator, U: Generator where T.Element == U.Element>` 表示`T`和`U`符合`Generator`协议的约束，而其相关的类型必须相同。

任何类型实参取代一个类型形参时必须满足所有放置在类型参数的约束和限制条件。

通过编写带有不同的限制条件和约束的泛型形参子句，您可以重载一个泛型函数或初始化函数。当你调用一个重载的泛型函数或初始化函数，编译器将使用这些约束来解决这些重载的函数或初始化函数调用。

当然您可以继承一个泛型类，但子类也必须是一个泛型类。

泛型形参子句的语法

	generic-parameter-clause → <generic-parameter-listrequirement-clause opt>
	generic-parameter-list → generic-parameter | generic-parameter,generic-parameter-list
	generic-parameter → type-name
	generic-parameter → type-name:type-identifier
	generic-parameter → type-name:protocol-composition-type
	requirement-clause → where requirement-list
	requirement-list → requirement | requirement,requirement-list
	requirement → conformance-requirement  same-type-requirement
	conformance-requirement → type-identifier:type-identifier
	conformance-requirement → type-identifier:protocol-composition-type
	same-type-requirement → type-identifier==type-identifier

##泛型实参子句
泛型实参子句用来指定类型实参。一个泛型实参子句包含在尖括号中（<>），并具有如下形式：
	
	<generic argument list>

泛型实参列表是一个用逗号分隔的类型参数列表。一个类型实参只是一个用来替换实质的类型参数的名称而已。这意味着，类型实参是泛型类型的专用版本。作为一个例子，Swift标准库定义了一个泛型的字典类型为：

	struct Dictionary<KeyType: Hashable, ValueType>: Collection, DictionaryLiteralConvertible {
    	/* ... */
	}

泛型`Dictionary`类型，`Dictionary<String,Int>`是由泛型形参`KeyType:Hashable`和`ValueType`组成，并在实际应用中赋予类型实参`String`和`Int`。每种类型实参必须满足泛型形参的所有约束，其中包括在`where`子句中指定的任何附加限制条件。在上面的例子中，`KeyType`类型形参被限制为要符合`Hashable`协议，因此字符串也必须符合`Hashable`协议。

您也可以用一个类型实参来取代类型形参，当自身就是一个专用版本的泛型类型时。例如，你可以用一个`Array<T>`取代`T`。当用于一个专用版本数组`Array<Int>`时，形成一个数组，其元素本身也是整数数组。

	let arrayOfArrays: Array<Array<Int>> = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]

如泛型形参子句所描述，您不应当使用泛型实参子句来指定一个泛型函数或初始化函数的类型实参。

泛型实参子句的语法
	
	generic-argument-clause → <generic-argument-list>
	generic-argument-list → generic-argument | generic-argument, generic-argument-list
	generic-argument → type
