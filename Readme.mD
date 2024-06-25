# SPRING BOOT 3 - MICHELLI BRITO

## ANOTAÇÕES SPRING

- @Component - Classe mais genérica;
- @Service - Classe de serviço, onde envolve regras de negócio;
- @Repository - Classe onde terá transações com base de dados;
- @Controller - Terá endpoints.
- @RestController - Específico para API Rest;
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

### Mapeamento o DTO de entrada com records
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






#### GET

<hr>


