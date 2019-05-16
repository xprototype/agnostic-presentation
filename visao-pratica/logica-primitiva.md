# Lógica primitiva

{% hint style="info" %}
Os exemplos são meramente ilustrativos, e tentam reproduzir o que nossa experiência em contato com outros desenvolvedores tem demonstrado.
{% endhint %}

### A abordagem tradicional

Imagine que sua aplicação deve alternar a visibilidade do campo de _CPF_ e _CNPJ_ com base no campo _Tipo de Pessoa_ que pode ser _Pessoa Física_ ou _Pessoa Jurídica_. Em uma abordagem tradicional usando tecnologia web teríamos algo parecido com os trechos à seguir.

No HTML:

{% code-tabs %}
{% code-tabs-item title="index.html" %}
```markup
<div>
  <div>Tipo de Pessoa</div>
  <label>
    <small>Pessoa Física</small>
    <input
      name="personType"
      type="radio"
      value="pessoa-fisica"
      onchange="changePersonType('pessoa-fisica')"
    >
  </label>
  <label>
    <small>Pessoa Jurídica</small>
    <input
      name="personType"
      type="radio"
      value="pessoa-juridica"
      onchange="changePersonType('pessoa-juridica')"
    >
  </label>
</div>
<div>
  <div>Documento</div>
  <label id="CPF">
    <small>CPF</small> <input name="CPF">
  </label>
  <label id="CNPJ">
    <small>CNPJ</small> <input name="CNPJ">
  </label>
</div>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Já no Javascript teríamos algo como:

{% code-tabs %}
{% code-tabs-item title="script.js" %}
```javascript
window.onload = function() {
  const cpf = document.querySelector('#CPF')
  const cnpj = document.querySelector('#CNPJ')

  cpf.style.display = 'none'
  cnpj.style.display = 'none'

  function changePersonType (personType) {
    cpf.style.display = 'none'
    cnpj.style.display = 'none'

    if (personType === 'pessoa-fisica') {
      cpf.style.display = ''
    }
    if (personType === 'pessoa-juridica') {
      cnpj.style.display = ''
    }
  }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Para ter um resultado semelhante à:

![Exemplo dispon&#xED;vel em https://jsfiddle.net/wilcorrea/oaf3gedr](../.gitbook/assets/image%20%283%29.png)

Como podemos ver acima, há alguma separação entre a apresentação \(o HTML\) e os comportamentos \(o Javascript\) na abordagem tradicional quando é feita dessa forma. Seria possível reusar o arquivo `script.js` desde que o novo `index.html` se comprometesse à manter uma estrutura parecida com a desta primeira versão.

### Como os componentes Vue têm sido construídos

Ao migrar o exemplo anterior para componentes não é incomum termos algo parecido com o que será mostrado à seguir.

{% code-tabs %}
{% code-tabs-item title="MyForm.vue" %}
```markup
<template>
  <div class="MyForm">
    <div>
      <div>Tipo de Pessoa</div>
      <label>
        <small>Pessoa Física</small>
        <input
          v-model="personType"
          type="radio"
          value="pessoa-fisica"
          @change="changeSelected('pessoa-fisica')"
        >
      </label>
      <label>
        <small>Pessoa Jurídica</small>
        <input
          v-model="personType"
          type="radio"
          value="pessoa-juridica"
          @change="changeSelected('pessoa-juridica')"
        >
      </label>
    </div>
    <div>
      <div>Documento</div>
      <label v-show="selected === 'pessoa-fisica'">
        <small>CPF</small> <input v-model="CPF">
      </label>
      <label v-show="selected === 'pessoa-juridica'">
        <small>CNPJ</small> <input v-model="CNPJ">
      </label>
    </div>
  </div>
</template>

<script>
export default {
  data: () => ({
  	selected: '',
	personType: '',
    CPF: '',
    CNPJ: ''
  }),
  methods: {
    changeSelected (selected) {
      this.selected = selected
    }
  }
}
</script>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Exemplo disponível [aqui](https://jsfiddle.net/wilcorrea/eupyzak2).

Esta "conversão" tem um inconveniente, o componente passa a ser responsável pela lógica primitiva e pela regra de negócio de definir qual campo será visível.

