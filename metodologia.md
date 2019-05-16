# Grupos de Controle

## Os grupos de controle do Agnostic Prototype Design

A base do Agnostic Prototype Design é organizar a aplicação em 3 grandes grupos de controle: Primitive Logic, Circumstantial Logic e Business Logic.

![](.gitbook/assets/image%20%284%29.png)

### Lógica primitiva

Quando escrevemos um componente ele possui uma lógica primitiva interna que é inerente ao seu papel. Um input de texto recebe e devolve valores, um toggle tem aquela animação para mapear um estado booleano, exibição de rótulos, impressão de mensagens, representação de estados de erro e coisas do gênero.

Essa parte primitiva do componente faz parte do escopo dele e não deveria se misturar com outras responsabilidades dos domínios que a aplicação gerencia. Este grupo de recursos é bem operacional, agnóstico e poderia comumente ser separado do resto da aplicação e ser usado em projetos variados.

### Regras de negócio

Este grupo é responsável por toda a lógica de negócio que o sistema gerencia.  Os nomes dos campos, os serviços executados para gerenciar os dados. Os domínios que a aplicação gerencia ficam todos aqui.  Um exemplo clássico é o de cadastro de pessoa. Quando temos que determinar que o cadastro de pessoa jurídica deve usar CNPJ e o de pessoa física isso é inerente da regra de negócio e isso deve ser mapeado nos componentes que usaremos.

Enquanto pensamos em APD temos que pensar que os componentes representam esses estados o que é diferente de ser esse estado. Sendo assim a lógica de negócio não fica incorporado dentro dos arquivos de componentes, ela é isolada em documentos da linguagem base da aplicação, indiferente da sua escolha de apresentação.

### Configurações circunstancias

Em um dado momento sua aplicação terá que imprimir os componentes e combinar os comportamentos primitivos dos componentes base com sua lógica de negócio. Nesse ponto será possível ver surgir um terceiro grupo de controle de estados e comportamentos. Esse grupo é o mais sutil de se perceber e pode ser que você já o tenha visto sem se dar conta.

Um componente de `<input>` geralmente tem um marcação base. Para otimizar a construção do componente costumamos colocar o próprio `<label>` dentro dessa mesma composição. Avançando com a exemplificação, quando estamos criando um componente para representar um estado do nosso negócio também temos que ver que há um rótulo à ser associado à ele que nem é responsabilidade do componente nem da lógica de negócio. Antes que alguém possa concluir que o rótulo desse estado está intrinsecamente relacionado à regra de negócio eu lembro que podemos ter internacionalização e que isso pode variar de acordo com outros fatores, como regionalismos.

Para resolver esse grupo de controle podemos construir componentes controladores que mapeiem isso ou criar documentos de configuração contendo as variações necessárias para configurar os componentes base, ou combinar os dois.

### 

