{
"title" : "S.O.L.I.D - Os 5 princípios do design orientado a objeto",
"author":"Royopa",
"date":"12-04-2015",
"tag":"solid",
"slug" : "solid-os-primeiros-5-principios-do-design-orientado-a-objeto",
"category":"Solid"
}

Tradução do artigo [S.O.L.I.D: The First 5 Principles of Object Oriented Design, de Samuel Oloruntoba](https://scotch.io/bar-talk/s-o-l-i-d-the-first-five-principles-of-object-oriented-design)


S.O.L.I.D é um acrônimo para os primeiros cinco princípios de design orientado a objetos (OOD) criado por Robert C. Martin, popularmente conhecido como [Uncle Bob](http://en.wikipedia.org/wiki/Robert_Cecil_Martin).
Estes princípios quando combinados, facilita para um programador desenvolver um software que seja fácil de se manter e estender. Eles também facilitam que desenvolvedores evitem códigos malcheirosos, facilita na refatoração do código e são também uma parte do desenvolvimento ágil ou desenvolvimento adaptativo de software.

Nota: Esse é um simples artigo que somente diz o que é S.O.L.I.D.

S.O.L.I.D significa:

Quando o acrônimo é expandido as siglas podem parecer complicadas, mas elas são bem simples de entender.

S – Single-responsiblity principle
O – Open-closed principle
L – Liskov substitution principle
I – Interface segregation principle
D – Dependency Inversion Principle
Vamos olhar cada princípio individualmente para entender porque S.O.L.I.D pode ajudar a tornar os desenvolvedores melhores.

Single-responsibility Principle ou Princípio da Responsabilidade Única
----------------------------------------------------------------------

Abreviado como S.R.P – esse princípio estabelece que:

Uma classe deve ter uma e apenas uma razão para mudar, o que significa que uma classe deve ter uma única responsabilidade.
Por exemplo, digamos que temos algumas formas e gostaríamos de somar todas as áreas dessas formas. Bem, isso é bastante simples, certo?

```php
class Circle {
    public $radius;

    public function __construct($radius) {
        $this->radius = $radius;
    }
}

class Square {
    public $length;

    public function __construct($length) {
        $this->length = $length;
    }
}
```
Primeiro, nós criamos nossas classes de formas e definimos os construtores com os parâmetros obrigatórios. Em seguida, criaremos a classe AreaCalculator e escreveremos nossa lógica para somar as áreas de todas as formas previstas.

```php
class AreaCalculator {

    protected $shapes;

    public function __construct($shapes = array()) {
        $this->shapes = $shapes;
    }

    public function sum() {
        // logic to sum the areas
    }

    public function output() {
        return implode('', array(
            "<h1>",
                "Sum of the areas of provided shapes: ",
                $this->sum(),
            "</h1>"
        ));
    }
}
```
Para usar a classe AreaCalculator, nós simplesmente instanciamos a classe e passamos um array de formas, e exibimos a saída no fim da página.

```php
$shapes = array(
    new Circle(2),
    new Square(5),
    new Square(6)
);

$areas = new AreaCalculator($shapes);

echo $areas->output();
```
O problema com o método de saída é que a classe AreaCalculator lida com lógica da saída de dados. Então, o que fazer se o usuário quiser que a saída de dados seja um json ou outro tipo?

Toda a lógica precisaria ser tratada pela classe AreaCalculator e é isso que vai contra o princípio SRP; a classe AreaCalculator só deve somar as áreas de formas previstas, não deve se importar se o usuário deseja json ou HTML.

Assim, para corrigir isso, você pode criar uma classe SumCalculatorOutputter e usá-la para manipular qualquer lógica que você precisa para lidar com a exibição da soma das formas previstas.

A classe SumCalculatorOutputter funcionaria assim:

```php
$shapes = array(
    new Circle(2),
    new Square(5),
    new Square(6)
);

$areas = new AreaCalculator($shapes);
$output = new SumCalculatorOutputter($areas);

echo $output->JSON();
echo $output->HAML();
echo $output->HTML();
echo $output->JADE();
```
Agora, qualquer lógica que você precisa para a saída dos dados para o usuário é tratada pela classe SumCalculatorOutputter.

Open-closed Principle ou Princípio Aberto-fechado
-------------------------------------------------

Objetos ou entidades devem ser abertos para extensão, mas fechados para modificação.
Isto significa resumidamente que uma classe deve ser facilmente extensível sem modificar a própria classe. Vamos dar uma olhada na classe AreaCalculator, especialmente o método soma.
```php
public function sum() {
    foreach($this->shapes as $shape) {
        if(is_a($shape, 'Square')) {
            $area[] = pow($shape->length, 2);
        } else if(is_a($shape, 'Circle')) {
            $area[] = pi() * pow($shape->radius, 2);
        }
    }

    return array_sum($area);
}
```
Se nós quiséssemos que o método sum seja capaz de somar as áreas de mais formas, nós teríamos que adicionar mais blocos if/else, o que vai contra o princípio Open-closed.

Uma maneira de melhorar o método soma é remover a lógica para calcular a área de cada forma fora do método sum e anexá-la para a sua classe forma.
```php
class Square {
    public $length;

    public function __construct($length) {
        $this->length = $length;
    }

    public function area() {
        return pow($this->length, 2);
    }
}
```
A mesma coisa deve ser feita para a classe Circle, um método área deve ser adicionado. Agora, para calcular a soma de qualquer forma deve ser tão simples como:

```php
public function sum() {
    foreach($this->shapes as $shape) {
        $area[] = $shape->area;
    }

    return array_sum($area);
}
```
Agora nós podemos criar outra classe forma e passar a maneira de calcular a soma sem quebrar nosso código. NO entanto, agora surge um novo problema, como sabemos que o objeto passado para a classe AreaCalculator é uma forma ou se a forma tem um método com o nome area?

Programar para uma interface é uma parte integrante do S.O.L.I.D., um exemplo rápido é que criamos uma interface que implementa todas as formas:

```php
interface ShapeInterface {
    public function area();
}

class Circle implements ShapeInterface {
    public $radius;

    public function __construct($radius) {
        $this->radius = $radius;
    }

    public function area() {
        return pi() * pow($this->radius, 2);
    }
}
```
Em nosso método sum da classe AreaCalculator nós podemos checar se as formas previstas são realmente instâncias da interface Shape, caso contrário será lançada uma exceção:

```php
public function sum() {
    foreach($this->shapes as $shape) {
        if(is_a($shape, 'ShapeInterface')) {
            $area[] = $shape->area();
            continue;
        }

        throw new AreaCalculatorInvalidShapeException;
    }

    return array_sum($area);
}
```

Liskov substitution principle
-----------------------------

Let q(x) be a property provable about objects of x of type T. Then q(y) should be provable for objects y of type S where S is a subtype of T.
All this is stating is that every subclass/derived class should be substitutable for their base/parent class.

Still making use of out AreaCalculator class, say we have a VolumeCalculator class that extends the AreaCalculator class:

```php
class VolumeCalculator extends AreaCalulator {
    public function __construct($shapes = array()) {
        parent::__construct($shapes);
    }

    public function sum() {
        // logic to calculate the volumes and then return and array of output
        return array($summedData);
    }
}
```
In the SumCalculatorOutputter class:

```php
class SumCalculatorOutputter {
    protected $calculator;

    public function __constructor(AreaCalculator $calculator) {
        $this->calculator = $calculator;
    }

    public function JSON() {
        $data = array(
            'sum' => $this->calculator->sum();
        );

        return json_encode($data);
    }

    public function HTML() {
        return implode('', array(
            '<h1>',
                'Sum of the areas of provided shapes: ',
                $this->calculator->sum(),
            '</h1>'
        ));
    }
}
```
If we tried to run an example like this:

```php
$areas = new AreaCalculator($shapes);
$volumes = new AreaCalculator($solidShapes);

$output = new SumCalculatorOutputter($areas);
$output2 = new SumCalculatorOutputter($volumes);
The program does not squawk, but when we call the HTML method on the $output2 object we get an E_NOTICE error informing us of an array to string conversion.
```

To fix this, instead of returning an array from the VolumeCalculator class sum method, you should simply:

```php
public function sum() {
    // logic to calculate the volumes and then return and array of output
    return $summedData;
}
```
The summed data as a float, double or integer.

Interface segregation principle
-------------------------------

A client should never be forced to implement an interface that it doesn’t use or clients shouldn’t be forced to depend on methods they do not use.
Still using our shapes example, we know that we also have solid shapes, so since we would also want to calculate the volume of the shape, we can add another contract to the ShapeInterface:

```php
interface ShapeInterface {
    public function area();
    public function volume();
}
```
Any shape we create must implement the volume method, but we know that squares are flat shapes and that they do not have volumes, so this interface would force the Square class to implement a method that it has no use of.

ISP says no to this, instead you could create another interface called SolidShapeInterface that has the volume contract and solid shapes like cubes e.t.c can implement this interface:

```php
interface ShapeInterface {
    public function area();
}

interface SolidShapeInterface {
    public function volume();
}

class Cuboid implements ShapeInterface, SolidShapeInterface {
    public function area() {
        // calculate the surface area of the cuboid
    }

    public function volume() {
        // calculate the volume of the cuboid
    }
}
```

This is a much better approach, but a pitfall to watch out for is when type-hinting these interfaces, instead of using a ShapeInterface or a SolidShapeInterface.

You can create another interface, maybe ManageShapeInterface, and implement it on both the flat and solid shapes, this way you can easily see that it has a single API for managing the shapes. For example:
```php
interface ManageShapeInterface {
    public function calculate();
}

class Square implements ShapeInterface, ManageShapeInterface {
    public function area() { /*Do stuff here*/ }

    public function calculate() {
        return $this->area();
    }
}

class Cuboid implements ShapeInterface, SolidShapeInterface, ManageShapeInterface {
    public function area() { /*Do stuff here*/ }
    public function volume() { /*Do stuff here*/ }

    public function calculate() {
        return $this->area() + $this->volume();
    }
}
```
Now in AreaCalculator class, we can easily replace the call to the area method with calculate and also check if the object is an instance of the ManageShapeInterface and not the ShapeInterface.

Dependency Inversion principle
------------------------------

The last, but definitely not the least states that:

Entities must depend on abstractions not on concretions. It states that the high level module must not depend on the low level module, but they should depend on abstractions.
This might sound bloated, but it is really easy to understand. This principle allows for decoupling, an example that seems like the best way to explain this principle:

```php
class PasswordReminder {
    private $dbConnection;

    public function __construct(MySQLConnection $dbConnection) {
        $this->dbConnection = $dbConnection;
    }
}
```
First the MySQLConnection is the low level module while the PasswordReminder is high level, but according to the definition of D in S.O.L.I.D. which states that Depend on Abstraction not on concretions, this snippet above violates this principle as the PasswordReminder class is being forced to depend on the MySQLConnection class.

Later if you were to change the database engine, you would also have to edit the PasswordReminder class and thus violates Open-close principle.

The PasswordReminder class should not care what database your application uses, to fix this again we “code to an interface”, since high level and low level modules should depend on abstraction, we can create an interface:

interface DBConnectionInterface {
    public function connect();
}
The interface has a connect method and the MySQLConnection class implements this interface, also instead of directly type-hinting MySQLConnection class in the constructor of the PasswordReminder, we instead type-hint the interface and no matter the type of database your application uses, the PasswordReminder class can easily connect to the database without any problems and OCP is not violated.

```php
class MySQLConnection implements DBConnectionInterface {
    public function connect() {
        return "Database connection";
    }
}

class PasswordReminder {
    private $dbConnection;

    public function __construct(DBConnectionInterface $dbConnection) {
        $this->dbConnection = $dbConnection;
    }
}
```
According to the little snippet above, you can now see that both the high level and low level modules depend on abstraction.

Conclusion
----------

Honestly, S.O.L.I.D might seem to be a handful at first, but with continuous usage and adherence to its guidelines, it becomes a part of you and your code which can easily be extended, modified, tested, and refactored without any problems.
