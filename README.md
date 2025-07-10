# Tutorial Arquitetura Hexagonal - CRUD de Usuários - Api - MongoDB (nosql) - Kafka(mensageria)

Aprenda na prática como aplicar a arquitetura hexagonal em microsserviços utilizando Java, Spring Boot, MongoDB e Kafka
Criaremos o CRUD de clientes

# Parte 1: Criação do CREATE de clientes

**Passo 1: Criando a classe Customer na camada Domain**

Começamos o projeto pela classe de domínio.

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

obs.: A camada **domain** deve ser independente de qualquer tecnologia externa: ela não pode conter dependências de frameworks, nem ser acessada diretamente por camadas externas sem passar pelas interfaces (portas) da aplicação. Veja que importamos os getter e setter na mão e não utilizamos a tecnologia lombok do framework Spring.

**Passo 2: Criando a classe Address na camada Domain**

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

https://github.com/DaniloArantesSilva/hexagonal-architecture
