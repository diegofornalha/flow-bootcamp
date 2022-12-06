# Flow é uma nova rede blockchain construída desde o início para o mainstream. Por que criar um novo ecossistema? Porque os existentes não estão prontos para o planeta inteiro.

Pense no seu aplicativo web3 favorito. Pode ser qualquer coisa construída em uma blockchain - um site de negociação da NFT como OpenSea ou MagicEden ou talvez um site de negociação como o UniSwap. Caramba, poderia até ser apenas um livro simples que você fez! Agora imagine sua mãe ou sua avó tentando usá-lo. Eles teriam que baixar uma carteira, configurar uma "frase secreta" e depois *comprar* cripto apenas para experimentar!

O flow muda o jogo. Tudo no flow é feito para escala. Sua avó pode começar a usar *qualquer* Flua o aplicativo em 10 segundos com apenas o endereço de e-mail dela. Nenhuma configuração de carteira é necessária. As taxas de transação são subsidiadas ( gratuitamente para iniciantes ), mesmo na rede principal.

![https://bafkreibuwlozakdcha3hoksn3r3ou5gexr2xquxteh2a4zgqjgjlnxluk4.ipfs.nftstorage.link/](https://bafkreibuwlozakdcha3hoksn3r3ou5gexr2xquxteh2a4zgqjgjlnxluk4.ipfs.nftstorage.link/)

Vamos passar muito tempo mergulhando no Flow neste projeto e você terá tempo de sobra para aprender sobre o que diabos é o Flow **realmente construindo** nele.

Não se preocupe, chegaremos a um monte de coisas como como Flow tem baixas taxas de gás, como é realmente rápido, etc.

eu **não** quer que passemos muito tempo em teoria aqui.

A última coisa que quero que você faça é descer pela toca do coelho blockchain e começar a assistir toneladas de vídeos aleatórios do YT ou postagens da Wikipedia. Eu acho que fazer essas coisas é bom, *mas termine este projeto primeiro*. Então desça a toca do coelho!

Prometo que toda a sua pesquisa fará muito mais sentido quando você realmente enviar este projeto.

Eu acho que é valioso ter uma compreensão básica de alguns dos conceitos e obter uma imagem de alto nível de como as coisas estão funcionando no Flow! Então, vamos fazer isso: ).

### Contratos inteligentes

Os contratos inteligentes de flow são muito parecidos com outras cadeias de bloqueio. Aqui está uma recapitulação das partes que você **precisa** para saber.

[Fonte da imagem](https://docs.onflow.org/dapp-development/flow-dapp-anatomy/)

![https://camo.githubusercontent.com/798a13ccda50911f0704486acfebfc3ee24c5e62b433517bb549b85db96c0949/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f537954304f773375632e706e67](https://camo.githubusercontent.com/798a13ccda50911f0704486acfebfc3ee24c5e62b433517bb549b85db96c0949/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f537954304f773375632e706e67)

A maioria dos seus usuários começa no cliente - este pode ser um site simples ou um aplicativo móvel. Eles conectam a carteira ao seu cliente e interagem com a blockchain. Sua conta vive no blockchain e se comunica com contratos inteligentes para fazer coisas como comprar tokens ou NFTs.

A única coisa "nova" aqui é a [Flow Client Library](https://www.npmjs.com/package/@onflow/fcl). Este é apenas um pacote que facilita muito a comunicação com carteiras e contratos inteligentes. É como Ethers.js ou Web3js, exceto que é muito mais fácil de usar! O FCL faz com que você não precise se preocupar com a carteira em que o usuário está - você escreverá o código de comunicação da carteira uma vez e funcionará para *todo* Carteira de flow!

Se você não conhece contratos inteligentes, pense neles como máquinas de venda automática que só aceitam cheques. Não há blockchain nesta foto, então imagine lol

![https://camo.githubusercontent.com/e8ea615cf9b68071f9db74bb86c27ac3d869da63a3c751d29c6e7ffc1a7ad4d4/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f7231696435643474632e706e67](https://camo.githubusercontent.com/e8ea615cf9b68071f9db74bb86c27ac3d869da63a3c751d29c6e7ffc1a7ad4d4/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f7231696435643474632e706e67)

O atendente ( reage ao aplicativo ) pergunta o que você deseja e fornece um cheque ( transação ), você assina com sua caneta ( aplicativo de carteira como Blocto, Lilico ou Metamask ) e você o envia à máquina de venda automática ( contrato inteligente ). A máquina de venda automática é **em** o blockchain e ele faz o que sua transação indica se a transação seguir as regras do blockchain. Em nosso exemplo, a transação pode estar tirando algumas fichas de você e dando uma coca-cola diet!

### 🏦Contas

O Flow também usa um novo tipo de estrutura de dados chamada "Contas" para armazenar as coisas. Você pode pensar em uma conta Flow, assim como uma conta de email ou uma conta do Dropbox / Google Drive / Baidu Cloud. Eles têm credenciais de acesso ( login / senha ) e armazenam coisas dentro deles como arquivos e texto.

Aqui está a estrutura de uma conta Flow: [Fonte da imagem](https://youtu.be/pRz7EzrWchs?t=37)

![https://camo.githubusercontent.com/b19585e3f2b0d845bfffc77be1a17feb16d5f7b95e8da615178dc371a4e02109/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f726b6c4d77616175352e706e67](https://camo.githubusercontent.com/b19585e3f2b0d845bfffc77be1a17feb16d5f7b95e8da615178dc371a4e02109/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f726b6c4d77616175352e706e67)

As contas de flow têm duas partes principais:

1. Área de armazenamento do contrato: é aqui que o código do seu contrato inteligente vive. Você pode armazenar quantos contratos inteligentes quiser. Isso significa que você também pode [atualizar contratos inteligentes](https://docs.onflow.org/cadence/language/contract-updatability/) **interfaces** facilmente! Esta área também contém o que você pode usar para interagir com ele.
    
3. Sistema de arquivos da conta: as contas de flow têm seu próprio sistema de arquivos! Você pode pensar nisso como pastas na área de trabalho que possuem arquivos dentro delas. Os três caminhos válidos são:
- `/storage/`
    
    : acessível apenas pelo proprietário da conta
    
- `/private/`
    
    : como uma API privada, o proprietário e as contas permitidas têm acesso
    
- `/public/`
    
    : como uma API pública, qualquer pessoa na rede pode acessar isso
    

Se pensarmos no exemplo da máquina de venda automática, o sistema de arquivos será como o espaço de armazenamento **por dentro** a máquina de venda automática.

Ícone de Flaticon

![https://camo.githubusercontent.com/145fcb86f5e0cb814cb048b99b0c78ca3b78ae7dca8cfe8ba2e5555f24e43f83/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f53314c444e754574712e706e67](https://camo.githubusercontent.com/145fcb86f5e0cb814cb048b99b0c78ca3b78ae7dca8cfe8ba2e5555f24e43f83/68747470733a2f2f6861636b6d642e696f2f5f75706c6f6164732f53314c444e754574712e706e67)

Infelizmente, não podemos armazenar Cocas de dieta nesses domínios de armazenamento, apenas objetos de dados. Wtf é um objeto de dados? Qualquer estrutura de dados em um contrato, seja um recurso ou uma sequência simples. "Variável "Olá Mundo? Jogue lá dentro. Um "cofre" que acompanha os saldos dos tokens? Em você vai! Mapeamento de NFTs para proprietários? Sim.

#
### 💚 Mais informações sobre Cadence

O flow vem com uma nova e impressionante linguagem de programação chamada Cadence. A sintaxe foi inspirada em outros idiomas impressionantes, como Swift e Rust, com foco na clareza.

Mas por que? Por que eles tiveram que criar um novo idioma? Solidez / Ferrugem não eram suficientes?

Não. A programação do Blockchain é um espaço completamente novo. Quando o software se move para novos espaços, você precisa de novos paradigmas. As linguagens de programação existentes foram criadas para programar computadores como os conhecíamos - privados e apátridas. As cadeias de blocos são inerentemente diferentes ( distribuídas, estatais e públicas ), de modo que as mesmas primitivas de idioma não se aplicam mais. É como tentar usar um forno de pedra de 1.000 anos para cozinhar ramen / macarrão de microondas de 60 segundos!

Quando os computadores estavam se tornando hardware de commodities, precisávamos **portátil** código. C nasceu. De CPUs de supercomputador a microcontroladores, ele rodava em todo o hardware!

Quando as UIs gráficas estavam se tornando uma coisa nos anos 90, vimos que Java com sua Programação Orientada a Objetos era um ótimo ajuste.

Com o surgimento de aplicativos da web que fizeram scripts do lado do cliente, usamos JavaScript.

E agora, com os requisitos de escassez e acesso do blockchain, temos o Cadence.
#
### 🔒 Programação Orientada a Recursos

O problema com Solidez e WASM ( Ferrugem ) é que os "recursos escassos" são apenas estruturas de dados. Se um contrato inteligente não adicionar guardas e verificações, qualquer pessoa poderá alterar a propriedade de um ativo atualizando uma estrutura de dados como um mapeamento. Você possui NFTs, mas apenas porque o contrato diz isso. **Se o contrato não for bem estabelecido, alguém poderá alterar quem é o proprietário sem a permissão deles.**

Cadence resolve isso com a programação orientada a recursos. Esta não foi uma decisão aleatória, muita [pesquisa](https://www.onflow.org/technical-paper) foi feito para chegar aqui. Eu não quero entrar em teoria profunda aqui, **tudo o que você precisa saber é**:

Os objetos nas cadeias de blocos têm dois requisitos principais:

1. Eles devem ser controlavelmente escassos
2. Seu acesso deve ser controlado e aplicado

Para atender a esses requisitos, a Cadence usa "recursos". Quando fazemos de algo um recurso, ele deve seguir estas regras:

1. **Cada Recurso existe exatamente em um lugar a qualquer momento**
    
Os recursos não podem ser duplicados ou excluídos acidentalmente, por erro de programação ou código malicioso.
    
2. **A propriedade de um recurso é definida por onde é armazenado**

Não há razão central que precise ser consultada para determinar a propriedade.
    
4. **O acesso aos métodos em um recurso é limitado ao proprietário**
  
[Fonte](https://www.onflow.org/post/resources-programming-ownership)

Por exemplo, apenas o proprietário de um CryptoKitty pode iniciar uma operação de criação que levará ao nascimento de um novo Kitty.
    

Essas regras são aplicadas tanto no tempo de compilação *e* em tempo de execução. Portanto, se você escrever um código de cadence incorreto que não verifique se você tem permissão para transferir uma NFT, a transferência **não vai acontecer!** 

O blockchain dirá essencialmente "Ei, você está tentando fazer algo que não tem permissão, saia daqui" e cancele sua transação.

O único dos recursos é que, devido ao forte sistema de tipos estáticos da Cadence, eles só podem existir em um local por vez e não podem ser copiados ou perdidos por causa de um erro de codificação. Em vez de uma abordagem no estilo de livro para registrar a propriedade, os recursos vinculam diretamente a propriedade de um ativo à conta que o possui, economizando os recursos no armazenamento da conta.

Ainda mais frio, como os recursos são apenas tipos de dados, eles podem possuir outro recurso. Então se você é fazendo uma versão na cadeia de Pokemon, seu Pikachu pode **próprio** itens como uma pedra da evolução ou powerups!

Se você está lutando para entender isso, não se preocupe, você o receberá assim que começarmos a escrever código! Por enquanto, você pode pensar em Cadence como um mundo onde você não tem permissão para roubar coisas porque as leis da física o impedem de rir

Leitura suficiente, vamos provar alguns botões em nosso teclado!