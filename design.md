# O Design

No [Skeleton Quasar](https://github.com/xprototype/skeleton-quasar) foi adotado um design que fornece modelos de abstrações que podem ser usadas para desacoplar seu código da apresentação \(frameworks CSS e Javascript\) que será utilizada para a apresentação. O projeto como um todo possui opções de uso para roteadores, organização de componentes e modularização na base de código deste projeto, mas elas farão sentido com o contexto que será apresentado.

Buscando ter uma maturidade na implementação o modelo utilizado está sendo batizado de **Agnostic Presentation Design** \(_APD_\) e veremos mais detalhes sobre ele à seguir.

## **Agnostic Presentation Design**

Agnostic Presentation Design \(Design de Apresentação Agnóstica\) é o nome da abordagem que visa criar estruturas de dados para criar as apresentações independente das ferramentas utilizadas para apresentação. Abaixo você pode ver um modelo de como esse padrão funciona.

![Figura 1: modelo b&#xE1;sico de apresenta&#xE7;&#xE3;o](.gitbook/assets/image%20%281%29.png)

O modelo acima mostra como são separadas as responsabilidades dentro do ciclo de vida da apresentação. A lógica de negócio e os serviços são criados fora da camada de apresentação e são mapeados por componentes e rotinas capazes de interpretar e lidar com a notação apresentada.

### Como isso funciona

O Agnostic Presentation Design irá guiá-lo para não usar a lógica de negócios dentro de seus componentes. O mantra é manter a **estratégia de apresentação** apenas como uma camada de visualização e não permitir que lógicas de negócio sejam aplicadas nessa camada.

Ao tentar fazer isso é comum ter problemas com a relação entre o local onde eventos e outras rotinas são declaradas e onde são realizadas de fato. O APD busca minimizar a lacuna criada por essa estratégia com o gerenciamento do escopo. Assim, você declara imperativamente suas propriedades e seus comportamentos em uma estrutura que representa o esboço do componente em questão e essa estrutura será associada ao componente que será renderizado da maneira mais conveniente.

Você pode reusar sua rotina em variados cenários diferentes. Você pode mudar sua camada de apresentação inteira tendo como única premissa manter os contratos e comportamentos da anterior. Pode usar outro framework diferente do Quasar e pode usar outras ferramentas de renderização como React e outras com um impacto reduzido sobre o projeto.

### O esboço

O esboço \(_Schema_\) é um recurso que agrupa as propriedades do domínio da entidade e especifica seu comportamento. O esboço principal \(classe, módulo ou qualquer outra estrutura de dados escolhida\) será capaz de fornecer um conjunto mínimo de recursos para construir o objetivo principal de sua natureza. Deve ser extensível e fornecer gateways para ser especializado em contextos específicos.

Os comportamentos da estrutura do esboço são projetados para serem executados em outros escopos. Portanto, a base do projeto deve ser capaz de manter os recursos fornecidos pelo esboço disponíveis nos comportamentos criados para ele.

![Figura 2: diagrama de vis&#xE3;o geral da apresenta&#xE7;&#xE3;o usando APD](.gitbook/assets/image%20%282%29.png)

Como você pode ver na imagem acima, para dizer o que um projeto implementa _Agnostic Presentation Design_ irá requerer a existência de uma camada dedicada para fornecer os recursos definidos no esboço para a camada de renderização da tela. Essa camada intermediária pode variar de framework de componentes ou de framework de renderização. Um projeto APD pode ter sua lógica de negócio sendo usada em Vue ou React sem muitos problemas, já que haverá uma gateway e uma cadeia de componentes preparados para isso.

### Exemplos práticos

O primeiro passo para exemplificar a estratégia de APD será criar um arquivo para representar o _**Service**_. Vamos consumir a API pública da saga _Star Wars_ em nosso exemplo.

```javascript
const serviceStarWars = () => {
    return fetch('https://swapi.co/api/people/')
      .then(response => response.json()
      .then((data) => data.results))
}
```

Note que é apenas uma função que consulta o endpoint usando [fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) e extrai da resposta um os resultados.

O próximo exemplo será uma estrutura para representar a estrutura que a [API](https://swapi.co/documentation) responde.

```javascript
const businessLogicStarWars = () => {
  return {
    homeworld: {
      label: 'Home World',
      type: String,
      format (value, row) {
          return `<a href="${value}" target="_blank">${value}</a>`
      }
    },
    name: {
      label: 'Name',
      type: String
    },
    birth_year: {
      label: 'Birth Year',
      type: Date
    }
  }
}
```

Esta é uma função bem simples para criar um objeto para mapear as propriedades do nosso serviço e corresponde à _**Business Logic**_ apresentada na _Figura 1_ no início da página

Para representar um _**Controller Component**_ teremos o seguinte trecho.

```javascript
const tableStarWars = () => ({
  template: '<app-table v-bind="attrs"></app-table>',
  data: () => ({
    attrs: {
      columns: businessLogicStarWars(),
      rows: []
    }
  }),
  methods: {
      receive (rows) {
      this.setRows(rows)
    },
    setRows (rows) {
      this.attrs.rows = rows
    }
  },
  mounted () {
      serviceStarWars().then(this.receive)
  }
})
```

A principal característica desse componente é que provavelmente será mapeado à uma rota da aplicação e não terá nada além de um template que carregará um único componente \(_**Container Component**_\) e alguma relação com o _**Service**_ e/ou _**Business Logic**_.

Um _**Container Component**_ é um componente agnóstico. Ele provavelmente será registrado globalmente e terá uma estrutura altamente flexível.

```markup
<template>
  <table>
    <tr>
      <th
        v-for="(column, key) in columns"
        :key="key"
      >
        {{ column.label }}
      </th>
    </tr>
    <tr
      v-for="(row, index) in rows"
      :key="index"
    >
      <td
        v-for="(column, key) in columns"
        :key="key"
      >
        <div
          v-if="column.format"
          v-html="column.format(row[key], row)"
        />
        <div v-else>
          {{ row[key] }}
        </div>
      </td>
    </tr>
  </table>
</template>

<script>
  Vue.component('app-table', {
    template: '#app-table',
    props: {
        columns: {
        type: Object
      },
        rows: {
        type: Array
      }
    }
  })
</script>
```

Os exemplos passados são, por razões óbvias, o mais simples possível, porém esse componente poderia ter vários desdobramentos, interagir com o service do controller através de eventos e muitas outras coisas.

```javascript
new Vue({
  el: "#app",
  components: {
      TableStarWars: tableStarWars()
  }
})
```

Para ver tudo isso funcionando podemos fazer isso e montar nosso componente controlador. O exemplo funcional está disponível nesse [link aqui](https://jsfiddle.net/wilcorrea/rja5pe7c).

{% api-method method="get" host="" path="" %}
{% api-method-summary %}
dadadad
{% endapi-method-summary %}

{% api-method-description %}

{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="" type="string" required=false %}

{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```

```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

