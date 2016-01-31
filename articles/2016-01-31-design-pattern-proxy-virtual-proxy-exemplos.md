{
"title" : "Design Pattern Proxy - Virtual Proxy - Exemplos",
"author":"Royopa",
"date":"31-01-2016",
"tag":"design pattern, proxy, virtual proxy",
"slug" : "design-pattern-proxy-virtual-proxy-exemplos",
"category":"Design Patterns"
}

Veremos como implementar o Design Pattern Proxy do tipo Virtual Proxy abaixo,
Um artigo com a descrição do Design Pattern Proxy pode ser lido nesse endereço: [http://www.royopa.url.ph/2016/01/30/o-design-pattern-proxy](http://www.royopa.url.ph/2016/01/30/o-design-pattern-proxy).

Exemplo em PHP
--------------------

A amostra de código abaixo implementa uma ImageProxy que adia o carregamento de uma imagem.

```php
/**
 * Proxy design pattern (lazy loading)
 * 
 * @author Enrico Zimuel (enrico@zimuel.it) 
 * @see    http://en.wikipedia.org/wiki/Proxy_pattern
 */

interface ImageInterface
{
    public function display();
}

class RealImage implements ImageInterface
{
    protected $filename;
    
    public function  __construct($filename)
    {
        $this->filename = $filename;
        $this->loadFromDisk();
    }
    
    protected function loadFromDisk()
    {
        echo "Loading {$this->filename}\n";
    }
    
    public function display()
    {
        echo "Display {$this->filename}\n";
    }
}

class ProxyImage implements ImageInterface
{
    protected $id;
    
    protected $image;
    
    public function  __construct($filename)
    {
        $this->filename = $filename;
    }
    
    public function display()
    {
        if (null === $this->image) {
            $this->image = new RealImage($this->filename);
        }

        return $this->image->display();
    }
}
```

Veja abaixo o exemplo de uso das classes acima

```php
$filename = 'test.png';

// Ao instanciar um objeto Image o carregamento da imagem ocorre logo 
// que o objeto é instanciado
$image1 = new Image($filename);
echo $image1->display();

// Ao instanciar o objeto ProxyImage o carregamento da imagem não é necessário
$image2 = new ProxyImage($filename);

// O carregamento da imagem só irá ocorrer na primeira chamada do método 
// display
echo $image2->display();
```

Exemplo em Java
---------------

ImageInterface.java

```java
public interface ImageInterface
{
    public void display();
}
```

RealImage.java

```java
public class RealImage implements ImageInterface
{
    private String fileName;

    public RealImage(String fileName)
    {
        this.fileName = fileName;
        loadFromDisk(fileName);
    }

    @Override
    public void display()
    {
        System.out.println("Displaying " + fileName);
    }

    private void loadFromDisk(String fileName)
    {
        System.out.println("Loading " + fileName);
    }
}
```

ProxyImage.java

```java
public class ProxyImage implements ImageInterface
{
    private RealImage realImage;
    private String fileName;

    public ProxyImage(String fileName)
    {
        this.fileName = fileName;
    }

    @Override
    public void display()
    {
        if (realImage == null) {
            realImage = new RealImage(fileName);
        }
        
        realImage.display();
    }
}
```

ProxyPatternDemo.java

```java
public class ProxyPatternDemo
{
    public static void main(String[] args)
    {
        ImageInterface image = new ProxyImage("test_10mb.jpg");

        //image will be loaded from disk
        image.display(); 
        System.out.println("");
          
        //image will not be loaded from disk
        image.display();     
    }
}
```

Links usados para fazer esse artigo:

[https://github.com/ezimuel/PHP-design-patterns/blob/master/Proxy.php](https://github.com/ezimuel/PHP-design-patterns/blob/master/Proxy.php)
[https://sourcemaking.com/design_patterns/proxy/php](https://sourcemaking.com/design_patterns/proxy/php)
[http://www.tutorialspoint.com/design_pattern/proxy_pattern.htm](http://www.tutorialspoint.com/design_pattern/proxy_pattern.htm)
