

<p align='center'>
<b>YouX Challenge</b>
</p>

<br>

## Frameworks

- 📑 [Vitesse](https://github.com/antfu/vitesse) 

- ⚡️ [Vue 3](https://github.com/vuejs/vue-next), [Vite 2](https://github.com/vitejs/vite)

- 🎨 [Windi CSS](https://github.com/windicss/windicss)

<br>

## Dependencias

- 💻 [json-server](https://github.com/typicode/json-server)

- 💾 [axios](https://github.com/axios/axios)

- 🔒 [crypto-js](https://github.com/brix/crypto-js)

- 📁 [concurrently](https://github.com/open-cli-tools/concurrently)

- 🎭 [maska](https://github.com/beholdr/maska)

<br>

## Anotações

### Desenvolvimento

- O projeto foi iniciado a partir de um template chamado "Vitesse", que engloba Vite, Vue, e diversas outras libs, e em seguida removido algumas features e conteúdos prontos.

- A ideia inicial que tive para o inicio do desenvolvimento desse projeto, foi pensando em armazenar os usuários num json-server, e partir de lá, fazer as checagens.

- O primeiro dia teve como foco o desenvolvimento da interface, utilizando o Windi CSS para a customização, peguei algumas refêrencias na internet, mas no fim, acabei tentando deixar de uma forma com que parecesse oficial da YouX

- Adicionei uma ferramenta chamada Mask para transformar os inputs de CPF e campos de altura, peso, de forma que ficassem limitadas para o usuário

- Em seguida comecei a adicionar funcionalidades visuais, como verificar os campos em branco , checar se a quantidade de caracteres confere à de um CPF real, etc...

- Adicionei o json-server para criar um pseudo banco de dados e deixei pré-desenvolvido o sistema de registros.

- Ao criar o login, me deparei com um problema, não existia uma autenticação real para o json-server... Procurei na internet e achei um integrador, mas não encontrei nada a respeito de como desenvolver utilizando o vue.

- Nesse ponto comecei a ter alguns problemas com o Vitesse, pois havia algumas coisas prontas, como o Pinia, que é um sistema para Stores como o Vuex, mas eu nunca tinha utilizado nada parecido até agora, o servidor não autenticava, e fiquei presa, pensei em recomeçar o projeto, mas já estava muito longe pra isso.

- Pensei em outra estratégia, que era armazenar o usuário no localStorage, o que não é tão seguro, mas acabou resolvendo todos os meus problemas com login, e autenticação de páginas. Depois disso, adicionei o Axios para fazer GET e POST no meu json-server e utilizei o localStorage para fazer a verificação de login do usuário. O mais grave problema dessa situação é que não existe um sistema de segurança pra autenticação do meu json-server, e isso significa que qualquer aplicativo externo com POST conseguiria adicionar um novo usuário com permissão de medicos.

-  Depois que quase tudo estava resolvido, decidi buscar algum método para criptografar o CPF e a Senha, procurei algumas ferramentas, e muitas foram incompatíveis com o Vue 3. Até que encontrei a crypto-js , que conseguiu rodar no meu projeto sem erros, então implementei, um sha256 para o CPF e o mesmo para a senha , porém com a soma entre nome e senha. ( aqui passou uma coisa despercebida, o crypto-js começou a me impedir de buildar o projeto, passei horas procurando alguma solução, não consegui nada, e eu não tinha tempo para trocar a criptografia denovo ).

- Com tudo criado, faltava apenas a parte de mostrar a quantidade de pacientes registrados em cada região, tentei utilizar a API , mas aparentemente ela está obsoleta em vue 3, e sinceramente a escrita do teste não ficou 100% clara pra mim a respeito desse item; existe um link , mas aparentemente está quebrado, e o texto diz algo sobre geometria, vendo isso eu pensei que seria interessante encontrar a geometria de cada estado e criar um mapa do Brasil com cliques que mostrariam os resultados da api a cada clique.

- Por ultimo, eu corrigi os ultimos probleminhas, e começei a organizar o código com os componentes em seus devidos lugares, e tentei comentar tudo o que achei útil.

<br>

### Conclusão

- Por conta do problema com o crypto-js não estou conseguindo buildar, e como o problema surgiu, e eu só percebi no final, achei que não seria tão legal , perder muito tempo me preocupando com isso, pois poderia me atrasar muito e eu não conseguir terminar o projeto. Dado esse problema , eu não consegui hospedar o site para ter alguma noção inicial, porém o servidor iria travar pois ele depende de um "backend" que é o json-server para efetuar o login.

<br>

## Instalação

> Requer Node >=14

### Instalação simples

Você pode instalar clonando esse repositorio como abaixo

```bash
git clone https://github.com/emymonteiro/youx-challenge
cd youx-challenge
pnpm i # Caso não possua pnpm instalado, primeiro utilize: npm install -g pnpm
```


## Como usar

### Development

Use o comando e a pagina estará em http://localhost:4000 | o json-server em http://localhost:3000

```bash
pnpm start
```
