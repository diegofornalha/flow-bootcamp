Nosso contrato está pronto! Vamos dar uma volta em uma rede real: )

🔐Criar nova conta Testnet
A primeira coisa que precisamos fazer é criar uma nova conta de testnet. Há duas partes nisso: usaremos a CLI de fluxo pela primeira vez, executará este comando:

flow keys generate
Você deve obter uma resposta como:

Desktop/Learn-flow/FlowNFTs$ flow keys generate
 If you want to create an account on testnet with the generated keys use this link:
https://testnet-faucet.onflow.org/?key=02febe07cd5cae683e...7b0ff6af0591


 Store private key safely and don't share with anyone!
Private Key      82349cd3f8a8b02d6132ac04deac657f34edb9c0e87b4377af7943c9e4ae20b8
Public Key       02febe07cd5cae683e...7b0ff6af0591
Agradável! Você acabou de criar um par de chaves de fluxo usando a CLI! Clique no link impresso, resolva o captcha e clique em "Criar conta".

🚨 SALVAR SPOT! 🚨 Salve o endereço da conta ( do site ) e as chaves geradas em algum lugar à mão!

Estaremos implantando nossos contratos com eles.

Você pode estar se perguntando por que tivemos que fazer uma "conta" depois que criamos um par de chaves público / privado. Wtf? A "conta" não é feita com as chaves públicas / privadas???

Não. Ao contrário do Bitcoin e do Ethereum, os endereços Flow não são derivados de chaves públicas criptográficas. Contas e chaves são duas coisas diferentes. Depois de ter um par de teclas, você precisa executar uma função na cadeia para criar uma conta usando-a. Foi o que o site fez. Também nos deu 1.000 fichas de fluxo. Woohoo!

Se voltarmos ao exemplo do cofre do banco:

O cofre é o blockchain
A "conta" é uma caixa dentro do cofre
O "endereço" é o número da caixa
As "chaves" são usadas para desbloquear a caixa


Uma pequena imprecisão nesta imagem é que a chave pública ( o texto na chave ) e o endereço da conta ( o número do cofre ) não são os mesmos. Além disso, podemos ter mais de duas chaves por conta lol

Devido a essa dissociação, podemos usar o controle de várias contas com uma chave e uma conta com várias chaves, como um multi-sig.

Você pode ler mais sobre isso aqui.

🔑Adicione nossas chaves e conta ao nosso projeto
Precisamos informar o projeto Flow em nosso computador sobre essas chaves. Abra nosso flow.json arquive e adicione nossas chaves e a conta.

Dentro do seu flow.json na parte inferior, você deve ver uma seção para accounts. Adicionaremos nossa conta logo abaixo do emulator-account fazendo nosso arquivo ficar assim:

{
  "emulators": {
    "default": {
      "port": 3569,
      "serviceAccount": "emulator-account"
    }
  },
  "contracts": {},
  "networks": {
    "emulator": "127.0.0.1:3569",
    "mainnet": "access.mainnet.nodes.onflow.org:9000",
    "testnet": "access.devnet.nodes.onflow.org:9000"
  },
  "accounts": {
    "emulator-account": {
      "address": "f8d6e0586b0a20c7",
      "key": "3314a7fca73b5c091da87027265180a5d85e6930047f2d9bf98cc979d52c5022"
    },
    "testnet-account": {
      "address": "REPLACE_WITH_ACCOUNT_ADDRESS_FROM_WEBSITE",
      "key": "REPLACE_WITH_PRIVATE_KEY_GENERATED_VIA_CLI"
    }
  },
  "deployments": {}
}
Você tem duas coisas aqui - address e key. Substitua-os pelos salvos no site da torneira da rede de teste de fluxo e no terminal.

Agora você tem tudo o que precisa para implantar contratos e interagir com a rede de teste Flow!

🔥Implante nosso contrato
Recapitulação tão rápida: temos nossa CLI de fluxo instalada, inicializamos nosso projeto de fluxo e configuramos nossa conta de rede de teste. Feijão fresco. Vamos agora configurar o contrato que vamos implantar.

No flow.json você deve ver uma seção para contratos que atualmente se parece com:

"contracts": {},
Adicione nosso contrato a ele assim:

"contracts": {
		"BottomShot": "./contracts/BottomShot.cdc"
	}
Muito simples, não é?

Em seguida, role para baixo até a parte inferior do arquivo para "deployments": {} e adicione isto:

"deployments": {
	"testnet": {
		"testnet-account": [
			"BottomShot"
		]
	}
}
É assim que dizemos à nossa CLI de fluxo o que queremos implantar, com qual conta queremos implantá-la e nossa rede de escolha. Se tivéssemos outro contrato que queremos implantar na rede de teste, apenas o adicionaríamos abaixo do valor "BottomShot" e ele os implantaria em ordem.

A parte final disso está mudando o endereço da importação em nosso contrato. Altere a primeira linha para:

import NonFungibleToken from 0x631e88ae7f1d7c20;
Este é o endereço para o qual o contrato é implantado no Rede de teste de fluxo. Como a equipe Flow já o implantou, não precisamos reimplantá-lo.

E agora, no momento mágico, insira este comando no terminal para implantar:

flow project deploy --network testnet
Se tudo correr bem, você verá uma resposta de:

Deploying 1 contracts for accounts: testnet-account

BottomShot -> 0x7b6adb682517f137 (bd892198518206e8fed6f7d7bba520b94bc9036ff5cefc856eef242c114756ec)


BOOM! Você acabou de implantar seu primeiro contrato no testnet Flow!!!

Você pode ver todos os contratos nos quais implantou https://flow-view-source.com/testnet/

Coloque o endereço da sua conta e você verá o seu contrato NFT doente!



Recapitulação rápida Nesta seção:

Criamos chaves de fluxo e uma conta de testnet
Configuramos nosso ambiente com nossas chaves e o contrato
Implementamos nosso contrato em uma rede real!
Bem feito! Em seguida, faremos um aplicativo que permitirá alguém NFTs de hortelã deste contrato: D

🚨Relatório de andamento 🚨
Olá, implante de contrato inteligente Flow.

Poste o URL do seu contrato implantado no flow-view-source na seção #progress