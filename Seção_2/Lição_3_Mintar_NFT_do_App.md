# 

Hora de fazer a parte mais mágica do nosso aplicativo - o botão de hortelã!

É aqui que você percebe o quão conveniente é a arquitetura Flow. Lembra das suas transações e scripts? Você também pode usá-los no front-end! Incluí transações e scripts comuns dentro da pasta Cadence. Por enquanto, precisaremos apenas de dois deles, um dos quais você já usou antes 🤯

🚨**AVISO DE ATUALIZAÇÃO**🚨 Você precisará acessar os arquivos de script e atualizar o endereço da conta + nome do contrato em todos os lugares! Deixei o nome como "BottomShot", caso você fosse preguiçoso e não mudasse o nome do seu contrato. Se você não alterar o endereço ou o nome, sua função de hortelã provavelmente só cometerá um erro lol

Volte para o seu `App.js` e vamos começar a construir um `mint` função.

Comece importando o `mintNFT` transações que escrevemos anteriormente e um ajudante `getTotalSupply` script que incluí:

`#App.js
import { mintNFT } from "./cadence/transactions/mintNFT_tx";
import { getTotalSupply } from "./cadence/scripts/getTotalSupply_script";`

Logo após as funções de autenticação, adicione esta função de menta:

  `const mint = async() => {

    let _totalSupply;
    try {
      _totalSupply = await fcl.query({
        cadence: `${getTotalSupply}`
      })
    } catch(err) {console.log(err)}
    
    const _id = parseInt(_totalSupply) + 1;
    
    try {
      const transactionId = await fcl.mutate({
        cadence: `${mintNFT}`,
        args: (arg, t) => [
          arg(user.addr, types.Address), //address to which the NFT should be minted
          arg("CatMoji # "+_id.toString(), types.String), // Name
          arg("Cat emojis on the blockchain", types.String), // Description
          arg("ipfs://bafybeigmeykxsur4ya2p3nw6r7hz2kp3r2clhvzwiqaashhz5efhewkkgu/"+_id+".png", types.String),
        ],
        proposer: fcl.currentUser,
        payer: fcl.currentUser,
        limit: 99
      })
      console.log("Minting NFT now with transaction ID", transactionId);
      const transaction = await fcl.tx(transactionId).onceSealed();
      console.log("Testnet explorer link:", `https://testnet.flowscan.org/transaction/${transactionId}`);
      console.log(transaction);
      alert("NFT minted successfully!")
    } catch (error) {
      console.log(error);
      alert("Error minting NFT, please check the console for error details!")
    }
  }`

Isso é muito menos assustador do que parece. Vamos dividir passo a passo!

    `let _totalSupply;
    try {
      _totalSupply = await fcl.query({
        cadence: `${getTotalSupply}`
      })
    } catch(err) {console.log(err)}
    
    const _id = parseInt(_totalSupply) + 1;`

A primeira coisa que fazemos é usar o script getTotalSupply para buscar o número total de NFTs cunhadas. Usamos esse número + 1 como o ID da próxima NFT. A nova coisa aqui é a `fcl.query` chamada de função. FCL, vamos executar scripts para buscar *qualquer* dados que queremos da blockchain. Isso é super útil, pois você pode usar apenas scripts que outras pessoas escreveram para buscar coisas que você precisa! Passamos um arquivo de cadência inteiro aqui. Sinta-se livre para conferir `/cadence/scripts/getTotalSupply_script.js` para ver como é.

Logo depois, temos a carne da função, a `mint` transação:

`const transactionId = await fcl.mutate({
  cadence: `${mintNFT}`,
  args: (arg, t) => [
    arg(user.addr, types.Address), //address to which the NFT should be minted (connected wallet)
    arg("CatMoji # "+_id.toString(), types.String), // Name
    arg("Cat emojis on the blockchain", types.String), // Description
    arg("ipfs://bafybeigmeykxsur4ya2p3nw6r7hz2kp3r2clhvzwiqaashhz5efhewkkgu/"+_id+".png", types.String),
  ],
  proposer: fcl.currentUser,
  payer: fcl.currentUser,
  limit: 99
})`

Parte disso pode não fazer sentido para você agora, e tudo bem. Deixe-me dar uma explicação rápida do que está aqui em cima. Já usamos essa transação antes, apenas em um lugar diferente!

Este bloco cria um `mutate` solicitação usando FCL. "Mutar" significa mudar, isso é apenas uma solicitação de gravação! Temos 5 argumentos sendo passados aqui:

- `cadence`
    
    : esta é a transação de hortelã que escrevemos para cunhar uma NFT localmente. Diz ao blockchain o que mudar.
    
- `args_id`
    
    : estes são apenas os argumentos para a transação. É aqui que passamos detalhes como o nome da NFT e o link da mídia. Estou usando o
    
    campo para percorrer as NFTs carregadas!
    
- `proposer`
    
    : a conta que envia a transação
    
- `payer`
    
    : a conta que paga as taxas de gás ( taxas de computação ) para esta transação
    
- `limit`
    
    : o limite de computação para esta transação.
    

Lembre-se, a biblioteca que importamos chamada `@onflow/types`? Aqui, usamos para informar à FCL qual é o tipo de dados de fluxo esperado, para que ele possa fazer a conversão ( de string para endereço, por exemplo ). A sintaxe para o `args` valor é estranho. Para que nossa transação de cadência funcione - ela requer um formato muito específico. Convertemos nossos tipos de JS dentro do valor real para `args` formatar nossos dados de uma maneira em que o Flow não fique bravo conosco: )

`arg` em `(arg, t)` é uma função e recebe duas variáveis - a primeira é o valor do argumento que precisa ser passado na transação de fluxo e a segunda é o tipo de dados.

Você pode ler mais sobre mutação [aqui](https://docs.onflow.org/fcl/reference/api/#mutate), o `arg` valor [aqui](https://docs.onflow.org/fcl/reference/api/#argumentfunction) e assinantes de transações [aqui](https://docs.onflow.org/concepts/transaction-signing).

Isso é tudo o que há para isso!

`console.log("Minting NFT now with transaction ID", transactionId);
const transaction = await fcl.tx(transactionId).onceSealed();
console.log("Testnet explorer link:", `https://testnet.flowscan.org/transaction/${transactionId}`);
console.log(transaction);
alert("NFT minted successfully!")`

No final, estamos apenas registrando o explorador da rede de testes e o objeto da transação após o fechamento da cadeia, garantindo que a cunhagem tenha sido bem-sucedida!

Faça uma função de renderização para este novo botão:

  `const RenderMintButton = () => {
    return (
      <div>
        <button className="cta-button button-glow" onClick={() => mint()}>
          Mint
        </button>
      </div>
    );
  }`

E a última peça, atualize seu JSX para usar o botão de hortelã:

`{user && user.addr ? <RenderMintButton /> : <RenderLogin />}`

Experimente e verifique seu console. eu vou `await` o resultado lol.

Você deve ver várias impressões no console do navegador, incluindo um link para o scanner blockchain do Flow testnet chamado Flowscan. Clique neste link!

[https://camo.githubusercontent.com/ec80e573ff7bcfdcb1751e58d86382a4b1d12eece313b010a41ebc2b4fa838bc/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f4279434946666671352e706e67](https://camo.githubusercontent.com/ec80e573ff7bcfdcb1751e58d86382a4b1d12eece313b010a41ebc2b4fa838bc/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f4279434946666671352e706e67)

Você pode ver a NFT sendo transferida para sua conta na rede real!

[https://camo.githubusercontent.com/e80ab91803dea0fbe72ae2d2fa91ce428496a30c7464981a682e0b8adf249114/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f4279594f74522d35392e706e67](https://camo.githubusercontent.com/e80ab91803dea0fbe72ae2d2fa91ce428496a30c7464981a682e0b8adf249114/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f4279594f74522d35392e706e67)

🎉 **PARABÉNS!** 🎉Você acabou de cunhar uma NFT na blockchain Flow!!!

Isso é um grande negócio. Você escreveu o contrato. Você o implantou. Você criou o aplicativo React. Você falou com o seu contrato. Você fez tudo isso. Chame a si mesmo de um desenvolvedor de pilha completa do jeito que você está empilhando essas habilidades!

### 🚨Relatório de andamento 🚨

Marco de Huuuuuuuuuuuuuge. Mostre! Poste o link da transação de cunhagem no Flowscan em #progress.