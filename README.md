# Introdução

Há algum tempo temos amadurecido a maneira de trabalhar com a interface das aplicações. Neste documento apresentamos uma estratégia de implementação que visa maximizar a escrita na linguagem nativa \(no nosso caso o Javascript\) e utilizar as ferramentas de apresentação apenas para o que elas foram criadas.

Para poder demonstrar esta abordagem criamos o projeto [Skeleton Quasar](https://github.com/xprototype/skeleton-quasar) que será discutido mais pra frente. Um projeto destinado à mostrar como criar estruturas inteligentes, visando o ganho de produtividade com a forma como é escrito e reduzir o tempo gasto com manutenção de código. Seu objetivo é manter um custo x benefício vantajoso entre acoplamento e coesão, representando as telas através de estruturas de dados agnósticas à apresentação. São mostradas formas de uso do roteador e composição de componentes que mesclam paradigmas da orientação a objetos clássica e programação funcional para gerir os recursos da lógica de negócio e aplicar os protótipos à interface da aplicação.

