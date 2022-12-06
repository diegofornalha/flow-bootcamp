# 
### 🤔 Uma nota sobre sua função de hortelã

A maneira como nossa função de hortelã foi configurada é um pouco incomum. Estamos fazendo duas coisas que muitas pessoas no Flow não fazem:

1. Estamos dando a todos a capacidade de cunhar
2. Estamos passando os metadados da NFT pelo front-end

A maneira mais segura de fazer balas NFT é usando um [Recurso NFTMinter](https://github.com/onflow/flow-nft/blob/master/contracts/ExampleNFT.cdc#L228). Esse recurso oferece às contas a capacidade de cunhar NFTs. Pense nisso como poderes administrativos para um contrato.

No momento, se alguém quisesse cunhar uma DogMoji NFT em nosso contrato, eles poderiam totalmente! Eles só precisariam alterar os argumentos que estão sendo passados para a transação de hortelã. Isso pode ser feito apenas escrevendo uma transação personalizada e executando-a usando a ferramenta Flow CLI lol. Uma maneira melhor de fazer os metadados da NFT é armazenar o hash do IPFS no contrato e incrementar o número do arquivo usando o Cadence. Dessa forma, as pessoas não podem cunhar NFTs com todos os tipos de valores arbitrários por meio do seu contrato.

Eu quero que você explore o [documentos](https://docs.onflow.org/), existem muitos tutoriais e documentação por aí. Você deve ser capaz de implementar essas alterações agora: )

### 🚀 Implante para o mundo ( GTFOL )

A implantação de um aplicativo React ficou tão fácil que não há razão para não fazê-lo neste momento, lol. Além disso, é grátis. Você chegou até aqui, implantar é o passo final. Além disso - seus colegas construtores no buildspace não devem ser privados de suas NFTs!! Por favor, dê-nos a oportunidade de cunhar suas raras criações hehe.

Basicamente:

- Empurre seu último código de front-end até o Github.
- Conecte o Vercel ao seu repo.
- Certifique-se de adicionar suas variáveis .env
- Implantar.
- Feito.

Nota: No Vercel, você precisará adicionar uma variável de ambiente `CI=false`. Isso garantirá que nossa compilação não falhe por causa de avisos.

[https://camo.githubusercontent.com/daa43421b435444beec8a23878d1138c1929e48b97c1a571745bbab0ca3056b0/68747470733a2f2f692e696d6775722e636f6d2f776e3255686a342e706e67](https://camo.githubusercontent.com/daa43421b435444beec8a23878d1138c1929e48b97c1a571745bbab0ca3056b0/68747470733a2f2f692e696d6775722e636f6d2f776e3255686a342e706e67)

### 🥞Carreiras na Web3

Toneladas de pessoas também conseguiram empregos em período integral nas principais empresas da web3 via buildspace. Estou constantemente vendo pessoas pregando suas entrevistas depois de fazer alguns projetos de espaço de construção.

**As pessoas parecem pensar que o web3 só precisa de pessoas que possam codificar contratos inteligentes ou escrever códigos que interfaces com o blockchain. Não é verdade.**

Há muito trabalho a fazer e a maior parte do trabalho nem precisa ser feita com contratos inteligentes, lol. Ser engenheiro na web3 significa apenas que você pega suas habilidades na web2 e as aplica à web3.

Quero repassar rapidamente o que significa "trabalhar na web3" como engenheiro. Você precisa ser um profissional da Flow? Você precisa saber como tudo sobre o blockchain funciona?

Por exemplo, digamos que você é um ótimo engenheiro de front-end. Se você terminou este projeto, tem quase tudo o que precisa para ser um ótimo engenheiro de front-end em uma empresa da web3. Por exemplo, a empresa pode dizer "Ei, — pls vá e construa nosso recurso de exibição NFT" — e você já teria uma idéia sólida de como fazer isso: ).

Eu só quero inspirar você a trabalhar na web3 lol. Essa merda é incrível. E seria legal se você desse uma chance; ).

Confira trabalhos no Flow [aqui](https://jobs.flowverse.co/), eles estão contratando para um monte de papéis legais!

### 🤟Seu NFT!

Entraremos em contato com sua NFT dentro de uma hora e enviaremos um e-mail assim que estiver na sua carteira. Está funcionando em um trabalho cron! Se você não receber o email dentro de 24 horas, pls pls pls envie uma mensagem para #feedback e marque @ alec # 8853.

Clique no botão na parte inferior desta página e envie seu link final. Caso contrário, nosso sistema não o marcará como "completo".

### 🏝 SEU FLOR

Agora que você é um desenvolvedor de contrato inteligente de fluxo adequado, enviaremos um ✨*exclusivo*✨token chamado FLOAT no seu email.

Incluirá um código especial que você precisará reivindicar. Estes não são transferíveis, portanto, certifique-se de reivindicá-lo com sua carteira principal!

### 🌈 Antes de sair

Vá para #showcase em Discord e envie-nos um link para o seu produto final com o qual podemos mexer: ).

Além disso, você deve twittar totalmente seu projeto final e mostrar ao mundo sua criação épica! O que você fez não foi fácil de forma alguma. Talvez até faça um pequeno vídeo mostrando seu projeto e anexe-o ao tweet. Faça seu tweet parecer bonito e mostre-se!!

E se você quiser, marque @web3dev_: ). Isso nos dá muita motivação sempre que vemos pessoas enviarem seus projetos. Além disso, você pode inspirar outra pessoa a entrar no Flow.

Dê-nos que a dopamina atingiu pls.

Por fim, o que também seria incrível é se você nos dissesse em #feedback como gostou deste projeto e da estrutura do projeto. O que você mais amava no espaço de construção? O que foi péssimo? O que gostaria que mudássemos para projetos futuros? Seu feedback seria incrível!

[Veja yah ao redor!!!](https://twitter.com/AlmostEfficient)

### ♥ Agradecimentos

Apesar do que possa parecer, eu fiz **não** bombeie todo esse conteúdo sozinho. eu tinha **muito** de ajuda para tornar este projeto tão incrível quanto era.

Gritos para nossos ATs INCRÍVEIS ( você definitivamente deve segui-los no Twitter ):

- [Rohit](https://twitter.com/rohithandique_)
    
    - Contrato, front-end, modelo e muito mais
    
- [Matt](https://twitter.com/TopShotTurtles)
    
    - Ideias, partes do guia, Vibrações de fluxo maxi
    
- [Sean](https://twitter.com/helloitsme_sl)
    
    - Estilo frontal
    

Um enorme agradecimento a toda a equipe de Flow Devrel e educação por todos os seus comentários: Andrea, Alex, Srinjoy, Kim, Tyllen + todos que eu esqueci.

Por último, mas def. não menos importante, o ponto 1 para aprender o desenvolvimento de cadências ( buildspace é o número 0 ok shh ): Jacob Tucker e o DAO da Cidade Esmeralda.