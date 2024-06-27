# <center>SPRING BOOT 3 - MICHELLI BRITO 

## PRINCIPAIS ANOTAÇÕES SPRING

### Bom, primeiro valos lembrar sobre **Inversão de Controle**?

É um padrão de projeto, algo abstrato onde definimos todas as dependência de um determinado objeto, sem termos a necessidade de criar ou gerenciar. Nós passamos esse papel para o Spring (seu core.)

O **core** do Sring, portanto, **terá toda a responsabilidade de gerenciar todas as dependências**.

### Ok, mas como o Spring fará isso?

Ele utiliza de uma implementação (a forma concreta), a **Injeção de Dependência**. Portanto, a **Injeção de Dependência, nada mais é, do que a implementação concreta, da Injeção de Controle**. 😊

Dessa forma, o Spring gerenciará todos esses **Beans**. **Beans**, são os objetos que vamos criando conforme vamos construindo nossas aplicações. E assim, o Spring vai cuidar das instancias de todos esses objetos.




O Spring anottations possui diversos tipos de anotações, tais como:


### Stereotype
Precisamos mostrar ao Spring quais Beans (objetos), ele irá gerenciar. Um controller, Service, Repository... tudo isso é um Bean. Um objeto passa a ser Bean quando passamos as anotações.

- @Component - Todas as classes são Component. Essa é uma anotação mais genérica;
- @Repository - Classe onde terá lógicas de negócio do banco de dados (transações);
- @Service - Classe de serviço, onde envolve regras e lógicas de negócio;
- @Controller - Utilizada quando utilizamos uma aplicação que envolve camadas MVC (model view controller);
- @RestController - Para API Rest. Se teremos somente endpoints, expondo a API na Web, utilizamos este.
<hr>

### Core (Se divide em dois)
Tudo que envolve a base do Spring, está contida no Core, que fica dentro do projeto SpringFrameWork. Possui várias configurações prontas e também 
a possibilidade de customizar e criar configurações necessárias para nossa aplicação.

#### 1. Beans
Agora que o Spring sabe quais classes serão Beans em virtude das anotações acima utilizadas (Esteriótipos), ele precisa saber **onde ele irá injetar essas instancias quando necessárias**.

Bom, nós utilizamos o **Bean Service** dentro do nosso **Controller**, por exemplo. Pois assim podemos usar os métodos **findAll**, **findById**, etc.
Portanto, a classe **Controller** terá uma **dependência da classe Service**.

Precisamos **mostrar essa depência pro Spring**, para que ela saiba onde injetá-las quando necessário. Assim sendo, criaremos os **Pontos de Injeção (Dependência) de uma classe com a outra**.

```java
@Autowired
@Qualifier("parkingSpotServiceImpl")
private ParkingSpotService parkingSpotService;
```
- @AutoWired - Sempre que necessário, o Spring injetará o Bean Service, dentro do Bean Controller.

**Inicialmente, o ParkingSpotService importado acima, é uma interface contendo diversos métodos. Essa interface pode ter outras classes utilizando os seus métodos
(implementando ela). Ou seja, o Spring não consegue identificar qual Bean será injetado. Qual a solução? Usar a Anotação abaixo: @Qualifier**

- @Qualifier - Dentro dela, passamos qual Classe (que está implementando a interface/service) será utilizada.


- @Value - Algumas vezes precisamos definir algumas variáveis nas propriedades que usamos dentro do codigo. Mas ao invés
de deixar isso fixo em um Controller, por exemplo, é uma boa práticar declarar essas propriedades dentro do arquivo properties.
Logo, se precisarmos fazer alguma melhoria/alteração (**até mesmo sem parar a aplicação**), fica muito mais fácil. Exemplo:
```properties
app.name=Parking Control API
app.port=80
app.host=parkingcontrolapp
```

Para que isso seja exibido ao utilizarmos um método, criamos uma variável dentro da classe passando o @Value.
```java
@Value("${app.name}")
private String name;

@Value("${app.port}")
private String appPort;

@Value("${app.host}")
private String appHost;
```
Para exibir os valores, é só dar um SOUT dentro do método. [Veja aqui](https://github.com/MichelliBrito/parking-control-api/blob/8df47619b3016659e88dedb591c2f90ba25b5f4e/src/main/java/com/api/parkingcontrol/controllers/ParkingSpotController.java#L72)
<hr>

#### 2. Context

- @Configuration - Podemos configurar Datas de forma global, declarar Beans...
**Sempre que o Spring iniciar a aplicação, ele irá olhar para essa classe e levar em consideração as customizações e configurações que definirmos nessa classe de Configuração.**
<hr>

- @ComponentScan - Podemos definir/excluir determinados pacotes que serão passados na anotação. Mostraremos para o Spring quais são os pacotes que ele irá fazer uma "varredura", 
buscando pelos Beans que ele irá gerenciar. **Serve só para algo customizado mesmo, pois o Spring em sí já faz uma varredura automática ao ser iniciado.**
```java
@Configuration
@ComponentScan(basePackages = "com.api.parkingcontrol")
public class BeanConfig{}
```
<hr>

- @Bean - Ao invés de declararmos pro spring usando as anotações esteriótipos, faremos de outra maneira.
    
    Criamos uma classe por exemplo, chamada MyBean.
    Dentro da BeanConfig, criaremos um método que irá retornar exatamente o Objeto que criamos.
```java
@Bean
public myBean myBean() {
    return new MyBean();
}
```   
Pro Spring saber detectar que esse MyBean será um Bean gerenciado, precisamos passar @Bean.

Para podermos visualizar o que está dentro da Classe MyBean, faremos uma injeção de dependência dentro da Classe ParkingSpotController, assim como fizemos com o @Value.

Usa o Autowired para fazer a importação, e pode chamar o metodo com myBean.method(). [Veja aqui](https://github.com/MichelliBrito/parking-control-api/blob/8df47619b3016659e88dedb591c2f90ba25b5f4e/src/main/java/com/api/parkingcontrol/controllers/ParkingSpotController.java#L73C1-L74C1)

**Ok, isso foi um Bean criado por nós. Mas eventualmente usaremos Beans advindos de bibliotecas terceirizadas, como fazer nestes casos?**

Dentro do nosso pom, inserimos uma dependência modelmapper, utilizada para conversões. [Veja aqui](https://github.com/MichelliBrito/parking-control-api/blob/8df47619b3016659e88dedb591c2f90ba25b5f4e/pom.xml#L38)

#### Declarando Bean de uma biblioteca externa

Declaremos o Bean Modelmapper dentro da classe que iremos utilizar, mostrando que mesmo que esse Bean não tenha sido criado por nós, o Spring irá também gerenciá-lo.

```java
(dentro da classe config)
@Bean
public ModelMapper modelMapper() {
    return new ModelPapper();
}
```
E dentro da controller, que é onde iremos iniciar, faremos a injeção. [Veja aqui](https://github.com/MichelliBrito/parking-control-api/blob/8df47619b3016659e88dedb591c2f90ba25b5f4e/src/main/java/com/api/parkingcontrol/services/impl/ParkingSpotServiceImpl.java#L29)
<hr>

- @Lazy - Quando a gente não define nada na classe criada (passa somente @Component), o Spring internamente usa a "criação ansiosa". Já criando estes Beans, os deixando disponíveis para uso.
    
    Mas podemos fazer um "start preguiçoso". **Ou seja, falar pro Spring criar esse Bean somente quando precisar**.
    
    Passamos a anotação @Lazy e será feito dessa maneira.

    E como acionar esse Bean em um momento específico pro Spring criar?

Bom, mesmo de sempre. Faremos a injeção de dependência no controller com @Autowired importanto o Bean e assim o Spring carregará o método com @Lazy.

**Caso esse LazyBean não esteja importado dentro de algum método, não será inicializado pois o Spring não encontrará nenhum ponto de injeção de dependência.**
<hr>

- @Primary - Como é possivel ver no repositório desse projeto nos links acima. Nós temos uma interface chamada ParkingSpotService. Essa interface está sofrendo duas importações (ParkingSpotServiceImpl e ParkingSpotServiceImplV2).

Se você não especificar qual ele irá considerar nos pontos de injeção que vamos criando, ele entrará em conflito. Precisamos declarar qual ele irá utilizar. Tipo quando passamos o arquivo no @Qualifier.

Então esse @Qualifier não irá existir. **Usaremos, portanto, o @Primary**. [Veja aqui](https://github.com/MichelliBrito/parking-control-api/blob/8df47619b3016659e88dedb591c2f90ba25b5f4e/src/main/java/com/api/parkingcontrol/services/impl/ParkingSpotServiceImpl.java#L18)

<hr>


- @Scope
- @PropertySource
- @PropertySources
- @Profile
<hr>

### Web

- @RestController
- @RequestMapping
- @PostMapping
- @GetMapping
- @PutMapping
- @DeleteMapping
- @RequestBody
- @PathVariable
- @RequestParam
- @CrossOrigin
<hr>

### Boot

- @SpringBootApplication
- @EnableAutoConfiguration
- @ConfigurationProperties
<hr>






## INÍCIO
<hr>

### Core Container

É dentro dele onde está a implementação de inversão de controle, utilizando a injeção de dependência para gerenciar todos esse Beans. Mas...

### O que é inversão de controle?  

É um padrão de projeto, onde um objeto declara suas dependência mas não as cria. O objeto deixa essa tarefa para o framework (Spring). O Spring fará esse trabalho no Container IoC (Core Container), utilizando a injeção de dependência.

### O que é Injeção de Dependência?

É algo mais concreto. É a implementação da injeção de controle quando necessária, é feita pelo Spring Framework.

### O que são Beans?

Nada mais é do que um objeto que é instanciado/montando/gerenciado por um Core Container do Spring, através da Inversão de Controle e Injeção de Dependência.
Exemplos.: @Component, @Service, @Repository, @Controller.

## Spring Boot

É como se fosse uma somatória do Spring FrameWork + Servidor embutido (Tomcat ou Netty) - XML <bean> Configuration ou @Configuration

Antigamente precisava criar vários arquivos XML pra definir algumas coisas, onde hoje tudo já vem configurado, automatizado e simplificado.
<hr>

## Overview do projeto

**PRODUTS API REStful - Products.**

### Model e Mapeamento da Entidade Product.
1. Criaremos um package model e uma classe ProductModel;
2. Passamos @Entity para ela se tornar uma entidade;
3. @Table(name = "TB_PRODUCTS");
4. Implementamos Serializable, passando private static final long serialVersionUID = 1L.

#### Inserção de atributos
Algumas coisas aqui estarão diferentes do curso do Nélio, uma vez que este curso é um pouco mais atualizado.
Por exemplo:
1. o passar o GenerationType do Id, usaremos AUTO;
2. O Id não será integer e sim UUID;
   - UUID é indicado para projetos que usam banco de dados, evita conflitos de termos ID semelhantes.
3. O value agora será BigDecimal.
```java
@Entity
@Table(name = "TB_PRODUCTS")
public class ProductModel implements Serializable {
    private static final long serialVersionUID = 1L;
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID idProduct;
    private String name;
    private BigDecimal value;
}
```
E agora, geramos nosso construtor e getters and setters. 😊
Rodando o código Spring, dentro do pgAdmin 4 (postgres), a tabela terá sido inserida.


### Criação do Repository com JPA Repository
Contaremos com diversos métodos prontos sem precisar implementar cada um deles.
Seja para salvar recursos, obter uma lista de recursos um determinado recurso pelo ID, enfim... fazer tudo com JPA com facilidade!

1. Criaremos um novo pacote **repositories** com uma interface chamada ProductRepository;
2. Como sabemos, extendemos JpaRepository<**Entidade**, **Identificador**>;
3. Passamos a anotação, pois precisamos mostrar pro spring que tal classe será um bean gerenciado por ele;
   - Neste, caso: @Repository.
```java
@Repository
public interface ProductRepository extends JpaRepository<ProductModel, UUID> {}
```

### Criação do Controller

1. Criaremos um **ponto de injeção**, visto que que Controller é dependente de Repository.
   - Para isso, utilizaremos o @Autowired, importando o ProductRepository.
2. Implementaremos agora os métodos CRUD.

<hr>

#### POST
O POST receberá o recurso, irá fazer uma validação inicial e salvar na base de dados.
Antes disso, para recebermos esses valores via JSON, precisaremos mapear esses campos.

Para isso, usaremos uma feature: **records**. Eles nos permitem iniciar esses objetos para transferência de dados, como os DTOS por exemplo.
Criaremos portanto, um DTO, ele:
1. Irá receber dados desse recurso "Product";
2. Fará a validação inicial;
3. Se tudo estiver OK, ele fará a conversão para o model e salva este recurso na base de dados.
<hr>

### Mapeamento DTO de entrada com records
1. Criamos o pacote dtos;
2. Criamos um Record DTO, denominado ProductRecordDto;
3. Passaremos os parâmetros do Product, menos ID pois não precisamos nos preocupar.
```java
public record ProductRecordDto(String name, BigDecimal Value){}
```
Destacando-se que o Record já possui métodos prontos, como: getters/constructores/equals&hashcode etc.

Records além disso, são **IMUTÁVEIS**, por isso, não possuem métodos **SETTERS**.

**Para validarmos, dentro do parâmetro passaremos algumas anotações, como:**
```java
public record ProductRecordDto(@NotBlank String name, @NotNull BigDecimal value) {}
```

<hr>

### MÉTODO POST

```java
@PostMapping("/products")
public ResponseEntity<ProductModel> saveProduct(@RequestBody @Valid ProductRecordDto productRecordDto) {
    var productModel = new ProductModel();
    BeanUtils.copyProperties(productRecordDto, productModel);
    return ResponseEntity.status(HttpStatus.CREATED).body(productRepository.save(productModel));
}
```
ResponseEntity - Retorno do método, neste caso do ProductModel, que na linha abaixo, será criado.

O método saveProduct vai receber como parâmetro (corpo da solicitação Http) um ProductRecordDto que conté um name e um value.

@RequestBody - Ajuda a deixar a aplicação no padrão REST, pois, sem ela, se mapeamos algum endereço, o Spring entende que é algo que está dentro da nossa aplicação, como um arquivo, por exemplo. O @ResponseBody vai entender que esse é um endereço de uma página da web, obedecendo ao princípio do REST de comunicação via protocolo HTTP.

@Valid - Para que esses dados entrem de fato em vigor

Dentro da função iniciamos um ProductModel. O Dto serve somente para receber o que está vindo na requisição do corpo em formato JSON.

Por isso na linha abaixo, usamos o BeanUtils.copyProperties para fazer a conversão de JSON para Model.

Nessa ultima linha, construimos o retorno. O retorno do tipo ResponseEntity será em duas partes:
1. Status - Se tudo ocorreu bem, precisamos enviar para o cliente que o recurso foi criado. Usamos portanto: HttpStatus.CREATED
2. Já no .body (corpo) dessa reposta, poderemos enviar o que foi salvo. (Nome e Valor) que o cliente colocou + o ID que será gerado.
3. E pra fazer esse save? Como pode ser observado, estamos importando o productRepository. Usaremos seu método .save dentro do body, passando nosso var productModel.
<hr>

#### MÉTODO GET
```java
@GetMapping("/products")
public ResponseEntity<List<ProductModel>> getAllProducts() {
    return ResponseEntity.status(HttpStatus.OK).body(productRepository.findAll());
}
```

Como são **TODOS** os produtos, ele irá nos retornar uma **List** do tipo ProductModel.

Diferente do código post, ele irá nos retornar o HttpStatus.OK. E o método de repository é o .findAll() que nos retornará uma lista.
<hr>

### MÉTODO GET (COM ID)

```java
@GetMapping("/products/{id}")
public ResponseEntity<Object> getOneProduct(@PathVariable(value = "id") UUID id) {
    Optional<ProductModel> productO = productRepository.findById(id);
    if (productO.isEmpty()) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body("Product not found.");
    }
    else {
        return ResponseEntity.status(HttpStatus.OK).body(productO.get());
    }
}
```

O @PathVariable nos ajuda a pegar o {id} passado no @Get.

O método nos retornará um Optional.

Instanciaremos um objeto ProductModel do tipo Optional, e entraremos no repository, usando o findById passando a ID do método como parâmetro.
Depois, é aquele if padrão. Se estiver vazio, lança a mensagem de not found. Se não estiver vazio, pega o product e passa no corpo da resposta.

<hr>

### MÉTODO PUT (UPDATE)

Put é parecido com o getOneProduct porque também precisamos passar o ID como parâmetro do que vamos atualizar.

```java
@PutMapping("/products/{id}")
public ResponseEntity<Object> updateProduct(@PathVariable(value = "id") UUID id,
                                                @RequestBody @Valid ProductRecordDto productRecordDto) {
    Optional<ProductModel> productO = productRepository.findById(id);
    if (productO.isEmpty()) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body("Product not found.");
    }
    var productModel = productO.get();
    BeanUtils.copyProperties(productRecordDto, productModel);
    return ResponseEntity.status(HttpStatus.OK).body(productRepository.save(productModel));
}
```

Continua recebendo o PathVariable e UUID.

Mas dessa vez, receberá também o RequestBody e Valid, pois precisamos do name e value (ProductRecordDto) para realizarmos o update.

Criamos a variável productO que utilizando o repository, irá verificar se primeiro, o produto existe (buscando pela ID).

Se existir, fazemos o if para saber se está vazio.

Se não estiver vazio, instanciaremos um ProductModel. Mas esse ProductModel, não será instanciado do zero igual na saveProduct. Iremos atribuir a essa variável o valor que ja recebeu da base dados "productO".

E daí damos o retorno padrão dando o OK e salvando.
<hr>

### MÉTODO DEL (DELETE)



```java
@DeleteMapping("/products/{id}")
public ResponseEntity<Object> deleteProduct(@PathVariable(value = "id") UUID id) {
    Optional<ProductModel> productO = productRepository.findById(id);
    if (productO.isEmpty()) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body("Product not found.");
    }
    productRepository.delete(productO.get());
    return ResponseEntity.status(HttpStatus.OK).body("Product deleted successfully.");
}
```
A partir de agora o código ja está mais autoexplicativo.

Passamos os mesmos parâmetros porém depois do if não precisamos intanciar um ProductModel.

Só passamos o delete do repository com o .get() para pegar toda a entidade.

Cabe destacar, que o repository possui outros deletes, tais como:
- deleteAll(), deleteById, etc...
<hr>

### Inserindo HATEOAS e Criação de hipermídias

Colocamos a dependência no maven.
```
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-hateoas</artifactId>
</dependency>
```

E extendemos na classe principal, tudo isso visando uma melhor **navegabilidade**.
```java
public class ProductModel extends RepresentationModel<ProductModel> implements Serializable {}
```

Agora, quando formos utilizar essa clase, internamente ele já vai ter alguns métodos (como add, por exemplo) para construimos alguns links para navegabilidade.
<hr>

### EXEMPLOS + ALTERAÇÕES DOS MÉTODOS ACIMA.

#### NA CLASSE PRODUCTCONTROLLER <<<
Como é possível ver nos métodos acima, **fazemos no proprio retorno a busca ou save ou qualquer outro método do repository**, iremos mudar isso.

Import dos links e methodOn
```java
import static org.springframework.hateoas.server.mvc.WebMvcLinkBuilder.linkTo;
import static org.springframework.hateoas.server.mvc.WebMvcLinkBuilder.methodOn;
```

Exemplo de alteração:
```java
ANTES

@GetMapping("/products")
public ResponseEntity<List<ProductModel>> getAllProducts() {
    return ResponseEntity.status(HttpStatus.OK).body(productRepository.findAll());
}
```

```java
DEPOIS

@GetMapping("/products")
public ResponseEntity<List<ProductModel>> getAllProducts() {
   List<ProductModel> productsList = productRepository.findAll();
   if (!productsList.isEmpty()) {
      for (ProductModel product : productsList) {
         UUID id = product.getIdProduct();
         product.add(linkTo(methodOn(ProductController.class).getOneProduct(id)).withSelfRel());
      }
   }
   return ResponseEntity.status(HttpStatus.OK).body(productsList);
}
```

Em suma:
- Criamos uma Lista do tipo ProductModel e passamos o findAll dentro dela;
- Fazemos um if para saber se ela está vazia, se não tiver:
  - Faremos um loop, criaremos uma variável ID e usaremos o product.add;
    - **O .add é uma extensão do proprio RepresentationModel!**
  - Dentro do add, usaremos linkTo, methodOn e withSelfRel.

linkTo - Fala pra qual endpoint/método vamos redirecionar o cliente quando ele clicar neste link.

methodOn - Qual controller está esse método? E qual é este método em sí que vai receber esse redirecionamento?

Bom, se o cliente está numa lista de produtos, vamos redirecionar ele para um produto específico, neste caso é o **getOneProduct**.

1. Primeiro definimos qual Controller está esse método (ProductController.class);
2. E depois qual o nome desse método. Usaremos o método de baixo getOneProduct;
   3. Como esse método recebe um id, passamos o id que criamos em cima. (Id de cada um dos produtos iterado pelo for).

withSelfRel - Redireciona para cada um de seus produtos em sí.

![img_2.png](img_2.png)

```java
ALTERAÇÃO GET ONE PRODUCT

@GetMapping("/products/{id}")
public ResponseEntity<Object> getOneProduct(@PathVariable(value = "id") UUID id) {
    Optional<ProductModel> productO = productRepository.findById(id);
    if (productO.isEmpty()) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body("Product not found.");
    }
    productO.get().add(linkTo(methodOn(ProductController.class).getAllProducts()).withRel("Products List"));
    return ResponseEntity.status(HttpStatus.OK).body(productO.get());
}
```

Única diferença é que não referenciamos por getOne, faremos o método .getAllProducts().

![img_1.png](img_1.png)