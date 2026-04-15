# SpringBoot_study
Estudo 

#Mapeamento @ManyToOne OU Mapeamento de Muitos para Um (N:1)

No modelo relacional, uma associação muitos-para-um (N:1) é quando se têm várias tuplas [] de uma tabela 'referenciando' uma tupla [] de uma tabela qualquer.
<img width="470" height="161" alt="image" src="https://github.com/user-attachments/assets/deb9ab93-4373-4feb-b04a-68360d112759" />

Nesse exemplo acima, lê-se assim:
Um Funcionário têm um Departamento, mas o Departamento têm muitos Funcionários.
A tabela Funcionários têm o relacionamento muitos-para-um com a tabela Departamento.

##Em JPA (JakartaPersistence): 
exemplo

domínios das entidades - Departamento (tabela = departamento) e Funcionario (tabela = funcionario)

Departamento - código 

@Entity //representa que a classe é uma entidade
@Table(name = "departamento") // nomeia a tabela no banco de dados
public class Departamento { 
 
  @Id // marca todo o campo como uma PK da entidade
  @GeneratedValue(strategy = GenerationType.IDENTITY) // gera automaticamente o ID e IDENTITY delega a geração para o banco de dados
  private Integer codigo;
 
  private String nome;
 
  // getters e setters omitidos
 
}

Funcionario - código

@Entity
@Table(name = "funcionario")
public class Funcionario {
 
  @Id
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  private Integer codigo;
 
  private String nome;
 
  // getters e setters omitidos
 
}

## Aonde entra o mapeamento @ManyToNe
1° - identificar o dono da relação = tabela funcionario
Objetivo - mapear o relacionamento que o funcionario tem com o departamento
2° - a tabela funcionario têm uma FK - foreign key (departamento_codigo)
3° - adiciona-se a 'anotação' como um novo atributo, mapeando o código

Funcionario - código

@Entity
@Table(name = "funcionario")
public class Funcionario {
 
  @Id
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  private Integer codigo;
 
  private String nome;
 
  @ManyToOne   // adicionamos aqui o mapeamento muitos-para-um
  private Departamento departamento;
 
  // getters e setters omitidos
 
}

## Ou seja, a anotação @ManyToOne  indica que há um relacioamento de Muitos-para-Um entre Funcionarios e Departamento

## Para testar o funcionamento do mapeamento é preciso:
1° - criar uma classe java com o método main, para obter uma instância de EntityManager

public class InclusaoMain {
 
  public static void main(String[] string) {
    EntityManagerFactory entityManagerFactory = Persistence
        .createEntityManagerFactory("FolhaPagamentoPU");
    EntityManager entityManager = entityManagerFactory.createEntityManager();
 
    // TODO vamos persistir e consultar entidades aqui
 
    entityManager.close();
    entityManagerFactory.close();
  }
 
}

2° - agora instancia-se e persiste a as instancias das duas entidades

// instanciamos um departamento
Departamento departamento1 = new Departamento(); // cria um novo objeto da classe na memória
departamento1.setNome("Vendas");
 
// instanciamos dois funcionários e atribuímos o departamento
Funcionario funcionario1 = new Funcionario();
funcionario1.setNome("João das Couves");
funcionario1.setDepartamento(departamento1);
 
Funcionario funcionario2 = new Funcionario();
funcionario2.setNome("Maria Abadia");
funcionario2.setDepartamento(departamento1);
 
// iniciamos uma 'transação' e persistimos os objetos
entityManager.getTransaction().begin();
 
entityManager.persist(departamento1);
entityManager.persist(funcionario1);
entityManager.persist(funcionario2); // persist significa salvar um novo objeto no banco de dados
 
entityManager.getTransaction().commit(); // commit significa confirmar as operações no banco de dados

## Conferir funcionamento, consultando os funcionários e acessando a associação com o departamento através do método getter.
public class ConsultaMain {
 
  public static void main(String[] string) {
    EntityManagerFactory entityManagerFactory = Persistence
        .createEntityManagerFactory("FolhaPagamentoPU");
    EntityManager entityManager = entityManagerFactory.createEntityManager();
 
    // busca o funcionário de código 1
    Funcionario funcionario1 = entityManager.find(Funcionario.class, 1);
 
    // busca o funcionário de código 2
    Funcionario funcionario2 = entityManager.find(Funcionario.class, 2);
 
    // obtém o nome do departamento associado aos funcionários
    System.out.printf("Funcionário %s está no departamento %s%n", 
        funcionario1.getNome(), funcionario1.getDepartamento().getNome());
     
    System.out.printf("Funcionário %s está no departamento %s%n", 
        funcionario2.getNome(), funcionario2.getDepartamento().getNome());
 
    entityManager.close();
    entityManagerFactory.close();
  }
 
}


