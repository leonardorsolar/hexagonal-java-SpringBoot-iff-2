# Tutorial Arquitetura Hexagonal - CRUD de Usuários | API + MongoDB (NoSQL) + Kafka (Mensageria)

Aprenda na prática como aplicar a **Arquitetura Hexagonal** em microsserviços utilizando **Java**, **Spring Boot**, **MongoDB** e **Kafka**.

Neste projeto, construiremos um **CRUD de Clientes**, passando por todas as camadas da arquitetura de forma clara e orientada.

---

Nesta segunda etapa, nosso foco será o desenvolvimento de uma **API CRUD de Usuários**. Você aprenderá, na prática, como:

-   **Isolar o domínio da aplicação**
-   **Garantir testabilidade da lógica de negócio**
-   **Promover o desacoplamento entre as camadas**

Essa separação é essencial para manter o código limpo, reutilizável e fácil de evoluir, especialmente em sistemas baseados em microsserviços.

## 🧩 Qual é a função da camada de **Domínio**?

A camada de **Domínio** (também chamada de camada de **modelo de negócio**) é o **coração da aplicação**. Ela representa os **conceitos centrais** do seu sistema e deve conter:

1. **Entidades**: objetos com identidade e ciclo de vida (como `Customer` e `Address`);
2. **Regras de negócio puras**: validações, comportamentos e restrições;
3. **Objetos de valor (Value Objects)**: como `Address`, que não tem identidade própria.

### ✔️ Sua função principal é **modelar o negócio**, de forma isolada, **sem depender de frameworks**, banco de dados, API, etc.

---

Vamos lá!

# Parte 1: Criação da classe clientes (Customer)

# Camada Domain

Começamos o projeto pela classe de domínio.

**Passo 1: Criando a classe Customer na camada Domain**

-   Acesse a pasta src/main/java/com/example/hexagonal/domain

-   Dentro de domain crie a classe Customer.java

Iremos criar a classe cliente com os seguintes atributos:
id;
name;
address;
cpf;
isValidCpf;

-   Dentro da classe adicione:

```java
package com.example.hexagonal.domain;

public class Customer {

    public Customer() {
        this.isValidCpf = false;
    }

    private String id;

    private Boolean isValidCpf;

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public Boolean getIsValidCpf() {
        return isValidCpf;
    }

    public void setIsValidCpf(Boolean isValidCpf) {
        this.isValidCpf = isValidCpf;
    }

}

```

obs.: A camada **domain** deve ser independente de qualquer tecnologia externa: ela não pode conter dependências de frameworks, nem ser acessada diretamente por camadas externas sem passar pelas interfaces (portas) da aplicação. Veja que importamos os getter e setter na mão e não utilizamos a tecnologia lombok do framework Spring.

**Passo 2: Criando do teste unitário**

**Criando um test unitário para Customer na camada Domain**

Para testar a classe `Customer` com Maven, você precisa:

1. **Criar uma classe de teste** com o framework JUnit;
2. Colocá-la na pasta `src/test/java`;
3. Escrever testes que **verifiquem os comportamentos dos métodos (get/set)** e **o construtor**.

---

### ✅ Exemplo de teste unitário para a classe `Customer`

Crie este arquivo:

```
src/test/java/com/example/hexagonal/domain/CustomerTest.java
```

Com o seguinte conteúdo:

```java
package com.example.hexagonal;

import static org.junit.jupiter.api.Assertions.assertFalse;
import static org.junit.jupiter.api.Assertions.assertNull;

import org.junit.jupiter.api.Test;

import com.example.hexagonal.domain.Customer;

public class CustomerTest {
    @Test
    void testCustomerDefaultConstructor() {
        Customer customer = new Customer();

        System.out.println("CPF válido? " + customer.getIsValidCpf());
        System.out.println("ID: " + customer.getId());

        assertFalse(customer.getIsValidCpf());
        assertNull(customer.getId());

    }
}
```

---

### ☑️ Como rodar os testes com Maven

Comente o teste que se encontra em src/test/java/com/example/hexagonal/HexagonalApplicationTests.java

No terminal, execute na raiz do projeto:

```bash
mvn test
```

O Maven irá:

-   Compilar o código da `src/main/java`
-   Compilar os testes da `src/test/java`
-   Rodar os testes com o JUnit

### 🧪 Exemplo de saída esperada:

```bash
[INFO] Running com.example.hexagonal.CustomerTest
=====> CPF válido? false
=====> ID: null
[INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0
```

Depois de realizado o etste simples, vamos atualizar o código da classe Customer:
src/main/java/com/example/hexagonal/domain/Customer.java

```java
package com.example.hexagonal.domain;

import org.springframework.boot.autoconfigure.amqp.RabbitConnectionDetails.Address;

public class Customer {

    public Customer() {
        this.isValidCpf = false;
    }

    public Customer(String id, String name, Address address, String cpf, Boolean isValidCpf) {
        this.id = id;
        this.name = name;
        this.address = address;
        this.cpf = cpf;
        this.isValidCpf = isValidCpf;
    }

    private String id;

    private String name;

    private Address address;

    private String cpf;

    private Boolean isValidCpf;

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Address getAddress() {
        return address;
    }

    public void setAddress(Address address) {
        this.address = address;
    }

    public String getCpf() {
        return cpf;
    }

    public void setCpf(String cpf) {
        this.cpf = cpf;
    }

    public Boolean getIsValidCpf() {
        return isValidCpf;
    }

    public void setIsValidCpf(Boolean isValidCpf) {
        this.isValidCpf = isValidCpf;
    }

}
```

Atualize os testes da classe Customer:
src/test/java/com/example/hexagonal/CustomerTest.java

```java
package com.example.hexagonal;

import static org.junit.jupiter.api.Assertions.assertFalse;
import static org.junit.jupiter.api.Assertions.assertNull;

import org.junit.jupiter.api.Test;

import com.example.hexagonal.domain.Customer;

class CustomerTest {

    @Test
    void testCustomerDefaultConstructor() {
        Customer customer = new Customer();

        System.out.println("CPF válido? " + customer.getIsValidCpf());
        System.out.println("ID: " + customer.getId());

        assertFalse(customer.getIsValidCpf());
        assertNull(customer.getId());
        assertNull(customer.getName());
        assertNull(customer.getAddress());
        assertNull(customer.getCpf());
    }

}
```

# Parte 2: Criação da classe Endereço do cliente (Address)

**Passo 1: Criando a classe Address na camada Domain**

Iremos criar a classe Address com os seguintes atributos:
street;
city;
state;

-   Dentro da classe adicione:

```java
package com.example.hexagonal.domain;

public class Address {

    private String street;

    private String city;

    private String state;

    public Address() {
    }

    public Address(String street, String city, String state) {
        this.street = street;
        this.city = city;
        this.state = state;
    }

    public String getStreet() {
        return street;
    }

    public void setStreet(String street) {
        this.street = street;
    }

    public String getCity() {
        return city;
    }

    public void setCity(String city) {
        this.city = city;
    }

    public String getState() {
        return state;
    }

    public void setState(String state) {
        this.state = state;
    }
}
```

**Passo 2: Criando do teste unitário**

Crie um test para a classe Address:

### ✅ Exemplo de teste unitário para a classe `Address`

Crie este arquivo:

```
src/test/java/com/example/hexagonal/domain/AddressTest.java
```

Com este conteúdo:

```java
package com.example.hexagonal;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertNull;

import org.junit.jupiter.api.Test;

import com.example.hexagonal.domain.Address;

public class AddressTest {
    @Test
    void testDefaultConstructor() {
        Address address = new Address();

        assertNull(address.getStreet());
        assertNull(address.getCity());
        assertNull(address.getState());
    }

    @Test
    void testParameterizedConstructor() {
        Address address = new Address("Rua A", "Campos", "RJ");

        assertEquals("Rua A", address.getStreet());
        assertEquals("Campos", address.getCity());
        assertEquals("RJ", address.getState());
    }

    @Test
    void testSettersAndGetters() {
        Address address = new Address();

        address.setStreet("Av. Brasil");
        address.setCity("Rio de Janeiro");
        address.setState("RJ");

        assertEquals("Av. Brasil", address.getStreet());
        assertEquals("Rio de Janeiro", address.getCity());
        assertEquals("RJ", address.getState());
    }
}

```

No terminal, execute na raiz do projeto:

```bash
mvn test
```

Para rodar somente o teste da classe AddressTest.java com Maven, use o seguinte comando:

```bash
mvn -Dtest=AddressTest test
```

## 🔍 E no seu código, há regras de negócio?

Atualmente, **não** há **regras de negócio implementadas diretamente** na classe `Customer`. O que você tem são apenas:

-   **Construtores**
-   **Atributos com getters e setters**
-   Uma **flag (`isValidCpf`)** que poderia indicar uma regra, mas ela não é validada aqui.

---

## ✅ Como adicionar uma regra de negócio?

Vamos supor que você queira garantir que o CPF seja válido no momento da criação do objeto. Essa lógica deveria estar no domínio. Exemplo:

```java
public Customer(String id, String name, Address address, String cpf) {
    this.id = id;
    this.name = name;
    this.address = address;
    this.setCpf(cpf); // já valida aqui
}

// Validação simples como exemplo
public void setCpf(String cpf) {
    if (!isValidCpf(cpf)) {
        throw new IllegalArgumentException("CPF inválido");
    }
    this.cpf = cpf;
    this.isValidCpf = true;
}

private boolean isValidCpf(String cpf) {
    // lógica de validação real de CPF aqui (mockada por enquanto)
    return cpf != null && cpf.length() == 11;
}
```

Assim, o **domínio assume a responsabilidade de manter a integridade do negócio**.

---

# Procimos passos:

Depois de criar as classes de domínio Customer e Address, o próximo passo na arquitetura hexagonal é começar a camada de aplicação, responsável por orquestrar os casos de uso do sistema.

https://github.com/DaniloArantesSilva/hexagonal-architecture
