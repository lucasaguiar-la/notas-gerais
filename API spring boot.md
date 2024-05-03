### Spring Boot API

Seguindo a arquitetura MVC (*Model* - *View* - *Controller*), esse tutorial irá mostrar como fazer uma API passo a passo com MySQL. Ao final, você poderá consultar um mapa da arquitetura de pastas para te guiar.

>[!NOTE]Obs
>
> No Spring Boot, as seguintes dependências devem ser instaladas:
> - MySQL Driver (*Para conexão com o MySQL*)
> - Spring Web (*Pacote de aplicação web com Tomcat, Servlet etc*)
> - Spring Data JPA (*Para simplificar a conexão com o bando de dados*)
> - Spring Web Dev Tools (*Auto reload do servidor*)

## 1° Passo - Contrução do banco de dados
```
CREATE DATABASE banco_db;
CREATE USER 'usuario_db'@localhost IDENTIFIED BY ´senha´;
GRANT ALL PRIVILEGES ON banco_db.* 'usuario_db'@localhost;
FLUSH PRIVILEGES;
```
## 2° Passo - Configurar a conexão com o banco de dados
Na pasta de *resourses* do projeto, no arquivo **application.properties**, iremos configurar as conexões:

```
spring.datasource.url=jdbc:mysql://localhost:3306/todo_db?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true

spring.datasource.username=usuario_db
spring.datasource.password=senha
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=update
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQLDialect

```

## 3° Passo - Montar a API

### 1 - Modelo
- Package model
  - Aqui é onde colocaremos nossa classe **Entity**

```
package com.exemplo.api.model;

import javax.persistence.*;

@Entity
@Table(name = "nome_tabela")
public class Cliente {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, length = 100)
    private String nome;

    @Column(nullable = false)
    private String email;

    @Column(nullable = false)
    private int idade;

    // getters and setters
    // construtor
}

```
> [!NOTE]Classe Entidade
> 
> A classe Entity é onde definimos a tabela/entidade do projeto. Como é uma classe para mapear, armazenar ou manipular dados pelo sistema, nós o colocamos no pacote **Model**.

<br>

### 2 - Repositório
- Package repository
  - Aqui é onde colocaremos nossa interface **Repository**

```
package com.exemplo.api.repository;

import com.exemplo.api.model.Cliente;
import org.springframework.data.jpa.repository.JpaRepository;

public interface ClienteRepository extends JpaRepository<Cliente, Long> {
}
```

> [!NOTE]Interface repositório
> 
>Ela define um contrato para acessar dados de forma independente da implementação real, promovendo uma estrutura mais modular e flexível. É aqui onde podemos definir métodos para o 'tratamento' dos dados.

<br>

### 3 - Serviço
- Package service
  - Aqui é onde colocaremos nossa interface **Service**

```
package com.exemplo.api.service;

import com.exemplo.api.model.Cliente;

import java.util.List;
import java.util.Optional;

public interface ClienteService {
    List<Cliente> getAllClientes();
    Optional<Cliente> getClienteById(Long id);
    Cliente createOrUpdate(Cliente cliente);
    void Cliente(Long id);
}

```

> [!NOTE]Interface serviço
> 
>A principal finalidade da interface service é promover uma separação clara entre as responsabilidades da camada de apresentação (como a interface do usuário) e a camada de acesso a dados (como os repositórios ou DAOs), centralizando a lógica de negócio em um componente específico.

<br>

### 4 - Serviço de Implementação
- Package service
  - Aqui é onde colocaremos nossa classe **ServiceImpl**

```
package com.exemplo.api.service;

import com.exemplo.api.model.Cliente;
import com.exemplo.api.repository.ClienteRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.Optional;

@Service
public class ClienteServiceImpl implements ClienteService {
    @Autowired
    private ClienteRepository clienteRepository;

    @Override
    public List<Cliente> getAllClientes() {
        return clienteRepository.findAll();
    }

    @Override
    public Optional<Cliente> Cliente(Long id) {
        return clienteRepository.findById(id);
    }

    @Override
    public Cliente createOrUpdate(Cliente cliente) {
        return cliente.save(cliente);
    }

    @Override
    public void deleteCliente(Long id) {
        clienteRepository.deleteById(id);
    }
}

```

> [!NOTE]Implementações de serviço 
> 
>A Service Implementation serve como a implementação concreta de uma interface de serviço, implementando a lógica de negócio específica da aplicação e facilitando a interação entre as camadas do sistema de forma organizada, modular e desacoplada.

<br>

### 5 - Controlador
- Package controller
  - Aqui é onde colocaremos nossa classe **Controller**

```
package com.exemplo.api.controller;

import com.exemplo.api.model.Cliente;
import com.exemplo.api.service.ClienteService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/clientes")
public class ClienteController {
    @Autowired
    private ClienteService clienteService;

    @GetMapping
    public ResponseEntity<List<Cliente>> getAllClientes() {
        List<Cliente> clientes = clienteService.getAllClientes();
        return new ResponseEntity<>(clientes, HttpStatus.OK);
    }

    @PostMapping
    public ResponseEntity<Cliente> createCliente(@RequestBody Cliente cliente) {
        Cliente createdCliente = clienteService.createOrUpdate(cliente);
        return new ResponseEntity<>(createdCliente, HttpStatus.CREATED);
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteCliente(@PathVariable Long id) {
        clienteService.deleteCliente(id);
        return new ResponseEntity<>(HttpStatus.NO_CONTENT);
    }
}


```

> [!NOTE]Controlador
> 
>Tem como principal objetivo receber requisições (geralmente HTTP em aplicações web) do usuário ou de outros sistemas, processar essas requisições, interagir com a camada de serviço (ou lógica de negócio) correspondente e, por fim, retornar uma resposta adequada ao cliente (como uma página web, dados JSON, etc.).

<br>

## 4° Passo - Testando a API

Você pode usar ferramentas como **Postman** ou **cURL** para testar a API:

**POST**<br>*http://localhost:8080/api/clientes* - Cria um novo cliente.

**GET**<br>*http://localhost:8080/api/clientes* - Retorna todos os clientes.

**json**<br>
{
    <br>"nome": "Estudar Spring Boot com MySQL",<br>
    "email": *emailteste@teste.com*,<br>
    "idade": 23<br>
}


# Mapa da arquitetura de pastas (exemplo)
```
todo-api-mysql/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/
│   │   │       └── exemplo/
│   │   │           └── api/
│   │   │               ├── ApiApplication.java
│   │   │               ├── controller/
│   │   │               │   └── ClienteController.java
│   │   │               ├── model/                  <-- Pacote contendo a classe de entidade
│   │   │               │   └── Cliente.java           <-- Classe de entidade (Entity)
│   │   │               ├── repository/
│   │   │               │   └── ClienteRepository.java
│   │   │               └── service/
│   │   │                   ├── ClienteService.java
│   │   │                   └── ClienteServiceImpl.java
│   │   └── resources/
│   │       └── application.properties
│   └── test/
│       └── java/
│           └── com/
│               └── exemplo/
│                   └── api/
│                       └── ApiApplicationTests.java
└── pom.xml

```