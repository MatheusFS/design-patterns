# 1. Padrões de projetos: Estratégia
### Índice
1. Padrões de projetos: Estratégia
    - 1.1. [Problema](#1-1-problema)
    - 1.2. [O que NÃO deveria fazer](#1-2-o-que-nao-deveria-fazer)
        - 1.2.1. [Árvore de trabalho](#1-2-1-arvore-de-trabalho)
        - 1.2.2. [Animal.php](#1-2-2-animal-php)
        - 1.2.3. [Cachorro.php](#1-2-3-dog-php)
        - 1.2.4. [Passaro.php](#1-2-4-bird-php)
    - 1.3. [Como deve considerar fazer](#1-3-como-deve-considerar-fazer)
        - 1.3.1. [Árvore de trabalho](#1-3-1-arvore-de-trabalho)
        - 1.3.2. [Voa.php](#1-3-2-voa-php)
        - 1.3.3. [PodeVoar.php](#1-3-3-podevoar-php)
        - 1.3.4. [NaoVoa.php](#1-3-4-naovoa-php)
        - 1.3.5. [Animal.php](#1-3-5-animal-php)
        - 1.3.6. [Cachorro.php](#1-3-6-cachorro-php)
        - 1.3.7. [Passaro.php](#1-3-7-passaro-php)
        - 1.3.8. [execucao.php](#1-3-8-execucao-php)

# 1.1. Problema
Animal::class
- Cachorro::class
- Passaro::class

Dar ao animal capacidade de voar

# 1.2. O que NÃO deveria fazer
Inserir um método em Animal::class é uma má ideia, pois este comportamento não é geral para toda sub-classes de animais (um pássaro pode voar, mas um cachorro não).
## 1.2.1. Árvore de trabalho
```
╔ 📁 projeto (raiz)
╚══╦ 📁 Modelos
───╠═══ 📄 Animal.php
───╚══╦ 📁 Animal
──────╠═══ 📄 Cachorro.php
──────╚═══ 📄 Passaro.php
```
## 1.2.2. Animal.php (*projeto\Modelos*)
```php
class Animal{
    ...
    public function voar(){...}
    ...
}
```
## 1.2.3. Cachorro.php (*projeto\Modelos\Animal*)
```php
class Cachorro extends Animal{
    ...
    public function voar(){ return 'Não posso voar'; }
    ...
}
```
## 1.2.4. Passaro.php (*projeto\Modelos\Animal*)
```php
class Passaro extends Animal{
    ...
    public function voar(){ return 'Voando'; }
    ...
}
```
# 1.3. Como deve considerar fazer
## 1.3.1. Árvore de trabalho
```
╔ 📁 projeto (raiz)
╠══╦ 📁 Estrategias
║──╠═══ 📄 Voa.php
║──╚══╦ 📁 Voa
║─────╠═══ 📄 PodeVoar.php
║─────╚═══ 📄 NaoVoa.php
╚══╦ 📁 Modelos
───╠═══ 📄 Animal.php
───╚══╦ 📁 Animal
──────╠═══ 📄 Cachorro.php
──────╚═══ 📄 Passaro.php
```
## 1.3.2. Voa.php (*projeto\Estrategias*)
```php
interface Voa{
    ...
    public function voar();
    ...
}
```
## 1.3.3. PodeVoar.php (*projeto\Estrategias\Voa*)
```php
class PodeVoar implements Voa{
    ...
    public function voar(){ return 'Voando'; }
    ...
}
```
## 1.3.4. NaoVoa.php (*projeto\Estrategias\Voa*)
```php
class NaoVoa implements Voa{
    ...
    public function voar(){ return 'Não posso voar'; }
    ...
}
```
**Desacoplamento** - encapsular o *conceito* ou o *comportamento* que varia, neste caso, a *capacidade de voar*.

**Composição** - ao invés de herança, os objetos são compostos por outros objetos com os comportamentos integrados.

`Comportamento pode mudar sem efeitos colaterais`

## 1.3.5. Animal.php (*projeto\Modelos*)
```php
use Projeto\Estrategia\Voa;
...
class Animal{

    protected $como_voa;
    ...
    public function definir_como_voa(Voa $como_voa){

        $this->como_voa = $como_voa;
    }

    public function tentar_voar(){

        return $this->como_voa->voar();
    }
    ...
}
```
## 1.3.6. Cachorro.php (*projeto\Modelos\Animal*)
```php
use Projeto\Estrategia\Voa\NaoVoa;
...
class Cachorro extends Animal{
    ...
    public function __construct(){
        
        // Define a estratégia de comporta-
        // mento que o objeto deve ter
        $this->definir_como_voa(new NaoVoa);
    }
    ...
}
```
## 1.3.7. Passaro.php (*projeto\Modelos\Animal*)
```php
use Projeto\Estrategia\Voa\PodeVoar;
...
class Passaro extends Animal{
    ...
    public function __construct(){
        
        // Comportamento que o objeto deve ter
        $this->definir_como_voa(new PodeVoar);
    }
    ...
}
```
## 1.3.8. execucao.php
```php
$cachorro   = new Cachorro;
$passaro    = new Passaro;

$cachorro->tente_voar(); // 'Não posso voar'
$passaro->tente_voar(); // 'Voando'

$cachorro->colocar_asas(); // definir_como_voa(new PodeVoar)

$cachorro->tente_voar(); // 'Voando'
```
